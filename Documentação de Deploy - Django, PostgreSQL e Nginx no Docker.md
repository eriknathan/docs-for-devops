# Documentação de Deploy - Django, PostgreSQL e Nginx no Docker

## Introdução

Este documento técnico estabelece um guia formal para a implantação (deploy) de aplicações web desenvolvidas com o framework **Django**. A arquitetura de implantação proposta utiliza **Docker** e **Docker Compose** para a orquestração de contêineres, garantindo um ambiente consistente e isolado.

A pilha de serviços é composta por:

- **Nginx**: Atuando como um proxy reverso de alta performance, responsável por receber as requisições HTTP, servir arquivos estáticos e de mídia, e encaminhar as requisições dinâmicas para a aplicação.
- **Gunicorn**: Um servidor WSGI (Web Server Gateway Interface) robusto para Python, que executa a aplicação Django em um ambiente de produção.
- **PostgreSQL**: Um sistema de gerenciamento de banco de dados relacional de código aberto, utilizado para a persistência dos dados da aplicação.

O objetivo é fornecer um processo padronizado e replicável, desde a configuração do projeto até a execução em um ambiente de produção.

## 1. Estrutura de Diretórios do Projeto

Para uma organização coesa e manutenível, recomenda-se a seguinte estrutura de diretórios na raiz do projeto:

```
project-root/
├── app/                  # Diretório principal da aplicação Django
│   ├── settings.py       # Arquivo de configurações do projeto
│   ├── wsgi.py           # Configuração da interface WSGI
│   └── ...
├── static/               # Diretórios para arquivos estáticos globais (CSS, JS, etc.)
├── media/                # Diretório para arquivos de mídia enviados por usuários
├── Dockerfile            # Define a imagem Docker da aplicação
├── docker-compose.yml    # Orquestra a inicialização e comunicação dos serviços
├── nginx.conf            # Arquivo de configuração do Nginx
├── entrypoint.sh         # Script de inicialização do contêiner da aplicação
├── requirements.txt      # Lista de dependências Python do projeto
├── manage.py             # Utilitário de linha de comando do Django
├── .env                  # Armazena variáveis de ambiente (não deve ser versionado)
└── .gitignore            # Especifica arquivos a serem ignorados pelo Git
```

## 2. Configuração do Projeto Django (`settings.py`)

O arquivo `settings.py` deve ser configurado para se adaptar a diferentes ambientes (ex: desenvolvimento e produção) de forma dinâmica, lendo suas configurações a partir de variáveis de ambiente. Esta abordagem evita a exposição de dados sensíveis no código-fonte.

**Dependência:** É necessária a biblioteca `python-dotenv` para carregar variáveis de um arquivo `.env`. Certifique-se de que ela esteja listada no `requirements.txt`.

### **Implementação da Configuração Dinâmica**

O código abaixo demonstra como estruturar o `settings.py` para carregar configurações de forma segura e flexível.

```python
# app/settings.py
import os
from pathlib import Path
from dotenv import load_dotenv

# Define o diretório base do projeto
BASE_DIR = Path(__file__).resolve().parent.parent

# Carrega as variáveis de ambiente definidas no arquivo .env
load_dotenv()

# --- Configurações Gerais de Segurança ---

# Chave secreta para operações criptográficas. Essencial para a segurança.
SECRET_KEY = os.getenv('SECRET_KEY')

# Modo de depuração. DEVE ser 'False' em produção para evitar a exposição de informações sensíveis.
DEBUG = os.getenv('DEBUG', 'False') == 'True'

# Lista de hosts/domínios que podem servir a aplicação.
ALLOWED_HOSTS = os.getenv('DJANGO_ALLOWED_HOSTS', '127.0.0.1,localhost').split(',')

# Lista de origins confiáveis para requisições que alteram o estado (POST, PUT, etc.),
# essencial para proteção contra CSRF (Cross-Site Request Forgery), especialmente em HTTPS.
CSRF_TRUSTED_ORIGINS = os.getenv('DJANGO_CSRF_TRUSTED_ORIGINS', '').split(',')


# --- Configuração do Banco de Dados ---

# A variável 'DATABASE' no .env determina a configuração a ser utilizada.
DATABASE_CONFIG = os.getenv('DATABASE', 'sqlite')

if DATABASE_CONFIG == 'postgresql':
    # Configuração para o banco de dados PostgreSQL (ambiente de produção)
    DATABASES = {
        'default': {
            'ENGINE': os.getenv('POSTGRES_ENGINE', 'django.db.backends.postgresql'),
            'NAME': os.getenv('POSTGRES_DB'),
            'USER': os.getenv('POSTGRES_USER'),
            'PASSWORD': os.getenv('POSTGRES_PASSWORD'),
            'HOST': os.getenv('POSTGRES_HOST'),
            'PORT': int(os.getenv('POSTGRES_PORT', 5432)),
        }
    }
else:
    # Configuração para o banco de dados SQLite (ambiente de desenvolvimento local)
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': BASE_DIR / 'db.sqlite3',
        }
    }


# --- Configurações Comuns da Aplicação ---

INSTALLED_APPS = [
    # ...
]

# ... (Outras configurações como MIDDLEWARE, TEMPLATES, etc.)

# --- Configuração de Arquivos Estáticos e de Mídia ---

# URL base para servir arquivos estáticos.
STATIC_URL = '/static/'
# Diretório onde o comando `collectstatic` irá agrupar todos os arquivos estáticos para produção.
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# URL base para servir arquivos de mídia enviados pelos usuários.
MEDIA_URL = '/media/'
# Diretório no sistema de arquivos onde os arquivos de mídia serão armazenados.
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

## 3. Arquivos de Configuração da Infraestrutura

Esta seção detalha os arquivos que definem e configuram a infraestrutura em contêineres.

### **`Dockerfile` (Otimizado com Multi-Stage Build)**

O `Dockerfile` utiliza uma abordagem de múltiplos estágios (_multi-stage build_) para otimizar a imagem final. O primeiro estágio (`builder`) é usado para compilar as dependências, enquanto o segundo estágio (`runtime`) copia apenas os artefatos necessários, resultando em uma imagem de produção menor, mais limpa e com uma superfície de ataque reduzida.

```Dockerfile

# --- Estágio 1: Builder ---
# Utiliza uma imagem completa para instalar dependências do sistema e compilar as bibliotecas Python.
FROM python:3.12-slim as builder

WORKDIR /app

# Instala dependências do sistema necessárias para compilar pacotes como o psycopg2.
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Copia o arquivo de dependências e as compila em formato "wheel" para uma instalação mais rápida no próximo estágio.
COPY requirements.txt .
RUN pip wheel --no-cache-dir --wheel-dir /app/wheels -r requirements.txt


# --- Estágio 2: Runtime ---
# Utiliza a mesma imagem base slim para a imagem final, garantindo leveza.
FROM python:3.12-slim

WORKDIR /app

# Cria um usuário e grupo de sistema não-root para executar a aplicação, uma prática de segurança essencial.
RUN addgroup --system app && adduser --system --group app

# Copia as dependências pré-compiladas do estágio 'builder' e as instala.
COPY --from=builder /app/wheels /wheels
COPY --from=builder /app/requirements.txt .
RUN pip install --no-cache /wheels/*

# Copia o script de entrypoint e concede permissão de execução.
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Copia o código-fonte da aplicação.
COPY . .

# Altera a propriedade dos arquivos para o usuário não-root.
RUN chown -R app:app /app

# Define o usuário não-root como o padrão para a execução de comandos.
USER app

# Expõe a porta que o Gunicorn utilizará para servir a aplicação.
EXPOSE 8000

# Define o script de entrypoint como o comando de inicialização do contêiner.
ENTRYPOINT ["/entrypoint.sh"]
```

### `entrypoint.sh` (Script de Inicialização)

Este script é executado no momento da inicialização do contêiner `web`. Sua função é preparar o ambiente da aplicação antes de iniciar o servidor Gunicorn, executando tarefas essenciais como a aplicação de migrações de banco de dados e a coleta de arquivos estáticos.

```bash
#!/bin/sh
# entrypoint.sh

# A verificação da disponibilidade do banco de dados agora é gerenciada pelo healthcheck do Docker Compose.
sleep 5
echo "O banco de dados está pronto. Executando comandos de inicialização do Django..."

# Aplica as migrações do banco de dados.
python manage.py migrate --noinput

# Coleta todos os arquivos estáticos para o diretório STATIC_ROOT.
python manage.py collectstatic --noinput

# Inicia o servidor Gunicorn para servir a aplicação.
# O número de workers é um parâmetro de performance que deve ser ajustado
# com base nos recursos de CPU do servidor host. A fórmula (2 * CORES) + 1 é um bom ponto de partida.
echo "Iniciando o servidor Gunicorn..."
gunicorn app.wsgi:application --bind 0.0.0.0:8000 --workers 3
```

### **`docker-compose.yml` (Orquestração de Serviços)**

Este arquivo define e configura todos os serviços da aplicação (`db`, `web`, `nginx`), suas redes e volumes. O uso de `healthcheck` garante que o serviço da aplicação só inicie após o banco de dados estar totalmente operacional.

```yaml
---
services:
  # Serviço do Banco de Dados PostgreSQL
  db:
    image: postgres:15-alpine
    container_name: postgres_db
    volumes:
      - postgres_data:/var/lib/postgresql/data
    env_file:
      - .env
    networks:
      - app_network
    restart: unless-stopped
    healthcheck:
      # Comando que verifica se o servidor PostgreSQL está pronto para aceitar conexões.
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s

  # Serviço da Aplicação Django/Gunicorn
  web:
    build: .
    container_name: django_app
    volumes:
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    env_file:
      - .env
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app_network
    restart: unless-stopped

  # Serviço do Proxy Reverso Nginx
  nginx:
    image: nginx:stable-alpine
    container_name: nginx_server
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - static_volume:/app/staticfiles:ro # Monta como somente leitura (read-only)
      - media_volume:/app/media:ro       # Monta como somente leitura (read-only)
    depends_on:
      - web
    networks:
      - app_network
    restart: unless-stopped

networks:
  app_network:
    driver: bridge

volumes:
  postgres_data:
  static_volume:
  media_volume:
```

### `nginx.conf` (Configuração do Proxy Reverso)

Configura o Nginx para servir como a porta de entrada da aplicação. Ele serve arquivos estáticos e de mídia diretamente para máxima performance e atua como proxy reverso para as requisições dinâmicas, encaminhando-as para o Gunicorn.

```bash
# nginx.conf

# Define um grupo de servidores upstream para a aplicação Django.
upstream django_server {
    # 'web:8000' refere-se ao serviço 'web' na porta 8000, conforme definido no docker-compose.yml.
    server web:8000;
}

server {
    listen 80;
    server_name localhost; # Em produção, substitua por seu nome de domínio.

    # Define o tamanho máximo do corpo da requisição (ex: para uploads de arquivos).
    client_max_body_size 50M;

    # Bloco de localização para servir arquivos de mídia diretamente.
    location /media/ {
        alias /app/media/;
    }

    # Bloco de localização para servir arquivos estáticos diretamente.
    location /static/ {
        alias /app/staticfiles/;
    }

    # Bloco de localização para todas as outras requisições (proxy para o Django).
    location / {
        proxy_pass http://django_server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### **`.env` (Arquivo de Variáveis de Ambiente)**

Este arquivo centraliza todas as configurações que variam entre ambientes ou que são sensíveis. **Nunca deve ser incluído no controle de versão (Git).**

```bash
# .env

# --- Configurações Gerais do Django ---
SECRET_KEY='substitua-por-uma-chave-segura-e-aleatoria'
# Mude para True apenas para depuração. DEVE ser False em produção.
DEBUG=False
# Hosts/Domínios permitidos para a aplicação, separados por vírgula.
DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1,seu_dominio.com
# Origins confiáveis para requisições POST, especialmente com HTTPS.
DJANGO_CSRF_TRUSTED_ORIGINS=http://localhost:80,http://127.0.0.1:80,https://seu_dominio.com

# --- Configurações do Banco de Dados ---
# Defina como 'postgresql' para produção (Docker) ou 'sqlite' para desenvolvimento local.
DATABASE=postgresql

# Configurações do PostgreSQL (ignoradas se DATABASE=sqlite)
POSTGRES_ENGINE=django.db.backends.postgresql
POSTGRES_DB=nome_do_banco
POSTGRES_USER=usuario_do_banco
POSTGRES_PASSWORD=senha_forte_e_segura
POSTGRES_HOST=db # Nome do serviço do banco de dados no docker-compose.yml
POSTGRES_PORT=5432
```

## 4. Procedimentos de Execução

1. **Configuração do Ambiente**: Preencha o arquivo `.env` com as configurações apropriadas para o ambiente desejado (desenvolvimento ou produção).
2. **Execução em Ambiente de Produção (com Docker)**:
    - Certifique-se de que `DATABASE=postgresql` e `DEBUG=False` no arquivo `.env`.
    - Execute o build das imagens e inicie os contêineres em modo "detached":
        ```
        docker-compose up --build -d
        ```
    - Para a primeira execução, crie um superusuário para acessar a área administrativa do Django:
        ```
        docker-compose exec web python manage.py createsuperuser
        ```
    - A aplicação estará acessível em `http://localhost` ou no domínio configurado.
3. **Execução em Ambiente de Desenvolvimento (Local, sem Docker)**:
    - Certifique-se de que `DATABASE=sqlite` no arquivo `.env`.
    - Crie e ative um ambiente virtual Python.
    - Instale as dependências: `pip install -r requirements.txt`.
    - Execute as migrações iniciais: `python manage.py migrate`.
    - Inicie o servidor de desenvolvimento do Django: `python manage.py runserver`.

## 5. Gerenciamento e Manutenção

#### **Atualização da Aplicação**

Para aplicar alterações no código-fonte da aplicação, reconstrua a imagem do serviço `web` e reinicie os contêineres:

```
docker-compose up -d --build web
```

#### **Visualização de Logs**

Para monitorar os logs de um serviço em tempo real (ex: `web`):

```
docker-compose logs -f web
```

#### **Parada e Limpeza do Ambiente**

Para parar e remover os contêineres, redes e **volumes** (atenção: a opção `-v` remove permanentemente os dados do banco de dados):

```
docker-compose down -v
```

## 6. Considerações para um Ambiente de Produção Avançado

- **HTTPS com Let's Encrypt**: É imperativo proteger a comunicação com certificados SSL/TLS. O Nginx pode ser integrado ao Certbot para automatizar a obtenção e renovação de certificados gratuitos do Let's Encrypt.
- **Estratégia de Backup do Banco de Dados**: Implemente uma rotina de backup automatizada para o volume do PostgreSQL. Isso pode ser feito com um serviço adicional no Docker Compose que executa `pg_dump` periodicamente e envia os backups para um armazenamento seguro e externo (ex: Amazon S3, Google Cloud Storage).
