# Siemens STAR-CCM+ Introdução

## Introdução

A Simcenter STAR-CCM+ é uma solução multifísica completa para a simulação de produtos e projetos. Este Runbook o conduzirá pelo processo de implantação de um cluster Simcenter STAR-CCM+ no Oracle Cloud com rede de baixa latência entre os nós de computação. Executar o Simcenter STAR-CCM+ no Oracle Cloud é bastante simples, siga este guia para todas as dicas e truques.

Por favor, note: Você precisa fornecer sua própria licença para Simcenter STAR-CCM+!

![](images/simulation.png " ")

## Arquitetura

A arquitetura deste runbook é como segue, temos uma pequena máquina (bastion) na qual você se conectará. Os nós de computação estarão em uma rede privada separada vinculada à rede RDMA RoCE v2. O bastion poderá ser acessado por meio do SSH de qualquer pessoa com a chave (ou da VNC se você decidir ativá-la). Os nós de computação só estarão acessíveis por meio do bastion dentro da rede. Isso torna-se possível com 1 Rede Virtual na Nuvem com 2 sub-redes, uma pública e uma privada.

### **Infraestrutura de Linha de Base**

As Redes de Cluster são suportadas nas regiões a seguir. Em cada caso, recomendamos usar a arquitetura de referência de linha de base e ajustá-la, conforme necessário, para atender aos seus requisitos específicos:

*   VCN do Marcador
*   Sub-rede Pública, Lista de Segurança, Tabela de Roteamento
*   Sub-rede Privada, Lista de Segurança, Tabela de Roteamento
*   Gateway de Internet
*   Gateway NAT
*   Nós de Cálculo
*   Bastion Host em uma Sub-rede Pública
*   Nós de Computação HPC em Sub-rede Privada

![](images/architecture.png " ")

A infraestrutura de linha de base acima fornece as seguintes especificações:

*   Rede
    *   1 RDMA de 100 Gbps sobre Ethernet convergente (ROCE) v2
    *   Latência tão baixa quanto 1,5 μs
*   Nós de Computação HPC (BM.HPC2.36)
    *   6,4 TB de armazenamento NVME SSD local por nó
    *   36 núcleos por nó
    *   384 GB de memória por nó

### **Infraestrutura Opcional**

### Armazenamento

Além do armazenamento SSD NVME que vem com a configuração HPC, você também pode anexar volumes em blocos em 32k IOPS por volume, com o suporte do SLA de melhor desempenho da Oracle. Se você estiver usando nossas soluções para iniciar a infraestrutura, um compartilhamento nfs será instalado por padrão no armazenamento SSD NVME em /mnt. Você também pode instalar seu próprio sistema de arquivos paralelo no topo do armazenamento SSD NVME ou do armazenamento em blocos, dependendo dos seus requisitos de desempenho.

### Nó do Visualizador

Você pode criar um nó visualizador, como uma VM GPU ou uma máquina BM, dependendo dos seus requisitos. Este nó do visualizador pode ser seu bastion host ou pode ser separado. Dependendo dos requisitos de segurança da carga de trabalho, o nó do visualizador pode ser colocado na sub-rede privada ou pública.

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020