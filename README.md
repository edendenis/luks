<!-- LOGOTIPO DO PROJETO -->
<div style="display: flex; justify-content: center;">
   <a href="https://github.com/edendenis/luks">
     <img src="figures/gold_edf_technology_logo_transparent_background_and_gold_name.png" alt="Logo" width="160" height="160">
   </a>
</div>

<h3 align="center">Como configurar/instalar/usar o `luks` no `Linux Ubuntu`</h3>

<!-- <div style="display: flex; justify-content: center;">
  <a href="https://zenodo.org/doi/10.5281/zenodo.10668919">
    <img src="https://zenodo.org/badge/758237447.svg" alt="DOI">
  </a>
</div> -->

<p align="center">
 Neste documento estão contidos os principais comandos e configurações para configurar/instalar/usar o `luks` no `Linux Ubuntu`.
 <br />
 <a href="https://github.com/edendenis/luks"><strong>Explore os documentos »</strong></a>
 <br />
 <br />
 <a href="https://github.com/edendenis/luks">Ver demonstração</a>
 ·
 <a href="https://github.com/edendenis/luks">Relatar bug</a>
 ·
 <a href="https://github.com/edendenis/luks">Solicitar recurso</a>
</p>


# Como configurar/instalar/usar o `luks` no `Linux Ubuntu`

## Resumo

Neste documento estão contidos os principais comandos e configurações para configurar/instalar/usar o `luks` no `Linux Ubuntu`.

## _Abstract_

_In this document are contained the main commands and settings to set up/install/use the `luks` on `Linux Ubuntu`._


### Construído com

Esta seção deve listar todas as principais estruturas/bibliotecas usadas para inicializar seu projeto. Deixe quaisquer complementos/plugins para a seção de agradecimentos. Aqui estão alguns exemplos.

* [![Python 3.8](https://img.shields.io/badge/Python%203.8-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
* [![Anaconda](https://img.shields.io/badge/Anaconda-44A833?style=flat-square&logo=anaconda&logoColor=white)](https://www.anaconda.com/)

<p align="right">(<a href="#readme-top">voltar ao topo</a>)</p>


<!-- COMEÇANDO -->
### Começando

Este é um exemplo de como você pode dar instruções sobre como configurar seu projeto localmente.
Para obter uma cópia local instalada e funcionando, siga estas etapas simples de exemplo.

### Pré-requisitos

Este é um exemplo de como listar os itens necessários para usar o software e como instalá-los.
* Python 3.8
* Anaconda 24.1.0
* Git
* IDE para executar o arquivo `.ipynb` (PyCharm, Spyder, VS Code etc.)

<p align="right">(<a href="#readme-top">voltar ao topo</a>)</p>


## Descrição [2]

### `luks`

LUKS (Linux Unified Key Setup) é uma especificação de criptografia de disco completo amplamente utilizada no `Linux`. Ele permite criar volumes criptografados, fornecendo uma camada adicional de segurança para os dados armazenados no disco. O `LUKS` gerencia chaves de criptografia, permitindo o acesso seguro aos dados somente após a autenticação bem-sucedida do usuário. É uma ferramenta valiosa para proteger informações confidenciais em sistemas `Linux`.

### `lvm`

LVM (Logical Volume Manager) é uma tecnologia de gerenciamento de armazenamento flexível para sistemas Linux. Permite criar volumes lógicos que podem ser redimensionados facilmente, independentemente do tamanho do disco físico subjacente. Com o LVM, os administradores de sistema podem criar, redimensionar e mover volumes lógicos sem interromper o sistema ou perder dados. Isso proporciona maior flexibilidade e eficiência na alocação e gerenciamento de espaço em disco.

### `recuezilla`

`Rescuezilla` é uma ferramenta de recuperação de sistema baseada em Linux, projetada para ajudar na restauração e backup de dados em caso de falhas no sistema. Com uma interface amigável e recursos poderosos, como clonagem de disco e recuperação de arquivos perdidos, o `Rescuezilla` é uma solução eficaz para lidar com problemas de software e proteger seus dados importantes.


## 1. Configurar/Instalar/Usar as `partições` no `Linux Ubuntu` (caso ainda não esteja instalado) **COM** criptografia [2]

Para configurar/instalar/usar as `partições` no `Linux Ubuntu`, siga as configuraçõea abaixo (de prefeência do menor armazenamento para o MAIOR):

| Número | Size (MB)   | Type of the new partition | Local for the new partition | Use as                        | Mount point       | Tipo       | Início da partição | Final da partição     | Observação(ções) |
|:------:|:-----------:|:-------------------------:|:---------------------------:|:-----------------------------:|:-----------------:|:-----------|:-------------------|:----------------------|:-----------------|
| 1      | `512`       | `Primary`                 | `Beginning of this space`   | `EFI System Partition`        | `/boot/efi`       | `fat32`    | `1MiB`             | `513MiB`              | Quando se fala em deixar um espaço de `1 MiB` não alocado no início do disco, isso geralmente é feito para garantir que a primeira partição comece com um alinhamento de setor adequado, o que pode ajudar na performance do disco e na compatibilidade com sistemas `UEFI`. |
| 2      | `512`       | `Primary`                 | `Beginning of this space`   | `Ext4 journaling file system` | `/boot`           | `ext4`     | `513MiB`           | `2561Mib`             | N/A              |
| 3      | Restante    | `Primary`                 | `Beginning of this space`   | `Ext4 journaling file system` | `/sda4_crypt`     | `Unknown`  | `2561MiB`          | Até o final do HD/SSD | N/A              |
| 4      | Igual à memória RAM física do computador para poder habilitar o `hibernate corretamente` ou, pelo menos, `1024` | `Primary`                   | `Beginning of this space`     | `swap area`/`linux-area`    | `/swap`  | `ext4`  | `2561MiB` | `35329MiB` (baseado em um computador com memória RAM de `32GB`, ajustar para outros tamanhos de memória se for o caso) | N/A              |
| 5      | `262144`    | `Primary`                   | `Beginning of this space`     | `Ext4 journaling file system`                      | `/`             | `ext4`  | `35329MiB` (baseado em um computador com memória RAM de `32GB`, ajustar para outros tamanhos de memória se for o caso) | `297470MiB` | **NÃO** criar como `/root` |
| 6      | Restante    | `Logical`                   | `Beginning of this space`     | `Ext4 journaling file system`                      | `/home`         | `ext4`  | `297470MiB` | Até o final do HD/SSD | N/A |


## 2. Formatar e criar as partições usando o `Terminal Emulator` do `Linux Ubuntu`

### 2.1 Formatar um disco pelo `Terminal Emulator` do `Linux Ubuntu`

Para formatar um disco no Linux, você normalmente precisará de dois passos principais: criar uma partição no disco e, em seguida, criar um sistema de arquivos nessa partição. Aqui está um guia geral sobre como fazer isso usando ferramentas comuns de linha de comando como fdisk ou `parted` para criar partições e mkfs para formatar essas partições.

1. **Identificar o Disco**: Primeiro, é importante confirmar qual disco você deseja usar. Você pode listar todos os dispositivos de armazenamento com o seguinte comando: `sudo lsblk`

2. **Criar Partição no Disco**: Você pode usar fdisk (para discos menores ou sistemas mais antigos) ou `parted` (recomendado para discos maiores ou para um uso mais avançado). Vou usar `parted` para um exemplo com um disco `/dev/sda`. Usando parted:

    2.1 **Cria uma tabela de partição GPT**: `sudo parted /dev/sda --script mklabel gpt`

    2.2 **Cria uma partição primária que ocupa todo o disco**: `sudo parted /dev/sda --script mkpart primary ext4 1MiB 100%` 
    ```
    
3. **Desmontar a Partição**: Quando você souber o ponto de montagem, por exemplo, `/dev/sda1` ou algum outro diretório, use o comando umount para desmontá-lo: `sudo umount /dev/sda1`

4. **Formatar a Partição**: Depois de criar a partição, você pode formatá-la com um sistema de arquivos. O tipo de sistema de arquivos que você escolhe dependerá de suas necessidades (por exemplo, `ext4`, `xfs`, `ntfs` etc.).

    4.1 **Formatar como `ext4`**: Suponha que a partição criada seja `/dev/sda1`: `sudo mkfs.ext4 /dev/sda1`

**Considerações de Segurança**

- **Backup**: Sempre certifique-se de ter backups dos dados antes de formatar um disco, pois esse processo apagará todos os dados existentes na unidade.

- **Verificação**: Confirme sempre o dispositivo que você está formatando usando o comando `lsblk` ou `fdisk -l` para evitar apagar dados de outra unidade por engano.

**Alternativas de Sistema de Arquivos**

- **Para sistemas Windows (NTFS)**: `sudo mkfs.ntfs /dev/sda1`

- **Para sistemas de alta performance (XFS)**: `sudo mkfs.xfs /dev/sda1`

- **Para armazenamento em rede ou servidores (Btrfs)**: `sudo mkfs.btrfs /dev/sda1`

Cada sistema de arquivos tem suas vantagens e é escolhido com base nas necessidades específicas de uso, como desempenho, confiabilidade, e características do sistema operacional que será usado com ele.

### 2.2 Criar as partições usando o `Terminal Emulator` do `Linux Ubuntu`

1. **Identificar o Disco**: Primeiro, é importante confirmar qual disco você deseja usar. Você pode listar todos os dispositivos de armazenamento com o seguinte comando: `sudo lsblk`

2. **Criar Tabela de Partições**: Vamos criar uma nova tabela de partições no disco (isso apagará todos os dados existentes no disco, então tenha certeza de que é o disco correto e que todos os dados importantes foram salvos): `sudo parted /dev/sda --script mklabel gpt`

3. **Criar Partições**: Agora, vamos criar as partições necessárias. Vou criar uma partição para o `EFI` (necessária para sistemas UEFI), uma para `/boot` (opcional, mas recomendada para gerenciar kernels e inicializações mais facilmente), e uma grande partição para o `LUKS` que abrigará o sistema operacional e os dados.

    3.1 **Criar partição `EFI` de `512 MB`**:
    
    ```
    sudo parted /dev/sda --script mkpart primary fat32 1MiB 513MiB
    sudo mkfs.vfat -F 32 /dev/sda2
    ```

    3.2 **Criar partição `/boot` de `1 GB` (opcional)**:

    ```
    sudo parted /dev/sda --script mkpart primary ext4 513MiB 1537MiB
    sudo mkfs.ext4 /dev/sda3
    ```
    
    3.3 **Criar partição `LUKS` (o restante do disco)**: `sudo parted /dev/sda --script mkpart primary 1537MiB 100%`
    ```

4. **Configurar `LUKS`**: Vamos configurar `LUKS` na terceira partição criada (suponho que seja `/dev/sda3`):

    ```
    sudo cryptsetup luksFormat /dev/sda4
    sudo cryptsetup open /dev/sda4 sda4_crypt
    ```

5. **Verificação da Configuração LUKS**: Se as partições foram criadas e criptografadas através do instalador, o `/dev/sda4` pode não ser o dispositivo que você precisa desbloquear. Se você tiver mais de um disco rígido ou várias partições, o dispositivo criptografado com LUKS pode ter outro nome. Usando o ambiente Live CD/USB, você pode verificar quais dispositivos são reconhecidos como LUKS com o comando: `sudo blkid | grep LUKS`

6. **Use o `cryptsetup` para desbloquear a partição LUKS**: `sudo cryptsetup luksOpen /dev/sda4 sda4_crypt`

  Aqui, `sda4_crypt` é o nome que você está dando ao mapeamento desbloqueado. Você pode ser solicitado a digitar a senha de criptografia que você configurou durante a instalação.

7. **Fechar qualquer mapeamento ativo**: Você mencionou que tentou abrir `/dev/sda3 como sda4_crypt`, mas isso falhou porque já estava em uso. Vamos verificar todos os mapeamentos LUKS e fechá-los: `sudo dmsetup ls --target crypt`

    7.1 Se você encontrar o mapeamento `luks-<UUID>` ou `sda4_crypt`, tente fechá-lo:

    ```
    sudo cryptsetup close <UUID>
    sudo cryptsetup close sda4_crypt
    ```

8. **Desativar LVM e Swap**: Se `/dev/sda4` está tentando ser usado para um volume físico `LVM` (pvcreate falhou por estar em uso), você precisa garantir que não está ativo como um volume físico ou área de `swap`:

    ```
    sudo vgchange -an
    sudo swapoff -a
    ```

9. **Tentar abrir o mapeamento novamente**: Depois de garantir que não há mapeamentos ativos ou uso do `LVM`/`swap`, tente abrir o mapeamento novamente: `sudo cryptsetup open /dev/sda3 sda4_crypt`

10. **Verificar se o dispositivo está realmente livre**: Verifique se há algum outro processo utilizando o dispositivo: `sudo lsof | grep /dev/sda3`

11. **Configurar `LVM` sobre `LUKS`** Com o volume `LUKS` aberto, configuraremos o `LVM`:

    ```
    sudo pvcreate /dev/mapper/sda4_crypt
    sudo vgcreate vgxubuntu /dev/mapper/sda4_crypt
    sudo lvcreate -L 32G vgxubuntu -n swap
    sudo lvcreate -L 256G vgxubuntu -n ubuntu_root # partição home para o `Linux Ubuntu`
    sudo lvcreate -L 128G vgxubuntu -n kali_root # partição home para o `Kali Linux`
    sudo lvcreate -l 100%FREE -n home vgxubuntu # Ajuste para os 100% livre do disco ou conforme o espaço disponível e necessidade
    sudo mkswap /dev/vgxubuntu/swap
    sudo mkfs.ext4 /dev/vgxubuntu/ubuntu_root
    sudo mkfs.ext4 /dev/vgxubuntu/kali_root
    sudo mkfs.ext4 /dev/vgxubuntu/home
    ```

**Explicação do Comando**

- **`l 100%FREE`**: Esta opção especifica que o volume lógico deve usar todas as unidades de extensão livres restantes no grupo de volumes. É útil para maximizar o uso do espaço em disco disponível sem ter que calcular o tamanho exato disponível manualmente.

- **`n home`**: Define o nome do volume lógico como home.

- **`vgxubuntu`**: É o nome do grupo de volumes onde o volume lógico será criado.

### 2.2.1 Criar as partições usando o `Terminal Emulator` depois que o `Linux Ubuntu` foi instalado

você definitivamente pode particionar o `/home` e `/` em sistemas `Linux`. No seu caso, parece que você já tem uma configuração que usa `LVM` (Logical Volume Manager) sobre `LUKS` (Linux Unified Key Setup), o que é uma excelente maneira de gerenciar múltiplas partições lógicas sobre uma única partição criptografada. Usar `LVM` sobre `LUKS` permite uma flexibilidade considerável no gerenciamento de partições sem comprometer a segurança.

Dentro de `sda4_crypt`, você já tem um grupo de volumes (`vgxubuntu`) com dois volumes lógicos definidos (`vgxubuntu-root` e `vgxubuntu-swap_1`). Você pode continuar a subdividir este espaço para criar partições separadas para `/` (raiz) e `/home`, ou qualquer outra configuração desejada.

Aqui está como você pode ajustar sua configuração para incluir uma partição separada para `/home`, supondo que você deseje criar essa nova partição dentro do volume criptografado existente:

1. **Assegure-se de que o `LVM` está instalado**: `sudo apt install lvm2`

2. **Abra o volume criptografado (se ainda não estiver aberto)**: `sudo cryptsetup luksOpen /dev/sda4 sda4_crypt`

3. **Verifique os volumes lógicos e o espaço disponível**:

    ```
    sudo vgdisplay vgxubuntu
    sudo lvdisplay
    ```

4. **Redimensione o volume lógico `vgxubuntu-root`, se necessário**:

    4.1 **Primeiro, reduza o sistema de arquivos (assumindo que é `ext4`)**:

    ```
    sudo e2fsck -f /dev/vgxubuntu/root
    sudo resize2fs /dev/vgxubuntu/root <NewSizeG>G
    ```

    4.2 **Depois, reduza o volume lógico**: `sudo lvreduce -L <NewSizeG>G /dev/vgxubuntu/root`

5. **Crie um novo volume lógico para `/home`**:

    ```
    sudo lvcreate -n home -L <HomeSizeG>G vgxubuntu
    sudo mkfs.ext4 /dev/vgxubuntu/home
    ```

6. **Monte os volumes lógicos no sistema de arquivos**: Adicione entradas ao `/etc/fstab` para montar automaticamente no boot:

    ```
    /dev/vgxubuntu/root    /    ext4    defaults    0    1
    /dev/vgxubuntu/home    /home    ext4    defaults    0    2
    ```

7. **Monte manualmente se necessário**: `sudo mount /dev/vgxubuntu/home /home`

Ao seguir esses passos, você pode efetivamente gerenciar o layout do seu disco, aproveitando os benefícios da flexibilidade do `LVM` e a segurança do `LUKS`. Essa abordagem é ideal para configurações de multi-boot ou para usuários que desejam manter os dados do usuário separados do sistema operacional, facilitando backups e upgrades.

### 2.2.2 Criar as partições usando o `Terminal Emulator` para mais de um sistema operacional

Se você tiver mais de um sistema operacional instalado e quiser manter os ambientes completamente isolados um do outro, é recomendável criar partições separadas para `/` (raiz) e `/home` para cada sistema operacional. Isso garante que as configurações, programas e dados de um sistema não interfiram ou afetem o outro, o que é especialmente importante se os sistemas operacionais forem diferentes ou para fins distintos, como desenvolvimento e produção, ou uso pessoal e profissional.

Usando `LVM` sobre `LUKS`, como no seu caso, você pode facilmente criar múltiplos volumes lógicos dentro do mesmo grupo de volumes, o que permite uma gestão eficiente do espaço e uma separação clara dos sistemas. Aqui está como você pode proceder para configurar cada sistema operacional com suas próprias partições `/` e `/home`:

1. **Assegure-se de que há espaço suficiente**: Certifique-se de que o volume criptografado tem espaço livre suficiente para acomodar as partições de todos os sistemas operacionais que você deseja instalar. Você pode verificar o espaço livre com: `sudo vgdisplay vgxubuntu`

2. **Crie volumes lógicos para cada sistema operacional**: Para cada sistema operacional, você vai querer criar um volume lógico para a raiz (`/`) e um para o home (`/home`). Por exemplo, para dois sistemas operacionais, `Ubuntu` e `Kali Linux`, você poderia fazer:

    ```
    sudo lvcreate -n ubuntu-root -L <SizeForUbuntuRoot>G vgxubuntu
    sudo mkfs.ext4 /dev/vgxubuntu/ubuntu-root

    sudo lvcreate -n ubuntu-home -L <SizeForUbuntuHome>G vgxubuntu
    sudo mkfs.ext4 /dev/vgxubuntu/ubuntu-home

    sudo lvcreate -n kali-root -L <SizeForKaliRoot>G vgxubuntu
    sudo mkfs.ext4 /dev/vgxubuntu/kali-root

    sudo lvcreate -n kali-home -L <SizeForKaliHome>G vgxubuntu
    sudo mkfs.ext4 /dev/vgxubuntu/kali-home
    ```

3. **Configure o bootloader**:

    3.1 Você precisará configurar o bootloader (como `GRUB`) para gerenciar a inicialização múltipla. Durante a instalação de cada sistema operacional, certifique-se de instalar o `GRUB` no local apropriado e configurar cada entrada para apontar para os volumes lógicos corretos.

    3.2 Isso geralmente é feito automaticamente pelo instalador do sistema operacional, mas pode requerer ajustes manuais em casos mais complexos.

4. **Atualize as configurações de montagem (`/etc/fstab`)**: Para cada sistema, atualize o arquivo `/etc/fstab` para garantir que os volumes lógicos corretos sejam montados nas posições corretas de `/` e `/home`.

5. **Instalação dos sistemas operacionais**: Proceda com a instalação dos sistemas operacionais nos volumes lógicos específicos que você criou para cada um.

Esta abordagem lhe dará uma grande flexibilidade e isolamento entre os sistemas operacionais, aproveitando as vantagens do LVM para fácil gerenciamento e expansão das partições conforme necessário.

## 3. Utilizar o `LUKS` para criptografar seu HD ou SSD e depois usar o `Rescuezilla` para copiar ou clonar o disco criptografado

É possível utilizar o `LUKS` para criptografar seu HD ou SSD e depois usar o `Rescuezilla` para copiar ou clonar o disco criptografado. `Rescuezilla` é uma ferramenta de recuperação e clone de disco que suporta diversas funcionalidades, incluindo trabalhar com discos criptografados por `LUKS`. Aqui estão os passos para fazer isso:

### 3.1 Criptografar o disco com `LUKS`

1. **Instale o Ubuntu com criptografia `LUKS`:** Siga os passos que mencionei anteriormente para instalar o Ubuntu com criptografia `LUKS`. Certifique-se de que todo o disco ou as partições específicas estejam criptografadas conforme sua necessidade.

### 3.2 Usando o `Rescuezilla` para copiar o HD/SSD criptografado

1. **Prepare o `Rescuezilla`:**

    1.1 Baixe a imagem `ISO` mais recente do `Rescuezilla` do site oficial.

    1.2 Crie um USB bootável com a imagem do `Rescuezilla` usando um programa como `Rufus` ou `Etcher`.

2. **Boot pelo USB do `Rescuezilla`:** Reinicie o computador e use o menu de `boot` para iniciar a partir do USB que contém o `Rescuezilla`.

3. **Acessar a ferramenta de clone/cópia:**

    3.1 No `Rescuezilla`, você terá opções para `"Backup"` ou `"Restore"`. Selecione "Backup" se deseja criar uma cópia do seu disco criptografado.

    3.2 Você precisa desbloquear o disco criptografado com `LUKS` fornecendo a senha de criptografia durante o processo no `Rescuezilla`.

4. **Realizar o backup:**

    4.1 Escolha o disco ou a partição criptografada que deseja copiar.

    4.2 Selecione o destino para onde a imagem do disco será salva. Pode ser outro disco rígido, um dispositivo USB ou um local de rede.

5. **Validar e finalizar o processo:**

    5.1 Siga as instruções na tela para completar o backup.

    5.2 O `Rescuezilla` criará uma imagem do seu disco criptografado, que pode ser restaurada posteriormente ou usada para clonagem em outro disco.

#### Considerações importantes

- **Criptografia:** Quando você copia um disco criptografado com o `Rescuezilla`, a imagem resultante também estará criptografada. Para acessar os dados, será necessário desbloquear a imagem com a mesma senha LUKS utilizada no disco original.

- **Restauração:** Para restaurar o disco a partir de uma imagem criptografada, você precisará novamente desbloquear a criptografia usando o `Rescuezilla` ou outro software compatível com LUKS.

`Rescuezilla` é uma ferramenta bastante flexível e poderosa para operações de backup e restauração, inclusive com suporte para discos criptografados, tornando-o uma excelente escolha para a manutenção de segurança dos seus dados.


## 4. `GRUB` (GRand Unified Bootloader) pode funcionar com sistemas em que um dos HDs ou SSDs está criptografado usando `LUKS`

o `GRUB` (GRand Unified Bootloader) pode funcionar com sistemas em que um dos HDs ou SSDs está criptografado usando `LUKS`. No entanto, existem algumas considerações importantes para garantir que a configuração funcione corretamente, especialmente quando você tem um sistema `dual-boot` com `Linux` e `Windows`:

### 4.1 Configuração do `GRUB` para Dual Boot com Disco Criptografado

1. **Instalação do `GRUB`:**

    1.1 O `GRUB` deve ser instalado no disco que é inicializado primeiro no BIOS (geralmente o HD/SSD com o `Linux`).

    1.2 Durante a instalação do Linux, o instalador geralmente detecta outros sistemas operacionais, como o Windows, e adiciona uma entrada para eles no menu do `GRUB` automaticamente.

2. **Configuração com LUKS:**

    2.1 Se você criptografar o disco Linux com LUKS durante a instalação, o instalador do Ubuntu, por exemplo, configurará o `GRUB` para solicitar a senha de desbloqueio do LUKS na inicialização. Isso permite que o `GRUB` acesse os arquivos necessários para a inicialização do Linux.

    2.2 A partição `/boot` precisa ser acessível para que o `GRUB` possa carregar o kernel e a imagem initramfs. Se você optar por criptografar a raiz (`/`) mas deixar `/boot` descriptografada, o `GRUB` poderá iniciar sem problemas. Se /boot também estiver criptografada, o `GRUB` precisa da capacidade de desbloquear essa partição, o que pode exigir configurações adicionais.

3. **Iniciar o Windows:**

    3.1 O Windows não estará ciente da criptografia LUKS e não será afetado por ela diretamente, pois assume-se que o Windows está em outro disco não criptografado.

    3.2 O `GRUB` deverá ser capaz de iniciar o Windows normalmente, selecionando a entrada correspondente no menu do `GRUB`.


### 4.2 Instalar/Reparar Bootloader Ubuntu `GRUB`

**ATENÇÂO**: Tenha em mente que o `GRUB` deve ser instalado no primário da sequência de `boot`, logo, se ele for instalado em outro disco, este, por sua vez, deve ser alterado para ser o primeiro disco na inicialização do computador.

Geralmente acontece quando o `GRUB`, o bootloader do `Linux`, não consegue encontrar a partição de boot correta. Isso pode ser causado por vários motivos, como a configuração errada do `GRUB` ou problemas relacionados à criptografia LUKS que impede o `GRUB` de acessar os arquivos necessários.

1. **Boot pelo Live CD/USB do `Ubuntu`**: Use o mesmo meio que você usou para instalar o `Ubuntu`. Selecione a opção `"Try Ubuntu without installing"` para iniciar o sistema operacional a partir do USB/CD sem instalar nada no seu disco.

2. **Identificar a partição criptografada**: Depois de iniciar o sistema a partir do Live CD/USB, abra um terminal e utilize o comando `lsblk` para identificar a partição criptografada. Provavelmente será a mesma partição onde o `Ubuntu` foi instalado: `sudo lsblk`

    Procure por uma partição com o tipo `crypt`. Se você seguiu os passos anteriores, a partição se chama `sda4_crypt`.

3. **Abrir a partição criptografada**: Você precisará abrir a partição criptografada para acessar os arquivos de sistema. Substitua `/dev/sda3` pelo dispositivo correto que você identificou anteriormente: `sudo cryptsetup luksOpen /dev/sda3 sda4_crypt`

    Você precisará digitar a senha que configurou para a criptografia `LUKS`.

4. **Consultar o nome das partições**: Digite o comando: `ls -al /dev/mapper`

5. **Montar o sistema de arquivos raiz**: Após abrir a partição criptografada, monte-a em um diretório para poder acessar seus arquivos: `sudo mount /dev/mapper/sda4_crypt /mnt`

6. **Montar as partições adicionais**: Se você tiver partições separadas para `/boot/efi`, `/boot` e `/home`, precisará montá-las também. Isso é importante para que o `GRUB` possa acessar todos os arquivos necessários:

    ```
    sudo mkdir /mnt/boot
    sudo mkdir /mnt/boot/efi
    sudo mkdir /mnt/home
    sudo mount /dev/sda2 /mnt/boot
    sudo mount /dev/sda1 /mnt/boot/efi
    sudo mount /dev/mapper/vgxubuntu-home /mnt/home
    ```

7. **Reinstalar o `GRUB` no `root`**: Com o sistema de arquivos montado, reinstale o tente instalar o `GRUB` no `root`, se não vá para o próximo Item. Isso deverá configurá-lo para reconhecer corretamente a partição criptografada durante o `boot`:

    ```
    sudo grub-install --root-directory=/mnt /dev/sda  # Substitua /dev/sda pelo disco (não partição) onde o `GRUB` deve ser instalado
    sudo update-grub
    ```

    7.2 **Reinstalar o `GRUB` no `UEFI` (ALTERNATIVA)**: Com o sistema de arquivos montado, reinstale o tente instalar o `GRUB` no `efi`, se não vá para o próximo Item. Isso deverá configurá-lo para reconhecer corretamente a partição criptografada durante o `boot`:

    ```
    sudo grub-install --target=x86_64-efi --efi-directory=/mnt/boot/efi --bootloader-id=Ubuntu --root-directory=/mnt
    sudo chroot /mnt update-grub
    ```

8. **Desmontar as partições**: Desmonte as partições com o comando:

    ```
    sudo umount /mnt/boot/efi
    sudo umount /mnt/boot
    sudo umount /mnt/home
    ```

9. **Reiniciar**: Após completar esses passos, desmonte as partições e reinicie o computador:

    ```
    sudo umount /mnt/boot /mnt
    sudo reboot
    ```

Tente esses passos e veja se resolve o problema. Se continuar enfrentando problemas, pode ser necessário verificar as configurações do `GRUB` ou a integridade da instalação do `Ubuntu` e da criptografia `LUKS`.


##### Considerações Adicionais

- **Atualizações do GRUB**: Sempre que o kernel do Linux for atualizado, o GRUB pode precisar ser atualizado. Isso geralmente é feito automaticamente pelas atualizações do gerenciador de pacotes, mas é importante verificar se o GRUB ainda pode acessar e iniciar ambos os sistemas operacionais após as atualizações.

- **Criptografia de `/boot`**: A criptografia de `/boot` é mais complexa e pode exigir passos adicionais para configurar o GRUB adequadamente. Para a maioria dos usuários, manter `/boot` descriptografado enquanto criptografa / oferece um bom equilíbrio entre segurança e conveniência.

- **Falhas de Boot**: Se você encontrar problemas ao iniciar, uma USB Live do Linux pode ser usada para reparar o GRUB ou ajustar as configurações de criptografia LUKS.

##### Pontos Importantes

- **Cuidado com UUIDs**: A clonagem de discos pode resultar em discos com UUIDs idênticos, o que pode causar confusão para o sistema operacional. Verifique e atualize os UUIDs nas configurações de montagem (`/etc/fstab`) e outras configurações necessárias.

- **Configurações de criptografia**: Se o disco original não estava criptografado e o novo está, você terá que ajustar as configurações para refletir isso, atualizando o `crypttab` e o `fstab` conforme necessário.

Este processo pode ser bastante técnico, e cada passo deve ser executado com cuidado para evitar perda de dados.

### 4.3 Diagnosticar e corrigir o problema do `BusyBox` acoplada ao `initramfs`

Ao reiniciar o computador, caso retorne a mensagem abaixo:

```
BusyBox v1.30.1 (Ubuntu 1:1.30.1-7ubuntu3) built-in shwell (ash)
Enter 'help' for a list of built-in commands

(initramfs)
```

A tela que você está vendo é a shell do BusyBox acoplada ao initramfs, o que geralmente indica que o sistema não consegue encontrar o sistema de arquivos raiz ou há algum problema com ele. Isso pode acontecer por várias razões, como uma referência incorreta no UUID no `GRUB`, problemas no sistema de arquivos, ou falha na montagem da partição criptografada.

Aqui estão algumas etapas para diagnosticar e corrigir o problema:

1. **Verificar o UUID**: Verifique se o UUID da partição raiz listado no `GRUB` corresponde ao UUID real da partição. Você pode fazer isso reiniciando no Live CD/USB, abrindo um terminal, e utilizando o comando sudo blkid. Então compare este UUID com o que está configurado no `GRUB` (você pode verificar isso no arquivo `/boot/grub/grub.cfg`).

1. **Desbloqueie a partição LUKS (certificando-se de que a senha está correta)**: Para montar o sistema de arquivos corretamente, você precisa primeiro identificar e ativar o volume lógico. Aqui estão os passos para fazer isso: `sudo cryptsetup luksOpen /dev/sda3 sda4_crypt`

2. **Monte a partição `sda4_crypt`**:

    ```
    sudo mkdir /mnt
    sudo mount /dev/mapper/sda4_crypt /mnt
    ```

3. **Ative o volume lógico LVM**: `sudo vgchange -ay`

    Isso ativará todos os volumes lógicos disponíveis. O comando acima deve dar um nome ao volume lógico que foi ativado. Se seguiu os passos das Seções anteriores, o nome se chamará: `vgxubuntu`

4. **Identifique o volume lógico**: `sudo lvs`

    Este comando irá listar todos os volumes lógicos disponíveis. Anote o nome do volume que corresponde ao sistema de arquivos raiz. Se seguiu os passos das Seções anteriores, o nome se chamará: `ubuntu_root`

5. **Monte o volume lógico que contém o sistema de arquivos raiz**: `sudo mount /dev/mapper/sda4_crypt /mnt`

    Substitua `VG_NAME` e `LV_NAME` pelos nomes corretos do seu volume lógico.

6. Se houver um volume separado para `/boot` (ou `/boot/efi` para `UEFI`), você precisará montá-lo também. Se seguiu os passos das Seções anteriores:

    ```
    sudo mkdir /mnt/boot
    sudo mount /dev/sda2 /mnt/boot  # Está invertido por conta do número da partição
    ```

    Substitua `VG_NAME` e `LV_BOOT` pelo nome do volume lógico de `boot`.

    7.1 Se você tiver um volume separado para `/boot/efi` (apenas sistemas `UEFI`), não esqueça de montá-lo também:

    ```
    sudo mkdir /mnt/boot/efi
    sudo mount /dev/sda1 /mnt/boot/efi  # Está invertido por conta do número da partição
    ```

    Substitua `sdXY` pelo dispositivo `EFI` correto.

    Não há problema algum em ter as partições `/boot` e `/boot/efi` fora do LVM. Na verdade, é comum que essas partições estejam fora do LVM, especialmente a partição `EFI`, pois o firmware `UEFI` precisa acessá-las diretamente.

    A partição `/boot/efi` deve ser uma partição de sistema `EFI` formatada com `FAT32`, e a partição `/boot` normalmente contém os kernels e imagens `initramfs` para o processo de inicialização. Ambas devem ser montadas diretamente pelas suas designações de dispositivo (por exemplo, `/dev/sda1` para `EFI` e `/dev/sda2` para `/boot`) em vez de nomes de volume lógico LVM.

    7.2 **Verifique se a partição `EFI` está formatada como `FAT32`**: Verifique o sistema de arquivos da partição `EFI` com o seguinte comando: `sudo blkid /dev/sda1`

    Isso deve retornar `TYPE="vfat"`, que é o identificador para partições `FAT32`. Se isso não acontecer, pode haver um problema com a partição `EFI`.

    7.3 **Depois de montar, verifique se os arquivos da `EFI` estão presentes**: `ls -al /mnt/boot/efi`

    Você deveria ver diretórios como `EFI`, `Microsoft`, `Boot` etc.

8. **Monte os sistemas de arquivos virtuais**: Certifique-se de que você está montando estes diretórios antes de entrar no `chroot`. Saia do `chroot` se você já estiver dentro e execute:

    ```
    sudo mount --bind /dev /mnt/dev
    sudo mount --bind /dev/pts /mnt/dev/pts
    sudo mount --bind /proc /mnt/proc
    sudo mount --bind /sys /mnt/sys
    sudo mount --bind /run /mnt/run
    ```

    Essas montagens garantem que o ambiente do sistema operacional dentro do chroot funcione corretamente, permitindo que comandos e programas acessem informações do hardware e do sistema operacional `host`.

9. **Entre no ambiente `chroot` novamente**: `sudo chroot /mnt`

10. **Dentro do `chroot`, tente atualizar o `initramfs`**: `update-initramfs -u` ou `update-initramfs -c -k -all`

    Este comando deverá agora executar sem emitir os erros anteriores.

    8.1 **Instalar o `firmware` faltante**: Se você confirmar que o sistema inicia e pode acessar a internet ou se você pode montar um dispositivo com os arquivos necessários, você pode tentar instalar o `firmware` faltante.

    Os pacotes de `firmware` para dispositivos AMD geralmente podem ser encontrados nos repositórios oficiais da sua distribuição `Linux`. No `Debian` e em sistemas baseados no `Ubuntu`, você pode instalar os pacotes de `firmware` com o comando:

    ```
    sudo apt update
    sudo apt install firmware-amd-graphics
    ```

    Você pode precisar configurar repositórios adicionais ou habilitar componentes non-free para acessar esses pacotes.

11. **Dentro do `chroot`, continue com a atualização do `GRUB`**: `update-grub`

12. **Saia do `chroot` e desmonte as partições**: Quando terminar todas as configurações e atualizações dentro do `chroot`, você deve sair e desmontar todos os diretórios montados para evitar problemas ao reiniciar:

    ```
    exit
    sudo umount /mnt/boot/efi
    sudo umount /mnt/boot
    sudo umount /mnt/dev/pts
    sudo umount /mnt/dev
    sudo umount /mnt/proc
    sudo umount /mnt/sys
    sudo umount /mnt/run
    sudo umount -l /mnt  # o `-l` força a desmontagem
    sudo systemctl reboot
    ```

## 5. Código completo para configurar/instalar/usar

Para configurar/instalar/usar o `luks` no `Linux Ubuntu`sem precisar digitar linha por linha, você pode seguir estas etapas:

1. Abra o `Terminal Emulator`. Você pode fazer isso pressionando: `Ctrl + Alt + T`

2. Digite o seguinte comando e pressione `Enter`:

    ```
    NÂO há.
    ```


## Referências

[1] OPENAI. ***Criptografia de disco luks.*** Disponível em: <https://chat.openai.com/c/8910b4a0-0cf5-449b-9fb4-14646df6d613> (texto adaptado). Acessado em: 16/04/2024 13:47.

[2] OPENAI. ***Vs code: editor popular.*** Disponível em: <https://chat.openai.com/c/b640a25d-f8e3-4922-8a3b-ed74a2657e42> (texto adaptado). Acessado em: 16/04/2024 13:48.

