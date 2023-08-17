# Provisionar uma instância de GPU

## Introdução

Neste laboratório, examinaremos as etapas necessárias para provisionar uma instância de GPU no OCI com o Ubuntu em execução nela.

**Observação:** É necessária uma conta PAYG para provisionar instâncias de GPU no OCI. Se você estiver usando a avaliação grátis do Oracle de 30 dias, poderá usar todas as instâncias de VM e BMs disponíveis.

Tempo estimado do workshop: 15 minutos

### Objetivos

*   Acessar o Oracle Cloud
*   Provisionar uma instância de GPU no OCI

### O que você precisa?

*   Um usuário (com um nome de usuário e uma senha) que faz parte de um grupo de usuários no OCI. Verifique a página [Gerenciando usuários](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm) para ler como **Para criar um usuário** e como **Para adicionar um usuário a um grupo** usando a console.
*   Nome da Conta do Cloud - O nome da sua tenancy (fornecido pelo administrador ou no seu e-mail de boas-vindas do Oracle Cloud)
*   Um grupo de usuários. Verifique a página [Gerenciando grupos](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managinggroups.htm#three) para ler como **Para criar um grupo** usando a console.
*   Políticas designadas ao grupo que permitem que os usuários iniciem instâncias de computação. Verifique a página [Políticas Comuns](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm#top) para ler quais políticas você precisa **Para permitir que os usuários iniciem instâncias do serviço Compute** usando a console.
*   Um compartimento para o usuário. Verifique a página [Gerenciando Compartimentos](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingcompartments.htm#uscons) para ler como **Para criar um compartimento** usando a console.
*   Verifique os limites do serviço para o número máximo permitido de instâncias de GPU por domínio de disponibilidade na região em que você deseja provisionar a instância de GPU. Para aumentar o limite de serviço, siga as etapas na página [Limites de Serviço](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/servicelimits.htm#top).

## Tarefa 1: Fazer Log-in no Oracle Cloud

Se você tiver saído do Oracle Cloud, use estas etapas para acessar novamente.

1.  Vá para [cloud.oracle.com](https://cloud.oracle.com) e informe o Nome da sua Conta do Cloud e clique em **Próximo**. Este é o nome escolhido ao criar sua conta na seção anterior. NÃO é seu endereço de email. Se você esqueceu o nome, consulte o e-mail de confirmação.
    
    ![](images/cloud-oracle.png " ")
    
2.  Expanda a seta após _"Oracle Cloud Infrastructure Direct Sign-In"_ para revelar os campos de entrada de log-in.
    
    ![](images/cloud-login-tenant.png " ")
    
3.  Informe as credenciais da sua Conta do Cloud e clique em **Acessar**. Seu nome de usuário é seu endereço de e-mail. A senha é o que você escolheu quando se inscreveu em uma conta.
    
    ![](images/oci-signin.png " ")
    
4.  Agora você acessou o Oracle Cloud!
    
    ![](images/oci-console-home-page.png " ")
    

## Tarefa 2: Iniciar uma instância de GPU

1.  Depois de fazer log-in, clique no botão **Criar uma instância de VM** na seção **Ações Rápidas**.
    
    ![](images/click-create-vm-instance.png " ")
    

Como alternativa, você pode clicar no menu de serviço na parte superior esquerda e, em seguida, em **Compute** -> **Instâncias**. Uma nova página é aberta com um botão azul **Criar Instância** no qual você clica.

2.  Preencha o nome da instância de computação.
    
    ![](images/fill-in-name.PNG " ")
    
3.  Selecione seu compartimento na lista drop-down.
    
    ![](images/select-compartment.PNG " ")
    
4.  Antes de selecionar o domínio de disponibilidade, verifique em **Governança** -> **Limites, Cotas e Uso**, em que domínio de disponibilidade da região selecionada você tem instâncias de GPU disponíveis. Selecione o domínio de disponibilidade no qual a instância de computação será provisionada.
    
    ![](images/select-AD.PNG " ")
    
5.  Clique no botão **Alterar imagem**.
    
    ![](images/change-image.PNG " ")
    
    Selecione a imagem que você deseja usar para sua instância de computação e clique no botão **Selecionar Imagem**. Nós selecionaremos Ubuntu 18.04, que é recomendado pela Faceswap, mas qualquer outra distribuição Linux e sistema operacional Windows também pode funcionar.
    
    ![](images/select-image.PNG " ")
    
6.  Clique no botão **Alterar Forma**.
    
    ![](images/change-shape.PNG " ")
    
    Selecione **Máquina Virtual** e **Especialidade e Legado**. Lá você encontrará a instância de GPU e a VM.Standard.E2.1 **Always Free**. Microforma (caso você não esteja possuindo uma conta PAYG para executar instâncias de GPU).
    
    ![](images/select-VM-SL.PNG " ")
    
    Selecione a instância GPU que você deseja provisionar, leia e marque a caixa _Revisei e aceito os seguintes documentos: Termos de uso da Oracle e da NVIDIA_ e clique no botão **Selecionar forma**. No nosso caso, é a VM.GPU2.1 baseada na arquitetura de GPU Pascal P100 com 1 GPU. Você pode solicitar limites de serviço para máquinas virtuais e bare metal com base em V100 (arquitetura de GPU NVIDIA Volta) e A100 (arquitetura de GPU NVIDIA Ampere) também.
    
    ![](images/select-GPU.PNG " ")
    
7.  Caso já tenha criado uma VCN no seu compartimento, selecione a VCN e a sub-rede na qual você deseja colocar sua instância de computação. Caso contrário, você poderá clicar em **Criar nova rede virtual na nuvem**, selecionar um nome e um compartimento para sua VCN e um nome para sua sub-rede pública. Você pode manter o bloco CIDR padrão ou ajustá-lo. Você pode optar por designar um endereço IP público à sua instância. A designação de um endereço IP público torna essa instância acessível pela internet. Se você não tiver certeza se precisa de um endereço IP público, poderá sempre designar um posteriormente.
    
    ![](images/create-vcn.PNG " ")
    
8.  Caso queira usar o par de chaves pública e privada SSH que você criou no _Lab 1 - Create SSH keys_, clique em **Escolher arquivos de chaves públicas** e selecione o(s) arquivo(s) de chaves públicas SSH ou clique em **Colar chaves públicas** e cole a(s) chave(s) pública(s) SSH no campo. Você também pode criar uma chave pública e privada SSH no OCI e salvá-la em sua máquina local. Observe que, se você não fornecer chaves SSH, não poderá estabelecer conexão com a instância usando SSH.
    
    ![](images/add-SSH.PNG " ")
    
9.  A etapa final é especificar o tamanho do volume de inicialização, escolhendo se deseja ou não usar criptografia em trânsito e como gerenciar as chaves de criptografia. Você pode ir com o tamanho do volume de inicialização padrão que é 46.6 GB para syxtems Linux ou aumentá-lo para no máximo 32 TB. Você pode deixar todas as caixas desmarcadas e clicar em **Criar** (para finalizar a criação da instância de computação).
    

![](images/boot-volume.PNG " ")

Após alguns minutos, sua instância de computação GPU mudará seu status para **RUNNING**

![](images/instance-running.PNG " ")

## **Confirmação**

*   **Criado por/Data** - Maria Patelkou, Arquiteto de Soluções HPC, programa Oracle Proposal to Production, março de 2021
*   **Data da Última Atualização** - Maria Patelkou, Arquiteto de Soluções HPC, programa Proposta para Produção da Oracle, março de 2021