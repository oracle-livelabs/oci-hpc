# STAR-CCM+ 설치

## 소개

이 실습에서는 STAR-CCM+를 설치합니다.

예상 실습 시간: 25분

## 작업 1: 특정 라이브러리 추가

_**Marketplace에서 CFD Ready Cluster를 사용한 경우 이 단계가 필요하지 않습니다.**_

1.  모든 컴퓨트 노드의 Oracle Linux 이미지에 추가해야 하는 라이브러리가 몇 개 있습니다.
    
            sudo yum -y install libSM libX11 libXext libXt
        

## 작업 2: 바이너리 다운로드

Siemens PLM 웹 사이트에서 STAR-CCM+ 설치 프로그램을 다운로드하거나 scp를 사용하여 시스템으로 푸시할 수 있습니다.

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

또 다른 가능성은 설치 프로그램을 오브젝트 스토리지에 업로드하는 것입니다.

1.  콘솔의 주 메뉴에서 Object Storage를 선택합니다.
2.  오른쪽 상단에 있는 올바른 영역 선택
3.  왼쪽에 있는 올바른 구획 선택
4.  아직 생성되지 않은 버킷 생성
5.  버킷에서 업로드 객체를 선택하고 설치 프로그램의 경로를 지정합니다.
6.  Installer 객체의 오른쪽에 있는 3개의 점을 선택하고 Create Pre-Authenticated Request를 선택합니다.
7.  URL이 손실된 경우 다시 가져올 수 없지만 새 사전 인증된 요청을 재생성할 수 있습니다.

설치 프로그램 양식 객체 스토리지 다운로드

        wget PAR_URL
    

버전에 따라 설치 프로그램의 tar 풀기 또는 압축 풀기

        tar -xf installer.tgz
        unzip installer.tgz
    

## 작업 3: 설치

1.  공유 위치에서 설치 프로그램을 실행합니다. 기본적으로 HPC 클러스터에는 모든 컴퓨트 노드에 NFS 공유가 마운트되어 있습니다.
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월