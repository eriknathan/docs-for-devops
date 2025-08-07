# Restic

Restic é um software de backup rápido, eficiente e seguro. Escrito em GO e capaz de criptografar dados com AES-256.

Documentação disponível em: https://restic.readthedocs.io/en/latest/
## Instalação

No ubuntu a instalação é feita com o seguinte comando:

```bash
sudo apt install restic
```

## Repositórios

O Restic trabalha com o repositório que versiona os snapshots de backups. Os repositórios podem ser armazenados localmente ou em um serviço de storage remoto como o AWS S3.
### Iniciando um repositório no AWS S3

Um novo repositório é criado com o seguinte comando:

```bash
restic init --repo s3:s3.amazonaws.com/<bucket_name_here> init
```

Na criação do repositório é solicitado a criação de uma senha.

```bash
restic init --repo s3:s3.amazonaws.com/<bucket_name_here> init
enter password for new repository:
enter password again:
created restic repository 085b3c76b9 at s3:s3.amazonaws.com/<bucket_name_here>
Please note that knowledge of your password is required to access the repository.
Losing your password means that your data is irrecoverably lost.
```

> Não é possível recuperar a senha em caso de perca ou esquecimento e sem ela é impossível restaurar os dados.

### Listando os snapshots

Os snapshots de um repositório podem ser visualizados com o seguinte comando:

```bash
restic --repo s3:s3.amazonaws.com/<bucket_name_here> snapshots
```

Será listado todos os snapshots disponíveis como no exemplo abaixo:

```bash
ID        Time                 Host             Tags        Paths
-------------------------------------------------------------------------------------------------------------
04c949ac  2022-05-13 11:09:47  ip-172-31-90-35              /var/lib/docker/volumes/jira_jira_home/_data/data
c136dc48  2022-05-13 11:28:20  ip-172-31-90-35              /var/lib/docker/volumes/jira_jira_home/_data/data
62f8099a  2022-05-13 12:00:01  ip-172-31-90-35              /var/lib/docker/volumes/jira_jira_home/_data/data
d1e5612f  2022-05-13 13:00:01  ip-172-31-90-35              /var/lib/docker/volumes/jira_jira_home/_data/data
-------------------------------------------------------------------------------------------------------------
4 snapshots
```

### Removendo um snapshot

É possível remover um snapshot com o seguinte comando:

```bash
restic --repo s3:s3.amazonaws.com/<bucket_name_here> forget id_do_repositorio
```

É possível que durante a remoção de um repositório o restic acuse que o mesmo encontra-se travado por algum processo e isso pode ser resolvido com o comando:

```bash
restic unlock --repo s3:s3.amazonaws.com/<bucket_name_here>
```

## Backup

Após ter iniciado um repositório podemos criar o primeiro snapshot com o comando:

```bash
restic -r s3:s3.amazonaws.com/<bucket_name_here> --verbose backup /caminho/do/diretorio/a/ser/salvo
```

> Durante a criação do snapshot será solicitada a inserção da senha do repositório. Para rotinas automatizadas podemos passar a senha atráves de um arquivo com o parâmetro -p arquivo_da_senha

> O Restic implementa os backups de forma incremental, então basta executar o mesmo comando que ele verifica as alterações realizadas no diretório e envia para o AWS S3 somente os arquivos modificados ou adicionados.

## Backup

Para resturar um backup pode ser pego o último snapshot ou um especifíco. Para restaurar uma backup a partir do último ponto usamos o comando:

```bash
restic --repo s3:s3.amazonaws.com/<bucket_name_here> restore latest --target /caminho/do/diretorio/onde/salvar/os/dados
```

Para um ponto especifíco deve ser informado ao invés de latest o id do snapshot.

```bash
restic --repo s3:s3.amazonaws.com/<bucket_name_here> restore id_do_snapshot --target /caminho/do/diretorio/onde/salvar/os/dados
```
