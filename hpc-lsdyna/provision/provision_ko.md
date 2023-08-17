# Oracle Marketplace 이미지에서 HPC 클러스터 프로비전

## 소개

이 실습에서는 OCI Marketplace에서 HPC 클러스터를 프로비저닝합니다. OCI 마켓플레이스는 클라우드 콘솔 내에서 쉽게 배포할 수 있는 다양한 산업 솔루션을 위한 원스톱 숍입니다. 여기서는 Automated HPC Cluster Deployment 시장을 활용합니다. 마켓플레이스의 템플릿은 최소한의 노력으로 워크로드를 시작하는 데 필요한 모든 구성 요소를 포함하여 고성능 컴퓨팅 솔루션의 기반을 제공합니다. 이러한 실습이 진행됨에 따라 타사 애플리케이션에서 이러한 기준을 통합하고 활용하여 워크플로우를 가속화하는 방법을 알아볼 수 있습니다.

예상 실습 시간: 60분

### 목표

개발자 또는 데이터 엔지니어로서,

*   OCI Marketplace로 이동
*   OCI Marketplace에서 HPC 클러스터 이미지 배포
*   OCI에서 스택 보기 및 실행

### 필수 조건

*   인스턴스 VM 표준 2.1 또는 BM.HPC2.36 구성을 생성할 수 있는 권한이 있는 Oracle Cloud Infrastructure 계정입니다.

## 작업 1: OCI Marketplace로 이동

1.  왼쪽 상단 모서리에 있는 햄버거 메뉴(?)를 클릭합니다. ![](./images/click_hamburger.png)
    
2.  아래로 스크롤하여 마켓플레이스를 클릭하세요. ![](./images/click_marketplace.png)
    
3.  마켓플레이스에서 **HPC**를 검색하고 HPC 클러스터를 선택합니다. ![](./images/marketplace.png)
    
4.  **Oracle 표준 약관 및 제한 사항**을 검토하고 수락한 다음 **스택 실행**을 클릭하십시오. ![](./images/launch_stack.png)
    

## 작업 2: HPC 스택 구성

스택을 실행하면 스택 정보를 입력할 스택 생성 마법사로 재지정됩니다.

1.  **스택 정보**에서 스택에 이름을 제공하고 _(선택사항)_ 현재 속하지 않은 구획에 배치하도록 지정합니다. ![](./images/stack_p1.png)
    
2.  컴퓨트 구성 및 배스천에 대해 원하는 **AD**를 선택합니다.
    
3.  공용 **ssh key** 복사-붙여넣기 ![](./images/stack_p2_1.png)
    
4.  클러스터에 대한 **컴퓨트 인스턴스** 수를 입력합니다. ![](./images/stack_p2_2.png)
    
5.  **Network Options(네트워크 옵션)**에서 기본값을 그대로 둡니다. ![](./images/stack_p2_3.png)
    
6.  **다음**을 눌러 구성을 검토합니다. ![](./images/stack_p3.png)
    
7.  **생성**을 누릅니다.
    

**주:** 스택을 프로비전하는 데는 15분 정도 걸립니다.

## 작업 3: 실행 중인 스택 보기

스택을 생성하면 실행 중인 스택 작업을 볼 수 있는 페이지로 재지정됩니다. 아래와 같이 나타나야 합니다. 여기에서 다음을 확인할 수 있습니다.

1.  **로그**
2.  Terraform 스크립트에 전달되거나 Terraform 스크립트에 의해 생성된 **변수**
3.  Terraform 스크립트로 프로비전된 **연관된 리소스**
4.  **출력**
5.  작업의 **상태 보기**

![](./images/stack_detail_provisioning.png)

현재 구획의 모든 스택을 볼 수 있는 이 페이지로 이동하려면 왼쪽 상단 및 **솔루션 및 플랫폼** 아래에서 **리소스 관리자** > **스택** 위로 마우스를 가져가십시오.

![](./images/nav_resource_manager.png)

모두 완료! 이렇게 해서 Oracle Marketplace Image에서 HPC 클러스터를 프로비저닝하기 위한 데모가 완료되었습니다.

_이제 다음 실습으로 진행할 수 있습니다._

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Last Updated By/Date** - Harrison Dvoor, 2020년 10월

# <<<<<<< 헤드

> > > > > > > 업스트림/마스터