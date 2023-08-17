# Exécution de STAR-CCM+

## Présentation

Dans cet exercice, vous allez exécuter STAR-CCM+.

Temps de laboratoire estimé : 10 minutes

## Tâche 1 : exécution de STAR-CCM+

1.  L'exécution de Star-CCM+ est assez simple : vous pouvez démarrer l'interface graphique si vous avez une session VNC démarrée avec
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  Pour spécifier l'hôte sur lequel vous devez exécuter, vous devez créer un fichier machine. Vous pouvez la générer comme suit, ou manuellement. Le format est hostname:corenumber.
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  Pour s'exécuter sur plusieurs noeuds, placez model.sim sur l'unité de partage NFS (Ex:/mnt/nfs-share/work/) et remplacez CORENUMBER et PODKEY.
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## Tâche 2 : Implémentations MPI et RDMA

Les performances peuvent vraiment varier en fonction du MPI que vous utilisez. 3 sont pris en charge par Star-CCM+ prêt à l'emploi.

*   IBM Platform MPI : plate-forme par défaut ou de drapeau
*   Ouvrir MPI : Signaler intel
*   Intel MPI : Indicateur openmpi3 Pour spécifier des options, vous pouvez utiliser l'indicateur -mppflags lors de l'utilisation d'OCI RDMA sur un réseau de cluster, vous devez indiquer les options suivantes :

1.  **OpenMPI**
    
    Pour RDMA :
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    En outre, au lieu de désactiver l'hyperthreading, vous pouvez également forcer le MPI à le fixer sur les 36 premiers coeurs :
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    Pour RDMA :
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    Pour RDMA :
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    Pour de meilleures performances :
    
        -prot -aff:automatic:bandwidth
        
    
    Pour épingler les 36 premiers threads :
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020