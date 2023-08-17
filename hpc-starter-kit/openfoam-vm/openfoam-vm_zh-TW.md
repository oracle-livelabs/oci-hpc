# 在 OCI 上的標準 VM 中執行 OpenFoam 專案

## 簡介

此實驗室的目的是協助評估 Oracle Cloud Infrastructure 中的 OpenFOAM CFD 軟體。它會自動下載和設定 OpenFOAM。OpenFOAM 是自 2004 年起發行並主要由 OpenCFD Ltd 開發的免費開源 CFD 軟體。它在商業和學術組織大部分的工程和科學領域都有一個大型的使用者群。OpenFOAM 提供多種功能，可解決任何涉及化學反應、亂流和熱傳遞、聲學、固體力學和電磁學的複雜流體流動。

這個實驗室會逐步引導您將 OpenFoam 安裝在基本的 VM.Standard 2.1 資源配置中，但若為實際部署，則需要 BM.HPC2.36 等較大的資源配置。部分高效能資源配置僅適用於特定區域和可用性網域。

我們將為此實驗室建立兩個節點、公用子網路上的叢集節點，以及專用子網路中的工作者運算節點。為了存取工作節點，我們會先建立 headnode，然後在 headnode 上產生 ssh 金鑰，在建立工作節點時使用該公開金鑰。

若要**日誌問題**，請按一下[此處](https://github.com/oracle/learning-library/issues/new)以前往 github oracle 儲存區域問題送出表單。

預估實驗室時間：60 分鐘

### 目標

身為開發人員，資料工程師，

1.  手動部署高效能運算執行處理
2.  建立叢集網路
3.  建立網路檔案系統
4.  設定 VNC
5.  安裝 OpenFoam 和 Paraview

### 必要條件

1.  具有建立執行處理 VM 標準 2.1 或 BM.HPC2.36 資源配置之權限的 Oracle Cloud Infrastructure 帳戶。

### 基礎架構術語

*   工作者節點：提供執行運算模擬工作負載或其他工程工作負載之處理能力的 HPC 運算執行處理。在這個示範運算型態 BM.HPC2.36 節點中，是工作節點。
*   Head node：可排定並送出要在 Worker 節點上執行之所有運算工作的運算執行處理。「標頭」節點和「堡壘主機」節點可以多次使用相同機器。針對此示範，我們正在佈建堡壘主機節點。
*   堡壘主機節點：使用公用 IP 的運算執行處理，會作為連線至一般位於專用網路中工作節點的進入點。
*   RoCE 網路：RDMA over Converged Ethernet (RoCE) 是一種網路協定，允許透過乙太網路進行遠端直接記憶體存取 (RDMA)。

## 任務 1：佈建 Oracle 虛擬雲端網路

1.  建立執行處理之前，必須先設定虛擬雲端網路。選取左上角的功能表，然後選取「網路與虛擬雲端網路」![市場](images/vcn.png) ![市場](images/create_vcn.png) ![市場](images/vcn_content.png)
    
2.  在下一頁，為您的 VCN 選取下列項目：
    
    *   名稱
    *   區間
    *   CIDR 區塊 (範例：10.0.0.0/16)
3.  捲動到底端，然後按一下「建立 VCN」
    
4.  建立子網路
    
    1.  公用子網路
        *   名稱：也就是 hpc\_public
        *   子網路類型：區域
        *   CIDR 區塊：10.0.0.0/24
        *   路由表：「預設路由表」
        *   子網路存取：公用子網路
        *   安全清單：「預設安全清單」
    2.  專用子網路
        *   名稱：hpc\_private
        *   子網路類型：區域
        *   CIDR 區塊：10.0.3.0/24
        *   路由表：選取上一個步驟中建立的路由表
        *   子網路存取：專用子網路
        *   安全清單：選取上一個步驟中建立的安全清單
5.  按一下**建立子網路** ![市場](images/create_subnet.png) ![市場](images/create_subnet_content.png)
    
6.  建立網際網路閘道
    
    1.  按一下您建立的 `hpc_vcn`，然後在頁面左側的**資源**功能表上，選取**網際網路閘道**，建立網際網路閘道。![市場](images/create_IG.png) ![市場](images/create_IG_content.png)

注意：這將會建立網際網路閘道，必須與路由表關聯。在此情況下，由於「預設路由表」將用於公用子網路，因此網際網路閘道應該與該路由表關聯。

7.  將路由規則新增至路由表。在頁面左側的**資源**功能表上，選取**`hpc_vcn` 的預設路由表**，然後按一下**新增路由規則**
    *   目標類型：網際網路閘道
    *   目的地 CIDR 區塊：0.0.0.0/0
    *   區間中的目標網際網路閘道：您建立的網際網路閘道 ![市場](images/route_table.png) ![市場](images/route_rules.png)

## 作業 2：建立叢集節點

我們將為此實驗室建立兩個節點，即公用子網路上叢集的 **Headnode** ，以及專用子網路中的**工作者運算節點**。為了存取工作節點，我們會先建立 headnode，然後在 headnode 上產生 ssh 金鑰，在建立工作節點時使用該公開金鑰。

注意：針對此實驗室，我們只會運用基本 VM.Standard2.1 資源配置，但對於實際部署，較大的資源配置 (例如 BM.HPC2.36) 則適用。部分高效能資源配置僅適用於特定區域和可用性網域。

1.  #### 建立 Headnode
    
    *   名稱：也就是 hpc\_head
    *   映像檔或作業系統：最新版本的 Oracle Linux (預設)
    *   可用性網域：可供 VM.Standard 2.1 資源配置使用的網域
    *   執行處理資源配置：
        *   VM.Standard 2.1 版
        *   其他任何型態 (BM.HPC2.36 偏好 (若有的話)
    *   虛擬雲端網路：以前建立 VCN
    *   子網路：之前建立的公用子網路
    *   SSH 金鑰：連附您的公開金鑰檔
    
    1.  在左上方的功能表上，選取「運算」並建立執行處理。![市場](images/compute.png) ![市場](images/compute_bm.png) ![市場](images/compute_vm.png)
        
    2.  SSH 進入您的 headnode，並產生可供叢集進行通訊的 ssh 金鑰。
        
            ```
            $ ssh -i <ssh_key> opc@<headnode_ip_address>
            
            $ ssh-keygen
            ```
            Note: Do not change the ssh key file location (/home/opc/.ssh/id_rsa) and hit enter when asked about a passphrase (twice).
            
            1. Run and Copy the whole string, which will be used in creating the worker node
            
            ```
            $ cat ~/.ssh/id_rsa.pub
            ```
            
2.  **建立工作節點**
    
    1.  在左上方的功能表上，選取「運算」，然後使用下列詳細資訊建立執行處理：
        
        *   名稱：也就是 hpc\_worker1
        *   映像檔或作業系統：最新版本的 Oracle Linux (預設)
        *   可用性網域：可供 VM.Standard 2.1 資源配置使用的網域
        *   執行處理資源配置：
            *   VM.Standard 2.1 版
            *   其他任何型態 (BM.HPC2.36 偏好 (若有的話)
        *   虛擬雲端網路：以前建立 VCN
        *   子網路：之前建立的專用子網路
        *   SSH 金鑰：從上一個步驟複製的公開金鑰字串。
    2.  SSH 進入工作節點。  
        返回已登入標頭節點的主控台，然後從標頭節點將專用 IP 位址與 ssh 帶入工作節點
        
            ```
            $ ssh opc@10.x.x.x
            ```
            

## 作業 3：設定 NAT 閘道

\*\* 請注意，此僅適用於工作節點 \*\*  

1.  選取工作節點，然後按一下左側**資源**功能表中的**連附 VNIC**
2.  選取**編輯 VNIC**
3.  取消勾選**略過來源 / 目的地檢查**，勾選**更新 VNIC** ![市場](images/nat_gateway.png)

## 作業 4：掛載磁碟機

注意：只有在節點資源配置有連附的 NVMe (BM.HPC2.36 有一個，不是 VM.Standard2.1) 時，HPC 機器有本機 NVMe 儲存體，但預設不會掛載此儲存體。如果使用 VM.Standard2.1，請跳至步驟 5

1.  SSH 登入您的 headnode，執行下方命令

    ```
     $ lsblk
    ```
    The drive should be listed with the NAME on the left (Probably nvme0n1, if it is different, change it in the next commands)
    

headnode 將有安裝和模型的共用磁碟機。這會在所有不同的工作節點之間共用。每個工作節點也會掛載要在 NVMe 磁碟機本機執行的磁碟機。在此範例中，共用目錄將會是 500 GB，但是可以自由變更該目錄。

2.  在工作節點上分割磁碟機 (選擇性)

    ```
     $ sudo yum -y install gdisk
    ```
    ```
     $ sudo gdisk /dev/nvme0n1
     $ > n    # Create new partition
     $ > 1    # Partition Number
     $ >      # Default start of the partition
     $ > +500G # Size of the shared partition
     $ > 8300  # Type = Linux File System
     $ > n     # Create new partition
     $ > 2     # Partition Number
     $ >       # Default start of the partition
     $ >       # Default end of the partition, to fill the whole drive
    
     $ > 8300  # Type = Linux File System 
    
     $ > w      # Write to file
     $ > Y      # Accept Changes
    ```
    

3.  設定工作節點的磁碟機格式

    ```
    $ sudo mkfs -t ext4 /dev/nvme0n1
    ```
    

4.  格式化 headnode `sudo mkfs -t ext4 /dev/nvme0n1p1 sudo mkfs -t ext4 /dev/nvme0n1p2` 上的分割區
    
5.  建立目錄並掛載磁碟機
    
6.  Headnode (本機與共用)：選取 /mnt/share 作為 500G 分割區的掛載目錄，然後選取 /mnt/local 作為較大的分割區。
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1p1 /mnt/share
         sudo chmod 777 /mnt/share
         sudo mount /dev/nvme0n1p2 /mnt/local
         sudo chmod 777 /mnt/local
        
        
7.  Headnode (share) :
    
         sudo mkdir /mnt/share
         sudo mount /dev/nvme0n1 /mnt/share
         sudo chmod 777 /mnt/share
        
8.  工作節點：選取 /mnt/local 作為整個磁碟機的掛載目錄。
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1 /mnt/local
         sudo chmod 777 /mnt/local
        

## 作業 5：建立網路檔案系統

1.  建立掛載目標。在左上角的功能表上，按一下**檔案儲存 (File Storage)** 和**掛載目標 (Mount Target)** 。

    - New Mount Target Name: Enter a name (example: openfoam_fs)
    - Virtual Cloud Network: Select the VCN created above
    - Subnet: Select the public VCN
    

2.  Headnode 在公用子網路中，我們將會讓防火牆保持在最新狀態，並透過下列方式新增例外：  
    

    ```
    sudo firewall-cmd --permanent --zone=public --add-service=nfs
    sudo firewall-cmd --reload
    ```
    

3.  按一下已建立的掛載目標，然後按一下匯出路徑，再按一下掛載命令，然後複製這些應該如下的命令：`sudo yum install nfs-utils sudo mkdir -p /mnt/share sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share`
    
4.  在工作節點上，由於它們位於具有限制存取安全清單的專用子網路，因此我們可以完全停用防火牆。接著，我們就可以安裝 nfs-utils 工具並掛載 nfs，就像我們上述一樣。
    

    ```
    sudo systemctl stop firewalld
    sudo yum -y install nfs-utils
    sudo mkdir  -p /mnt/share
    sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share
    ```
    

## 作業 6：安裝 OpenFOAM

1.  **連線所有工作節點**  
    

    Each worker node needs to be able to talk to all the worker nodes. SSH communication works but most applications have issues if all the hosts are not in the known host file. To disable the known host check for nodes with address in the VCN, you can deactivate with the following commands. You may need to modify it slightly if your have different addresses in your subnets. Put the following code block in a shell script and run the script. 
    
        ```
        for i in 0 1 2 3
        do
            echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
            echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done
        ```
    

2.  **建立機器清單**  
    

    OpenFOAM on the headnode does not automatically know which compute nodes are available. You can create a machinefile at `/mnt/share/machinelist.txt with` the private IP address of all the nodes along with the number of CPUs available. The headnode should also be included. The format for the entries is `private-ip-address cpu=number-of-cores`
    
        ```
        10.0.0.2 cpu=1
        10.0.3.2 cpu=1
        ```
    

3.  **Headnode**  
    

    Install from sources, modify the path to the tarballs in the next commands. This example has the foundation OpenFOAM sources. OpenFOAM from ESI has also been tested. To share the installation between the different compute nodes, install on the network file system.
    
    
        ```
        sudo yum groupinstall -y 'Development Tools'
        sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        cd /mnt/share
        wget -O - http://dl.openfoam.org/source/7 | tar xvz
        wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        mv OpenFOAM-7-version-7 OpenFOAM-7
        mv ThirdParty-7-version-7 ThirdParty-7
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        cd /mnt/share/OpenFOAM-7
        ./Allwmake -j
    
        ```
    

4.  **工作節點**  
    
        sudo yum -y install openmpi openmpi-devel
        cd /mnt/share
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        
        

## 作業 7：執行模擬工作負載並轉譯輸出

1.  在 Headnode 上，執行下列使用 Paraview 套裝程式轉換輸出所需的命令。
    
         ```
         $ sudo yum install -y mesa-libGLU
         $ cd /mnt/share
         $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php > file.tar.gz
         $ tar -xf file.tar.gz
         ```
        
2.  在 headnonde 上執行這些指令來設定 VNC 伺服器：  
    
        $ sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver
        

3.  使用其中一個工作節點之 /mnt/share/work 中的 [scripts](../scripts/motorbike_RDMA.tgz) 下載此壓縮檔。使用 `tar -xf motorbike_RDMA.tgz` 解壓縮檔案
    
4.  在執行工作負載之前，我們必須編輯 allrun 檔案，以符合我們建立的架構。首先，我們將從 OpenFOAM 安裝程式資料夾移動資料夾
    
         ```
         $ model_drive=/mnt/share
         $ sudo mkdir $model_drive/work
         $ sudo chmod 777 $model_drive/work
         $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
         $ cd /mnt/share/work/motorBike/system
         ```
        
5.  編輯檔案系統 /decomposeParDict，並將此行 numberOfSubdomains 6；變更為 numberOfSubdomains 12；或將需要多少處理程序。然後在 hierarchicalCoeffs 區塊中，將 n 從 n (3 2 1)；變更為 n (4 3 1)；如果將 3 個值相乘，則會得到 numberOfSubdomains
    

以 1 個 VM.Standard2.1 工作節點的組態執行：

      =========                 |
      \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
       \\    /   O peration     | Website:  https://openfoam.org
        \\  /    A nd           | Version:  7
         \\/     M anipulation  |
    \*---------------------------------------------------------------------------*/
    FoamFile
    {
        version     2.0;
        format      ascii;
        class       dictionary;
        object      decomposeParDict;
    }
    
    // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
    
    numberOfSubdomains 2;
    
    method          hierarchical;
    // method          ptscotch;
    
    simpleCoeffs
    {
        n               (4 1 1);
        delta           0.001;
    }
    
    hierarchicalCoeffs
    {
        n               (2 1 1);
        delta           0.001;
        order           xyz;
    }
    
    manualCoeffs
    {
        dataFile        "cellDecomposition";
    }
    
    
    // ************************************************************************* //
    

6.  下次編輯 /mnt/share/work/motorBike 中的 Allrun 檔案，如下所示：\`\`\` #！/bin/sh cd ${0%/\*} || exit 1 # 從此目錄執行 NP=$1 install\_drive=/mnt/share # 來源教學執行函數 . $WM\_PROJECT\_DIR/bin/tools/RunFunctions
    
         # Copy motorbike surface from resources directory
         cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
         cp $install_drive/machinelist.txt hostfile
        
         runApplication surfaceFeatures
        
         runApplication blockMesh
        
         runApplication decomposePar -copyZero
         echo "Running snappyHexMesh"
         mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.snappyHexMesh
         ls -d processor* | xargs -I {} rm -rf ./{}/0
         ls -d processor* | xargs -I {} cp -r 0 ./{}/0
         echo "Running patchsummary"
         mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
         echo "Running potentialFoam"
         mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
         echo "Running simpleFoam"
         mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam
        
         runApplication reconstructParMesh -constant
         runApplication reconstructPar -latestTime
        
         foamToVTK
         touch motorbike.foam
         ```
        
7.  請確定您位於工作節點，並執行工作負載：`$ ssh worker_node_IP $ cd /mnt/share/work/ $ ./Allrun 2`
    
         ```
         $ ./Allrun 2
         $ Cleaning /mnt/share/work case
         $ Mesh Dimensions: (40 16 16)
         $ Cores:36: 6, 6, 1
         $ Running surfaceFeatures on /mnt/share/work
         $ Running blockMesh on /mnt/share/work
         $ Running decomposePar on /mnt/share/work
         $ Running snappyHexMesh
         $ Running patchsummary
         $ Running potentialFoam
         $ Running simpleFoam
         $ Running reconstructParMesh on /mnt/share/work
         $ Running reconstructPar on /mnt/share/work
         219.95
        
         ```
        

8.  順利完成工作負載之後，請在您的機器上設定 VNC 用戶端，就像這樣。提供堡壘主機伺服器和 VNC 連接埠的公用 IP ![市場](images/24-VNC_Connection.png)

9.  選擇性，如果您不允許開啟 VNC 連接埠 5901，或是基於安全理由想讓 SSH 通道使用這個連接埠，請使用下列命令在連接埠 5901 上執行 SSH 通道，而不開啟安全清單中的連接埠
    
10.  使用終端機視窗中的下列指令，從筆記型電腦 / 桌面建立通道。連接埠 5901 的此處通訊將透過 ssh 連接埠 22 進行，IP 位址 150.136.41.3 則是堡壘主機伺服器的公用 IP 位址。
    
        ```
        ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3
        ```
        
11.  請勿關閉上述 ssh 通道終端機視窗。現在起，開始 VNC 工作階段，這次不使用 IP 位址，也就是使用 port 5901 上的 "localhost"，即使這個連接埠並未在子網路的安全清單中開啟 。 ![市場](images/28-ssh_Tunnel.png)
    

12.  從堡壘主機伺服器內啟動 Paraview 應用程式
    
        ```
        $ cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview
        ```
        
13.  在 Paraview 應用程式視窗中，\[File (檔案) -> Open (開啟) -> Path "/mnt/gluster-share/work"\] 並選取檔案名稱 motorbike.foam。檔案必須是 0 位元組，而且應該正確 。 ![市場](images/25-Paraview_Menu.png)
    

14.  在視窗的左邊，在「特性」頁籤下，選取「網格區域」以選取所有值，然後取消選取開頭不是 motorBike\_ 前置碼的頂端值。確定已選取所有以 motorBike\_ 開頭的值。點擊 套用 鍵，有些錯誤會跳出，忽略彈出視窗來檢視 VNC 主控台中的影像顯示結果 。 ![市場](images/26-Mesh_Regions.png)

15.  螢幕上將顯示如下的影像。根據某些顯示設定，畫面上的影像看起來可能有點不同。 ![市場](images/27-Image_Rendering.png)

全部完成！這會完成在 OCI 上的標準 VM 上執行 OpenFoam 應用程式的示範。

_恭喜！您已順利完成研習_

這些是管理高效能運算執行處理的詳細資訊。如需完整的命令參考資訊，請參閱[此處](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)的 OCI 文件。

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者 / 日期** - Harrison Dvoor，2020 年 10 月

# <<<<<<< 標頭

> > > > > > > 上游 / 主軸