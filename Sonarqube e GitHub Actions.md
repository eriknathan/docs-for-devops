# SonarQube e GitHub Actions

Aprenda como integrar o SonarQube no GitHub Actions e torne seu código mais confiável e livre de vulnerabilidades.

## Visão Geral

O SonarQube é uma ferramenta de revisão de código automatizada e autogerenciada que ajuda sistematicamente a entregar código limpo. Como um elemento central da solução Sonar, o SonarQube se integra ao seu fluxo de trabalho existente e detecta problemas no código, permitindo que você realize inspeções contínuas de qualidade nos seus projetos.

Este documento descreve os passos para integrar o SonarQube com o GitHub Actions, permitindo que você automatize a análise de código e verificações de qualidade no seu repositório.

## Pré-requisitos

Antes de começar, certifique-se de que você possui:
- Uma conta no GitHub.
- Uma conta no SonarQube ou uma instância do SonarQube rodando na sua máquina local ou em um servidor remoto.
- Acesso ao repositório que deseja integrar com o SonarQube.
- Um GitHub App.
- Permissões do GitHub App.

## Passos a Seguir

### 1. Configurar o SonarQube
- Acesse o SonarQube no seu navegador.
- Faça login no SonarQube com as credenciais de administrador.
- Clique no ícone de Administrador → Minha Conta → Segurança → Gerar para criar um token para o seu projeto. Anote/salve esse token, pois ele será necessário posteriormente (forneça os detalhes conforme necessário, como tipo de token e data de expiração).
### 2. Criar um novo projeto no SonarQube
- Clique no botão “Criar projeto” no painel do SonarQube.
- Selecione GitHub ou Manual.
- Se desejar criar um novo projeto, clique em “Criar projeto” no painel do SonarQube, forneça o “Nome de exibição do projeto” e a “Chave do projeto” e clique em “Configurar”.
- Você receberá um arquivo YML e a chave do projeto (`sonar.projectKey`) com base no seu projeto. Copie esse arquivo, pois precisaremos dele mais tarde.

### 3. Adicionar o Token e URL do SonarQube nas Secrets do GitHub
- Acesse seu repositório no GitHub e clique na aba “Settings” (Configurações).
- Clique em “Secrets” na barra lateral.
- Clique em “New secret” (Nova secret) e insira `SONAR_TOKEN` como o nome.
- Insira o token gerado para o seu projeto no SonarQube.
- Crie outra secret para `SONAR_HOST_URL` como nome e insira a URL do painel do SonarQube como valor.
- Clique em “Add secret” para salvar.

### 4. Configurar o GitHub Actions
- Acesse seu repositório no GitHub e clique na aba “Actions”.
- Clique no botão “New workflow” (Novo workflow) e selecione “Set up a workflow yourself” (Configurar um workflow manualmente).
- Dê um nome ao seu workflow e crie um novo arquivo YAML.
- Cole o conteúdo do arquivo YML que copiamos anteriormente.
- Cole também o arquivo `sonar-project.properties`.
- Exemplo de arquivo YAML:
	```yaml    
    name: Integração SonarQube
    on:
      push:
        branches:
          - main
    jobs:
      sonarqube:
        name: Análise SonarQube
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
            with:
              fetch-depth: 0
          - name: Executar SonarQube Scan
            uses: sonarsource/sonarqube-scan-action@master
            env:
              SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
              SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
	```

## Conclusão
Pronto! O GitHub Actions agora executará uma análise do SonarQube a cada push na branch principal. Você pode visualizar os resultados da análise no SonarQube acessando o painel do projeto. Com essa integração, seu código estará mais seguro, confiável e livre de vulnerabilidades, garantindo maior qualidade e eficiência no desenvolvimento.