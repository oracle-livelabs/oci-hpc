# 오브젝트 스토리지에 Gromacs 설치 프로그램 추가

## 소개

이 연습에서는 Object Storage에 Gromacs Installer를 추가합니다.

예상 실습 시간: 5분

## 작업: 오브젝트 스토리지에 Gromacs 설치 프로그램 추가

_이 github에 제공된 terraform 스크립트 [zip file](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip)에는 Gromacs 2020.1, VMD 1.9.3에 대한 객체 스토리지 URL 및 [Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench)의 벤치마킹 모델 예가 이미 포함되어 있습니다. 이러한 URL을 변경하려면 variable.tf 파일을 수정하고 gromacs\_url, model\_url 및 visualizer\_url 값을 사전 인증된 고유 요청 URL로 바꿉니다._

1.  왼쪽 위에 있는 메뉴 ![](./images/menu.png)을 선택한 다음 오브젝트 스토리지 --> 오브젝트 스토리지를 선택합니다.
2.  새 버킷을 생성하거나 기존 버킷을 선택합니다. 생성하려면 다음을 누르십시오.![](./images/create_bucket.png)
3.  기본 옵션(Standard as Storage tiers 및 Oracle-Managed keys)은 그대로 둡니다. 클릭![](./images/create_bucket.png)
4.  새로 생성된 버킷 이름을 누른 후 다음을 선택합니다.![](./images/upload_object.png)
5.  Gromacs 설치 관리자 tar 파일을 선택하고 클릭![](./images/upload_object.png)
6.  방금 업로드한 객체의 오른쪽에 있는 3개의 점을 누르고 "Create Pre-Authenticated Request"를 선택합니다.
7.  다음 메뉴에서 기본 옵션을 그대로 두고 설치 프로그램 URL의 만료 날짜를 선택합니다. 클릭![](./images/pre-auth.png)
8.  다음 창에서 "PRE-AUTHENTICATED REQUEST URL"을 복사하고 유지합니다. 이 창을 닫은 후에는 검색할 수 없습니다. 풀리거나 만료되는 경우 다른 URL을 생성하는 다른 사전 인증된 요청을 항상 재생성할 수 있습니다.

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월