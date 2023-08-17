# Provisionar Cluster HPC da Imagem do Oracle Marketplace

## Introdução

Neste laboratório, você estará provisionando um Cluster de HPC do OCI Marketplace. O OCI Marketplace é um balcão único para uma ampla variedade de soluções do setor que podem ser facilmente implantadas em nossa console de nuvem. Aqui você estará aproveitando esse marketplace para a Implantação Automática de Cluster de HPC. O modelo do mercado fornece uma base para uma solução de Computação de Alto Desempenho, incluindo todos os componentes necessários para iniciar uma carga de trabalho com o mínimo de esforço. À medida que esses laboratórios avançarem, você verá como essa linha de base pode ser integrada e utilizada por aplicativos de terceiros para acelerar seu workflow.

Tempo Estimado de Laboratório: 60 minutos

### Objetivos

Como Desenvolvedor ou Engenheiro de Dados,

*   Navegue para o OCI Marketplace
*   Implantar imagem de Cluster de HPC do OCI Marketplace
*   Exibir e Executar uma Pilha no OCI

### Pré-requisitos

*   Uma conta do Oracle Cloud Infrastructure com privilégios para criar uma configuração VM padrão 2.1 ou BM.HPC2.36 da instância.

## Tarefa 1: Navegar até o OCI Marketplace

1.  Clique no menu de hambúrguer no canto superior esquerdo. ![mercado](images/click_hamburger.png)
    
2.  Role para baixo e clique em Marketplace. ![mercado](images/click_marketplace.png)
    
3.  Pesquise no marketplace o **HPC** e selecione o Cluster HPC. ![mercado](images/marketplace.png)
    
4.  Verifique e aceite os **Termos e Restrições Padrão da Oracle** e clique em **Iniciar Pilha**. ![mercado](images/launch_stack.png)
    

## Tarefa 2: Configurar a Pilha HPC

Depois de iniciar a pilha, você será redirecionado ao Assistente de Criação de Pilha, onde preencherá as informações da Pilha.

1.  Em **Informações da Pilha**, forneça um nome à Pilha e _(opcionalmente)_ especifique para implantar em um compartimento no qual você não está no momento. ![mercado](images/stack_p1.png)
    
2.  Selecione o **AD** desejado para as formas de computação e o bastion.
    
3.  Copie-coloque sua **chave ssh** pública ![mercado](images/stack_p2_1.png)
    
4.  Digite o número de **Instâncias de Computação** para o cluster ![mercado](images/stack_p2_2.png)
    
5.  Em **Opções de Rede**, deixe os padrões ![mercado](images/stack_p2_3.png)
    
6.  Clique em **Próximo** para revisar a configuração. ![](./images/stack_p3.png)
    
7.  Clique em **Criar**
    

**Observação:** levará aproximadamente 15 minutos para provisionar a Pilha.

## Tarefa 3: Exibir a Pilha em execução

Depois de criar a Pilha, ela o redirecionará para uma página em que você poderá exibir o job de pilha em execução. Deve parecer algo abaixo. Aqui você pode ver:

1.  **Registros**
2.  **Variáveis** transmitidas ou geradas pelo script Terraform
3.  **Recursos Associados** provisionados pelo script Terraform
4.  **Saídas**
5.  **Exibir Estado** do job

![mercado](images/stack_detail_provisioning.png)

Para navegar até essa página em que você pode exibir todas as suas Pilhas no compartimento atual, clique no Menu de Hambúrguer (\_\_\_\_) na parte superior esquerda e, em **Soluções e Plataforma**, passe o mouse sobre **Resource Manager** > **Pilhas**.

![mercado](images/nav_resource_manager.png)

Tudo pronto! Isso conclui a demonstração do provisionamento de um Cluster HPC da Imagem do Oracle Marketplace.

_Agora você pode ir para o próximo laboratório_

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Última Atualização por/Data** - Harrison Dvoor, Outubro de 2020

# <<<<<<<<< CABEÇALHO

> > > > > > > upstream/mestre