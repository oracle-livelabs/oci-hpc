# Executando LS-DYNA

## Introdução

Neste laboratório, você executará LS-DYNA.

Tempo Estimado de Laboratório: 10 minutos

## Executando LS-DYNA

Correr LS-DYNA é bem simples. Para especificar o host no qual você precisa executar, é necessário criar um arquivo de máquina. Você pode gerá-lo como segue ou manualmente. O formato é hostname:corenumber para Plataforma e IntelMPI.

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

Alguns parâmetros de execução podem ser especificados por um arquivo de parâmetros: pfile

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

Este pfile específico informa LSDyna para não fazer dump em muitas informações do disco. Usa memória para armazenar arquivos locais e armazenar arquivos globais em /mnt/nfs-share/benchmark/one\_global\_dir.

O local ANother para armazenar arquivos locais se ele não couber na memória é /mnt/localdisk/tmp para usar o NVMe local na máquina para armazenar esses arquivos.

Para executar em vários nós, coloque o modelo na unidade de compartilhamento (Ex:/mnt/nfs-share/work/). O exemplo fornecido aqui é executar o modelo de 3 carros. Você pode adicioná-lo ao armazenamento de objetos como o instalador e baixá-lo ou digitá-lo na máquina.

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

Certifique-se de definir todas as variáveis certas para que o mpi seja executado corretamente. Execute-o com o seguinte comando para Intel MPI (alterar o nome do modelo e o número do núcleo):

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

Para MPI da plataforma:

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## Caso Especial

Para algum modelo, os executáveis da Douple Precision não serão suficientes para fazer a decomposição do modelo. Você pode usar a versão de precisão dupla do executável ou pode fazer a decomposição em precisão dupla e ainda executar em precisão única para obter velocidade.

Para a decomposição, adicione ncycles=2 à linha de comando e adicione esta parte ao pfile:

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

O modelo será decomposto em um arquivo chamado decomposição e armazenado no diretório do modelo. Durante a segunda execução, LS-Dyna verá esse arquivo e não redo a decomposição.

Para a execução em Precisão única, você pode usar os mesmos comandos no pfile.

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020