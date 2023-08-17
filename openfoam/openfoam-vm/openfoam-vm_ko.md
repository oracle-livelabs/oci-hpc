# OCI의 표준 VM에서 OpenFoam 프로젝트 실행

## 소개

다음 작업은 Oracle Cloud Infrastructure의 OpenFOAM CFD 소프트웨어 평가를 지원하기 위해 설계되었습니다. OpenFOAM를 자동으로 다운로드하고 구성하려면 작업을 완료합니다. OpenFOAM는 2004년부터 OpenCFD Ltd.가 주로 출시 및 개발한 무료 오픈 소스 CFD 소프트웨어입니다. 그것은 상업 및 학술 기관에서 엔지니어링 및 과학의 대부분의 영역에 걸쳐 큰 사용자 기반을 가지고 있습니다. OpenFOAM에는 화학 반응, 난류 및 열 전달과 관련된 복잡한 유체 흐름에서 음향, 고체 역학 및 전자기학에 이르기까지 다양한 기능이 있습니다.

기본 VM.Standard 2.1 구성에서 OpenFoam 설치 과정을 안내하지만 실제 배치의 경우 BM.HPC2.36과 같은 더 큰 구성이 적합합니다. 일부 고성능 구성은 특정 지역 및 가용성 도메인에서만 사용할 수 있습니다.

두 개의 노드(공용 서브넷의 클러스터에 대한 헤드노드 및 전용 서브넷의 작업자 컴퓨트 노드)를 생성합니다. 워커 노드에 액세스하기 위해 먼저 헤드노드를 생성한 다음 헤드노드에 SSH 키를 생성하고 워커 노드를 생성할 때 해당 공용 키를 사용합니다.

**문제를 기록**하려면 [여기](https://github.com/oracle/learning-library/issues/new)를 눌러 Github Oracle 저장소 문제 제출 양식으로 이동하십시오.

예상 시간: 60분

### 목표

개발자, 데이터 엔지니어,

1.  고성능 컴퓨팅 인스턴스를 수동으로 배치합니다.
    
2.  클러스터 네트워크 생성
    
3.  네트워크 파일 시스템 만들기
    
4.  VNC 설정
    
5.  OpenFoam 및 Paraview 설치
    

### 필수 조건

*   인스턴스 VM 표준 2.1 또는 BM.HPC2.36 구성을 생성할 수 있는 권한이 있는 Oracle Cloud Infrastructure 계정입니다.

### 기반 구조 용어

*   작업자 노드: 계산 시뮬레이션 또는 기타 엔지니어링 작업 로드의 작업 로드를 실행할 수 있는 처리 능력을 제공하는 HPC 컴퓨트 인스턴스입니다. 이 데모 컴퓨트 구성에서 BM.HPC2.36 노드는 워커 노드입니다.
    
*   헤드 노드: 작업자 노드에서 실행되도록 모든 계산 작업을 스케줄링하고 제출할 수 있는 컴퓨트 인스턴스입니다. 여러 번 헤드 노드와 배스천 노드가 동일한 시스템이 될 수 있습니다. 이 데모에서는 배스천 노드를 프로비전하는 중입니다.
    
*   배스천 노드: 공용 IP를 사용하는 컴퓨트 인스턴스는 일반적으로 전용 네트워크에 있는 워커 노드에 접속하기 위한 시작점 역할을 합니다.
    
*   RoCE network: RDMA over Converged Ethernet(RoCE)은 이더넷 네트워크를 통해 RDMA(Remote Direct Memory Access)를 허용하는 네트워크 프로토콜입니다.
    

## 작업 1: Oracle 가상 클라우드 네트워크 프로비전

1.  인스턴스를 생성하기 전에 가상 클라우드 네트워크를 구성해야 합니다. 왼쪽 위에 있는 메뉴를 선택한 다음 **네트워킹** 및 **가상 클라우드 네트워크**를 선택합니다.
    
    ![](./images/vcn.png " ")
    
    ![](./images/create_vcn.png " ")
    
    ![](./images/vcn_content.png " ")
    
2.  다음 페이지에서 VCN에 대해 다음을 선택합니다.
    
    *   이름
        
    *   구획
        
    *   CIDR 블록(예: 10.0.0.0/16)
        
3.  아래로 스크롤한 후 Create VCN을 누릅니다.
    
4.  서브넷 생성
    
    1.  공용 서브넷
        
        *   이름: 예: hpc\_public
            
        *   서브넷 유형: 지역별
            
        *   CIDR 블록: 10.0.0.0/24
            
        *   경로 테이블: "기본 경로 테이블"
            
        *   서브넷 액세스: 공용 서브넷
            
        *   보안 목록: "Default Security List"
            
    2.  전용 서브넷
        
        *   이름: hpc\_private
            
        *   서브넷 유형: 지역별
            
        *   CIDR 블록: 10.0.3.0/24
            
        *   경로 테이블: 이전 단계에서 생성된 경로 테이블 선택
            
        *   서브넷 액세스: 전용 서브넷
            
        *   보안 목록: 이전 단계에서 생성된 보안 목록 선택
            
5.  **서브넷 생성**을 누릅니다.
    
    ![](./images/create_subnet.png " ")
    
    ![](./images/create_subnet_content.png " ")
    
6.  생성한 `hpc_vcn`을 누르고 페이지 왼쪽의 **리소스** 메뉴에서 **인터넷 게이트웨이**를 선택하고 인터넷 게이트웨이를 생성합니다.
    
    ![](./images/create_IG.png " ")
    
    ![](./images/create_IG_content.png " ")
    
    > **참고:** 인터넷 게이트웨이가 생성되므로 경로 테이블과 연관되어야 합니다. 이 경우 공용 서브넷에 기본 경로 테이블이 사용되므로 인터넷 게이트웨이가 해당 경로 테이블과 연관되어야 합니다.
    
7.  라우트 테이블에 라우트 규칙을 추가합니다. 페이지 왼쪽의 **리소스** 메뉴에서 **`hpc_vcn`의 기본 경로 테이블**을 선택하고 **경로 규칙 추가**를 누릅니다.
    
    *   대상 유형: 인터넷 게이트웨이
        
    *   대상 CIDR 블록: 0.0.0.0/0
        
    *   구획의 대상 인터넷 게이트웨이: 사용자가 생성한 인터넷 게이트웨이
        
    
    ![](./images/route_table.png " ")
    
    ![](./images/route_rules.png " ")
    

## 작업 2: 클러스터 노드 만들기

이 작업을 위해 두 개의 노드, 즉 공용 서브넷의 클러스터에 대한 **헤드노드**와 전용 서브넷에 **작업자 컴퓨트 노드**를 생성합니다. 워커 노드에 액세스하기 위해 먼저 헤드노드를 생성한 다음 헤드노드에 SSH 키를 생성하고 워커 노드를 생성할 때 해당 공용 키를 사용합니다.

> **참고:** 이 작업에서는 기본 VM.Standard2.1 구성만 사용하지만 실제 배치의 경우 BM.HPC2.36과 같은 더 큰 구성이 적합합니다. 일부 고성능 구성은 특정 지역 및 가용성 도메인에서만 사용할 수 있습니다.

1.  헤드노드 생성
    
    *   이름: 예: hpc\_head
        
    *   이미지 또는 운영 체제:최신 버전 Oracle Linux(기본값)
        
    *   가용성 도메인: VM.Standard 2.1 구성에 사용 가능한 도메인
        
    *   인스턴스 구성:
        
        *   VM.Standard 2.1
            
        *   다른 모양 (가능한 경우에 선호되는 BM.HPC2.36)
            
    *   가상 클라우드 네트워크: 이전에 생성된 VCN
        
    *   서브넷: 이전에 생성된 공용 서브넷
        
    *   SSH 키: 공용 키 파일 연결
        
    
    1.  왼쪽 상단 메뉴에서 Compute를 선택하고 Instance를 생성합니다.
        
        ![](./images/compute.png " ")
        
        ![](./images/compute_bm.png " ")
        
        ![](./images/compute_vm.png " ")
        
    2.  헤드노드에 SSH를 사용하고 클러스터가 통신할 특정 SSH 키를 생성합니다.
        
            $ <copy>ssh -i <ssh_key> opc@<headnode_ip_address></copy>
            
            $ <copy>ssh-keygen</copy>
            
        
        > **주:** ssh 키 파일 위치(/home/opc/.ssh/id\_rsa)를 변경하지 말고 문장암호에 대해 묻는 메시지가 표시되면 Enter 키를 누릅니다(두 번).
        
        전체 문자열을 실행하고 복사합니다. 이 전체 문자열은 워커 노드 생성에 사용됩니다.
        
            $ <copy>cat ~/.ssh/id_rsa.pub</copy>
            
2.  작업자 노드 생성
    
    1.  왼쪽 상단 메뉴에서 Compute를 선택하고 다음 세부 정보로 instance를 생성합니다.
        
        *   이름: 예: hpc\_worker1
            
        *   이미지 또는 운영 체제:최신 버전 Oracle Linux(기본값)
            
        *   가용성 도메인: VM.Standard 2.1 구성에 사용 가능한 도메인
            
        *   인스턴스 구성:
            
            *   VM.Standard 2.1
                
            *   다른 모양 (가능한 경우에 선호되는 BM.HPC2.36)
                
        *   가상 클라우드 네트워크: 이전에 생성된 VCN
            
        *   서브넷: 이전에 생성된 전용 서브넷
            
        *   SSH 키: 위의 한 단계에서 복사된 공용 키 문자열입니다.
            
    2.  SSH를 작업자 노드로 설정합니다. 헤드 노드에 로그인한 콘솔로 돌아가서 헤드 노드에서 전용 IP 주소 및 SSH를 작업자 노드로 가져옵니다.
        
            $ <copy>ssh opc@10.x.x.x</copy>
            

## 태스크 3: NAT 게이트웨이 설정

> **주:** 워커 노드 전용입니다.

1.  워커 노드를 선택하고 왼쪽의 **리소스** 메뉴에서 **연결된 VNIC**를 누릅니다.
    
2.  **Edit VNIC**를 선택합니다.
    
3.  **Skip Source/ Destination Check**가 선택된 경우 선택을 취소하고 **Update VNICs**를 누릅니다.
    
    ![](./images/nat_gateway.png " ")
    

## 작업 4: 드라이브 마운트

> **주:** 노드 구성에 NVMe이 연결된 경우에만(BM.HPC2.36에는 VM.Standard2.1이 아닌 1개가 있음), HPC 시스템에는 로컬 NVMe 스토리지가 있지만 기본적으로 마운트되지 않습니다. VM.Standard2.1을 사용하는 경우 5단계로 건너뜁니다.

1.  헤드노드에 SSH를 사용하고 아래 명령을 실행하십시오.
    
        $ <copy>lsblk</copy>
        
    
    드라이브는 왼쪽의 NAME과 함께 나열되어야 합니다(다른 경우 nvme0n1, 다음 명령에서 변경).
    
    헤드노드에 설치 및 모델이 포함된 공유 드라이브가 있습니다. 모든 다른 워커 노드 간에 공유됩니다. 또한 각 작업자 노드는 NVMe 드라이브에서 로컬로 실행할 드라이브를 마운트합니다. 이 예에서 공유 디렉토리는 500GB이지만 언제든지 변경할 수 있습니다.
    
2.  워커 노드에서 드라이브 분할(선택사항)
    
        $ <copy>sudo yum -y install gdisk</copy>
        
    
        $ <copy>sudo gdisk /dev/nvme0n1</copy>     $ > n    # Create new partition
        $ > 1    # Partition Number
        $ >      # Default start of the partition
        $ > +500G # Size of the shared partition
        $ > 8300  # Type = Linux File System
        $ > n     # Create new partition
        $ > 2     # Partition Number
        $ >       # Default start of the partition
        $ >       # Default end of the partition, to fill the whole drive
        
        $ > 8300  # Type = Linux File System
        
        $ > w      # Write to file
        $ > Y      # Accept Changes
        
3.  워커 노드의 드라이브 포맷
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1</copy>
        
4.  헤드노드의 분할 영역 형식 지정
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p1</copy>
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p2</copy>
        
5.  디렉토리 생성 및 드라이브 마운트
    
6.  Headnode(로컬 및 공유): 500G 분할 영역에 대한 마운트 디렉토리로 /mnt/share를 선택하고 큰 분할 영역에 대해서는 /mnt/local을 선택합니다.
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1p1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1p2 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        
7.  헤드노드(공유):
    
        $ <copy>sudo mkdir /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        
8.  작업자 노드: 전체 드라이브의 마운트 디렉토리로 /mnt/local를 선택합니다.
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        

## 작업 5: 네트워크 파일 시스템 만들기

1.  마운트 대상을 만듭니다. 왼쪽 상단 메뉴에서 **File Storage** 및 **Mount Target**을 누릅니다.
    
    *   새 마운트 대상 이름: 이름(예: openfoam\_fs)을 입력합니다.
        
    *   가상 클라우드 네트워크: 위에 생성된 VCN 선택
        
    *   서브넷: 공용 VCN 선택
        
2.  헤드노드는 공용 서브넷에 있으며, 방화벽을 작동하고 다음을 통해 예외사항을 추가합니다.  
    
        <copy>sudo firewall-cmd --permanent --zone=public --add-service=nfs</copy>
        <copy>sudo firewall-cmd --reload</copy>
        
3.  생성된 마운트 대상을 누른 다음 익스포트 경로를 누르고 마운트 명령을 누르고 다음과 같은 명령을 복사합니다.
    
        <copy>sudo yum install nfs-utils</copy>
        <copy>sudo mkdir -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        
4.  작업자 노드는 액세스를 제한하는 보안 목록이 있는 전용 서브넷에 있으므로 방화벽을 완전히 사용 안함으로 설정할 수 있습니다. 그런 다음 위와 같이 nfs-utils 도구를 설치하고 nfs를 마운트할 수 있습니다.
    
        <copy>sudo systemctl stop firewalld</copy>
        <copy>sudo yum -y install nfs-utils</copy>
        <copy>sudo mkdir  -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        

## 작업 6: OpenFOAM 설치

1.  **모든 작업자 노드 연결**  
    
    각 작업자 노드는 모든 작업자 노드와 통신할 수 있어야 합니다. SSH 통신은 작동하지만 모든 호스트가 알려진 호스트 파일에 없는 경우 대부분의 응용 프로그램에 문제가 있습니다. VCN에 주소가 있는 노드에 대해 알려진 호스트 검사를 사용 안함으로 설정하려면 다음 명령을 사용하여 비활성화할 수 있습니다. 서브넷에 다른 주소가 있는 경우 약간 수정해야 할 수 있습니다. 다음 코드 블록을 셸 스크립트에 넣고 스크립트를 실행합니다.
    
        <copy>for i in 0 1 2 3
        do
             echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
             echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done</copy>
        
2.  **machinelist 만들기**  
    
    헤드노드의 OpenFOAM는 사용 가능한 컴퓨트 노드를 자동으로 알지 못합니다. 사용 가능한 CPU 수와 함께 모든 노드의 전용(private) IP 주소인 `/mnt/share/machinelist.txt with`에 machinefile을 만들 수 있습니다. headnode도 포함되어야 합니다. 항목의 형식은 `private-ip-address cpu=number-of-cores`입니다.
    
        <copy>10.0.0.2 cpu=1
        10.0.3.2 cpu=1</copy>
        
3.  **헤드노드**
    
    소스에서 설치하고 다음 명령의 쓰레기 경로를 수정합니다. 이 예에는 기초 OpenFOAM 소스가 있습니다. ESI의 OpenFOAM도 테스트되었습니다. 서로 다른 컴퓨트 노드 간에 설치를 공유하려면 네트워크 파일 시스템에 설치합니다.
    
        <copy>$ sudo yum groupinstall -y 'Development Tools'
        $ sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        $ cd /mnt/share
        $ wget -O - http://dl.openfoam.org/source/7 | tar xvz
        $ wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        $ mv OpenFOAM-7-version-7 OpenFOAM-7
        $ mv ThirdParty-7-version-7 ThirdParty-7
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        $ echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc
        $ cd /mnt/share/OpenFOAM-7
        $ ./Allwmake -j</copy>
        
4.  **작업자 노드**  
    
        $ <copy>sudo yum -y install openmpi openmpi-devel
        $ cd /mnt/share
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        $ echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc</copy>
        

## 작업 7: 시뮬레이션 작업 로드 실행 및 출력 렌더링

1.  헤드노드에서 Paraview 패키지를 사용하여 출력을 렌더링하는 데 필요한 다음 명령을 실행합니다.
    
        $ <copy>sudo yum install -y mesa-libGLU
        $ cd /mnt/share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php >  file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  headnonde에서 다음 명령을 실행하여 VNC 서버를 설정합니다.  
    
        $ <copy>sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver</copy>
        
3.  워커 노드 중 하나에서 /mnt/share/work의 [스크립트](../scripts/motorbike_RDMA.tgz)를 사용하여 이 zip을 다운로드합니다. `tar -xf motorbike_RDMA.tgz`를 사용하여 파일 압축을 풉니다.
    
4.  작업 로드를 실행하기 전에 작성한 구조와 일치하도록 allrun 파일을 편집해야 합니다. 먼저 OpenFOAM 설치 프로그램 폴더에서 폴더를 이동합니다.
    
        $ <copy>model_drive=/mnt/share
        $ sudo mkdir $model_drive/work
        $ sudo chmod 777 $model_drive/work
        $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
        $ cd /mnt/share/work/motorBike/system</copy>
        
5.  system/decomposeParDict 파일을 편집하고 이 행 numberOfSubdomains 6, numberOfSubdomains 12 또는 필요한 프로세스 수를 변경합니다. 그런 다음 hierarchicalCoeffs 블록에서 n을 n(3 2 1)에서 n(4 3 1)으로 변경합니다. 이 3개의 값을 곱하면 numberOfSubdomains이 반환됩니다.
    
    1 VM.Standard2.1 작업자 노드의 구성으로 실행하려면 다음을 수행합니다.
    
        =========                 |
        \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
         \\    /   O peration     | Website:  https://openfoam.org
          \\  /    A nd           | Version:  7
           \\/     M anipulation  |
        \*---------------------------------------------------------------------------*/
        FoamFile
        {
             version     2.0;
             format      ascii;
             class       dictionary;
             object      decomposeParDict;
        }
        
        // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
        
        numberOfSubdomains 2;
        
        method          hierarchical;
        // method          ptscotch;
        
        simpleCoeffs
        {
             n               (4 1 1);
             delta           0.001;
        }
        
        hierarchicalCoeffs
        {
             n               (2 1 1);
             delta           0.001;
             order           xyz;
        }
        
        manualCoeffs
        {
             dataFile        "cellDecomposition";
        }
        
        // ************************************************************************* //
        
6.  그런 다음 /mnt/share/work/motorBike의 Allrun 파일을 다음과 같이 편집합니다.
    
        <copy>#!/bin/sh
        cd ${0%/*} || exit 1    # Run from this directory
        NP=$1
        install_drive=/mnt/share
        # Source tutorial run functions
        . $WM_PROJECT_DIR/bin/tools/RunFunctions
        
        # Copy motorbike surface from resources directory
        cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
        cp $install_drive/machinelist.txt hostfile
        
        runApplication surfaceFeatures
        
        runApplication blockMesh
        
        runApplication decomposePar -copyZero
        echo "Running snappyHexMesh"
        mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.snappyHexMesh
        ls -d processor* | xargs -I {} rm -rf ./{}/0
        ls -d processor* | xargs -I {} cp -r 0 ./{}/0
        echo "Running patchsummary"
        mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
        echo "Running potentialFoam"
        mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
        echo "Running simpleFoam"
        mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam
        
        runApplication reconstructParMesh -constant
        runApplication reconstructPar -latestTime
        
        foamToVTK
        touch motorbike.foam</copy>
        
7.  워커 노드에 있는지 확인하고 작업 로드를 실행합니다.
    
        $ <copy>ssh worker_node_IP
        $ cd /mnt/share/work/
        $ ./Allrun 2
        
        $ ./Allrun 2
        $ Cleaning /mnt/share/work case
        $ Mesh Dimensions: (40 16 16)
        $ Cores:36: 6, 6, 1
        $ Running surfaceFeatures on /mnt/share/work
        $ Running blockMesh on /mnt/share/work
        $ Running decomposePar on /mnt/share/work
        $ Running snappyHexMesh
        $ Running patchsummary
        $ Running potentialFoam
        $ Running simpleFoam
        $ Running reconstructParMesh on /mnt/share/work
        $ Running reconstructPar on /mnt/share/work
        219.95</copy>
        
8.  작업 로드가 성공적으로 완료되면 다음과 같이 시스템에서 VNC 클라이언트를 구성합니다. 배스천 서버 및 VNC 포트의 공용 IP 제공
    
    ![](./images/24-VNC_Connection.png " ")
    
9.  선택 사항으로, VNC 포트 5901을 열 수 없거나 이 포트에 대한 SSH 터널을 만들려는 보안상의 이유로 인해 허용되지 않는 경우 다음 명령을 사용하여 보안 목록에서 포트를 열지 않고 포트 5901에서 SSH 터널을 만듭니다.
    
10.  터미널 창에서 다음 명령을 사용하여 랩탑/데스크탑에서 터널을 만듭니다. 여기서 포트 5901에 대한 통신은 SSH 포트 22에서 수행되고 IP 주소 150.136.41.3은 배스천 서버의 공용 IP 주소입니다.
    
        $ <copy>ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
11.  위의 SSH 터널 터미널 창을 닫지 마십시오. 이제 VNC 세션을 시작하고 IP 주소 대신 이번에는 서브넷의 보안 목록에서 이 포트가 열리지 않더라도 포트 5901에서 "localhost"를 사용합니다.
    
    ![](./images/28-ssh_Tunnel.png " ")
    
12.  배스천 서버 내에서 Paraview 응용 프로그램 시작
    
        $ <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview</copy>
        
13.  Paraview application window에서 File -> Open -> Path "/mnt/gluster-share/work"를 선택하고 파일 이름 motorbike.foam를 선택합니다. 0바이트 파일이 되며 이는 양호해야 합니다.
    
    ![](./images/25-Paraview_Menu.png " ")
    
14.  창 왼쪽의 \[속성\] 탭에서 \[메시지 영역\]을 선택하여 모든 값을 선택한 다음 motorBike\_ 접두어로 시작하지 않는 최상위 값의 선택을 취소합니다. motorBike\_로 시작하는 모든 값이 선택되었는지 확인합니다. Apply(적용) 버튼을 누르면 일부 오류가 팝업되고 VNC 콘솔에서 이미지의 렌더링을 볼 수 있도록 팝업되는 오류 창이 무시됩니다.
    
    ![](./images/26-Mesh_Regions.png " ")
    
15.  아래와 같은 이미지가 화면에 렌더링됩니다. 일부 디스플레이 설정에 따라 화면의 이미지가 약간 다를 수 있습니다.
    
    ![](./images/27-Image_Rendering.png " ")
    

모두 완료! 이렇게 해서 OCI의 표준 VM에서 OpenFoam 애플리케이션 실행 데모가 완료되었습니다.

_축하합니다! 워크샵을 성공적으로 완료했습니다._

고성능 컴퓨트 인스턴스 관리에 대한 자세한 정보입니다. 전체 명령 참조는 [여기](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)에서 OCI 설명서를 확인하십시오.

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Last Updated By/Date** - Harrison Dvoor, 2021년 1월