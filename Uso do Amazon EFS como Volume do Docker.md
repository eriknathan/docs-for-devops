# Uso do Amazon EFS como Volume do Docker

## 1. Introdução

Amazon Elastic File System (EFS) é um sistema de arquivos elástico e escalável que pode ser usado como armazenamento persistente para containers Docker. Este documento detalha os passos necessários para configurar um volume Docker utilizando EFS.

## 2. Criando um EFS na AWS

1. Acesse o **AWS Console**.
2. Navegue até **EFS (Elastic File System)**.
3. Clique em **Criar sistema de arquivos**.
4. Selecione a **VPC** onde os containers estão rodando.
5. Configure o **Security Group** para permitir tráfego na **porta 2049 (NFS)**.
6. Anote o **File System ID** do EFS criado.

## 3. Instalando o Cliente NFS no Servidor

No servidor onde o Docker está rodando (exemplo: uma instância EC2), instale o cliente NFS:

```sh
sudo apt update && sudo apt install -y nfs-common
```

Verifique se o EFS pode ser montado manualmente:

```sh
sudo mount -t nfs4 fs-XXXXXXXX.efs.REGION.amazonaws.com:/ /mnt/efs
```

Caso tudo esteja correto, desmonte:

```sh
sudo umount /mnt/efs
```

## 4. Configurando o Docker Compose com EFS

Adicione a configuração do volume no arquivo `docker-compose.yml`:

```yaml
version: '3.8'

services:
  app:
    image: minha-aplicacao
    volumes:
      - efs-volume:/app/data
    restart: always

volumes:
  efs-volume:
    driver: local
    driver_opts:
      type: "nfs"
      o: "addr=fs-XXXXXXXX.efs.REGION.amazonaws.com,nfsvers=4.1"
      device: ":/"
```

> **Nota:** Substitua `fs-XXXXXXXX.efs.REGION.amazonaws.com` pelo **File System ID** correto e ajuste a região.

## 5. Subindo os Containers

Execute o seguinte comando para iniciar os containers com EFS:

```sh
docker compose up -d
```

Para verificar se o volume foi criado corretamente:

```sh
docker volume inspect efs-volume
```

## 6. Verificando a Persistência dos Dados

Para testar se os arquivos estão sendo armazenados corretamente no EFS:

```sh
docker exec -it <container_id> sh
cd /app/data
touch test.txt
exit
```

Agora, monte o EFS manualmente e verifique se o arquivo `test.txt` está lá:

```sh
ls /mnt/efs
```

Se o arquivo estiver presente, o EFS está configurado corretamente como volume do Docker.

## 7. Conclusão

Agora o Docker está utilizando **Amazon EFS como volume persistente**, garantindo que os dados sejam compartilhados entre containers e escaláveis conforme necessário. Essa abordagem permite maior flexibilidade e confiabilidade no armazenamento de dados.
