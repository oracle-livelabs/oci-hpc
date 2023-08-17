# 运行 STAR-CCM+

## 简介

在本练习中，您将运行 STAR-CCM+。

估计实验室时间：10 分钟

## 任务 1：运行 STAR-CCM+

1.  运行 Star-CCM+ 非常简单：如果您具有以以下方式启动的 VNC 会话，则可以启动 GUI
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  要指定需要运行的主机，需要创建计算机文件。可以按以下方式生成，也可以手动生成。格式为 hostname:corenumber。
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  要在多个节点上运行，请将 model.sim 放在 nfs 共享驱动器 (Ex:/mnt/nfs-share/work/) 上，并替换 CORENUMBER 和 PODKEY。
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## 任务 2:MPI 实施和 RDMA

根据您正在使用的 MPI，性能可能会有所不同。3 由 Star-CCM+ 现成支持。

*   IBM Platform MPI：默认或标志平台
*   打开 MPI：标志智能
*   Intel MPI：标志 openmpi3 要指定选项，可以在群集网络上使用 OCI RDMA 时使用标志 -mppflags，您需要指定以下选项：

1.  **OpenMPI**
    
    对于 RDMA：
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    此外，您还可以强制 MPI 将其固定在前 36 个核心上，而不是禁用超线程：
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    对于 RDMA：
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    对于 RDMA：
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    为了获得更好的表现：
    
        -prot -aff:automatic:bandwidth
        
    
    要固定前 36 个线程：
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月