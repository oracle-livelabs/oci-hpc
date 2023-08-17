# OCI CLI 명령 및 Terraform을 사용하여 HPC 작업

## 소개

`ocihpc`는 OCI(Oracle Cloud Infrastructure)에서 HPC 애플리케이션의 배포를 간소화하는 도구입니다. HPC CLI(명령행 인터페이스)를 사용하면 단일 명령을 사용하여 전용 베어메탈 HPC 컴퓨트 인스턴스 및 Mellanox RDMA 네트워킹으로 모든 크기의 HPC 클러스터를 배포할 수 있습니다. HPC CLI를 사용하기 위해 Terraform 또는 Oracle Cloud Infrastructure Resource Manager를 이해할 필요가 없습니다. 핵심 Oracle Cloud Infrastructure CLI를 기반으로 구축되며 사전 구축된 Terraform 스크립트를 포함합니다.

HPC CLI 사용 시의 이점은 다음과 같습니다.

1.  단일 명령을 통해 RDMA over Converged Ethernet(RoCE) v2를 통해 신속하게 완전한 컴퓨트 클러스터를 실행합니다.
2.  기본 클러스터 네트워크를 실행하는 데 필요한 Terraform 또는 Oracle Cloud Infrastructure Resource Manager에 대한 지식이 없습니다.
3.  MPI(Message Passing Interface) 배치 지원에는 Open MPI, Intel MPI 및 Platform MPI를 사용한 Mellanox OFED를 포함하여 RDMA에서 병렬 처리를 실행하기 위한 전체 소프트웨어 패키지 세트가 포함됩니다.
4.  사용자 정의 가능 자체 Terraform 스크립트를 실행하거나 도구와 함께 제공된 기존 스크립트에 추가하여 자체 애플리케이션을 설치할 수 있습니다.

예상 실습 시간: 60분

### 목표

개발자 또는 데이터 엔지니어로서,

1.  Oracle 명령행 인터페이스를 사용하여 HPC 클러스터 네트워크 배치
2.  Cli를 통한 스택 보기, 실행 및 관리

### 필수 조건

1.  Terraform 스택 실행에 충분한 사용자 인증서가 있는 Oracle Cloud Infrastructure 계정입니다.
2.  생성된 SSH 키

### 정책

`ocihpc`에서 사용하는 OCI 사용자 계정에 OCI 리소스 관리자에 대해 필요한 정책이 구성되어 있어야 합니다. 필요한 정책에 대한 자세한 내용은 [이 링크](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm)를 확인하십시오.

## 작업 1: macOS/Linux에 ocihpc 설치

1.  다음 명령으로 최신 릴리스를 다운로드하고 추출합니다.
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  1단계에서 다운로드한 파일의 압축을 풉니다.
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  ocihpc 이진을 실행 가능한 상태로 만듭니다.
    
        $ chmod +x ./ocihpc 
        
4.  ocihpc 바이너리를 PATH로 이동합니다.
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  작동하는지 테스트합니다.
    
        $ ocihpc version 
        

## 작업 2: Windows에 ocihpc 설치

1.  [이 링크](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip)에서 최신 릴리스를 다운로드하고 추출합니다.
    
2.  PATH에 ocihpc 바이너리를 추가합니다.
    
3.  작동하는지 테스트합니다.
    
        $ ocihpc.exe version 
        

## 작업 3: 명령줄에서 ssh 키 쌍 만들기

창을 사용하고 있다면 [이 링크](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm)를 참고하세요.

1.  명령을 입력하기 위해 셸 또는 터미널을 엽니다.
    
2.  .oci 폴더로 이동합니다.
    
        $ cd Users/enjli/.ssh
        
3.  폴더를 생성하지 않은 경우 인증서를 저장할 .ssh 디렉토리를 생성합니다.
    
        $ mkdir Users/enjli/.ssh
        
4.  폴더에 id\_rsa 키 쌍이 표시되지 않으면 다음 명령을 입력하고 메시지가 표시되면 이름과 문장암호를 제공하십시오. 기본값인 2048비트의 RSA 키로 키가 생성됩니다.
    
        $ ssh-keygen
        

## 작업 4: API 서명 키 생성

API 요청은 전용 키로 서명되며 Oracle은 공용 키를 사용하여 요청의 신뢰성을 확인합니다. 창을 사용하고 있다면 [이 링크](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other)를 참고하세요.

1.  인증서를 저장할 .oci 디렉토리를 생성하지 않은 경우 생성합니다.
    
        $ mkdir Users/enjli/.oci
        
2.  다음 명령으로 전용 키를 생성합니다.
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  자신만 전용 키 파일을 읽을 수 있는지 확인합니다.
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  공용 키를 생성합니다.
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## 작업 5: Oracle Cloud Infrastructure에 공용 키 추가

이제 전용/공용 키 콤보가 있으므로 사용자 설정에서 OCI 콘솔에 추가해야 합니다.

1.  `oci_api_key_public.pem`가 포함된 .oci 폴더로 이동하여 공개 키를 복사합니다.
    
2.  OCI 콘솔에 로그인하고 Menu를 누르고 Identity and Users를 선택합니다. 사용자를 선택하고 사용자 상세내역 페이지로 이동합니다.
    
3.  \[API 키\] 섹션에서 \[공개 키 추가\]를 클릭합니다. ![](./images/ResourcesMenu.png) ![](./images/APIKeys.png)
    
4.  공용 키 추가에서 CLI에서 복사한 공용 키 붙여넣기 ![](./images/AddPublicKey.png)
    

## 작업 6: 구성

이 단계에서는 CLI에 필요한 구성에 대해 설명하고 CLI 기능을 확장할 수 있는 선택적 구성을 포함합니다.

1.  CLI를 사용하기 전에 .oci 폴더에 Oracle Cloud Infrastructure 계정 작업에 필요한 자격 증명이 포함된 구성 파일을 만들어야 합니다. 설치 대화 상자를 사용하거나 텍스트 편집기를 사용하여 수동으로 이 파일을 만들 수 있습니다.
    
2.  _ocihpc configure_를 실행하여 OCI에 액세스할 수 있는 적합한 구성이 있는지 확인합니다. 이 도구는 구성을 만드는 과정을 안내합니다.
    

구성 파일이 기록되는 위치 알림이 전송됩니다.

_구성 파일이 저장됨: /Users/enjli/.oci/config_

구성 파일을 만들려면 1이 필요합니다. 사용자 OCID(화면 오른쪽 상단에 있는 > 사용자 설정 > 사용자 정보 탭의 프로파일 섹션에 있음), 2. 테넌시 OCID(Administration > Tenancy Details > Tenancy Information 탭), 3. 현재 사용 중인 지역(예: us-phoenix-1, us-ashburn-1 등), 4. 사용자의 지문(사용자 정보 탭에 있음), 5. API 전용 서명 키에 대한 경로입니다.

`config` 파일의 예는 다음과 같습니다. ![](./images/oci_config.png)

## 태스크 7: 목록

`ocihpc list`를 실행하여 사용 가능한 스택 목록을 가져올 수 있습니다.

예:

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## 작업 8: 초기화

1.  배치 소스로 사용할 폴더를 생성합니다.

중요: 스택당 다른 폴더를 사용합니다. 동일한 폴더에서 둘 이상의 스택을 초기화하지 마십시오. 그렇지 않으면 도구가 이전 도구를 덮어씁니다.

2.  해당 폴더로 변경하고 `ocihpc init <stack name>`를 실행합니다. `ocihpc`는 필요한 파일을 해당 폴더로 다운로드합니다.
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**IMPORTANT**: ocihpc deploy 명령을 실행하기 전에 /Users/enjli/ocihpc-test/config.json 파일의 내용을 편집합니다.

## 작업 9: 배치

1.  배치하기 전에 `config.json` 파일의 값을 변경해야 합니다. 변수는 배치하는 스택에 따라 다릅니다. 클러스터 네트워크의 예 `config.json`는 다음과 같습니다.
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  `config.json` 파일을 수정하려면 새로 만든 디렉토리(이 경우ocihpc-test)로 이동하고 texteditor 또는 notepad를 사용하여 "config.json" 파일을 엽니다. ![](./images/config_json.png)
    

이 파일은 1단계에서 구성한 구성 파일과 다릅니다.

이 구성 파일의 경우 다음이 필요합니다.

1.  테넌시의 HPC 리소스가 포함된 가용성 도메인 정보(Administration > Tenancy Details > Scroll down to the Service Limits section > Compute > and scroll down to find "BM.HPC2.36") - 아래 스크린샷에서 총 6개의 BM.HPC2.36 시스템이 AD-2에서 사용되고 있으며 그 중 0개가 현재 사용되고 있습니다. ![](./images/hpc_resource.png)
    
2.  리소스(VM.standard2.1 구성) 배스천 구성이 이미 채워져 있어야 하는 경우 배스천 AD는 선택한 모든 AD일 수 있습니다(VM.Standard2.1).
    
3.  노드 수 - 이 실습에서는 모든 HPC 리소스를 사용할 수 있도록 2가 제공됩니다.
    
4.  공용 SSH 키
    

**노트**

1.  `config.json`의 값을 변경한 후 `ocihpc deploy <arguments>`를 사용하여 스택을 배치할 수 있습니다. 이 명령은 Oracle Cloud Resource Manager에서 스택을 생성하고 이를 사용하여 스택을 배치합니다.
    
2.  지원되는 스택의 경우 `ocihpc deploy` 명령에 추가하여 배치할 노드 수를 설정할 수 있습니다. 스택이 지원하지 않거나 값을 제공하지 않을 경우 툴이 기본 숫자로 배치됩니다.
    
3.  예를 들어, 다음 명령은 5개의 노드가 있는 클러스터 네트워크를 배치합니다.
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  이 툴은 `<stack name>-<current directory>-<random-number>`로 구성된 배치 이름을 생성합니다.
    
    예:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## 태스크 10: 연결

배치가 완료되면 연결할 수 있는 배스천/헤드노드 IP가 표시됩니다.

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

`ocihpc get ip` 명령을 실행하여 연결 세부 정보를 가져올 수도 있습니다.

## 태스크 11: 관리

또한 cli 명령을 사용하여 리소스를 쉽게 관리하고 추적할 수 있습니다.

1.  특정 구획에 배치된 모든 스택 목록을 생성하려면 `oci resource-manager job list -c [OCID OF COMPARTMENT]`을 사용합니다.
    
2.  스택 또는 구획에서 작업 목록을 생성하려면 생성된 시간순으로 정렬합니다. `oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  작업 세부 정보와 함께 작업을 검토하려면 `oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  지정된 작업에 대한 로그 항목을 JSON 형식으로 보려면 `oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`을 사용합니다.
    
5.  스택 및 연관된 작업을 다른 구획으로 이동하려면 `oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## 작업 12: 삭제

배치가 완료되면 스택 폴더로 변경하고 `ocihpc delete --stack <stack name>`를 실행하여 삭제할 수 있습니다.

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

모두 완료! OCI CLI 툴을 사용하여 첫번째 고성능 컴퓨트 인스턴스를 성공적으로 배치했습니다.

_이제 다음 실습으로 진행할 수 있습니다._

고성능 컴퓨트 인스턴스 관리에 대한 자세한 정보입니다. 전체 명령 참조는 [여기](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)에서 OCI 설명서를 확인하십시오.

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Last Updated By/Date** - Harrison Dvoor, 2020년 10월

# <<<<<<< 헤드

> > > > > > > 업스트림/마스터