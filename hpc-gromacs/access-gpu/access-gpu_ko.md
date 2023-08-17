# GPU 노드에 접근

## 소개

이 실습에서는 GPU 노드에 액세스합니다.

예상 실습 시간: 5분

## 작업: GPU 노드에 액세스

1.  리소스 관리자에서 작업이 성공적으로 완료되면 **출력** 아래의 왼쪽 하단 메뉴에서 GPU 노드에 대한 공용 IP 주소 및 전용 키를 찾을 수 있습니다.
    
2.  개인 키를 로컬 시스템에 복사하고 키의 권한을 변경한 후 인스턴스에 로그인합니다.
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  GPU 노드에 로그인하면 /mnt/block에서 Gromacs를 실행할 수 있습니다. GPU 인스턴스에서 Gromacs를 실행하는 방법에 대한 특정 명령은 README.md 파일을 참조하십시오.
    

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월