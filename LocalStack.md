# O que é o LocalStack?

**[LocalStack](https://localstack.cloud/)** é um emulador de serviço em nuvem que roda em um único contêiner no seu laptop ou no ambiente de IC. Com o LocalStack, você pode executar seus aplicativos AWS ou Lambdas inteiramente em sua máquina local sem se conectar a um provedor de nuvem remota!

Esteja você testando aplicativos CDK complexos ou configurações do Terraform, ou apenas começando a aprender sobre os serviços da AWS, o LocalStack ajuda a acelerar e simplificar seu fluxo de trabalho de teste e desenvolvimento.

O LocalStack suporta um número crescente de **[Serviços AWS](https://docs.localstack.cloud/user-guide/aws/)**, como AWS **[Lambda](https://docs.localstack.cloud/user-guide/aws/lambda/)**, **[S3](https://docs.localstack.cloud/user-guide/aws/s3/)**, DynamoDB, **[Kinesis](https://docs.localstack.cloud/user-guide/aws/kinesis/)**, **[SQS](https://docs.localstack.cloud/user-guide/aws/sqs/)**, SNS e muito mais! **[LocalStack Pro](https://localstack.cloud/pricing)** suporta APIs adicionais e recursos avançados para facilitar o desenvolvimento da nuvem!

Você pode encontrar uma lista abrangente de APIs suportadas em nossas **[Cobertura de recursos](https://docs.localstack.cloud/user-guide/aws/feature-coverage/)** página.

O LocalStack também fornece recursos adicionais para facilitar sua vida como desenvolvedor de nuvem! Confira LocalStack's **[Ferramentas do desenvolvedor de nuvem](https://docs.localstack.cloud/user-guide/tools/)**

## Inicio

Ao iniciarmos o desenvolvimento de aplicações com AWS, encontramos frequentemente um desafio: como testar nossa aplicação de maneira eficiente e sem gastar muito dinheiro. Para superar esse obstáculo, foi criado o LocalStack, que simula o ambiente da AWS em seu próprio computador, tornando o processo de teste mais econômico e eficiente.

O LocalStack possui uma versão paga, mas os recursos da versão community nos ajuda no dia-a-dia executando serviços como Secret Manager, SQS, SSM e outros recursos.

[LocalStack](https://localstack.cloud/)

## Pré-Requisitos

A seguir segue os requisitos necessários para executar o LocalStack:

- **Docker** - [https://www.docker.com/](https://www.docker.com/)
- **AWS Cli** - [https://aws.amazon.com/pt/cli/](https://aws.amazon.com/pt/cli/)

O **AWS Cli** é necessário para efetuar os testes e verificar se a instancia do LocalStack subiu corretamente. Siga os passos de instalação no endereço acima.

O LocalStack utiliza o Docker para sua execução. Essa é a forma mais fácil para inicia-lo em sua **máquina local.**

## Repositórios

Será necessário acessar o repositório do LocalStack no GitHub para efetuar a execução:

- **LocalStack** - https://github.com/localstack/localstack.git

## Executando o LocalStack

Após os pré-requisitos estarem instalados corretamente, podemos efetuar a execução do LocalStack. Para isso, vamos efetuar o **clone do repositório** no GitHub:

```bash
git clone https://github.com/localstack/localstack.git
```

Para executar o LocalStack, entre no diretório onde foi clonado o projeto e execute o docker-compose:

```bash
docker compose up 
```

O clone foi efetuado justamente para obter esse arquivo docker-compose.yml

Se deu tudo certo, irá aparecer uma tela conforme imagem abaixo, informando a subida do serviço e sua respectiva porta:

![Untitled](localstat.png)

> **Importante:** O LocalStack utiliza da porta 8080 para subida de alguns serviços, por isso verifique se não existe nenhuma outra aplicação utilizando o mesmo número de porta.

## Testando o LocalStack

> É preciso realizar a instalação do AWS CLI

Antes de iniciar crie um perfil especifico para a execução do **LocalStack** a partir do **AWS Cli**:

```bash
aws configure
```

Utilizaremos o **AWS Cli** para criar um recurso no **LocalStack** simulando a execução em um ambiente real.

No arquivo de config do aws cli, criamos um novo perfil e especificamos a região.

```bash
[profile localstack]
region = us-east-1
```

No arquivo de credencials, criamos também um para o localstack:

```bash
[localstack]
aws_access_key_id= localstack
aws_secret_access_key = localstack
```

Normalmente essa etapa é realizada no aws configure.

---
Neste caso vamos criar um recurso do **AWS S3 Bucket**:

[Armazenamento S3 - Simple Storage Service - Amazon Web Services](https://aws.amazon.com/pt/s3/)

### O Processo

Nesta etapa realizaremos a criação de um bucket no S3, fazemos o envio de um arquivo para ele e realizando o download do mesmo para um novo diretório

```bash
# Criando o bucket chamado "usuario"
aws --endpoint http://localhost:4566 --profile localstack s3 mb s3://usuario
make_bucket: usuario

# Listando o que tem no bucket
aws --endpoint http://localhost:4566 --profile localstack s3 ls s3://usuario

# Criando um arquivo para mandar pro bucket
nano teste.txt  

# Enviando esse arquivo para o bucker       
aws --endpoint http://localhost:4566 --profile localstack s3 cp teste.txt s3://usuario
upload: ./teste.txt to s3://usuario/teste.txt 

# Verificando se o arquivo foi enviado para o bucket com sucesso
aws --endpoint http://localhost:4566 --profile localstack s3 ls s3://usuario          
2023-02-09 11:46:09         22 teste.txt

# Criando um diretório para fazer o download do arquivo nele               
mkdir bkp

# Entrando no diretório          
cd bkp  
                     
# Realizando o donwload do arquivo para o diretório criado
aws --endpoint http://localhost:4566 --profile localstack s3 cp s3://usuario/teste.txt .
download: s3://usuario/teste.txt to ./teste.txt     

# Listando o arquivo baixado no diretório         
ls    
teste.txt
```

### O Log desse processo

```bash
# LOG - Criando o bucket chamado usuario
2023-02-09T14:43:08.710  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.CreateBucket => 200
# LOG - Listando o que tem no bucket
2023-02-09T14:43:38.273  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.ListObjectsV2 => 200
# LOG - Enviando esse arquivo para o bucker       
2023-02-09T14:46:09.623  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.PutObject => 200
# LOG - Listando o que tem no bucket
2023-02-09T14:47:26.490  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.ListObjectsV2 => 200
# LOG - Realizando o donwload do arquivo para o diretório criado
2023-02-09T14:48:44.219  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.HeadObject => 200
2023-02-09T14:48:44.233  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.GetObject => 200
```

Sua instancia do LocalStack está em funcionando perfeitamente !!!!!!!!!

## Observações Importantes

- Na execução do comando aws cli devemos especificar o endpoint do LocalStack. A região é opcional:

    ```bash
    ... --endpoint http://localhost:4566 ... --region sa-east-1
    ```

- Importante lembrar de criar um perfil para o **LocalStack** no **AWS Cli**
- O **LocalStack** utiliza da porta **8080**. Caso tenha alguma aplicação utilizando essa porta, altere o arquivo do **docker-compose.yml** conforme mostrado abaixo:

    ```bash
    ...
    ports:
          - "4566:4566"
          - "4571:4571"
          - "${PORT_WEB_UI-8081}:${PORT_WEB_UI-8080}"
    ...
    ```

## Por fim.

Caso precise testar os recursos básicos da AWS e não quer gastar rios de dinheiros, utilize o LocalStack que irá ajudá-lo na simulação de um ambiente local da AWS.
