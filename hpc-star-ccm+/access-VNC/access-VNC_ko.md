# VNC 액세스

## 소개

이 실습에서는 VNC에 액세스합니다.

예상 실습 시간: 10분

## 태스크: VNC 액세스

1.  SSH 터널을 통해 인스턴스에 접속합니다. 컴퓨터에서 아래 ssh PORT를 사용하여 연결하면 5900 + N의 결과가 발생합니다. N의 출력이 1, PORT가 5901인 경우 N은 표시 번호입니다. 출력이 9인 경우 PORT는 5909입니다. public\_ip는 VNC 서버를 실행하는 헤드노드의 공용 IP 주소입니다. 이전 지침을 사용한 경우 포트는 5901이 됩니다.
    
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        
2.  이제 localhost:N을 VNC 서버로 사용하고 vnc 설치 중 설정한 비밀번호를 사용하여 VNC 뷰어를 사용하여 연결할 수 있습니다. 원하는 VNC 클라이언트를 선택하거나 이 설명서를 사용하여 로컬 시스템에 설치할 수 있습니다.
    

*   [윈도우-TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [맥 OS / 윈도우 - RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월