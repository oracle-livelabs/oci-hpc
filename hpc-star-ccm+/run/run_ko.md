# STAR-CCM+ 실행

## 소개

이 연습에서는 STAR-CCM+를 실행합니다.

예상 실습 시간: 10분

## 작업 1: STAR-CCM+ 실행

1.  Star-CCM+ 실행은 매우 간단합니다. VNC 세션을 시작한 경우 GUI를 시작할 수 있습니다.
    
            /mnt/share/install/version/STAR-CCM+version/star/bin/starccm+
        
2.  실행해야 하는 호스트를 지정하려면 machinefile을 생성해야 합니다. 다음과 같이 생성하거나 수동으로 생성할 수 있습니다. 형식은 hostname:corenumber입니다.
    
            sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
        
3.  여러 노드에서 실행하려면 nfs 공유 드라이브(Ex:/mnt/nfs-share/work/)에 model.sim를 배치하고 CORENUMBER 및 PODKEY를 대체합니다.
    
            /mnt/nfs-share/install/15.02.009/STAR-CCM+15.02.009/star/bin/starccm+ -batch -power\\ 
            -licpath 1999@flex.cd-adapco.com -podkey PODKEY -np CORENUMBER 
            -machinefile machinefile /mnt/nfs-share/work/model.sim
        

## 작업 2: MPI 구현 및 RDMA

사용 중인 MPI에 따라 성능이 달라질 수 있습니다. 3은 Star-CCM+에서 즉시 지원됩니다.

*   IBM Platform MPI: 기본 또는 플래그 플랫폼
*   MPI 열기: 플래그 인텔
*   Intel MPI: 플래그 openmpi3 옵션을 지정하려면 -mppflags 플래그를 사용할 수 있습니다. 클러스터 네트워크에서 OCI RDMA를 사용하는 경우 다음 옵션을 지정해야 합니다.

1.  **OpenMPI**
    
    RDMA의 경우:
    
            -mca btl self -x UCX_TLS=rc,self,sm -x HCOLL_ENABLE_MCAST_ALL=0 -mca coll_hcoll_enable 0 -x UCX_IB_TRAFFIC_CLASS=105 -x UCX_IB_GID_INDEX=3 
        
        
    
    또한 하이퍼 스레딩을 사용 안함으로 설정하는 대신 MPI가 처음 36개 코어에 고정하도록 강제할 수도 있습니다.
    
            --cpu-set 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        
2.  **IntelMPI**
    
    RDMA의 경우:
    
        -mppflags "-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0"
        Additionaly, instead of disabling hyper-threading, you can also force the MPI to pin it on the first 36 cores:
        -genv I_MPI_PIN_PROCESSOR_LIST=0-33 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71
        
3.  **PlatformMPI**
    
    RDMA의 경우:
    
        -mppflags "-intra=shm -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL"
        
        
    
    더 나은 성능을 위해:
    
        -prot -aff:automatic:bandwidth
        
    
    처음 36개 스레드를 고정하려면
    
        -cpu_bind=MAP_CPU:0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19 ,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35
        

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월