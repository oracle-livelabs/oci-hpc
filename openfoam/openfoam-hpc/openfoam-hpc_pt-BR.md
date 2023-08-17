# Executar Projeto OpenFoam no Cluster HPC

## Introdução

A configuração do hardware HPC e a implantação de software de aplicativo podem levar muito tempo e incluir várias operações complexas. Com essa demonstração, todas essas tarefas são automatizadas em uma imagem do marketplace do OCI e um ambiente HPC totalmente funcional, juntamente com o software de simulação OpenFoam, está disponível logo após o provisionamento. Tudo o que é necessário é executar a carga de trabalho no ambiente HPC totalmente funcional e executar o aplicativo de visualização, como o Paraview, para renderizar a saída.

Tempo Estimado: 60 minutos

### Arquitetura de referência

![](./images/29_Architecture.png " ")

### Sobre o OpenFoam

A Dinâmica Computacional de Fluidos (CFD) é a simulação de movimento de fluidos e transferência de calor utilizando análise numérica. O software CFD, como OpenFOAM, pode servir como uma ferramenta poderosa para muitos setores de engenharia para melhorar os projetos, visualizar o impacto e detectar ineficiências em um sistema. No entanto, o poder computacional necessário para processar uma simulação pode ser muito intensivo. Supercomputadores e clusters HPC são frequentemente usados para executar modelos grandes e complexos dentro de um prazo razoável. Você calculará e renderizará o modelo aerodinâmico de uma motocicleta usando um software CFD de código-fonte aberto, OpenFOAM, em um cluster do Oracle HPC

### Terminologia de infraestrutura

1.  Nó de trabalho: instâncias de computação HPC que fornecem o poder de processamento para executar a carga de trabalho de simulações computacionais ou outras cargas de trabalho de engenharia. Nesta forma de computação de demonstração, os nós BM.HPC2.36 são os nós de trabalho.
    
2.  Nó principal: instância de computação de onde todos os jobs computacionais podem ser programados e submetidos para serem executados nos nós de Trabalho. Muitas vezes, o nó Head e o nó Bastion podem ser a mesma máquina. Para esta demonstração, estamos provisionando o nó Bastion.
    
3.  Nó GPU: Uma instância de computação especializada com processadores GPU para renderizar a saída da carga de trabalho computacional. Esta demonstração não está provisionando uma máquina GPU.
    
4.  Nó bastion: Uma instância de computação com IP Público, atua como ponto de entrada para estabelecer conexão com nós de trabalho que geralmente estão na rede Privada.
    
5.  Rede RoCE: RDMA sobre Ethernet Convertida (RoCE) é um protocolo de rede que permite acesso remoto direto à memória (RDMA) por uma rede Ethernet.
    

### Objetivos

1.  Provisione um ambiente HPC totalmente funcional usando a imagem do marketplace da OCI
    
2.  Implantação do aplicativo de simulação baseado em CFD chamado OpenFoam
    
3.  Executar carga de trabalho do Openfoam
    
4.  Converta a saída usando o aplicativo de visualização Paraview.
    

### Pré-requisitos

Certifique-se de ter acesso ao seguinte:

1.  Autorize o serviço Compute a gerenciar namespaces de tag em seu nome usando a seguinte política:
    
    "Permitir que o serviço compute\_management use tag-namespace na tenancy"
    
2.  Forma de Bare Metal BM.HPC2.36 permitida apenas nesta Demonstração. São necessárias, no mínimo, 2 configurações de computação no mesmo Domínio de Disponibilidade.
    
3.  Imagem de computação de VM para servidor bastion. Não precisa estar no mesmo Domínio de Disponibilidade que os nós HPC.
    
4.  As credenciais do usuário devem ter Cota para criar ou usar a VCN existente com sub-rede privada e pública.
    
5.  Acesso à imagem do marketplace "Cluster CFD Ready". A versão da imagem do marketplace usada neste DEMO é (Versão: 20200625).
    

## Tarefa 1: Iniciar imagem do marketplace

1.  Antes de o usuário iniciar a imagem do marketplace, verifique o domínio Disponibilidade em que 2 ou mais nós HPC com a forma BM.HPC2.36 estão disponíveis. Vá para **Menu** -> **Governança** -> **Limites**, **Cotas e Uso**
    
    ![](./images/01-Service_Limits.png " ")
    
2.  Vá para **Menu** -> **Marketplace** -> **Aplicativos**
    
    ![](./images/02-Marketplace.png " ")
    
3.  Na caixa de pesquisa, digite "cfd"
    
    ![](./images/03-Search_marketplace.png " ")
    
4.  Clique na Imagem, selecione-a e clique no botão "**Launch Stack**". Verifique se você está no compartimento certo.
    
    ![](./images/04-Launch_Stack.png " ")
    
5.  Quando a Pilha for Iniciada, preencha os detalhes para criar recursos, incluindo nós de trabalho HPC, servidor bastion, componentes de Rede, servidor VCN. A pilha também solicitará entradas para implantar pacotes como openMPI e openFoam.
    
6.  Forneça o nome da Pilha abaixo
    
    ![](./images/05-Create_Stack01.png " ")
    
7.  Selecione o Domínio de Disponibilidade no qual você tem nós HPC disponíveis para provisionamento. Forneça a chave pública ssh que será necessária para estabelecer conexão com o servidor Bastion.
    
    ![](./images/06-Create_Stack02.png " ")
    
8.  Selecione o Domínio de Disponibilidade para provisionar o servidor Bastion. Pode ser diferente do Domínio de Disponibilidade nos nós HPC. Selecione a forma de host do Bastion à sua escolha. Ative a caixa de seleção para instalar o servidor VNC no servidor Bastion e forneça a senha que será necessária para fazer a conexão VNC mais tarde.
    
    ![](./images/07-Create_Stack03.png " ")
    
9.  Para nós de Trabalho, a única opção selecionada é BM.HPC2.36. Você precisará de no mínimo 2 configurações de computação HPC para concluir o provisionamento. Selecione 2 ou mais nós de computação na tela abaixo. Todas as opções podem ficar à esquerda para o padrão.
    
    ![](./images/08-Create_Stack04.png " ")
    
10.  Em opções de Rede, crie uma nova VCN usando os valores padrão especificados na pilha. Esta Demonstração cria uma nova VCN criada por meio dessa pilha. Mas também existe a opção de usar a VCN existente.
    
    ![](./images/09-Create_Stack05.png " ")
    
11.  Para a seção Sistema de arquivos, use as opções abaixo que criarão a montagem de NFS e instalarão o sistema de arquivos gluster no topo dela.
    
    ![](./images/10-Create_Stack06.png " ")
    
12.  Clique na caixa de seleção "INSTALL OPENFOAM" para instalar o aplicativo OpenFoam nesta pilha HPC.
    
    ![](./images/11-Create_Stack07.png " ")
    
13.  Na Última página da pilha, verifique os detalhes que foram inseridos e, se tudo estiver OK, clique no botão "Criar" para iniciar o provisionamento da instalação da infraestrutura e do software.
    
    ![](./images/12-Create_Stack08.png " ")
    
14.  Verificar o andamento da instalação da pilha HPC
    
    ![](./images/13-StackJob.png " ")
    
15.  Quando o provisionamento for concluído em cerca de 20 minutos, o arquivo de log deverá conter a mensagem a seguir. Os logs podem ser encontrados em Menu -> Resource Manager -> Stacks(clique no nome da pilha) -> Jobs (Clique no link do nome do job para exibir o log completo)
    
    ![](./images/14-StackJobDetails.png " ")
    

## Tarefa 2: Validar a configuração

1.  Clique no link "Associar Recursos" à esquerda da tela para ver os recursos de infraestrutura provisionados por esta pilha. Recursos associados podem ser encontrados em **Menu** -> **Gerenciador de Recursos** -> **Pilhas**(clique no nome da pilha) -> **Jobs** (Clique no link do nome do job) -> **Associar Recursos**
    
    ![](./images/15-Stack_Resources.png " ")
    
2.  Em **Compute** -> **Instâncias**, 3 instâncias de computação serão provisionadas, 2 nós HPC em rede privada e 1 servidor Bastion com endereço IP Público.
    
    ![](./images/16-Compute_Details.png " ")
    
3.  Conecte-se ao servidor Bastion usando o endereço IP público como usuário opc usando a chave privada ssh para a qual foi feito upload de uma chave pública na pilha acima. Use este método para se conectar do Linux/Mac Desktop. Para desktop Windows, use Putty e forneça a chave privada ppk para fazer conexão.
    
    ![](./images/17-Bastion_ssh.png " ")
    
4.  Verifique o armazenamento NFS compartilhado que está montado em 2 nós HPC e no servidor bastion. Esse armazenamento NFS contém o software OpenFoam, que será usado para executar a carga de trabalho.
    
    ![](./images/18-Bastion_storage.png " ")
    
5.  O aplicativo OpenFoam é hospedado na pasta "/mnt/gluster-share/work". Verifique o arquivo de host que contém o endereço IP privilegiado dos 2 nós HPC nos quais a carga de trabalho será executada.
    
    ![](./images/19-Bastion_openfoam.png " ")
    
6.  A saída abaixo mostra um servidor VNC em execução na porta 5901. Esta porta será usada para fazer a conexão VNC com o bastion host.
    
    ![](./images/20-Bastion_VNCdetails.png " ")
    
7.  Verifique na lista de segurança do servidor Bastion nas regras de Entrada que a porta 5901 é permitida. No caso de a VNC estar em execução em qualquer outra porta e se essa porta não for permitida para tráfego de entrada na lista de segurança, crie uma nova regra de segurança para a mesma.
    
    ![](./images/21-SecList_OpenVNC_Port.png " ")
    
8.  Verifique os nomes dos nós HPC.
    
    ![](./images/22-ListHPCNodes.png " ")
    

## Tarefa 3: Executar carga de trabalho de simulação e Renderizar a saída

1.  No servidor Bastion, execute os comandos a seguir que serão necessários para renderizar a saída usando o pacote Paraview.
    
        <copy>$ sudo yum install -y mesa-libGLU
        $ cd /mnt/gluster-share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" https://www.paraview.org/paraview-downloads/download.php >file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  Conecte-se a um dos nós HPC do servidor bastion e execute a carga de trabalho
    
        <copy>$ ssh 172.16.1.2
        $ cd /mnt/gluster-share/work/
        $ ./Allrun 36</copy>
        
    
        ./Allrun 36
        Cleaning /mnt/gluster-share/work case
        Mesh Dimensions: (40 16 16)
        Cores:36: 6, 6, 1
        Running surfaceFeatures on /mnt/gluster-share/work
        Running blockMesh on /mnt/gluster-share/work
        Running decomposePar on /mnt/gluster-share/work
        Running snappyHexMesh
        Running patchsummary
        Running potentialFoam
        Running simpleFoam
        Running reconstructParMesh on /mnt/gluster-share/work
        Running reconstructPar on /mnt/gluster-share/work
        219.95
        [opc@inst-quqyz-accurate-swan work]$
        
3.  Assim que a carga de trabalho for concluída com sucesso, configure o cliente VNC em sua máquina como esta. Fornecer IP Público do servidor Bastion e da porta VNC
    
    ![](./images/24-VNC_Connection.png " ")
    
4.  OPÇÃOAMENTE, caso você não tenha permissão para abrir a porta VNC 5901 ou, devido ao motivo de segurança, queira fazer o túnel ssh para essa porta, use o comando a seguir para fazer o túnel ssh na porta 5901 sem abrir a porta na lista de segurança
    
    Crie um túnel no seu laptop/área de trabalho usando o comando a seguir na janela do terminal. Aqui a comunicação da porta 5901 será feita na porta ssh 22 e o endereço IP 150.136.41.3 é o endereço IP público do servidor bastion.
    
        <copy>$ ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
5.  Não feche a janela de terminal do túnel ssh acima. Agora inicie a sessão VNC e desta vez, em vez de o endereço IP, use "localhost" na porta 5901, mesmo que essa porta não esteja aberta na lista de segurança da sub-rede.
    
    ![](./images/28-ssh_Tunnel.png " ")
    
6.  Iniciar o aplicativo Paraview dentro do servidor bastion
    
        <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        ./paraview</copy>
        
7.  Na janela do aplicativo Paraview, File -> Open -> Path "/mnt/gluster-share/work" e selecione o nome do arquivo motorbike.foam. O arquivo de byte será zero e deverá estar correto.
    
    ![](./images/25-Paraview_Menu.png " ")
    
8.  À esquerda da janela, na guia Propriedades, selecione Regiões de Malha para selecionar todos os valores e desmarque os valores superiores que não começam com o prefixo motorBike\_. Certifique-se de que todos os valores que começam com motorBike\_ estejam selecionados. Clique no botão Aplicar, alguns erros aparecerão, ignore a janela de erro exibida para exibir a renderização da imagem na console VNC.
    
    ![](./images/26-Mesh_Regions.png " ")
    
9.  Uma imagem como a seguir será renderizada na tela. Com base em algumas configurações de exibição, a imagem na tela pode parecer um pouco diferente.
    
    ![](./images/27-Image_Rendering.png " ")
    

Tudo pronto! Isso conclui a demonstração da execução do aplicativo OpenFoam em um cluster HPC.

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Última Atualização por/Data** - Harrison Dvoor, janeiro de 2021