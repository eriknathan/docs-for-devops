Primeiramente precisamos criar o usuário da pessoa!
### **Listar Usuários**

```bash
sudo cat /etc/passwd
```
### **Criar Usuários**

```bash
sudo useradd -m nome_usuario
```

Logo após verificar na pasta `/Home` se a pasta do usuário foi criado: `/home/nome_usuario`
## **Criação da Chave SSH**

Para realizar a criação da chave ssh precisamos rodar o comando:
```bash
sudo ssh-keygen
```
### **O terminal vai pedir alguns interações, na primeira coloque o caminho onde deverá ficar a chave já atualizando o nome da chave!**

`Enter file in which to save the key (/root/.ssh/id_rsa): home/novo_usuário/.ssh/nome_usuario`

> Caso não tenha o diretório .ssh/, crie ele dentro da pasta do usuário: `mkdir .ssh/`

Após isso pode prosseguir apenas dando `enter`!

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): /home/novo_usuario/.ssh/nome_usuario
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/novo_usuario/.ssh/nome_usuario
Your public key has been saved in /home/novo_usuario/.ssh/nome_usuario
The key fingerprint is:
SHA256:6z/j4gbj45j4h5j24h425452jgbh2gphkh2/Z/Q root@DESKTOP-HVOONBM
The key's randomart image is:
+---[RSA 3072]----+
|..o.oo.+=o.=o. |
|.. ...+.=oo.* |
|o . = o.= . |
|o. .. . = . |
|o .. ...S. o |
| =. .. .o . |
|..= o. o. . |
|. o . .. .o E |
| ..ooo |
+----[SHA256]-----+
```

A chave foi criada no diretório. `/home/novo_usuario/.ssh/nome_usuario`
### **Da um cat na chave pública do novo usuário**

```bash
cat novo_usuario.pub
```

Copiar o conteúdo da chave publica, abrir em um editor de texto e renomear o final, exemplo: **(seu_usuario@DESKTOP-43HN54T)**, para: **novo_usuario@DESKTOP-43HN54T**

Copiar todo o conteúdo já com o final alterado!
### **Realizar a alteração na chave pública.**

```bash
sudo nano novo_usuario.pub
```

Com o conteúdo copiado, cole dentro do arquivo da chave pública do novo_usuario.pub

### **Criar o arquivo authorized_keys**

A chave pública é enviada a um servidor remoto de sua preferência para que você possa fazer login via SSH. A chave é adicionada a um arquivo especial dentro da conta de usuário em que você estará fazendo login chamado `/home/nome_usuario/.ssh/authorized_keys`

```bash
sudo nano authorized_keys
```

Cole o conteúdo da chave pública do novo usuário (já com o final alterado).
### **Realizar a troca do nome dos grupos**

Você pode perceber que após a criação ficará mais ou menos dessa forma:

```bash
rw-r--r-- 1 root root 579 Apr 1 08:31 authorized_keys
-rw------- 1 seu_usuario seu_usuario 2610 Apr 1 07:47 novo_usuario
-rw-r--r-- 1 seu_usuario seu_usuario 579 Apr 1 08:21 novo_usuario.pub
```

Você precisa alterar o nome dos grupos para o nome do novo usuário, para alterar rode este comando:

```bash
sudo chown -R novo_usuario:novo_usuario .ssh/
```

Com isso todo o conteúdo dentro do diretório .ssh/ vai está no grupo do novo usuário.

Se tiver feito tudo certo, deverá ficar desta forma:

```bash
rw-r--r-- 1 novo_usuario novo_usuario 579 Apr 1 08:31 authorized_keys
-rw------- 1 novo_usuario novo_usuario 2610 Apr 1 07:47 novo_usuario
-rw-r--r-- 1 novo_usuario novo_usuario 579 Apr 1 08:21 novo_usuario.pub
```

### **Mover a chave privada para seu usuário para realizar o teste local**

```bash
sudo cp novo_usuario /home/seu_usuario
```

Após isso, acessar a pasta e verificar se foi copiado com sucesso: `cd /home/seu_usuario`
### **Realizar a troca do grupo de root para seu usuário**

```bash
sudo chown seu_usuario:seu_usuario novo_usuario
```

---
# **Realizar o teste localmente**
### **Copiar a chave privada para seu computador**

```bash
scp servidor:/home/seu_usuario/novo_usuario .
```

O (.) referencia o diretório atual do seu usuário localmente, pode ser trocado por qualquer caminho de diretório que você queria guardar a chave.
### **Acessar a máquina do seu computador com a chave do novo_usuario**

```bash
ssh novo_usuario@servidor -i novo_usuario
```

# **Realizar o acesso ao BD com o DBeaver**

### **Fazer a conexão SSH no DBeaver**

Selecionar a opção: `User SSH Tunnel`
Informar o Host/IP:
Informa o User Name:
Authentication Method: Public Key
Selecionar o Private Key: diretório_onde_ta_salvo_a_chave/novo_usuario
Test tunnel configuration (conferir se a conexão foi realizada com sucesso)
### **Conectar com o BD**
  
Informar o Host:
Informa a Port:
Informa o Database:
Informa o Username:
Informa o Password:

---
# Modelo de envio para o desenvolvedor com as credenciais de acesso ao Banco:

> postgresql://user:password@host:port/database

### TUNEL_SSH:

IP: 0.000.00.00
USER: nome_usuario
KEY: nome_da_chave
### Nome do BD (Ex: DB - QA DO PROJETO):

HOST: 00.00.0.0
DB_NAME=nome_db
DB_USERNAME=username_db
DB_PASSWORD=password_db

### TUNEL_SSH:
IP: 44.204.218.76
USER: user
KEY: user

sudo chmod 600 nome_usuario