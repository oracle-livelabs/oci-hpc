# VNC를 통해 설정 및 액세스

## 소개

이 실습에서는 VNC 클라이언트(여기서 TigerVNC)를 통해 Ubuntu 인스턴스를 설정하고 액세스하는 데 필요한 단계를 안내합니다.

예상 워크샵 시간: 15분

### 목표

*   SSH를 통해 컴퓨트 인스턴스에 접속
*   Ubuntu 인스턴스에서 VNC 설정 및 구성
*   VNC 클라이언트를 통해 컴퓨트 인스턴스에 액세스합니다(예: TigerVNC).

### 필요한 사항

*   Complete Lab 1 및 Lab 2
*   [VCN 보안 목록에 수신 규칙을 추가](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using)하여 포트 5900+N에 대한 TCP 트래픽을 허용합니다. 여기서 N은 표시 번호(일반적으로 물리적 표시의 경우 :0)입니다. ![](images/vncseclist.PNG " ")

## 작업 1: SSH를 통해 컴퓨트 인스턴스에 접속

1.  선택한 터미널(명령줄 또는 콘솔이라고도 함)을 엽니다. 이 예에서는 로컬 Windows 시스템에서 작업하고 Windows PowerShell를 터미널로 사용합니다.
    
2.  구획의 인스턴스 목록에 있는 **컴퓨트 -> 인스턴스**에서 찾을 Ubuntu 인스턴스의 공용 IP 주소를 적어 둡니다.
    
3.  터미널에서 Ubuntu 컴퓨트 인스턴스 생성 중 사용한 SSH 키 쌍이 상주하는 폴더로 이동합니다. 이 경우 폴더를 _ssh_라고 합니다.
    

       <copy>cd .ssh</copy>
    

4.  다음 명령을 입력하여 _private-key_를 OpenSSH 개인 키로, _public ip_를 Ubuntu 인스턴스의 공용 IP 주소로 대체해야 하는 Ubuntu 컴퓨트 인스턴스에 액세스합니다.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## 작업 2: Ubuntu 인스턴스에서 VNC 설정 및 구성

1.  터미널에서 아래 명령을 실행하여 VNC 서버를 설치합니다.

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  데스크탑 환경을 설치합니다. 다음 명령을 입력하여 Xfce 데스크탑 환경을 설치하지만 다른 데스크탑 환경을 사용하도록 선택할 수 있습니다.

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  vncpasswd 명령을 실행하여 VNC 서버에 대한 암호를 설정합니다.

       <copy>vncpasswd</copy>
    

프롬프트가 나타나면 암호를 입력하고 다시 입력하여 확인합니다.

4.  다음 명령을 사용하여 VNC Server를 시작합니다.

       <copy>vncserver :1</copy>
    

다음 출력이 제공됩니다. ![](images/vncserver.PNG " ")

5.  다음 명령을 사용하여 VNC 서버 종료:

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  VNC 구성 파일은 ~/.vnc/xstartup에 있습니다. 원하는 텍스트 편집기로 편집하세요. 우리는 vim을 사용합니다.

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  파일 끝에 다음 라인을 추가합니다.

       <copy>exec /usr/bin/startxfce4 &</copy>
    

vim으로 작업하는 경우: **A**를 눌러 끝에 텍스트를 추가하고 위 명령을 복사합니다. ESC 키를 눌러 명령 모드로 전환합니다. **:wq** -를 입력하여 저장하고 종료합니다.

8.  다음 명령을 실행하여 VNC Server를 시작합니다.

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  다음 명령을 실행하여 인스턴스에 대한 SSH 접속을 닫습니다.

       <copy>exit</copy>
    

## 작업 3: VNC 데스크탑에 연결

VNC 클라이언트(예: TigerVNC) 및 SSH 터널링을 사용하여 원격 데스크탑에 연결할 수 있습니다.

1.  다음 명령을 사용하여 VNC 서버에 대한 SSH 터널을 생성합니다. 여기서 private-key는 전용 OpenSSH 키이고 public\_ip은 Ubuntu 컴퓨트 인스턴스의 전용 IP 주소입니다.

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  이 경우 TigerVNC가 될 vncviewer 클라이언트를 설치합니다.

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  로컬 시스템에 VNC 클라이언트(예: TigerVNC)를 다운로드하여 설치합니다. VNC 클라이언트를 사용하여 **127.0.0.1:5901**에 연결합니다. ![](images/tigervnc.PNG " ")
    
4.  암호를 묻는 메시지가 표시되면 VNC 서버 구성 중 생성된 암호를 입력합니다. ![](images/tigervncpwd.PNG " ")
    

원격 Ubuntu 데스크톱이 열립니다. ![](images/vncdesktop.PNG " ")

## **확인**

*   **작성자/날짜** - Maria Patelkou, HPC 솔루션 아키텍트, Oracle Proposal to Production 프로그램, 2021년 3월
*   **최종 업데이트 수행자/날짜** - Maria Patelkou, HPC 솔루션 아키텍트, Oracle Proposal to Production 프로그램, 2021년 3월