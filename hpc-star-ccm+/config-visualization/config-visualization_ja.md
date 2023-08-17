# ビジュアライゼーションの構成

## 概要

HPCワークロードでは、多くの場合、シミュレーションの出力をスケジュール、監視または分析するためのビジュアライゼーション・ツールが必要です。これらのシナリオでは、最適な解決と後処理のためにGPUビジュアライゼーション・ノードを作成することがよくあります。GUIはOCIインスタンスにはデフォルトではインストールされませんが、VNCまたはX11リモート表示プロトコルを使用して簡単に構成できます。次の各項では、TurboVNCおよびOpenGLを使用してパブリック・サブネットにGPUビジュアライゼーション・ノードを作成する方法について説明します。

推定ラボ時間: 25分

## タスク1: 要塞でのVNCの設定

デフォルトでは、Oracle Linuxマシンへの唯一のアクセスは、コンソール・モードでのSSH経由です。グラフィカル・インタフェースを表示する場合は、VNC接続を設定する必要があります。次のスクリプトは、デフォルトのユーザーopcに対して機能します。VNCセッションのパスワードは「HPC\_oci1」に設定されていますが、次の一連のコマンドで編集できます。現在SSH経由でヘッドノードに接続していない場合は、これらのコマンドをヘッドノードで実行する必要があるため、接続してください。

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
    

## タスク2: GPUインスタンスの追加

次のステップは、OCIでGPUユースケースを拡張するためにOpenGLを使用して実行します。詳細はブログを参照してください。

1.  コンソール内で、「Compute」→「Instances」に移動します。
    
2.  ビジュアライゼーション・ノードのコンピュート・インスタンスを作成します:
    
    *   目的のADの選択
    *   目的のGPUシェイプ(VMまたはBM)を選択します。
    *   GPU互換Oracle Linuxイメージを指定します 最新のOracle Linuxイメージは自動的にGPU対応になります。
    *   クラスタ・ネットワークVCNおよびパブリック・サブネットの選択
    *   公開SSHキーのコピー・ペースト
    *   「Create」をクリックします。
3.  インスタンスがプロビジョニングされるまで待機してから、次の方法でインスタンスにログインします。
    
        ssh opc@<public ip> -i <private key> 
        
4.  X Window System、ディスプレイマネージャー(GNOME/GDM)、およびデスクトップ環境(MATE)をインストールします。
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  VNCサーバーおよびVirtualGLをインストールします。VirtualGLは、LinuxまたはUnixコンソールでフル・ハードウェア・アクセラレーションを使用してOpenGLアプリケーションを実行できるオープン・ソースのツールキットです。
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  仮想セッションのGPU共有を有効にするようにXサーバーを構成します。次のコマンドを実行します。
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  仮想セッションのGPU共有を有効にするようにXサーバーを構成します。次のコマンドを実行します。
    
        sudo vglserver_config -config -s -f -t
        
8.  スクリーンセーバーの起動時にロックアウトされないようにするには、ローカルユーザーパスワードを後で使用できるように設定します。
    
        sudo passwd opc
        
9.  VNCパスワードを、ログオンに使用できるものに変更します。
    
        vncpasswd
        
10.  Xサーバーを再起動します。
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  GDMの有効化および起動:
    
        systemctl enable gdm --now
        
12.  VNCサーバーを起動します。
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  SSH転送なしでVNCサーバーに直接アクセスする場合は、セキュリティ・リストでポート5901/tcpでの接続が許可されていることを確認します。
    
    *   コンソールで「Networking」、「Virtual Cloud Networks」の順に移動します。
    *   「サブネット」、「パブリック・サブネット」の順に選択します。
    *   デフォルトのセキュリティ・リストに、次の詳細を含むイングレス・ルールを追加します:
        *   ステートレス: いいえ
        *   ソース・タイプ: CIDR
        *   ソースCIDR: 0.0.0.0/0
        *   IPプロトコル: TCP
        *   ソース・ポート範囲: すべて
        *   宛先ポート範囲: 5901ノート: 標準のVNCポートは5900にディスプレイ番号を加えたものです(たとえば、:1の場合は5901、:2の場合は5902)。
14.  次のように、ローカルファイアウォール設定でアクセスを許可します。
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020