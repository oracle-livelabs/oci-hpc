# Execute o projeto OpenFoam na VM padrão no OCI

## Introdução

Este laboratório foi projetado para ajudar na avaliação do Software CFD OpenFOAM no Oracle Cloud Infrastructure. Ele faz download e configura automaticamente o OpenFOAM. OpenFOAM é o software CFD de código aberto gratuito lançado e desenvolvido principalmente por OpenCFD Ltd desde 2004. Tem uma grande base de usuários na maioria das áreas de engenharia e ciência, tanto de organizações comerciais quanto acadêmicas. OpenFOAM tem uma ampla gama de recursos para resolver qualquer coisa, desde fluxos de fluidos complexos envolvendo reações químicas, turbulência e transferência de calor, para acústica, mecânica sólida e eletromagnetica.

Este laboratório orienta você na instalação OpenFoam em uma forma básica VM.Standard 2.1, mas, para uma implantação real, uma forma maior como BM.HPC2.36 seria apropriada. Algumas configurações de alto desempenho só estão disponíveis em Regiões e Domínios de Disponibilidade específicos.

Criaremos dois nós para este laboratório: o headnode do cluster na sub-rede pública e um nó de computação de trabalho na sub-rede privada. Para acessar os nós de trabalho, primeiro criaremos o headnode, depois geraremos uma chave ssh no headnode e usaremos essa chave pública ao criar o nó de trabalho.

Para **problemas de log**, clique [aqui](https://github.com/oracle/learning-library/issues/new) para ir para o formulário de envio de problemas do oracle repository do github.

Tempo Estimado de Laboratório: 60 minutos

### Objetivos

Como desenvolvedor, engenheiro de dados,

1.  Implante suas instâncias de Computação de Alto Desempenho manualmente
2.  Criar uma Rede de Clusters
3.  Criar um Sistema de Arquivos da Rede
4.  Configurar VNC
5.  Instalar OpenFoam e Paraview

### Pré-requisitos

1.  Uma conta do Oracle Cloud Infrastructure com privilégios para criar uma configuração VM padrão 2.1 ou BM.HPC2.36 da instância.

### Terminologia de infraestrutura

*   Nó de trabalho: instâncias de computação HPC que fornecem o poder de processamento para executar a carga de trabalho de simulações computacionais ou outras cargas de trabalho de engenharia. Nesta forma de computação de demonstração, os nós BM.HPC2.36 são os nós de trabalho.
*   Nó principal: instância de computação de onde todos os jobs computacionais podem ser programados e submetidos para serem executados nos nós de Trabalho. Muitas vezes, o nó Head e o nó Bastion podem ser a mesma máquina. Para esta demonstração, estamos provisionando o nó Bastion.
*   Nó bastion: Uma instância de computação com IP Público, atua como ponto de entrada para estabelecer conexão com nós de trabalho que geralmente estão na rede Privada.
*   Rede RoCE: RDMA sobre Ethernet Convertida (RoCE) é um protocolo de rede que permite acesso remoto direto à memória (RDMA) por uma rede Ethernet.

## Tarefa 1: Provisionar a Oracle Virtual Cloud Network

1.  Antes de criar uma instância, precisamos configurar uma Rede Virtual na Nuvem. Selecione o menu na parte superior esquerda e, em seguida, selecione Rede e Redes Virtuais na Nuvem ![mercado](images/vcn.png) ![mercado](images/create_vcn.png) ![mercado](images/vcn_content.png)
    
2.  Na próxima página, selecione o seguinte para sua VCN:
    
    *   Nome
    *   Compartimento
    *   Bloco CIDR (Exemplo: 10.0.0.0/16)
3.  Role para a parte inferior e clique em Criar VCN
    
4.  Criar Sub-redes
    
    1.  Sub-rede Pública
        *   Nome: ou seja, hpc\_public
        *   Tipo de Sub-rede: Regional
        *   Bloco CIDR: 10.0.0.0/24
        *   Tabela de roteamento: "Tabela de roteamento padrão"
        *   Acesso à Sub-rede: Sub-rede Pública
        *   Lista de Segurança: "Lista de Segurança Padrão"
    2.  Sub-rede privada
        *   Nome: hpc\_private
        *   Tipo de Sub-rede: Regional
        *   Bloco CIDR: 10.0.3.0/24
        *   Tabela de Roteamento: selecione a Tabela de Roteamento criada na etapa anterior
        *   Acesso de Sub-rede: Sub-rede Privada
        *   Lista de Segurança: selecione a lista de segurança criada na etapa anterior
5.  Clique em **criar sub-rede** ![mercado](images/create_subnet.png) ![mercado](images/create_subnet_content.png)
    
6.  Criação do Internet Gateway
    
    1.  Clique em `hpc_vcn` que você criou e no menu **Recursos** no lado esquerdo da página, selecione **Gateway de Internet**, crie o Gateway de Internet. ![mercado](images/create_IG.png) ![mercado](images/create_IG_content.png)

Observação: Isso criará o gateway de internet e precisará ser associado a uma tabela de roteamento. Nesse caso, como a Tabela de Roteamento Padrão será usada para a sub-rede pública, o gateway de internet deverá ser associado a essa tabela de roteamento.

7.  Adicionar Regras de Roteamento à Tabela de Roteamento. No menu **Recursos** no lado esquerdo da página, selecione **Tabela de Roteamento Padrão para `hpc_vcn`**, clique em **Adicionar Regras de Roteamento**
    *   Tipo de Destino: Gateway de Internet
    *   Bloco CIDR de Destino: 0.0.0.0/0
    *   Gateway de Internet de Destino no compartimento: Gateway de Internet que você criou ![mercado](images/route_table.png) ![mercado](images/route_rules.png)

## Tarefa 2: Criar Nós do Cluster

Criaremos dois nós para este laboratório: o **Headnode** do cluster na sub-rede pública e **um nó de computação de trabalho** na sub-rede privada. Para acessar os nós de trabalho, primeiro criaremos o headnode, depois geraremos uma chave ssh no headnode e usaremos essa chave pública ao criar o nó de trabalho.

Observação: Para este laboratório, utilizaremos apenas a forma básica VM.Standard2.1, mas para uma implantação real, uma forma maior como BM.HPC2.36 seria apropriada. Algumas configurações de alto desempenho só estão disponíveis em Regiões e Domínios de Disponibilidade específicos.

1.  #### Criar Headnode
    
    *   Nome: ou seja, hpc\_head
    *   Imagem ou sistema operacional:versão mais recente do Oracle Linux(padrão)
    *   Domínio de Disponibilidade: domínio disponível para formas VM.Standard 2.1
    *   Forma da Instância:
        *   VM.Standard 2.1
        *   qualquer outra forma (BM.HPC2.36 preferencial, se disponível)
    *   Rede Virtual na Nuvem: VCN criada antes
    *   Sub-rede: sub-rede pública criada antes
    *   Chave SSH: Anexe seu arquivo de chave pública
    
    1.  No menu superior esquerdo, selecione Computação e crie a instância. ![mercado](images/compute.png) ![mercado](images/compute_bm.png) ![mercado](images/compute_vm.png)
        
    2.  Use SSH no headnode e gere a chave ssh específica para que o cluster se comunique.
        
            ```
            $ ssh -i <ssh_key> opc@<headnode_ip_address>
            
            $ ssh-keygen
            ```
            Note: Do not change the ssh key file location (/home/opc/.ssh/id_rsa) and hit enter when asked about a passphrase (twice).
            
            1. Run and Copy the whole string, which will be used in creating the worker node
            
            ```
            $ cat ~/.ssh/id_rsa.pub
            ```
            
2.  **Criar Nó de Trabalho**
    
    1.  No menu superior esquerdo, selecione Compute e crie uma instância com os seguintes detalhes:
        
        *   Nome: ou seja, hpc\_worker1
        *   Imagem ou sistema operacional:versão mais recente do Oracle Linux(padrão)
        *   Domínio de Disponibilidade: domínio disponível para formas VM.Standard 2.1
        *   Forma da Instância:
            *   VM.Standard 2.1
            *   qualquer outra forma (BM.HPC2.36 preferencial, se disponível)
        *   Rede Virtual na Nuvem: VCN criada antes
        *   Sub-rede: sub-rede privada criada antes
        *   Chave SSH: string de chave pública copiada de uma etapa acima.
    2.  SSH no nó de trabalho.  
        Retorne à console conectada ao nó principal e obtenha o endereço IP privado e o ssh no nó de trabalho do nó principal
        
            ```
            $ ssh opc@10.x.x.x
            ```
            

## Tarefa 3: Configurar Gateway NAT

\*\* Observe que isso é apenas para o nó de trabalho \*\*  

1.  Selecione o nó de trabalho e clique em **VNICs Anexadas** no menu **recursos** à esquerda
2.  Selecionar **Editar VNIC**
3.  Desmarque **Ignorar Verificação de Origem/Destino** se estiver marcado e clique em **Atualizar VNICs** ![mercado](images/nat_gateway.png)

## Tarefa 4: Montando uma unidade

Observação: somente se a forma do nó tiver um NVMe anexado (BM.HPC2.36 tiver um, não VM.Standard2.1), as máquinas HPC terão o armazenamento NVMe local, mas ele não será montado por padrão. Vá para a Etapa 5 se estiver usando VM.Standard2.1

1.  Use SSH no seu headnode e execute os comandos abaixo

    ```
     $ lsblk
    ```
    The drive should be listed with the NAME on the left (Probably nvme0n1, if it is different, change it in the next commands)
    

O headnode terá a unidade compartilhada com a instalação e o modelo. Isso será compartilhado entre todos os diferentes nós de trabalho. Cada nó de trabalho também montará a unidade a ser executada localmente em uma unidade NVMe. Neste exemplo, o diretório de compartilhamento será de 500 GB, mas fique à vontade para alterar isso.

2.  Particione a unidade no nó de trabalho (opcional)

    ```
     $ sudo yum -y install gdisk
    ```
    ```
     $ sudo gdisk /dev/nvme0n1
     $ > n    # Create new partition
     $ > 1    # Partition Number
     $ >      # Default start of the partition
     $ > +500G # Size of the shared partition
     $ > 8300  # Type = Linux File System
     $ > n     # Create new partition
     $ > 2     # Partition Number
     $ >       # Default start of the partition
     $ >       # Default end of the partition, to fill the whole drive
    
     $ > 8300  # Type = Linux File System 
    
     $ > w      # Write to file
     $ > Y      # Accept Changes
    ```
    

3.  Formatar unidade nos nós de trabalho

    ```
    $ sudo mkfs -t ext4 /dev/nvme0n1
    ```
    

4.  Formate as partições no headnode `sudo mkfs -t ext4 /dev/nvme0n1p1 sudo mkfs -t ext4 /dev/nvme0n1p2`
    
5.  Crie um diretório e monte a unidade
    
6.  Headnode(local e compartilhar): selecione /mnt/share como o diretório de montagem para a partição 500G e /mnt/local para a maior.
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1p1 /mnt/share
         sudo chmod 777 /mnt/share
         sudo mount /dev/nvme0n1p2 /mnt/local
         sudo chmod 777 /mnt/local
        
        
7.  Headnode (compartilhamento):
    
         sudo mkdir /mnt/share
         sudo mount /dev/nvme0n1 /mnt/share
         sudo chmod 777 /mnt/share
        
8.  Nós de trabalho: selecione /mnt/local como o diretório de montagem de toda a unidade.
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1 /mnt/local
         sudo chmod 777 /mnt/local
        

## Tarefa 5: Criando um Sistema de Arquivos de Rede

1.  Criar Destino de Montagem. No menu superior esquerdo, clique em **File Storage** e **Mount Target**.

    - New Mount Target Name: Enter a name (example: openfoam_fs)
    - Virtual Cloud Network: Select the VCN created above
    - Subnet: Select the public VCN
    

2.  O headnode está em uma sub-rede pública, manteremos o firewall ativo e adicionaremos uma exceção por meio de:  
    

    ```
    sudo firewall-cmd --permanent --zone=public --add-service=nfs
    sudo firewall-cmd --reload
    ```
    

3.  Clique no ponto de acesso NFS que foi criado, clique no caminho de exportação e, em seguida, clique nos comandos de montagem e copie esses comandos que devem ter a aparência a seguir: `sudo yum install nfs-utils sudo mkdir -p /mnt/share sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share`
    
4.  Nos nós de trabalho, como eles estão em uma sub-rede privada com uma lista de segurança restringindo o acesso, podemos desativar totalmente o firewall. Em seguida, podemos instalar ferramentas nfs-utils e montar os nfs como fizemos acima.
    

    ```
    sudo systemctl stop firewalld
    sudo yum -y install nfs-utils
    sudo mkdir  -p /mnt/share
    sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share
    ```
    

## Tarefa 6: Instalar OpenFOAM

1.  **Conectando todos os nós de trabalho**  
    

    Each worker node needs to be able to talk to all the worker nodes. SSH communication works but most applications have issues if all the hosts are not in the known host file. To disable the known host check for nodes with address in the VCN, you can deactivate with the following commands. You may need to modify it slightly if your have different addresses in your subnets. Put the following code block in a shell script and run the script. 
    
        ```
        for i in 0 1 2 3
        do
            echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
            echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done
        ```
    

2.  **Crie uma lista de máquinas**  
    

    OpenFOAM on the headnode does not automatically know which compute nodes are available. You can create a machinefile at `/mnt/share/machinelist.txt with` the private IP address of all the nodes along with the number of CPUs available. The headnode should also be included. The format for the entries is `private-ip-address cpu=number-of-cores`
    
        ```
        10.0.0.2 cpu=1
        10.0.3.2 cpu=1
        ```
    

3.  **Headnode**  
    

    Install from sources, modify the path to the tarballs in the next commands. This example has the foundation OpenFOAM sources. OpenFOAM from ESI has also been tested. To share the installation between the different compute nodes, install on the network file system.
    
    
        ```
        sudo yum groupinstall -y 'Development Tools'
        sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        cd /mnt/share
        wget -O - http://dl.openfoam.org/source/7 | tar xvz
        wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        mv OpenFOAM-7-version-7 OpenFOAM-7
        mv ThirdParty-7-version-7 ThirdParty-7
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        cd /mnt/share/OpenFOAM-7
        ./Allwmake -j
    
        ```
    

4.  **Nó do trabalhador**  
    
        sudo yum -y install openmpi openmpi-devel
        cd /mnt/share
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        
        

## Tarefa 7: Executar carga de trabalho de simulação e Renderizar a saída

1.  No Headnode, execute os comandos a seguir que serão necessários para renderizar a saída usando o pacote Paraview.
    
         ```
         $ sudo yum install -y mesa-libGLU
         $ cd /mnt/share
         $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php > file.tar.gz
         $ tar -xf file.tar.gz
         ```
        
2.  Em headnonde, execute estes comandos para configurar o servidor VNC:  
    
        $ sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver
        

3.  Faça download desse zip com os [scripts](../scripts/motorbike_RDMA.tgz) em /mnt/share/work em um dos nós de trabalho. Descompacte o arquivo usando `tar -xf motorbike_RDMA.tgz`
    
4.  Antes de executarmos a carga de trabalho, precisamos editar o arquivo allrun para corresponder à arquitetura que criamos. Primeiro, moveremos a pasta da pasta do instalador OpenFOAM
    
         ```
         $ model_drive=/mnt/share
         $ sudo mkdir $model_drive/work
         $ sudo chmod 777 $model_drive/work
         $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
         $ cd /mnt/share/work/motorBike/system
         ```
        
5.  Edite o sistema de arquivos/decomposeParDict e altere essa linha numberOfSubdomains 6; para numberOfSubdomains 12; ou quantos processos você precisará. Em seguida, no bloco hierarchicalCoeffs, altere o n de n (3 2 1); para n (4 3 1); Se você multiplicar esses 3 valores, deverá obter o numberOfSubdomains
    

Para execução com uma configuração de 1 nó de trabalho VM.Standard2.1:

      =========                 |
      \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
       \\    /   O peration     | Website:  https://openfoam.org
        \\  /    A nd           | Version:  7
         \\/     M anipulation  |
    \*---------------------------------------------------------------------------*/
    FoamFile
    {
        version     2.0;
        format      ascii;
        class       dictionary;
        object      decomposeParDict;
    }
    
    // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
    
    numberOfSubdomains 2;
    
    method          hierarchical;
    // method          ptscotch;
    
    simpleCoeffs
    {
        n               (4 1 1);
        delta           0.001;
    }
    
    hierarchicalCoeffs
    {
        n               (2 1 1);
        delta           0.001;
        order           xyz;
    }
    
    manualCoeffs
    {
        dataFile        "cellDecomposition";
    }
    
    
    // ************************************************************************* //
    

6.  Em seguida, edite o arquivo Allrun em /mnt/share/work/motorBike para ter esta aparência: \`\`\` #!/bin/sh cd ${0%/\*} || sair 1 # Executar a partir deste diretório NP=$1 install\_drive=/mnt/share # Funções de execução do tutorial de origem . $WM\_PROJECT\_DIR/bin/tools/RunFunctions
    
         # Copy motorbike surface from resources directory
         cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
         cp $install_drive/machinelist.txt hostfile
        
         runApplication surfaceFeatures
        
         runApplication blockMesh
        
         runApplication decomposePar -copyZero
         echo "Running snappyHexMesh"
         mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.snappyHexMesh
         ls -d processor* | xargs -I {} rm -rf ./{}/0
         ls -d processor* | xargs -I {} cp -r 0 ./{}/0
         echo "Running patchsummary"
         mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
         echo "Running potentialFoam"
         mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
         echo "Running simpleFoam"
         mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam
        
         runApplication reconstructParMesh -constant
         runApplication reconstructPar -latestTime
        
         foamToVTK
         touch motorbike.foam
         ```
        
7.  Certifique-se de que você esteja no nó de trabalho e execute a carga de trabalho: `$ ssh worker_node_IP $ cd /mnt/share/work/ $ ./Allrun 2`
    
         ```
         $ ./Allrun 2
         $ Cleaning /mnt/share/work case
         $ Mesh Dimensions: (40 16 16)
         $ Cores:36: 6, 6, 1
         $ Running surfaceFeatures on /mnt/share/work
         $ Running blockMesh on /mnt/share/work
         $ Running decomposePar on /mnt/share/work
         $ Running snappyHexMesh
         $ Running patchsummary
         $ Running potentialFoam
         $ Running simpleFoam
         $ Running reconstructParMesh on /mnt/share/work
         $ Running reconstructPar on /mnt/share/work
         219.95
        
         ```
        

8.  Assim que a carga de trabalho for concluída com sucesso, configure o cliente VNC em sua máquina como esta. Fornecer IP Público do servidor Bastion e da porta VNC ![mercado](images/24-VNC_Connection.png)

9.  OPÇÃOAMENTE, caso você não tenha permissão para abrir a porta VNC 5901 ou, devido ao motivo de segurança, queira fazer o túnel ssh para essa porta, use o comando a seguir para fazer o túnel ssh na porta 5901 sem abrir a porta na lista de segurança
    
10.  Crie um túnel no seu laptop/área de trabalho usando o comando a seguir na janela do terminal. Aqui a comunicação da porta 5901 será feita na porta ssh 22 e o endereço IP 150.136.41.3 é o endereço IP público do servidor bastion.
    
        ```
        ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3
        ```
        
11.  Não feche a janela de terminal do túnel ssh acima. Agora inicie a sessão VNC e desta vez, em vez de o endereço IP, use "localhost" na porta 5901, mesmo que essa porta não esteja aberta na lista de segurança da sub-rede. ![mercado](images/28-ssh_Tunnel.png)
    

12.  Iniciar o aplicativo Paraview dentro do servidor bastion
    
        ```
        $ cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview
        ```
        
13.  Na janela do aplicativo Paraview, File -> Open -> Path "/mnt/gluster-share/work" e selecione o nome do arquivo motorbike.foam. O arquivo de byte será zero e deverá estar correto. ![mercado](images/25-Paraview_Menu.png)
    

14.  À esquerda da janela, na guia Propriedades, selecione Regiões de Malha para selecionar todos os valores e desmarque os valores superiores que não começam com o prefixo motorBike\_. Certifique-se de que todos os valores que começam com motorBike\_ estejam selecionados. Clique no botão Aplicar, alguns erros aparecerão, ignore a janela de erro exibida para exibir a renderização da imagem na console VNC. ![mercado](images/26-Mesh_Regions.png)

15.  Uma imagem como a seguir será renderizada na tela. Com base em algumas configurações de exibição, a imagem na tela pode parecer um pouco diferente. ![mercado](images/27-Image_Rendering.png)

Tudo pronto! Isso conclui a demonstração da execução do aplicativo OpenFoam em uma VM Padrão no OCI.

_Parabéns! Você concluiu o workshop com sucesso_

Estas são informações detalhadas sobre o gerenciamento da Instância de Computação de Alto Desempenho. Para obter uma referência completa de comandos, confira a documentação do OCI [aqui](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Última Atualização por/Data** - Harrison Dvoor, Outubro de 2020

# <<<<<<<<< CABEÇALHO

> > > > > > > upstream/mestre