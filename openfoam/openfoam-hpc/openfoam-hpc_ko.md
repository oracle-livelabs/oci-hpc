# HPC 클러스터에서 OpenFoam 프로젝트 실행

## 소개

HPC 하드웨어를 설정하고 그 위에 애플리케이션 소프트웨어를 배포하는 데 오랜 시간이 걸리며 다양한 복잡한 작업을 포함할 수 있습니다. 이 데모를 통해 이러한 모든 작업은 OCI 마켓플레이스 이미지 내에서 자동화되며, 프로비저닝 직후 OpenFoam 시뮬레이션 소프트웨어와 함께 완벽하게 작동하는 HPC 환경을 사용할 수 있습니다. 필요한 모든 것은 완전한 기능을 갖춘 HPC 환경에서 작업 로드를 실행하고 Paraview와 같은 시각화 응용 프로그램을 실행하여 출력을 렌더링하는 것입니다.

예상 시간: 60분

### 참조 아키텍처

![](./images/29_Architecture.png " ")

### OpenFoam 정보

CFD(전산 유체 역학)는 수치 분석을 사용한 유체 모션 및 열 전달 시뮬레이션입니다. OpenFOAM와 같은 CFD 소프트웨어는 많은 엔지니어링 산업에서 설계를 개선하고, 영향을 시각화하고, 시스템의 비효율을 감지하는 강력한 도구 역할을 할 수 있습니다. 그러나 시뮬레이션을 처리하는 데 필요한 계산 능력은 매우 집중적일 수 있습니다. 수퍼 컴퓨터 및 HPC 클러스터는 종종 합당한 시간 내에 크고 복잡한 모델을 실행하는 데 사용됩니다. Oracle HPC 클러스터 내에서 오픈 소스 CFD 소프트웨어 OpenFOAM를 사용하여 오토바이의 공기 역학 모델을 계산하고 렌더링합니다.

### 기반 구조 용어

1.  작업자 노드: 계산 시뮬레이션 또는 기타 엔지니어링 작업 로드의 작업 로드를 실행할 수 있는 처리 능력을 제공하는 HPC 컴퓨트 인스턴스입니다. 이 데모 컴퓨트 구성에서 BM.HPC2.36 노드는 워커 노드입니다.
    
2.  헤드 노드: 작업자 노드에서 실행되도록 모든 계산 작업을 스케줄링하고 제출할 수 있는 컴퓨트 인스턴스입니다. 여러 번 헤드 노드와 배스천 노드가 동일한 시스템이 될 수 있습니다. 이 데모에서는 배스천 노드를 프로비전하는 중입니다.
    
3.  GPU 노드: 계산 작업 로드의 출력을 렌더링하기 위한 GPU 프로세서가 있는 특수 컴퓨트 인스턴스입니다. 이 데모는 GPU 시스템을 프로비저닝하고 있지 않습니다.
    
4.  배스천 노드: 공용 IP를 사용하는 컴퓨트 인스턴스는 일반적으로 전용 네트워크에 있는 워커 노드에 접속하기 위한 시작점 역할을 합니다.
    
5.  RoCE network: RDMA over Converged Ethernet(RoCE)은 이더넷 네트워크를 통해 RDMA(Remote Direct Memory Access)를 허용하는 네트워크 프로토콜입니다.
    

### 목표

1.  OCI 마켓플레이스 이미지를 사용하여 완전한 기능을 갖춘 HPC 환경 프로비저닝
    
2.  이름이 OpenFoam인 CFD 기반 시뮬레이션 애플리케이션 배포
    
3.  Openfoam 작업 로드 실행
    
4.  Paraview 시각화 애플리케이션을 사용하여 출력을 렌더링합니다.
    

### 필수 조건

다음에 대한 액세스 권한이 있는지 확인합니다.

1.  다음 정책을 사용하여 나 대신 태그 네임스페이스를 관리하도록 컴퓨트 서비스에 권한을 부여합니다.
    
    "compute\_management 서비스가 테넌시의 tag-namespace를 사용하도록 허용"
    
2.  베어메탈 구성 BM.HPC2.36은 이 데모에서만 허용됩니다. 동일한 가용성 도메인에는 최소 2개의 컴퓨트 구성이 필요합니다.
    
3.  배스천 서버에 대한 VM 컴퓨트 이미지입니다. HPC 노드와 동일한 가용성 도메인에 있을 필요가 없습니다.
    
4.  전용 및 공용 서브넷이 있는 기존 VCN을 생성하거나 사용하려면 사용자 인증서에 할당량이 있어야 합니다.
    
5.  마켓플레이스 이미지 "CFD Ready Cluster"에 액세스합니다. 이 DEMO에 사용된 마켓플레이스 이미지의 버전은 (버전: 20200625)입니다.
    

## 작업 1: 마켓플레이스 이미지 실행

1.  사용자가 마켓플레이스 이미지를 실행하기 전에 BM.HPC2.36 구성의 HPC 노드를 2개 이상 사용할 수 있는 가용성 도메인을 확인하십시오. **Menu** -> **Governance** -> **Limits**, **Quotas and Usage**로 이동합니다.
    
    ![](./images/01-Service_Limits.png " ")
    
2.  **Menu** -> **Marketplace** -> **Applications**로 이동합니다.
    
    ![](./images/02-Marketplace.png " ")
    
3.  검색 상자에 "cfd"를 입력합니다.
    
    ![](./images/03-Search_marketplace.png " ")
    
4.  이미지를 누르고 선택한 다음 "**스택 실행**" 단추를 누릅니다. 올바른 구획에 있는지 확인합니다.
    
    ![](./images/04-Launch_Stack.png " ")
    
5.  스택이 실행되면 세부정보를 입력하여 HPC 작업자 노드, 배스천 서버, 네트워킹 구성요소, VCN 서버를 비롯한 리소스를 생성합니다. 또한 스택은 openMPI 및 openFoam와 같은 패키지를 배치하기 위한 입력을 요청합니다.
    
6.  아래의 스택 이름을 제공하십시오.
    
    ![](./images/05-Create_Stack01.png " ")
    
7.  프로비전에 사용 가능한 HPC 노드가 있는 가용성 도메인을 선택합니다. 배스천 서버에 접속하는 데 필요한 SSH 공용 키를 제공합니다.
    
    ![](./images/06-Create_Stack02.png " ")
    
8.  배스천 서버를 프로비전할 가용성 도메인을 선택합니다. HPC 노드의 가용성 도메인과 다를 수 있습니다. 선택한 배스천 호스트 구성을 선택합니다. VNC 서버를 Bastion 서버에 설치하고 나중에 VNC 접속을 설정하는 데 필요한 비밀번호를 제공하려면 체크박스를 사용으로 설정합니다.
    
    ![](./images/07-Create_Stack03.png " ")
    
9.  Worker 노드의 경우 BM.HPC2.36만 선택할 수 있습니다. 프로비전을 완료하려면 최소 2개의 HPC 컴퓨트 구성이 필요합니다. 아래 화면에서 컴퓨트 노드를 2개 이상 선택합니다. 모든 옵션을 기본값으로 그대로 사용할 수 있습니다.
    
    ![](./images/08-Create_Stack04.png " ")
    
10.  Network Options에서 스택에 전달된 기본값을 사용하여 새 VCN을 생성합니다. 이 데모는 이 스택을 통해 생성된 새 VCN을 생성합니다. 그러나 기존 VCN도 사용할 수 있습니다.
    
    ![](./images/09-Create_Stack05.png " ")
    
11.  파일 시스템 섹션의 경우 NFS 마운트를 만들고 위에 gluster 파일 시스템을 설치하는 아래 옵션을 사용하십시오.
    
    ![](./images/10-Create_Stack06.png " ")
    
12.  이 HPC 스택에 OpenFoam 애플리케이션을 설치하려면 "OPENFOAM 설치" 체크박스를 누르십시오.
    
    ![](./images/11-Create_Stack07.png " ")
    
13.  스택의 마지막 페이지에서 입력된 세부정보를 확인하고 모든 항목이 정상이면 "생성" 단추를 눌러 인프라 및 소프트웨어 설치 프로비전을 시작합니다.
    
    ![](./images/12-Create_Stack08.png " ")
    
14.  HPC 스택 설치 진행률 확인
    
    ![](./images/13-StackJob.png " ")
    
15.  프로비저닝이 약 20분 내에 완료되면 로그 파일에 다음 메시지가 포함되어야 합니다. 로그는 메뉴 -> 리소스 관리자 -> 스택(스택 이름 누르기) -> 작업(전체 로그를 보려면 작업 이름 링크를 누르십시오.)에서 찾을 수 있습니다.
    
    ![](./images/14-StackJobDetails.png " ")
    

## 태스크 2: 설정 검증

1.  이 스택에서 프로비전된 인프라 리소스를 보려면 화면 왼쪽의 "리소스 연관" 링크를 누르십시오. 리소스 연관은 **Menu** -> **Resource Manager** -> **Stacks**(스택 이름 누르기) -> **Jobs**(작업 이름 링크 누르기) -> **Associate Resources**에서 찾을 수 있습니다.
    
    ![](./images/15-Stack_Resources.png " ")
    
2.  **컴퓨트** -> **인스턴스**에서 컴퓨트 인스턴스 3개, 전용 네트워크의 HPC 노드 2개, 공용 IP 주소를 사용하는 Bastion 서버 1개가 프로비저닝됩니다.
    
    ![](./images/16-Compute_Details.png " ")
    
3.  위의 스택에서 공용 키가 업로드된 SSH 전용 키를 사용하여 공용 IP 주소를 opc 사용자로 배스천 서버에 접속합니다. 이 방법을 사용하여 Linux/Mac Desktop에서 연결합니다. Windows 바탕화면의 경우 Putty를 사용하고 연결을 위해 ppk 개인 키를 제공하십시오.
    
    ![](./images/17-Bastion_ssh.png " ")
    
4.  2개의 HPC 노드 및 배스천 서버에 마운트된 공유 NFS 스토리지를 확인합니다. 이 NFS 저장소에는 작업 로드 실행에 사용될 OpenFoam 소프트웨어가 포함되어 있습니다.
    
    ![](./images/18-Bastion_storage.png " ")
    
5.  OpenFoam 애플리케이션은 "/mnt/gluster-share/work" 폴더 아래에 호스트됩니다. 작업 로드가 실행될 2개의 HPC 노드의 대체 IP 주소를 포함하는 호스트 파일을 확인합니다.
    
    ![](./images/19-Bastion_openfoam.png " ")
    
6.  아래 출력은 포트 5901에서 실행 중인 VNC 서버를 보여줍니다. 이 포트는 배스천 호스트에 VNC 접속을 설정하는 데 사용됩니다.
    
    ![](./images/20-Bastion_VNCdetails.png " ")
    
7.  수신 규칙 아래의 배스천 서버 보안 목록에서 포트 5901이 허용되는지 확인하십시오. VNC가 다른 포트에서 실행 중이고 보안 목록의 수신 트래픽에 대해 해당 포트가 허용되지 않는 경우 동일한 포트에 대해 새 보안 규칙을 생성합니다.
    
    ![](./images/21-SecList_OpenVNC_Port.png " ")
    
8.  HPC 노드의 이름을 확인합니다.
    
    ![](./images/22-ListHPCNodes.png " ")
    

## 작업 3: 시뮬레이션 작업 로드 실행 및 출력 렌더링

1.  Bastion 서버에서 Paraview 패키지를 사용하여 출력을 렌더링하는 데 필요한 다음 명령을 실행합니다.
    
        <copy>$ sudo yum install -y mesa-libGLU
        $ cd /mnt/gluster-share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" https://www.paraview.org/paraview-downloads/download.php >file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  배스천 서버에서 HPC 노드 중 하나에 연결하고 작업 로드를 실행합니다.
    
        <copy>$ ssh 172.16.1.2
        $ cd /mnt/gluster-share/work/
        $ ./Allrun 36</copy>
        
    
        ./Allrun 36
        Cleaning /mnt/gluster-share/work case
        Mesh Dimensions: (40 16 16)
        Cores:36: 6, 6, 1
        Running surfaceFeatures on /mnt/gluster-share/work
        Running blockMesh on /mnt/gluster-share/work
        Running decomposePar on /mnt/gluster-share/work
        Running snappyHexMesh
        Running patchsummary
        Running potentialFoam
        Running simpleFoam
        Running reconstructParMesh on /mnt/gluster-share/work
        Running reconstructPar on /mnt/gluster-share/work
        219.95
        [opc@inst-quqyz-accurate-swan work]$
        
3.  작업 로드가 성공적으로 완료되면 다음과 같이 시스템에서 VNC 클라이언트를 구성합니다. 배스천 서버 및 VNC 포트의 공용 IP 제공
    
    ![](./images/24-VNC_Connection.png " ")
    
4.  선택 사항으로, VNC 포트 5901을 열 수 없거나 이 포트에 대한 SSH 터널을 만들려는 보안상의 이유로 인해 허용되지 않는 경우 다음 명령을 사용하여 보안 목록에서 포트를 열지 않고 포트 5901에서 SSH 터널을 만듭니다.
    
    터미널 창에서 다음 명령을 사용하여 랩탑/데스크탑에서 터널을 만듭니다. 여기서 포트 5901에 대한 통신은 SSH 포트 22에서 수행되고 IP 주소 150.136.41.3은 배스천 서버의 공용 IP 주소입니다.
    
        <copy>$ ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
5.  위의 SSH 터널 터미널 창을 닫지 마십시오. 이제 VNC 세션을 시작하고 IP 주소 대신 이번에는 서브넷의 보안 목록에서 이 포트가 열리지 않더라도 포트 5901에서 "localhost"를 사용합니다.
    
    ![](./images/28-ssh_Tunnel.png " ")
    
6.  배스천 서버 내에서 Paraview 응용 프로그램 시작
    
        <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        ./paraview</copy>
        
7.  Paraview application window에서 File -> Open -> Path "/mnt/gluster-share/work"를 선택하고 파일 이름 motorbike.foam를 선택합니다. 0바이트 파일이 되며 이는 양호해야 합니다.
    
    ![](./images/25-Paraview_Menu.png " ")
    
8.  창 왼쪽의 \[속성\] 탭에서 \[메시지 영역\]을 선택하여 모든 값을 선택한 다음 motorBike\_ 접두어로 시작하지 않는 최상위 값의 선택을 취소합니다. motorBike\_로 시작하는 모든 값이 선택되었는지 확인합니다. Apply(적용) 버튼을 누르면 일부 오류가 팝업되고 VNC 콘솔에서 이미지의 렌더링을 볼 수 있도록 팝업되는 오류 창이 무시됩니다.
    
    ![](./images/26-Mesh_Regions.png " ")
    
9.  아래와 같은 이미지가 화면에 렌더링됩니다. 일부 디스플레이 설정에 따라 화면의 이미지가 약간 다를 수 있습니다.
    
    ![](./images/27-Image_Rendering.png " ")
    

모두 완료! 이렇게 해서 HPC 클러스터에서 OpenFoam 응용 프로그램 실행 데모가 완료되었습니다.

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Last Updated By/Date** - Harrison Dvoor, 2021년 1월