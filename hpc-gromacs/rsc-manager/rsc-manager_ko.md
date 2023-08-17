# 리소스 관리자

## 소개

이 연습에서는 새 스택을 생성합니다.

예상 실습 시간: 5분

## 태스크: 자원 관리자

OCI 콘솔에는 필요한 모든 리소스를 생성하는 리소스 관리자가 제공됩니다. 스택을 생성하는 영역은 스택이 배치된 영역이 됩니다.

1.  왼쪽 위에서 메뉴 ![](./images/menu.png)을 선택한 다음 Resource Manager --> Stacks를 선택합니다. 스택이 실행될 왼쪽 필터 메뉴에서 이름 및 구획을 선택합니다.
    
2.  새 스택 생성: ![](./images/stack.png)
    
3.  Terraform 스크립트용 [zip 파일](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip)을 다운로드하고 **내 구성**을 선택하고 **을 선택합니다. ZIP FILE(우편 번호)** 버튼을 누르고 **Browse(찾아보기)** 링크를 누른 다음 다운로드한 zip 파일을 선택합니다. **Select**(선택)를 누릅니다.
    
    ![](./images/zip-file.png " ")
    

스택 구성을 완료하려면 [변수 선택](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/blob/master/Documentation/ResourceManager.md#select-variables) 섹션으로 이동합니다.

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월