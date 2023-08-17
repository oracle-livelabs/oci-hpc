# Ativar Rede de Clusters HPC

## Introdução

Há muitas maneiras de iniciar uma Rede de Clusters HPC, este guia de soluções abrangerá dois métodos diferentes:

*   Via Marketplace
*   Manualmente Dependendo do seu SO, você vai querer ir com um método específico. Se a imagem de marketplace da Rede de Clusters HPC ou nossos scripts Terraform do OCI HPC CN forem usados, isso será apenas para o Oracle Linux 7. Se você quiser usar CentOS, Ubuntu ou outro SO, será necessária uma configuração manual.

Tempo Estimado de Laboratório: 30 minutos

## **OPÇÃO 1**: Criação de Rede de Clusters por meio do Marketplace

O Marketplace contém aplicativos e imagens que podem ser implantados com nossa infraestrutura. Para clientes que desejam usar o Oracle Linux, uma imagem de HPC Cluster Network está disponível e pode ser iniciada diretamente no marketplace. Sugerimos a inicialização do [Cluster CFD Ready](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) que conterá as bibliotecas necessárias para CFD.

1.  No Marketplace, selecione Get App no canto superior direito. ![](images/get-app.png " ")
    
2.  Selecione a Região do OCI e clique em Acessar. ![](images/sign-in.png " ")
    
3.  Após o acesso, verifique a versão da imagem do Cluster HPC e selecione o Compartimento no qual o cluster será iniciado. Aceite os termos e condições e, em seguida, Inicie a Pilha.
    
    ![](images/launch-stack.png " ")
    
4.  Preencha os detalhes restantes da pilha:
    
    *   Selecione o AD desejado para as formas de computação e o bastion.
    *   Copie-se sua chave ssh pública
    *   Digite o número de instâncias do serviço Compute para o cluster
    *   Desmarque Install OpenFOAM
    *   Se precisar de mais de 6 TB de espaço em disco compartilhado, verifique GlusterFS e selecione quantos servidores você precisaria. (6 TB por servidor)
5.  Clique em Criar.
    
6.  Navegue até Ações do Terraform e clique em Aplicar. Isso iniciará o provisionamento de CN.
    
    ![](images/apply.png " ")
    
7.  Aguarde até que o job mostre 'Bem-sucedido', em seguida, navegue até Saídas para obter o bastion e os IPs privados do nó de computação.
    

## **OPÇÃO 2**: Criação de Rede de Clusters por meio da Configuração Manual

O Marketplace contém aplicativos e imagens que podem ser implantados com nossa infraestrutura. Para clientes que desejam usar o Oracle Linux, você pode criar manualmente uma rede de clusters da seguinte forma:

1.  Selecione a Região do OCI no canto superior direito.
    
2.  No menu principal, selecione Rede e Redes Virtuais na Nuvem
    
3.  Clique em Iniciar Assistente de VCN e selecione VCN com Conectividade de Internet
    
4.  Escolha e nomeie, o compartimento direito e use 172.16.0.0/16 como CIDR da VCN, 172.16.0.0/24 para Sub-rede Pública e 172.16.1.0/24 para Sub-rede Privada. Clique em **Próximo** e em **Criar**.
    
5.  No menu principal, selecione Compute, Instance e Create Instance
    
6.  Selecione **Alterar Imagem** e selecione Oracle Linux 7 - HPC Cluster Networking Image, na guia Imagem Oracle.
    
7.  Selecione o Domínio de Disponibilidade no qual você pode configurar uma instância do BM.HPC2.36
    
8.  Altere a forma para BM.HPC2.36 em Bare Metal e Especialidade
    
9.  Selecione a VCN e a sub-rede pública que você criou.
    
10.  Adicione uma chave pública para estabelecer conexão com a instância. Essa chave será usada em todas as instâncias de computação.
    
11.  Quando a máquina estiver ativa, clique na instância criada. Em Mais Ações, selecione Criar Configuração de Instância. Agora você pode encerrar a instância em Mais Ações.
    
12.  No menu principal, selecione Compute e, em seguida, Cluster Networks
    
13.  Clique em Criar Rede de Clusters e preencha todas as opções. Use a VCN, a sub-rede privada e a configuração de instância que você acabou de criar. Selecione o AD no qual você pode iniciar instâncias BM.HPC2.36.
    
14.  Inicie a rede de clusters.
    
15.  Enquanto estiver carregando, crie outra instância em Menu Principal, Computação e Instâncias.
    
16.  Coloque-o na sub-rede pública que acabou de ser criada, usando sua chave e forma públicas, deve ser VM.Standard2.1 ou semelhante. Este será o bastion que usaremos para estabelecer conexão com o cluster.
    
17.  SCP a chave para o cluster no bastion em /home/opc/.ssh/cluster\_key e copie-a também para /home/opc/.ssh/id\_rsa
    
18.  Instale a Ferramenta de Provisionamento no bastion por meio do seguinte comando:
    
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        
19.  Navegue até Compute e depois Pools de Instâncias na Console e colete todos os endereços IP do pool de rede de clusters. Ou use esse comando no bastion se você não tiver mais nada em execução na sua sub-rede privada.
    
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done
        
20.  Instale a Ferramenta de Provisionamento com o seguinte comando:
    
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020