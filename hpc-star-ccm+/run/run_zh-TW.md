# 正在執行 STAR-CCM+

## 簡介

在此實驗室中，您將執行 STAR-CCM+。

預估實驗室時間：10 分鐘

## 作業 1：執行 STAR-CCM+

1.  執行 Star- CCM+ 很簡單：如果您有 VNC 工作階段開始，您可以啟動使用者介面 。
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  若要指定需要執行的主機，您需要建立一個機器檔案。您可以依下列方式產生，或手動產生。格式為 hostname:corenumber。
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  若要在多個節點上執行，請將 model.sim 置於 nfs 共用磁碟機 (Ex:/mnt/nfs-share/work/)，並取代 CORENUMBER 和 PODKEY。
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## 工作 2:MPI 實作和 RDMA

視您使用的 MPI 而定，執行效果真的會有所不同。方塊中的 Star-CCM+ 支援 3。

*   IBM 平台 MPI：預設或旗標平台
*   未結 MPI：旗標 intel
*   Intel MPI：旗標 openmpi3 若要指定選項，您可以使用 -mppflags 在叢集網路上使用 OCI RDMA 時，您必須指定下列選項：

1.  **OpenMPI**
    
    針對 RDMA：
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    此外，您也可以強制 MPI 將它固定在前 36 個核心上，而非停用超繫線功能：
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    針對 RDMA：
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    針對 RDMA：
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    更佳表現：
    
        -prot -aff:automatic:bandwidth
        
    
    如果要釘選前 36 個執行緒：
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月