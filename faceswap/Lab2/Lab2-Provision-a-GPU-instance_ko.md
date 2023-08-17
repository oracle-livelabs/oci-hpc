# GPU 인스턴스 프로비전

## 소개

이 실습에서는 OCI에서 Ubuntu로 GPU 인스턴스를 프로비저닝하는 데 필요한 단계를 안내합니다.

**참고:** OCI에서 GPU 인스턴스를 프로비전하려면 PAYG 계정이 필요합니다. Oracle 30일 무료 체험판을 사용 중인 경우 사용 가능한 모든 VM 및 BM 인스턴스를 사용할 수 있습니다.

예상 워크샵 시간: 15분

### 목표

*   Oracle Cloud에 로그인
*   OCI에서 GPU 인스턴스 프로비전

### 필요한 사항

*   OCI에서 유저 그룹의 일부인 유저(유저 이름 및 암호 사용)입니다. [사용자 관리](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm) 페이지를 확인하여 **사용자 생성** 방법 및 콘솔을 사용하여 **그룹에 사용자를 추가하려면** 방법을 읽으십시오.
*   클라우드 계정 이름 - 테넌시의 이름(관리자 또는 Oracle Cloud 시작 전자메일에서 제공)입니다.
*   사용자 그룹입니다. 콘솔을 사용하여 **그룹을 생성하려면** [그룹 관리](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managinggroups.htm#three) 페이지를 확인하십시오.
*   사용자가 컴퓨트 인스턴스를 실행할 수 있도록 그룹에 지정된 정책입니다. [공통 정책](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm#top) 페이지에서 콘솔을 사용하여 **사용자가 컴퓨트 인스턴스를 실행할 수 있도록 허용**해야 하는 정책을 읽어보십시오.
*   사용자에 대한 구획입니다. 콘솔을 사용하여 **구획을 생성하려면** [구획 관리](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingcompartments.htm#uscons) 페이지를 확인하십시오.
*   GPU 인스턴스를 프로비전할 영역에서 가용성 도메인당 허용되는 최대 GPU 인스턴스 수에 대한 서비스 제한을 확인하십시오. 서비스 제한을 늘리려면 [서비스 제한](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/servicelimits.htm#top) 페이지의 단계를 따릅니다.

## 작업 1: Oracle Cloud에 로그인

Oracle Cloud에서 사인아웃한 경우 다음 단계에 따라 다시 사인인하십시오.

1.  [cloud.oracle.com](https://cloud.oracle.com)으로 이동하여 클라우드 계정 이름을 입력하고 **다음**을 누릅니다. 이전 섹션에서 계정을 생성할 때 선택한 이름입니다. 이메일 주소가 아닙니다. 이름을 잊어버린 경우 확인 전자메일을 참조하십시오.
    
    ![](images/cloud-oracle.png " ")
    
2.  _"Oracle Cloud Infrastructure 직접 사인인"_ 뒤의 화살표를 확장하여 로그인 입력 필드를 표시합니다.
    
    ![](images/cloud-login-tenant.png " ")
    
3.  클라우드 계정 인증서를 입력하고 **사인인**을 누릅니다. 사용자 이름은 이메일 주소입니다. 암호는 계정에 등록할 때 선택한 암호입니다.
    
    ![](images/oci-signin.png " ")
    
4.  이제 Oracle Cloud에 사인인되었습니다!
    
    ![](images/oci-console-home-page.png " ")
    

## 작업 2: GPU 인스턴스 실행

1.  로그인한 후 **빠른 작업** 섹션에서 **VM 인스턴스 생성** 단추를 누릅니다.
    
    ![](images/click-create-vm-instance.png " ")
    

또는 왼쪽 위에 있는 서비스 메뉴를 누른 다음 **Compute(컴퓨트)** -> **Instances(인스턴스)**를 누를 수도 있습니다. 클릭하는 파란색 **인스턴스 생성** 버튼이 있는 새 페이지가 열립니다.

2.  컴퓨팅 인스턴스에 대한 이름을 입력합니다.
    
    ![](images/fill-in-name.PNG " ")
    
3.  드롭다운 목록에서 구획을 선택합니다.
    
    ![](images/select-compartment.PNG " ")
    
4.  가용성 도메인을 선택하기 전에 **거버넌스** -> **제한, 할당량 및 사용량**에서 선택합니다. 여기서 선택된 영역의 가용성 도메인에 사용 가능한 GPU 인스턴스가 있습니다. 컴퓨트 인스턴스가 프로비전될 가용성 도메인을 선택합니다.
    
    ![](images/select-AD.PNG " ")
    
5.  **이미지 변경** 버튼을 누릅니다.
    
    ![](images/change-image.PNG " ")
    
    컴퓨트 인스턴스에 사용할 이미지를 선택하고 **이미지 선택** 단추를 누릅니다. Faceswap에서 권장하는 Ubuntu 18.04를 선택하지만 다른 Linux 배포판 및 Windows 운영 체제도 작동할 수 있습니다.
    
    ![](images/select-image.PNG " ")
    
6.  **구성 변경** 버튼을 누릅니다.
    
    ![](images/change-shape.PNG " ")
    
    **가상 머신** 및 **전문 분야 및 레거시**를 선택합니다. GPU 인스턴스와 **항상 무료** VM.Standard.E2.1이 제공됩니다. 마이크로 구성(GPU 인스턴스를 실행할 PAYG 계정을 소유하지 않는 경우).
    
    ![](images/select-VM-SL.PNG " ")
    
    프로비저닝할 GPU 인스턴스를 선택하고 읽기 및 체크박스를 선택합니다. _검토 후 다음 문서를 수락합니다. Oracle 및 NVIDIA 이용약관_을 선택하고 **구성 선택** 버튼을 누릅니다. 이 경우 GPU가 1개인 P100(NVIDIA Pascal GPU 아키텍처) 기반 VM.GPU2.1입니다. V100(NVIDIA Volta GPU 아키텍처) 및 A100(NVIDIA Ampere GPU 아키텍처)를 기반으로 가상 및 베어메탈 머신에 대한 서비스 제한을 요청할 수도 있습니다.
    
    ![](images/select-GPU.PNG " ")
    
7.  구획에 VCN을 이미 생성한 경우 컴퓨트 인스턴스를 배치할 VCN과 서브넷을 선택합니다. 그렇지 않은 경우 **새 가상 클라우드 네트워크 생성**을 누르고 VCN의 이름 및 구획과 공용 서브넷의 이름을 선택할 수 있습니다. 기본 CIDR 블록을 유지하거나 조정할 수 있습니다. 인스턴스에 공용 IP 주소를 지정하도록 선택할 수 있습니다. 공용 IP 주소를 지정하면 인터넷에서 이 인스턴스에 액세스할 수 있습니다. 공용 IP 주소가 필요한지 여부가 확실하지 않으면 항상 나중에 지정할 수 있습니다.
    
    ![](images/create-vcn.PNG " ")
    
8.  _Lab 1 - SSH 키 생성_에서 생성한 SSH 공용 및 전용 키 쌍을 사용하려는 경우, **공용 키 파일 선택**을 누르고 SSH 공용 키 파일을 선택하거나, **공용 키 붙여넣기**를 누르고 필드에 SSH 공용 키를 붙여 넣을 수 있습니다. OCI에서 SSH 공용 및 전용 키를 생성하고 로컬 시스템에 저장할 수도 있습니다. SSH 키를 제공하지 않을 경우 SSH를 사용하여 인스턴스에 접속할 수 없습니다.
    
    ![](images/add-SSH.PNG " ")
    
9.  마지막 단계는 전송 암호화 사용 여부와 암호화 키 관리 방법을 선택하여 부트 볼륨 크기를 지정하는 것입니다. Linux Syxtems의 경우 기본 부트 볼륨 크기인 46.6GB를 사용하거나 최대 32TB로 늘릴 수 있습니다. 모든 상자를 선택하지 않은 상태로 두고 **생성**을 눌러 컴퓨트 인스턴스 생성을 완료할 수 있습니다.
    

![](images/boot-volume.PNG " ")

몇 분 후 GPU 컴퓨트 인스턴스는 상태를 **RUNNING**으로 변경합니다.

![](images/instance-running.PNG " ")

## **확인**

*   **작성자/날짜** - Maria Patelkou, HPC 솔루션 아키텍트, Oracle Proposal to Production 프로그램, 2021년 3월
*   **최종 업데이트 수행자/날짜** - Maria Patelkou, HPC 솔루션 아키텍트, Oracle Proposal to Production 프로그램, 2021년 3월