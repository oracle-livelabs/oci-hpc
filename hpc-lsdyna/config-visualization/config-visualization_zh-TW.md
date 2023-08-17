# 設定視覺化

## 簡介

HPC 工作負載通常需要視覺化工具，才能排定、監督或分析模擬的輸出。在這些情況下，通常會希望建立 GPU 視覺化節點以取得最佳解析度和後續處理。OCI 執行處理預設不會安裝 GUI，不過，您可以使用 VNC 或 X11 遠端顯示協定輕鬆設定一個 GUI。下方子小節將逐步介紹如何使用 TurboVNC 和 OpenGL 在公用子網路中建立 GPU 視覺化節點。

預估實驗室時間：25 分鐘

## 作業 1：在您的堡壘主機上設定 VNC

預設只有透過主控台模式的 SSH，才能存取 Oracle Linux 機器。如果您要檢視圖形介面，您需要設定 VNC 連線 。下列指令碼將適用於預設使用者 opc。vnc 階段作業的密碼設為 "HPC\_oci1"，但可以在下一組命令中編輯。如果您目前未透過 SSH 連線至 headnode，請這麼做，因為這些指令必須在 headnode 上執行。

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
    

## 作業 2：新增 GPU 執行處理

以下步驟是使用 OpenGL 強化 OCI 上的 GPU 使用案例 - 請參閱部落格瞭解詳細資訊。

1.  在主控台中，依序瀏覽至「運算」和「執行處理」。
    
2.  建立視覺化節點的運算執行處理：
    
    *   選取想要的 AD
    *   選取想要的 GPU 資源配置 (VM 或 BM)
    *   指定 GPU 相容的 Oracle Linux 映像檔 將會自動啟用 GPU。
    *   選取叢集網路 VCN 和公用子網路
    *   複製 - 貼上您的公用 ssh 金鑰
    *   按一下「建立」。
3.  等待執行處理佈建，然後透過下列方式登入執行處理：
    
        ssh opc@<public ip> -i <private key> 
        
4.  安裝 X Window System、顯示管理員 (GNOME/GDM) 以及桌面環境 (MATE)：
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  安裝 VNC 伺服器與 VirtualGL。請注意，VirtualGL 是一種開放原始碼工具程式，可讓任何 Linux 或 Unix 主控台執行具有完整硬體加速的 OpenGL 應用程式。
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  設定 X 伺服器以啟用虛擬階段作業的 GPU 共用。執行下列命令：
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  設定 X 伺服器以啟用虛擬階段作業的 GPU 共用。執行下列命令：
    
        sudo vglserver_config -config -s -f -t
        
8.  若要避免在螢幕保護程式啟動時被鎖定，請將本機使用者密碼設定為稍後可以使用的項目：
    
        sudo passwd opc
        
9.  將您的 VNC 密碼變更為用於登入的資訊 ：
    
        vncpasswd
        
10.  重新啟動 X 伺服器：
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  啟用並啟動 GDM：
    
        systemctl enable gdm --now
        
12.  啟動 VNC 伺服器 ：
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  如果您想要直接存取 VNC 伺服器而不要使用 SSH 轉送，請確定您的安全性清單允許使用連接埠 5901/tcp 的連線。
    
    *   在主控台中，瀏覽至「網路」和「虛擬雲端網路」。
    *   選取子網路，然後選取公用子網路。
    *   在預設安全清單中，新增包含下列詳細資訊的「傳入規則」：
        *   無狀態：否
        *   來源類型：CIDR
        *   來源 CIDR：0.0.0.0/0
        *   IP 協定：TCP
        *   來源連接埠範圍：全部
        *   目的地連接埠範圍：5901 注意：標準 VNC 連接埠為 5900 加上顯示號碼 (例如：5901 代表 ：1，5902 代表 ：2)
14.  允許存取本機防火牆設定，如下所示：
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020