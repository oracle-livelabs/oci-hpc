# Instalando STAR-CCM+

## Introdução

Neste laboratório, você instalará STAR-CCM+.

Tempo Estimado de Laboratório: 25 minutos

## Tarefa 1: Adicionando bibliotecas específicas

_**Se você usou o Cluster Pronto para CFD do marketplace, essa etapa não será necessária**_

1.  Há algumas bibliotecas que precisam ser adicionadas à imagem do Oracle Linux em todos os nós de computação.
    
            sudo yum -y install libSM libX11 libXext libXt
        

## Tarefa 2: Fazer download dos binários

Você pode baixar o instalador STAR-CCM+ do site da Siemens PLM ou empurrá-lo para sua máquina usando scp.

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

Outra possibilidade é fazer upload do instalador no armazenamento de objetos.

1.  No menu principal da console, selecione Object Storage.
2.  Escolha a região correta na parte superior direita
3.  Selecione o compartimento correto no lado esquerdo
4.  Crie um bucket se você ainda não tiver um criado
5.  No bucket, selecione o objeto de upload e especifique o caminho do instalador.
6.  Selecione os 3 pontos no lado direito do objeto do instalador e selecione Criar Solicitação Pré-Autenticada
7.  Se você perder o URL, não poderá recuperá-lo, mas poderá gerar novamente uma nova Solicitação Pré-autenticada

Faça download do armazenamento de objetos do formulário do instalador com

        wget PAR_URL
    

Descompacte ou descompacte o instalador dependendo da sua versão

        tar -xf installer.tgz
        unzip installer.tgz
    

## Tarefa 3: Instalar

1.  Inicie o instalador em um local compartilhado. Por padrão, um cluster HPC tem um compartilhamento NFS montado em todos os nós de computação.
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020