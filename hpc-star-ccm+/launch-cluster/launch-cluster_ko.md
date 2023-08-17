# HPC 클러스터 네트워크 실행

## 소개

HPC 클러스터 네트워크를 시작하는 방법에는 여러 가지가 있으며 이 솔루션 설명서에서는 두 가지 방법을 다룹니다.

*   Marketplace를 통해
*   수동으로 OS에 따라 특정 방법을 사용하려고 합니다. HPC 클러스터 네트워크 마켓플레이스 이미지 또는 OCI HPC CN Terraform 스크립트가 사용되는 경우 이 스크립트는 Oracle Linux 7 전용입니다. CentOS, Ubuntu 또는 다른 OS를 사용하려면 수동 구성이 필요합니다.

예상 실습 시간: 30분

## **옵션 1**: 마켓플레이스를 통한 클러스터 네트워크 만들기

Marketplace는 인프라와 함께 배포할 수 있는 애플리케이션과 이미지를 보유하고 있습니다. Oracle Linux를 사용하려는 고객의 경우 HPC 클러스터 네트워크 이미지를 사용할 수 있으며 마켓플레이스 내에서 직접 실행할 수 있습니다. CFD에 필요한 라이브러리를 포함할 [CFD Ready Cluster](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211)를 시작하는 것이 좋습니다.

1.  마켓플레이스에서 오른쪽 상단의 앱 가져오기를 선택합니다. ![](images/get-app.png " ")
    
2.  OCI Region을 선택하고 Sign In을 누릅니다. ![](images/sign-in.png " ")
    
3.  사인인한 후 HPC 클러스터 이미지의 버전을 확인한 다음 클러스터가 실행될 구획을 선택합니다. 조항 및 조건에 동의한 다음 스택을 실행합니다.
    
    ![](images/launch-stack.png " ")
    
4.  스택의 나머지 세부 정보를 채웁니다.
    
    *   컴퓨트 구성 및 배스천에 대해 원하는 AD를 선택합니다.
    *   공용 SSH 키 복사-붙여넣기
    *   클러스터에 대한 컴퓨트 인스턴스 수를 입력합니다.
    *   Install OpenFOAM를 선택 해제합니다.
    *   6TB 이상의 공유 디스크 공간이 필요한 경우 GlusterFS을 확인하고 필요한 서버 수를 선택합니다(서버당 6TB).
5.  Create를 누릅니다.
    
6.  Terraform Actions로 이동한 후 Apply를 누릅니다. 그러면 CN 프로비전이 실행됩니다.
    
    ![](images/apply.png " ")
    
7.  작업이 'Succeeded'로 표시될 때까지 기다린 다음 Outputs로 이동하여 배스천 및 컴퓨트 노드 전용 IP를 가져옵니다.
    

## **옵션 2**: 수동 구성을 통해 클러스터 네트워크 만들기

Marketplace는 인프라와 함께 배포할 수 있는 애플리케이션과 이미지를 보유하고 있습니다. Oracle Linux를 사용하려는 고객의 경우 다음과 같이 클러스터 네트워크를 수동으로 만들 수 있습니다.

1.  오른쪽 상단에 있는 OCI Region을 선택합니다.
    
2.  기본 메뉴에서 Networking and Virtual Cloud Networks를 선택합니다.
    
3.  Start VCN Wizard를 누르고 VCN with Internet Connectivity를 선택합니다.
    
4.  올바른 구획을 선택하고 이름을 지정한 다음 172.16.0.0/16을 VCN CIDR로, 172.16.0.0/24를 공용 서브넷으로, 172.16.1.0/24를 전용 서브넷으로 사용합니다. **다음**, **생성**을 차례로 누릅니다.
    
5.  기본 메뉴에서 Compute, Instances, Create Instance를 차례로 선택합니다.
    
6.  **Change Image**를 선택하고 Oracle Image 탭 아래에서 Oracle Linux 7 - HPC Cluster Networking Image를 선택합니다.
    
7.  BM.HPC2.36 인스턴스를 시작할 수 있는 가용성 도메인을 선택합니다.
    
8.  베어메탈 및 전문 분야에서 BM.HPC2.36으로 구성 변경
    
9.  생성한 VCN 및 공용 서브넷을 선택합니다.
    
10.  인스턴스에 접속하려면 공용 키를 추가하십시오. 이 키는 모든 컴퓨트 인스턴스에서 사용됩니다.
    
11.  시스템이 작동되면 생성된 인스턴스를 누릅니다. More Actions에서 Create Instance Configuration을 선택합니다. 이제 More Actions에서 인스턴스를 종료할 수 있습니다.
    
12.  기본 메뉴에서 Compute를 선택한 다음 Cluster Networks를 선택합니다.
    
13.  Create Cluster Network를 누르고 모든 옵션을 입력합니다. 방금 생성한 VCN, 전용 서브넷 및 인스턴스 구성을 사용합니다. BM.HPC2.36 인스턴스를 실행할 수 있는 AD를 선택하십시오.
    
14.  클러스터 네트워크를 실행합니다.
    
15.  로드하는 동안 Main Menu(주 메뉴), Compute(컴퓨트) 및 Instances(인스턴스) 아래에 다른 인스턴스를 생성합니다.
    
16.  방금 생성한 공용(public) 서브넷에 넣습니다. 공용 키와 구성은 VM.Standard2.1 또는 이와 유사해야 합니다. 클러스터 연결에 사용할 배스천이 됩니다.
    
17.  배스천(/home/opc/.ssh/cluster\_key)의 클러스터에 키를 SCP하고 /home/opc/.ssh/id\_rsa에도 복사합니다.
    
18.  다음 명령을 통해 배스천에 프로비전 도구를 설치합니다.
    
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        
19.  콘솔에서 컴퓨트, 인스턴스 풀로 차례로 이동하여 클러스터 네트워크 풀에 대한 모든 IP 주소를 수집합니다. 또는 전용 서브넷에서 실행 중인 항목이 없는 경우 배스천에서 이 명령을 사용합니다.
    
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done
        
20.  다음 명령을 통해 Provisioning Tool을 설치합니다.
    
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월