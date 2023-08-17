# LS-DYNA 실행

## 소개

이 연습에서는 LS-DYNA를 실행합니다.

예상 실습 시간: 10분

## LS-DYNA 실행

LS-DYNA는 매우 간단합니다. 실행해야 하는 호스트를 지정하려면 machinefile을 생성해야 합니다. 다음과 같이 생성하거나 수동으로 생성할 수 있습니다. 형식은 플랫폼과 IntelMPI 모두에 대한 hostname:corenumber입니다.

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

일부 실행 파라미터는 파라미터 파일 pfile로 지정할 수 있습니다.

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

이 특정 pfile은 LSDyna에 많은 정보를 디스크에 덤프하지 않도록 지시합니다. 메모리를 사용하여 로컬 파일을 저장하고 전역 파일을 /mnt/nfs-share/benchmark/one\_global\_dir에 저장합니다.

메모리에 맞지 않을 경우 로컬 파일을 저장할 ANother 위치는 시스템에서 로컬 NVMe를 사용하여 해당 파일을 저장하는 /mnt/localdisk/tmp입니다.

여러 노드에서 실행하려면 모델을 공유 드라이브(Ex:/mnt/nfs-share/work/)에 배치합니다. 여기에 제공된 예는 3차 모델을 실행하는 것입니다. 설치 프로그램과 같은 오브젝트 스토리지에 추가하고 다운로드하거나 컴퓨터에 스크랩할 수 있습니다.

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

MPI가 올바르게 실행되도록 올바른 변수를 모두 설정했는지 확인합니다. Intel MPI에 대해 다음 명령을 사용하여 실행합니다(모델 이름 및 코어 번호 변경).

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

플랫폼 MPI의 경우:

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## 특수 사례

일부 모델의 경우 Douple Precision 실행 파일은 모델을 분해하는 데 충분하지 않습니다. 실행 파일의 이중 전체 자릿수 버전을 사용하거나 이중 자릿수로 분해할 수 있으며 단일 자릿수로 실행을 수행하여 속도를 높일 수 있습니다.

분해의 경우 명령행에 ncycles=2를 추가하고 pfile에 이 부분을 추가합니다.

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

모델은 분해라는 파일에 분해되어 모델 디렉토리에 저장됩니다. 두번째 실행 중 LS-Dyna는 이 파일을 보고 분해를 재실행하지 않습니다.

단일 자릿수로 실행되는 경우 pfile에 동일한 명령을 사용할 수 있습니다.

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월