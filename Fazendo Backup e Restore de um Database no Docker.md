# Fazendo Backup e Restore de um Database no Docker

## 1. Backup do Banco de Dados

```bash
docker exec -t nome_container_db pg_dump --no-owner -U user_db name_db > backup-database-20250312.sql
```

Este comando utiliza o **`pg_dump`** para realizar um backup do banco de dados chamado **`name_db`**. O backup é redirecionado para um arquivo chamado **`backup-database-20250312.sql`**.

- **`docker exec -t nome_container_db`**: Executa um comando dentro do contêiner chamado **`nome_container_db`**.
- **`pg_dump --no-owner -U user_db name_db`**: Utiliza o utilitário **`pg_dump`** para realizar um backup do banco de dados **`name_db`**. A opção **`-no-owner`** evita a inclusão de informações de propriedade, e **`U user_db`** especifica o usuário para autenticação.
- **`> backup-database-20250312.sql`**: Redireciona a saída do comando para um arquivo chamado **`backup-database-20250312.sql`**.
---
## 2. Acesso ao Shell do Container que vai ser feito o restore

```bash
docker exec -it nome_container_db /bin/sh
```
  
Este comando inicia um shell interativo (**`/bin/sh`**) dentro do contêiner chamado **`nome_container_db`**.

- **`docker exec -it nome_container_db`**: Executa um shell interativo (**`/bin/sh`**) dentro do contêiner chamado **`nome_container_db`**.
## 3. Conexão ao PostgreSQL dentro do contêiner

```sql
psql postgres namedb
```

Aqui, está sendo utilizado o utilitário **`psql`** para se conectar ao servidor PostgreSQL. A base de dados utilizada é **`namedb`** e o usuário é **`namedb`**.

- **`psql postgres namedb`**: Inicia uma sessão interativa com o servidor PostgreSQL. A base de dados utilizada é **`namedb`** e o usuário é **`postgres`**.
## 4. Desconexão de usuários do banco de dados

```sql
SELECT *, pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE pid <> pg_backend_pid()
AND datname = 'name_db';
```

Este bloco SQL consulta as atividades no banco de dados **`name_db`** e termina as conexões ativas, exceto a conexão do próprio processo.

- Esta consulta SQL procura por sessões ativas no banco de dados **`name_db`** e utiliza **`pg_terminate_backend`** para encerrar essas sessões, excluindo-as.
 
## 5. Exclusão do banco de dados (com erro)
  
```sql
drop database name_db;
```

Aqui, tenta-se excluir o banco de dados **`name_db`**, mas ocorre um erro devido à presença de conexões ativas.

- Tentativa de excluir o banco de dados **`name_db`**, que falha se houver conexões ativas. Se ser erro realizar o comando de Desconexão de usuário novamente.
## 6. Exclusão e recriação do banco de dados

```sql
drop database name_db;
create database name_db;
```

Após terminar as conexões ativas, o banco de dados **`name_db`** é excluído e recriado.
## 7. Restauração do backup no novo banco de dados

```sql
cat backup-database-20250312.sql | docker exec -i nome_container_db psql -U user_db -d name_db
```

O arquivo de backup **`backup-database-20250312.sql`** é lido e seu conteúdo é usado para restaurar o banco de dados **`name_db`** dentro do contêiner.

- Lê o conteúdo do arquivo de backup **`backup-database-20250312.sql`** e utiliza o utilitário **`psql`** dentro do contêiner para restaurar o banco de dados **`name_db`**.