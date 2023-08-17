# LS-DYNA 설치

## 소개

이 연습에서는 LS-DYNA를 설치해 봅니다.

예상 실습 시간: 25분

## 작업 1: 바이너리 다운로드

[LSTC 웹 사이트](http://www.lstc.com/download/ls-dyna)에서 LS-DYNA 바이너리를 다운로드하거나 scp를 사용하여 시스템에 푸시할 수 있습니다.

MPI용으로 만들어지고 RedHat Ent Srv 5.4용으로 컴파일된 버전을 가져옵니다. 조사 결과에 따르면 IntelMPI는 OCI에서 Platform MPI보다 빠르게 수행됩니다(ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1).tar.gz).

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

또 다른 가능성은 설치 프로그램을 오브젝트 스토리지에 업로드하는 것입니다.

1.  콘솔의 주 메뉴에서 Object Storage를 선택합니다.
2.  오른쪽 상단에 있는 올바른 영역 선택
3.  왼쪽에 있는 올바른 구획 선택
4.  아직 생성되지 않은 버킷 생성
5.  버킷에서 업로드 객체를 선택하고 설치 프로그램의 경로를 지정합니다.
6.  Installer 객체의 오른쪽에 있는 3개의 점을 선택하고 Create Pre-Authenticated Request를 선택합니다.
7.  URL이 손실된 경우 다시 가져올 수 없지만 새 사전 인증된 요청을 재생성할 수 있습니다.

설치 프로그램 양식 객체 스토리지 다운로드

    <copy>
        wget PAR_URL
    </copy>
    

버전에 따라 설치 프로그램의 tar 풀기 또는 압축 풀기

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## 작업 2: LS-DYNA 설치

1.  공유 위치에서 이진의 압축을 풉니다. 기본적으로 HPC 클러스터에는 모든 컴퓨트 노드에 NFS 공유 또는 Gluster 공유가 마운트되어 있습니다.
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## 작업 3: MPI 라이브러리 설치

**Intel MPI 2018**

모든 노드에서 이 명령을 실행합니다.

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**플랫폼 MPI**

해당 라이브러리를 설치합니다.

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

IBM 웹 사이트에서 tar 파일을 다운로드하고 다음을 실행합니다.

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

그런 다음 화면의 지침을 따르십시오. 공유 드라이브에 플랫폼을 설치하는 경우 모든 컴퓨트 노드에서 해당 플랫폼에 액세스할 수 있습니다.

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월