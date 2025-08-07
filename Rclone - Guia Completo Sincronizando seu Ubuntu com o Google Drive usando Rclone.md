# Rclone - Guia Completo Sincronizando seu Ubuntu com o Google Drive usando Rclone

Este guia irá cobrir a instalação do Rclone, a configuração do seu Google Drive (que chamaremos de `gdrive`) e os comandos essenciais para copiar, sincronizar e até mesmo "montar" seu Drive como uma pasta local no seu sistema.

#### **Passo 1: Instalação do Rclone**

A forma mais recomendada e simples de instalar o Rclone no Ubuntu (e garantir que você tenha a versão mais recente) é através do script oficial de instalação.

Abra o seu terminal (pressione `Ctrl+Alt+T`) e cole o seguinte comando:

```
curl https://rclone.org/install.sh | sudo bash
```

Este comando baixa o script de instalação e o executa com privilégios de administrador para instalar o Rclone no seu sistema. Após a conclusão, você pode verificar se a instalação foi bem-sucedida com o comando:

```
rclone --version
```

#### **Passo 2: Configuração do Acesso ao Google Drive**

Agora vem a parte mais importante: autorizar o Rclone a acessar sua conta do Google Drive. O Rclone possui um assistente interativo que facilita muito esse processo.

1. Inicie a Configuração
    
    No terminal, digite:
    
    ```
    rclone config
    ```
    
2. Crie um Novo "Remote"
    
    O Rclone chama cada conta de nuvem configurada de "remote". Como não há nenhum configurado, vamos criar um novo.
    
    O assistente irá mostrar:
    
    ```
    No remotes found - make a new one
    n) New remote
    q) Quit config
    n/q>
    ```
    
    Digite `n` e pressione **Enter**.
    
3. Dê um Nome ao Remote
    
    Você será solicitado a dar um nome. Vamos usar gdrive como você sugeriu.
    
    ```
    name> gdrive
    ```
    
    Digite `gdrive` e pressione **Enter**.
    
4. Escolha o Tipo de Armazenamento
    
    Uma longa lista de serviços de nuvem será exibida. Precisamos encontrar o Google Drive.
    
    ```
    ...
    17 / Google Drive
       \ "drive"
    ...
    Storage>
    ```
    
    Digite o número correspondente ao Google Drive (o número pode variar, mas o nome será `drive`) e pressione **Enter**.
    
5. Client ID e Client Secret
    
    O Rclone perguntará pelo client_id e client_secret.
    
    ```
    Google Application Client Id
    client_id>
    ...
    client_secret>
    ```
    
    **Simplesmente pressione Enter em ambos.** Deixar em branco fará com que o Rclone use suas próprias credenciais, o que é mais fácil para a maioria dos usuários.
    
6. Escopo de Permissão
    
    Você precisa definir o nível de acesso que o Rclone terá.
    
    ```
    scope>
    1 / Full access to all files, excluding Application Data Folder.
      \ "drive"
    2 / Read-only access to file metadata and file contents.
      \ "drive.readonly"
    ...
    ```
    
    A1 opção `1` (acesso total) é a mais comum e versátil. Digite `1` e pressione **Enter**.
    
7. **Outras Configurações**
    
    - `root_folder_id`: Pressione **Enter** para deixar em branco (acesso ao Drive inteiro).
    - `service_account_file`: Pressione **Enter** para deixar em branco.
    - `Edit advanced config?`: Digite `n` e pressione **Enter**.
8. Autenticação Remota (via Navegador)
    
    O assistente perguntará se você quer usar a auto-configuração.
    
    ```
    Remote config
    Use auto config?
     y) Yes (default)
     n) No
    y/n> y
    ```
    
    Digite `y` e pressione **Enter**.
    
    Neste momento, o Rclone irá gerar um link e pedirá para abri-lo no seu navegador. Copie o link e cole na barra de endereços do seu browser.
    
    - Faça login na sua conta do Google.
    - Conceda as permissões que o Rclone está solicitando (ele dirá "rclone wants to access your Google Account").
    - Após autorizar, você verá uma mensagem de "Success!" no navegador e poderá fechá-lo. O terminal detectará a autenticação automaticamente.
9. **Finalizar a Configuração**
    
    - O terminal perguntará se você quer configurar como um "Shared Drive (Team Drive)". Se você usa uma conta pessoal, digite `n` e pressione **Enter**.
    - Você verá um resumo da configuração do remote `gdrive`. Se tudo estiver correto, digite `y` para salvar.
    - Você voltará ao menu principal do `rclone config`. Agora você pode ver seu remote `gdrive` listado. Digite `q` para sair.

**Pronto! Seu Rclone está configurado.**

#### **Passo 3: Comandos Essenciais de Sincronização e Uso**

A sintaxe básica dos comandos é: `rclone [comando] [origem] [destino]`

O seu remote do Google Drive é sempre referenciado como `gdrive:`. Uma pasta dentro dele seria `gdrive:NomeDaPasta/Subpasta`.

**1. Listar Pastas e Arquivos**

- Para listar as pastas na raiz do seu Google Drive:
    
    ```
    rclone lsd gdrive:
    ```
    
- Para listar todos os arquivos em uma pasta específica:
    
    ```
    rclone ls gdrive:Backups/Documentos
    ```
    

2. Copiar Arquivos (copy)

O comando copy copia arquivos da origem para o destino, mas não apaga arquivos no destino que não existam na origem.

- **Upload (Local para Nuvem):** Copiar uma pasta do seu Ubuntu para o Google Drive.
    
    ```
    rclone copy ~/Documentos/Importantes gdrive:Backup/Documentos
    ```
    
- **Download (Nuvem para Local):** Copiar uma pasta do Google Drive para o seu Ubuntu.
    
    ```
    rclone copy gdrive:Fotos/2024 ~/Imagens/Viagem2024
    ```
    

3. Sincronizar Arquivos (sync)

O comando sync faz com que o destino se torne uma cópia exata da origem.

⚠️ **MUITO CUIDADO:** Se um arquivo existe no destino mas não na origem, **ele será apagado do destino** para que os dois fiquem idênticos.

- **Sincronizar do Local para a Nuvem (Backup):** Faz com que a pasta `Backup/Projetos` na nuvem seja um espelho exato da sua pasta local `~/Projetos`.
    
    ```
    rclone sync ~/Projetos gdrive:Backup/Projetos
    ```
    
- **Sincronizar da Nuvem para o Local:** Faz com que sua pasta local `~/TrabalhoRemoto` seja um espelho exato da pasta `Trabalho` na nuvem.
    
    ```
    rclone sync gdrive:Trabalho ~/TrabalhoRemoto
    ```
    

**Dica de Segurança: Simule Antes de Executar!**

Antes de usar sync, sempre teste com a flag --dry-run. Ela mostrará o que o Rclone iria fazer (copiar, apagar) sem realmente fazer nada.

```
rclone sync --dry-run ~/Projetos gdrive:Backup/Projetos
```

Dica de Usabilidade: Mostrar Progresso

Para ver o progresso das transferências (velocidade, arquivos, porcentagem), adicione a flag -P.

```
rclone sync -P ~/Projetos gdrive:Backup/Projetos
```

#### **Passo 4: Montando o Google Drive como um Disco Local (Avançado)**

Esta é uma das funcionalidades mais poderosas do Rclone. Ela permite que você acesse seu Google Drive como se fosse um HD externo conectado ao seu computador.

1. Crie uma Pasta para Montagem
    
    Primeiro, crie um diretório vazio onde o Drive será "montado".
    
    ```
    mkdir ~/GoogleDrive
    ```
    
2. **Execute o Comando de Montagem**
    
    ```
    rclone mount gdrive: ~/GoogleDrive &
    ```
    
    - `gdrive:` é o seu remote.
    - `~/GoogleDrive` é o ponto de montagem que criamos.
    - O `&` no final faz com que o comando seja executado em segundo plano, liberando seu terminal.

Agora você pode abrir seu gerenciador de arquivos, navegar até a pasta `GoogleDrive` na sua Home e ver todos os seus arquivos da nuvem. Você pode abri-los, editá-los e salvá-los diretamente.

3. **Para Desmontar** Quando quiser desmontar a pasta, use o comando:
    
    ```
    fusermount -u ~/GoogleDrive
    ```

---
#backup #drive