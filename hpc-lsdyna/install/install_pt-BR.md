# Instalando LS-DYNA

## Introdução

Neste laboratório, você instalará o LS-DYNA.

Tempo Estimado de Laboratório: 25 minutos

## Tarefa 1: Fazer Download dos Binários

Você pode fazer download dos binários LS-DYNA no [Site da LSTC](http://www.lstc.com/download/ls-dyna) ou enviá-los para sua máquina usando scp.

Use a versão que foi criada para o mpi e compilada para o Ent Srv 5.4 RedHat. De acordo com nossos resultados, o desempenho do IntelMPI é mais rápido que o MPI da Plataforma na OCI. (ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1).tar.gz)

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

Outra possibilidade é fazer upload do instalador no armazenamento de objetos.

1.  No menu principal da console, selecione Object Storage.
2.  Escolha a região correta na parte superior direita
3.  Selecione o compartimento correto no lado esquerdo
4.  Crie um bucket se você ainda não tiver um criado
5.  No bucket, selecione o objeto de upload e especifique o caminho do instalador.
6.  Selecione os 3 pontos no lado direito do objeto do instalador e selecione Criar Solicitação Pré-Autenticada
7.  Se você perder o URL, não poderá recuperá-lo, mas poderá gerar novamente uma nova Solicitação Pré-autenticada

Faça download do armazenamento de objetos do formulário do instalador com

    <copy>
        wget PAR_URL
    </copy>
    

Descompacte ou descompacte o instalador dependendo da sua versão

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## Tarefa 2: Instalar LS-DYNA

1.  Desfaça os binários em um local compartilhado. Por padrão, um cluster HPC tem um compartilhamento NFS ou um compartilhamento Gluster montado em todos os nós de computação.
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## Tarefa 3: Instalar bibliotecas MPI

**MPI Intel 2018**

Execute esses comandos em cada nó.

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**MPI da plataforma**

Instale essas bibliotecas:

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

Faça download do arquivo tar no site da IBM e execute:

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

Em seguida, siga as instruções na tela. Se você instalar plataforma em uma unidade de compartilhamento, ela estará acessível a todos os nós de computação.

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020