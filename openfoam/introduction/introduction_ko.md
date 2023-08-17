# HPC 클러스터 및 OCI의 표준 VM에서 OpenFOAM 프로젝트 실행

## 소개

이 런북은 Oracle Cloud Infrastructure의 OpenFOAM CFD 소프트웨어 평가를 지원하도록 설계되었습니다. OpenFOAM를 자동으로 다운로드하고 구성합니다. OpenFOAM는 2004년부터 OpenCFD Ltd.가 주로 출시 및 개발한 무료 오픈 소스 CFD 소프트웨어입니다. 그것은 상업 및 학술 기관에서 엔지니어링 및 과학의 대부분의 영역에 걸쳐 큰 사용자 기반을 가지고 있습니다. OpenFOAM에는 화학 반응, 난류 및 열 전달과 관련된 복잡한 유체 흐름에서 음향, 고체 역학 및 전자기학에 이르기까지 다양한 기능이 있습니다.

### HPC(고성능 컴퓨팅) 정보

HPC 또는 슈퍼컴퓨팅은 일상적인 컴퓨팅과 같지만 더 강력합니다. 여러 대의 컴퓨터와 스토리지 디바이스를 통합 패브릭으로 사용하여 매우 빠른 속도로 대량의 데이터를 처리하는 방법입니다. HPC는 과학, 엔지니어링 및 비즈니스에서 세계 최대의 문제를 탐색하고 이에 대한 답변을 찾을 수 있도록 합니다.

클러스터 네트워크는 고대역폭, 초저 대기 시간 네트워크와 연결된 고성능 컴퓨팅(HPC) 인스턴스 풀입니다. 클러스터의 각 노드는 다른 노드와 근접한 물리적 위치에 있는 베어메탈 시스템입니다. 노드 간 RDMA(원격 직접 메모리 액세스) 네트워크는 온프레미스 HPC 클러스터와 비교하여 1자리 마이크로초 미만의 대기 시간을 제공합니다.

고성능 컴퓨팅은 OCI 리전 내의 Oracle Cloud Infrastructure에서 제공됩니다.

OCI의 Oracle Marketplace Image 및 BM.HPC2.36에서 사용 가능한 고성능 컴퓨팅 인스턴스입니다.

Oracle Marketplace Image의 고성능 컴퓨팅 랙에는 HPC 클러스터 노드, 클러스터 네트워크 및 NFS 공유가 포함됩니다.

컴퓨트 노드는 컴퓨트 노드에 RDMA 기반 스토리지 액세스를 제공하는 클러스터 네트워크를 통해 연결됩니다.

현재 컴퓨트 노드당 단일 BM이 지원됩니다. 하드웨어 및 네트워크를 보호하면서 고객을 위한 루트 액세스가 가능하며, 컴퓨트 노드는 BM.HPC2.36을 사용하여 가상화됩니다.

_예상 시간_: 2시간

### 목표

*   RDMA(Remote Direct Memory Access)를 통해 상호 연결된 HPC 인스턴스를 사용하여 OpenFOAM 프로젝트 실행
    
*   Oracle Cloud Infrastructure(OCI)에서 일반 표준 VM(가상 머신)을 사용하여 OpenFOAM 프로젝트 실행
    

### 필수 조건

*   스택뿐만 아니라 구획 생성 권한이 있는 Oracle Cloud Infrastructure 계정입니다.
    
*   OCI 콘솔 및 Oracle Cloud 리소스(예: 가상 클라우드 네트워크, 컴퓨트 및 스토리지)에 대한 기본적인 이해가 도움이 됩니다.
    
*   OCI Cloud Architect Associate 시험을 완료했다고 가정합니다.
    

## 이 워크샵 정보

*   HPC 클러스터를 사용하여 OpenFOAM 프로젝트 실행
    
*   OCI의 Standard VM을 사용하여 OpenFOAM 프로젝트 실행
    

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Last Updated By/Date** - Harrison Dvoor, 2021년 1월