# STAR-CCM+ wird ausgeführt

## Einführung

In dieser Übung führen Sie STAR-CCM+ aus.

Geschätzte Laborzeit: 10 Minuten

## Aufgabe 1: STAR-CCM+ wird ausgeführt

1.  Die Ausführung von Star-CCM+ ist ziemlich einfach: Sie können die GUI starten, wenn Sie eine VNC-Session gestartet haben mit
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  Um den Host anzugeben, auf dem Sie ausführen müssen, müssen Sie eine Rechnerdatei erstellen. Sie können ihn wie folgt oder manuell generieren. Das Format lautet hostname:corenumber.
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  Um auf mehreren Knoten auszuführen, platzieren Sie model.sim auf dem nfs-share-Laufwerk (Ex:/mnt/nfs-share/work/), und ersetzen Sie CORENUMBER und PODKEY.
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## Aufgabe 2: MPI-Implementierungen und RDMA

Die Leistungen können sich je nach verwendeter MPI wirklich unterscheiden. 3 werden von Star-CCM+ unterstützt.

*   IBM Platform MPI: Standard- oder Flag-Plattform
*   MPI öffnen: Kennzeichen intel
*   Intel MPI: Flag openmpi3 Um Optionen anzugeben, können Sie das Flag -mppflags verwenden, wenn Sie OCI RDMA in einem Clusternetzwerk verwenden, müssen Sie die folgenden Optionen angeben:

1.  **OpenMPI**
    
    Für RDMA:
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    Außerdem können Sie statt der Deaktivierung des Hyperthreadings den MPI auch zwingen, ihn auf den ersten 36 Cores zu pinnen:
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    Für RDMA:
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    Für RDMA:
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    Für bessere Leistungen:
    
        -prot -aff:automatic:bandwidth
        
    
    So pinnen Sie auf den ersten 36 Threads:
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020