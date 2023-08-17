# Executando Projetos OpenFOAM em Clusters HPC e VMs Padrão no OCI

## Introdução

Este runbook foi projetado para ajudar na avaliação do Software CFD OpenFOAM no Oracle Cloud Infrastructure. Ele faz download e configura automaticamente o OpenFOAM. OpenFOAM é o software CFD de código aberto gratuito lançado e desenvolvido principalmente por OpenCFD Ltd desde 2004. Tem uma grande base de usuários na maioria das áreas de engenharia e ciência, tanto de organizações comerciais quanto acadêmicas. OpenFOAM tem uma ampla gama de recursos para resolver qualquer coisa, desde fluxos de fluidos complexos envolvendo reações químicas, turbulência e transferência de calor, para acústica, mecânica sólida e eletromagnetica.

### Sobre a Computação de Alto Desempenho (HPC)

HPC, ou supercomputação, é como computação diária, apenas mais poderosa. É uma maneira de processar grandes volumes de dados em velocidades muito altas usando vários computadores e dispositivos de armazenamento como um tecido coeso. A HPC permite explorar e encontrar respostas para alguns dos maiores problemas do mundo em ciência, engenharia e negócios.

Uma rede de clusters é um pool de instâncias de computação de alto desempenho (HPC) conectadas a uma rede de alta largura de banda e latência ultrabaixa. Cada nó do cluster é uma máquina bare metal localizada próxima aos outros nós. Uma rede RDMA (remote direct memory access) entre nós fornece latência tão baixa quanto microssegundos com um único dígito, comparável aos clusters HPC locais.

A Computação de Alto Desempenho é oferecida no Oracle Cloud Infrastructure, nas regiões da OCI.

Instância de Computação de Alto Desempenho disponível no Oracle Marketplace Image e BM.HPC2.36 no OCI.

O rack de Computação de Alto Desempenho na Imagem do Oracle Marketplace inclui nós de cluster HPC, rede de clusters e compartilhamento NFS.

Os nós de computação são conectados por meio da rede de clusters, que oferece acesso de armazenamento baseado em RDMA aos nós de computação.

Atualmente, há suporte para um único BM por nó de computação. Ele permite o acesso raiz para os clientes durante a proteção de hardware e rede, os nós de computação são virtualizados usando o BM.HPC2.36.

_Horário Estimado_: 2 horas

### Objetivos

*   Execute projetos OpenFOAM usando instâncias HPC interconectadas por meio de RDMA (Remote Direct Memory Access)
    
*   Execute projetos OpenFOAM usando VMs (Máquinas Virtuais) padrão regulares na Oracle Cloud Infrastructure (OCI)
    

### Pré-requisitos

*   Uma conta do Oracle Cloud Infrastructure com privilégios para criar um compartimento e uma pilha.
    
*   Familiaridade com os recursos da Console do OCI e do Oracle Cloud (ou seja, Redes Virtuais na Nuvem, Computação e Armazenamento) é útil.
    
*   Suposição de que você concluiu o Exame OCI Cloud Architect Associate.
    

## Sobre este Workshop

*   Executar projetos OpenFOAM usando Cluster HPC
    
*   Execute projetos OpenFOAM usando VM Padrão no OCI
    

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Última Atualização por/Data** - Harrison Dvoor, janeiro de 2021