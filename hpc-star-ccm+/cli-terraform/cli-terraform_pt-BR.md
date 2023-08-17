# Use os comandos da CLI do OCI e o Terraform para trabalhar com HPC

## Introdução

`ocihpc` é uma ferramenta para simplificar implantações de aplicativos HPC na OCI (Oracle Cloud Infrastructure). A CLI (Interface de Linha de Comando) HPC permite que você use um único comando para implantar clusters HPC de qualquer tamanho com nossas instâncias de computação HPC bare metal dedicadas e rede RDMA Mellanox. Você não precisa entender o Terraform ou o Oracle Cloud Infrastructure Resource Manager para usar a CLI HPC. Foi desenvolvido com base na CLI do Oracle Cloud Infrastructure principal e inclui scripts Terraform predefinidos.

Os benefícios de usar a CLI HPC incluem:

1.  Inicie rapidamente um cluster de computação completo com RDMA sobre Ethernet convergida (RoCE) v2 por meio de um único comando.
2.  Conhecimento Não Utilizado do Terraform ou do Oracle Cloud Infrastructure Resource Manager necessário para iniciar uma rede de clusters básica.
3.  A Implantação da interface de passagem de mensagens (MPI) inclui um conjunto completo de pacotes de software para execução de processamento paralelo com RDMA, incluindo Mellanox OFED com MPI aberto, MPI Intel e MPI da plataforma.
4.  Personalizável Você pode executar seus próprios scripts de terraform ou adicionar aos scripts existentes fornecidos com a ferramenta para instalar seus próprios aplicativos.

Tempo Estimado de Laboratório: 60 minutos

### Objetivos

Como Desenvolvedor ou Engenheiro de Dados,

1.  Implantar Rede de Clusters HPC usando a Interface de Linha de Comando da Oracle
2.  Exibir, executar e gerenciar a Pilha via Cli

### Pré-requisitos

1.  Uma conta do Oracle Cloud Infrastructure que tem credenciais do usuário suficientes para executar uma pilha do Terraform.
2.  Chaves SSH Geradas

### Políticas

A conta de usuário do OCI que você usa em `ocihpc` deve ter as políticas necessárias configuradas para o OCI Resource Manager. Verifique [este link](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm) para obter informações sobre as políticas necessárias.

## Tarefa 1: Instalando ocihpc no macOS/Linux

1.  Faça download da versão mais recente com o seguinte comando e extraia-o:
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  Descompacte o arquivo baixado na Etapa 1.
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  Torne o arquivo executável binário ocihpc.
    
        $ chmod +x ./ocihpc 
        
4.  Mova o binário ocihpc para seu PATH.
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  Teste se funciona.
    
        $ ocihpc version 
        

## Tarefa 2: Instalando ocihpc no Windows

1.  Baixe a versão mais recente [deste link](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip) e extraia-a.
    
2.  Adicione o binário ocihpc ao seu PATH.
    
3.  Teste se funciona.
    
        $ ocihpc.exe version 
        

## Tarefa 3: Criando um keypair ssh na Linha de Comando

Consulte [este link](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm) se estiver usando janelas.

1.  Abra um shell ou terminal para digitar os comandos.
    
2.  Navegue até a pasta .oci
    
        $ cd Users/enjli/.ssh
        
3.  Se você ainda não tiver criado a pasta, crie um diretório .ssh para armazenar as credenciais:
    
        $ mkdir Users/enjli/.ssh
        
4.  Se você não vir pares de chaves id\_rsa na pasta, informe o comando a seguir e forneça um nome e uma frase-senha quando solicitado. As chaves serão criadas com os valores padrão: chaves RSA de 2048 bits.
    
        $ ssh-keygen
        

## Tarefa 4: Gerar uma Chave de Assinatura da API

As suas solicitações da API serão assinadas com a sua chave privada, e o sistema Oracle usará a chave pública para verificar a autenticidade da solicitação. Consulte [este link](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other) se estiver usando janelas.

1.  Caso contrário, crie um diretório .oci para armazenar as credenciais:
    
        $ mkdir Users/enjli/.oci
        
2.  Gere a chave privada com os comandos a seguir.
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  Certifique-se de que apenas você possa ler o arquivo de chave privada:
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  Gere a chave pública:
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## Tarefa 5: Adicionar chave pública ao Oracle Cloud Infrastructure

Agora que você tem uma combinação de chave privada/pública, é necessário adicioná-la à console do OCI na definição do usuário:

1.  Navegue até sua pasta .oci contendo `oci_api_key_public.pem`, copie a chave pública.
    
2.  Faça log-in na console do OCI e clique em Menu e selecione Identidade e Usuários. Selecione um Usuário e navegue até a página Detalhes do Usuário.
    
3.  Clique em Adicionar Chave Pública na seção Chaves de API. ![](./images/ResourcesMenu.png) ![](./images/APIKeys.png)
    
4.  Cole a chave Pública que você copiou da CLI em Adicionar Chave Pública ![](./images/AddPublicKey.png)
    

## Tarefa 6: Configurar

Esta etapa descreve a configuração necessária para a CLI e inclui configurações opcionais que permitem estender a funcionalidade da CLI.

1.  Antes de usar a CLI, você precisa criar um arquivo de configuração na pasta .oci que contenha as credenciais necessárias para trabalhar com sua conta do Oracle Cloud Infrastructure. Você pode criar esse arquivo usando uma caixa de diálogo de configuração ou manualmente, usando um editor de texto.
    
2.  Execute _ocihpc configure_ para verificar se você tem uma configuração válida para acessar o OCI. A ferramenta orientará você na criação de uma configuração.
    

Você será notificado em que seu arquivo de configuração foi gravado em:

_Arquivo de configuração salvo em: /Users/enjli/.oci/config_

Para criar seu arquivo de configuração, será necessário: 1. O OCID do usuário (localizado na seção de perfil no canto superior direito da tela em > configurações do usuário > guia de informações do usuário), 2. OCID da Tenancy (Administração > Detalhes da Tenancy > guia Informações da Tenancy), 3. A região da qual você está trabalhando (ou seja, us-phoenix-1, us-ashburn-1 etc.), 4. Impressão digital do usuário (localizada na guia de informações do usuário), 5. Caminho para sua chave de assinatura privada da API.

Um exemplo de arquivo `config` seria semelhante a este: ![](./images/oci_config.png)

## Tarefa 7: Lista

Você pode obter a lista de pilhas disponíveis executando `ocihpc list`.

Exemplo:

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## Tarefa 8: Inicializar

1.  Crie uma pasta que você usará como origem de implantação.

IMPORTANTE: Use uma pasta diferente por pilha. Não inicialize mais de uma pilha na mesma pasta. Caso contrário, a ferramenta substituirá a anterior.

2.  Altere para essa pasta e execute `ocihpc init <stack name>`. `ocihpc` fará download dos arquivos necessários para essa pasta.
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**IMPORTANTE**: Edite o conteúdo do arquivo /Users/enjli/ocihpc-test/config.json antes de executar o comando de implantação ocihpc

## Tarefa 9: Implantar

1.  Antes de implantar, você precisa alterar os valores no arquivo `config.json`. As variáveis dependem da pilha que você implanta. Um exemplo de `config.json` para a Rede de Clusters seria semelhante a este:
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  Para modificar seu arquivo `config.json`, navegue até o diretório recém-criado (ocihpc-test neste caso) e abra o arquivo "config.json" usando texteditor ou notepad. ![](./images/config_json.png)
    

Observe que este não é o mesmo arquivo de configuração que configuramos na etapa 1.

Para esse arquivo de configuração, precisaremos:

1.  As informações do domínio de disponibilidade que contêm os recursos HPC em nossa tenancy (Administração > Detalhes da Tenancy > Role para baixo até a seção Limites de Serviço > Computação > e role para baixo para encontrar "BM.HPC2.36") - No screenshot abaixo, podemos ver que temos um total de 6 máquinas BM.HPC2.36 para usar no AD-2, 0 das quais estão em uso no momento. ![](./images/hpc_resource.png)
    
2.  O Bastion AD pode ser qualquer AD escolhido desde que haja recursos (forma VM.standard2.1) A forma do Bastion já deve ser preenchida - VM.Standard2.1
    
3.  Contagem de nós - para fins deste laboratório, vamos com 2 para usar todos os recursos de HPC
    
4.  Nossa chave ssh pública
    

**Observações**

1.  Depois de alterar os valores em `config.json`, você poderá implantar a pilha com `ocihpc deploy <arguments>`. Esse comando criará uma Pilha no Oracle Cloud Resource Manager e implantará a pilha usando-a.
    
2.  Para pilhas suportadas, você pode definir o número de nós que deseja implantar adicionando-o ao comando `ocihpc deploy`. Se a pilha não suportar ou se você não fornecer um valor, a ferramenta será implantada com os números padrão.
    
3.  Por exemplo, o seguinte comando implantará uma Rede de Clusters com 5 nós:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  A ferramenta gerará um nome de implantação que consiste em `<stack name>-<current directory>-<random-number>`.
    
    Exemplo:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## Tarefa 10: Conectar

Quando a implantação for concluída, você verá o bastion/headnode IP ao qual pode se conectar:

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

Você também pode obter os detalhes da conexão executando o comando `ocihpc get ip`.

## Tarefa 11: Gerenciar

Além disso, você pode usar comandos cli para gerenciar e manter facilmente os recursos:

1.  Para gerar uma lista de todas as pilhas implantadas em um compartimento específico: `oci resource-manager job list -c [OCID OF COMPARTMENT]`
    
2.  Para gerar uma lista de jobs em uma pilha ou compartimento, ordenados pelo horário de criação: `oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  Para revisar um job junto com os detalhes do job: `oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  Para exibir entradas de log do job especificado no formato JSON: `oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`
    
5.  Para mover uma Pilha e seus Jobs associados para outro compartimento: `oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## Tarefa 12: Excluir

Quando terminar sua implantação, você poderá excluí-la alterando para a pasta da pilha e executando `ocihpc delete --stack <stack name>`.

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

Tudo pronto! Você implantou com sucesso sua primeira Instância de Computação de Alto Desempenho usando a ferramenta CLI do OCI.

_Agora você pode ir para o próximo laboratório_

Estas são informações detalhadas sobre o gerenciamento da Instância de Computação de Alto Desempenho. Para obter uma referência completa a comandos, confira a documentação do OCI [aqui](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Última Atualização por/Data** - Harrison Dvoor, Outubro de 2020

# <<<<<<<<< CABEÇALHO

> > > > > > > upstream/mestre