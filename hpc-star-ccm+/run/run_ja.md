# STAR-CCM+の実行

## 概要

この演習では、STAR-CCM+を実行します。

予想ラボ時間: 10分

## タスク1: STAR-CCM+の実行

1.  Star-CCM+の実行は非常に簡単です。VNCセッションが開始されている場合は、GUIを起動できます。
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  実行する必要があるホストを指定するには、マシンファイルを作成する必要があります。次のように生成することも、手動で生成することもできます。書式はhostname:corenumberです。
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  複数のノードで実行するには、model.simをnfs-shareドライブ(Ex:/mnt/nfs-share/work/)に配置し、CORENUMBERおよびPODKEYを置き換えます。
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## タスク2: MPI実装とRDMA

パフォーマンスは、使用しているMPIによって大きく異なる場合があります。3は、初期状態でStar-CCM+でサポートされています。

*   IBM Platform MPI: デフォルトまたはフラグプラットフォーム
*   オープンMPI: フラグインテル
*   Intel MPI: フラグ openmpi3オプションを指定するには、フラグ-mppflagsを使用できます クラスタ・ネットワークでOCI RDMAを使用する場合、次のオプションを指定する必要があります。

1.  **OpenMPI**
    
    RDMAの場合:
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    さらに、ハイパースレッドを無効にする代わりに、MPIを最初の36コアに固定するように強制することもできます。
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    RDMAの場合:
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    RDMAの場合:
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    パフォーマンスを向上させるには:
    
        -prot -aff:automatic:bandwidth
        
    
    最初の36スレッドに固定するには:
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月