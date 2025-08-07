# Como Aumentar o Disco de uma Instância na AWS

Este documento descreve o processo de expansão do disco de uma instância em sistemas baseados em Linux.

## Passos

### 1. Verificar o Espaço em Disco Atual

Antes de iniciar o procedimento, é importante verificar o status atual do disco e suas partições:

```
df -h
```

### 2. Identificar os Dispositivos de Bloco e Partições

Liste os dispositivos de bloco para identificar o nome correto do disco que será expandido:

```
lsblk
```

### 3. Instalar Ferramentas Necessárias (se aplicável)

Em algumas distribuições, pode ser necessário instalar a ferramenta `cloud-guest-utils`, que contém o comando `growpart`:

```
sudo apt-get install cloud-guest-utils
```

### 4. Expandir a Partição

Expanda a partição principal do disco (ajuste conforme necessário, dependendo do dispositivo identificado no `lsblk`):

```
sudo growpart /dev/nvme0n1 1
```

Aqui, `/dev/nvme0n1` é o disco e `1` representa a partição que será expandida.

### 5. Redimensionar o Sistema de Arquivos

Após expandir a partição, redimensione o sistema de arquivos para ocupar o novo espaço disponível:

```
sudo resize2fs /dev/nvme0n1p1
```

Para sistemas XFS, utilize:

```
sudo xfs_growfs /
```

### 6. Verificar se o Espaço Foi Expandido Corretamente

Após a execução dos comandos, valide se a alteração foi aplicada corretamente:

```
df -h
```

## Considerações

- Certifique-se de fazer um snapshot da instância antes de realizar qualquer alteração.
    
- Se a instância estiver utilizando LVM, pode ser necessário um procedimento adicional para expandir o volume lógico.
    
- Para instâncias que utilizam discos EBS na AWS, é necessário primeiro aumentar o tamanho do volume pelo console ou via CLI antes de seguir este procedimento.