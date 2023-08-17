# Executando STAR-CCM+

## Introdução

Neste laboratório, você executará STAR-CCM+.

Tempo Estimado de Laboratório: 10 minutos

## Tarefa 1: Executando STAR-CCM+

1.  Executar Star-CCM+ é bastante simples: você pode iniciar a GUI se tiver uma sessão VNC iniciada com
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  Para especificar o host no qual você precisa executar, é necessário criar um arquivo de máquina. Você pode gerá-lo como segue ou manualmente. O formato é hostname:corenumber.
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  Para executar em vários nós, coloque model.sim na unidade nfs-share (Ex:/mnt/nfs-share/work/) e substitua CORENUMBER e PODKEY.
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## Tarefa 2: Implementações de IPM e RDMA

Os desempenhos podem realmente diferir dependendo do MPI que você está usando. 3 são suportados por Star-CCM+ prontos para uso.

*   IBM Platform MPI: Plataforma padrão ou de sinalização
*   Abrir MPI: Informações do indicador
*   Intel MPI: Flag openmpi3 Para especificar opções, você pode usar o flag -mppflags ao usar o OCI RDMA em uma Rede de Clusters, será necessário especificar estas opções:

1.  **OpenMPI**
    
    Para RDMA:
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    Além disso, em vez de desativar a hiperthreading, você também pode forçar o MPI a fixá-lo nos primeiros 36 núcleos:
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    Para RDMA:
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    Para RDMA:
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    Para melhores desempenhos:
    
        -prot -aff:automatic:bandwidth
        
    
    Para fixar nos primeiros 36 threads:
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020