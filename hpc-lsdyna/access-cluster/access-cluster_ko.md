# 내 클러스터에 액세스

## 소개

이 실습에서는 클러스터에 액세스합니다.

예상 실습 시간: 10분

## 작업: 클러스터에 액세스

1.  배스천의 공용 IP 주소는 왼쪽 하단 메뉴의 출력에서 찾을 수 있습니다. 기본 메뉴에서 인스턴스로 이동하면 배스천 인스턴스와 공용 IP도 찾을 수 있습니다.
    
2.  시스템에 액세스하기 위한 개인 키도 찾을 수 있습니다. 시스템에 있는 파일의 텍스트를 복사합니다. say/home/user/key:
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월