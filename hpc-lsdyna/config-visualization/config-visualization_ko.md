# 시각화 구성

## 소개

HPC 워크로드에는 시뮬레이션의 출력을 스케줄링, 모니터링 또는 분석하기 위한 시각화 도구가 필요한 경우가 많습니다. 이러한 시나리오에서는 최적의 해상도 및 사후 처리를 위해 GPU 시각화 노드를 생성하는 것이 좋습니다. GUI는 기본적으로 OCI 인스턴스에 설치되지 않지만, VNC 또는 X11 원격 표시 프로토콜을 사용하여 쉽게 구성할 수 있습니다. 아래 하위 섹션에서는 TurboVNC 및 OpenGL를 사용하여 공용 서브넷에 GPU 시각화 노드를 생성하는 방법을 안내합니다.

예상 실습 시간: 25분

## 작업 1: 배스천에 VNC 설정

기본적으로 Oracle Linux 시스템에 대한 액세스 권한은 콘솔 모드에서 SSH를 통해서만 가능합니다. 그래픽 인터페이스를 보려면 VNC 연결을 설정해야 합니다. 다음 스크립트는 기본 사용자 opc에서 작동합니다. vnc 세션의 암호는 "HPC\_oci1"로 설정되지만 다음 명령 세트에서 편집할 수 있습니다. SSH를 통해 현재 헤드노드에 연결되지 않은 경우 헤드노드에서 이러한 명령을 실행해야 하므로 연결하십시오.

        sudo yum -y groupinstall "Server with GUI"
        sudo yum -y install tigervnc-server mesa-libGL
        sudo systemctl set-default graphical.target
        sudo cp /usr/lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
        sudo sed -i 's/<USER>/opc/g' /etc/systemd/system/vncserver@:1.service
        sudo sed -ie '/^ExecStart=/a PIDFile=/home/opc/.vnc/%H%i.pid' /etc/systemd/system/vncserver@:1.service
        sudo mkdir /home/opc/.vnc/
        sudo chown opc:opc /home/opc/.vnc
        echo "password" | vncpasswd -f > /home/opc/.vnc/passwd
        chown opc:opc /home/opc/.vnc/passwd
        chmod 600 /home/opc/.vnc/passwd
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
    

## 작업 2: GPU 인스턴스 추가

아래 단계에서는 OCI의 GPU 사용 사례 향상을 위해 OpenGL를 사용합니다. 자세한 내용은 블로그를 참조하십시오.

1.  콘솔 내에서 컴퓨트, 인스턴스로 차례로 이동합니다.
    
2.  시각화 노드에 대한 컴퓨트 인스턴스 생성:
    
    *   원하는 AD 선택
    *   원하는 GPU 구성(VM 또는 BM) 선택
    *   GPU 호환 Oracle Linux 이미지 지정 최신 Oracle Linux 이미지는 자동으로 GPU가 사용으로 설정됩니다.
    *   클러스터 네트워크 VCN 및 공용 서브넷 선택
    *   공용 SSH 키 복사-붙여넣기
    *   Create를 누릅니다.
3.  인스턴스가 프로비전될 때까지 기다린 후 다음을 통해 인스턴스에 로그인합니다.
    
        ssh opc@<public ip> -i <private key> 
        
4.  X Window System, 디스플레이 관리자(GNOME/GDM) 및 데스크탑 환경(MATE)을 설치합니다.
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  VNC 서버와 VirtualGL를 설치합니다. VirtualGL는 모든 Linux 또는 Unix 콘솔이 전체 하드웨어 가속으로 OpenGL 애플리케이션을 실행할 수 있는 오픈 소스 툴킷입니다.
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  가상 세션에 대해 GPU 공유를 사용으로 설정하도록 X 서버를 구성합니다. 다음 명령을 실행합니다.
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  가상 세션에 대해 GPU 공유를 사용으로 설정하도록 X 서버를 구성합니다. 다음 명령을 실행합니다.
    
        sudo vglserver_config -config -s -f -t
        
8.  화면 보호기가 실행될 때 잠기지 않도록 하려면 로컬 사용자 암호를 나중에 사용할 수 있는 것으로 설정합니다.
    
        sudo passwd opc
        
9.  로그온에 사용할 수 있는 항목으로 VNC 비밀번호를 변경합니다.
    
        vncpasswd
        
10.  X 서버를 재시작합니다.
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  GDM 활성화 및 시작:
    
        systemctl enable gdm --now
        
12.  VNC 서버를 실행합니다.
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  SSH 전달 없이 VNC 서버에 직접 액세스하려면 보안 목록에서 포트 5901/tcp에 대한 연결을 허용하는지 확인합니다.
    
    *   콘솔에서 네트워킹, 가상 클라우드 네트워크로 차례로 이동합니다.
    *   서브넷과 공용 서브넷을 차례로 선택합니다.
    *   기본 보안 목록에서 다음 세부 정보와 함께 수신 규칙을 추가합니다.
        *   Stateless: 없음
        *   소스 유형: CIDR
        *   소스 CIDR: 0.0.0.0/0
        *   IP 프로토콜: TCP
        *   소스 포트 범위: 모두
        *   대상 포트 범위: 5901 참고: 표준 VNC 포트는 5900에 표시 번호를 더한 값입니다(예: :1의 경우 5901, :2의 경우 5902).
14.  다음과 같이 로컬 방화벽 설정에서 액세스를 허용합니다.
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020