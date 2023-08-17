# Ejecución de STAR-CCM+

## Introducción

En esta práctica, ejecutará STAR-CCM+.

Tiempo estimado de laboratorio: 10 minutos

## Tarea 1: Ejecución de STAR-CCM+

1.  La ejecución de Star-CCM+ es bastante sencilla: puede iniciar la GUI si tiene una sesión de VNC iniciada con
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  Para especificar el host en el que debe ejecutarse, debe crear un archivo de máquina. Puede generarlo como sigue o manualmente. El formato es nombre de host:corenumber.
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  Para ejecutar en varios nodos, coloque model.sim en la unidad nfs-share (Ex:/mnt/nfs-share/work/) y reemplace CORENUMBER y PODKEY.
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## Tarea 2: Implementaciones de MPI y RDMA

El rendimiento puede variar en función del MPI que esté utilizando. 3 son compatibles con Star-CCM+ listos para usar.

*   IBM Platform MPI: plataforma predeterminada o de indicadores
*   MPI abierto: Indicador intelectual
*   Intel MPI: Indicador openmpi3 Para especificar opciones, puede utilizar el indicador -mppflags al utilizar OCI RDMA en una red de cluster, deberá especificar estas opciones:

1.  **OpenMPI**
    
    Para RDMA:
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    Además, en lugar de desactivar el hyperthreading, también puede forzar a MPI a fijarlo en los primeros 36 núcleos:
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    Para RDMA:
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    Para RDMA:
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    Para mejores actuaciones:
    
        -prot -aff:automatic:bandwidth
        
    
    Para fijar en los primeros 36 hilos:
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020