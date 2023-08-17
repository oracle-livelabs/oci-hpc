# 在 HPC 叢集中執行 OpenFoam 專案

## 簡介

設定 HPC 硬體並在最上方部署應用程式軟體可能需要很長的時間，並且包含各種複雜的作業。透過此示範，您可以在 OCI 市集映像檔內自動化所有這些作業，並在佈建之後立即提供完整運作的 HPC 環境以及 OpenFoam 模擬軟體。所需的一切都是執行功能完整的 HPC 環境的工作負載，然後執行像 Paraview 這樣的視覺化應用程式來產生輸出。

預估時間：60 分鐘

### 參照架構

![](./images/29_Architecture.png " ")

### 關於 OpenFoam

計算流體動力學 (CFD) 是使用數值分析模擬流體運動與熱傳遞。CFD 軟體 (例如 OpenFOAM) 可作為許多工程產業的強大工具，以改進設計、視覺化影響及偵測系統中的無效率。不過，處理模擬所需的運算能力相當耗時。超級電腦和 HPC 叢集通常用來在合理的時間範圍內執行大型和複雜的模型。您將使用 Oracle HPC 叢集內的開放原始碼 CFD 軟體 OpenFOAM 來計算和彩現機車的空氣動力模型

### 基礎架構術語

1.  工作者節點：提供執行運算模擬工作負載或其他工程工作負載之處理能力的 HPC 運算執行處理。在這個示範運算型態 BM.HPC2.36 節點中，是工作節點。
    
2.  Head node：可排定並送出要在 Worker 節點上執行之所有運算工作的運算執行處理。「標頭」節點和「堡壘主機」節點可以多次使用相同機器。針對此示範，我們正在佈建堡壘主機節點。
    
3.  GPU 節點：具有 GPU 處理器的專用運算執行處理，以呈現運算工作負載的輸出。此示範未佈建 GPU 機器。
    
4.  堡壘主機節點：使用公用 IP 的運算執行處理，會作為連線至一般位於專用網路中工作節點的進入點。
    
5.  RoCE 網路：RDMA over Converged Ethernet (RoCE) 是一種網路協定，允許透過乙太網路進行遠端直接記憶體存取 (RDMA)。
    

### 目標

1.  使用 OCI 市集映像檔佈建功能完整的 HPC 環境
    
2.  部署名稱為 OpenFoam 的 CFD 模擬應用程式
    
3.  執行 Openfoam 工作負載
    
4.  使用 Paraview 視覺化應用程式轉譯輸出。
    

### 必要條件

請確定具有下列項目的存取權：

1.  使用下列原則授權運算服務代表您管理標記命名空間：
    
    「允許 compute\_management 服務在租用戶中使用 tag-namespace」
    
2.  此示範中只允許使用裸機資源配置 BM.HPC2.36。同一個可用性網域中至少需要 2 個運算資源配置。
    
3.  堡壘主機伺服器的 VM 運算映像檔。不需要與 HPC 節點位於相同的可用性網域中。
    
4.  使用者證明資料必須要有配額，才能建立或使用現有的 VCN 與專用和公用子網路。
    
5.  存取市集影像「CFD Ready Cluster」。此 DEMO 中使用的市集影像版本為 (版本：20200625)。
    

## 作業 1：啟動市集影像

1.  使用者啟動市集映像檔之前，請先檢查有 2 個或更多 HPC 節點可用資源配置 BM.HPC2.36 的可用性網域。前往**功能表** -> **治理** -> **限制**，**配額和用量**
    
    ![](./images/01-Service_Limits.png " ")
    
2.  前往**功能表** -> **市集** -> **應用程式**
    
    ![](./images/02-Marketplace.png " ")
    
3.  在搜尋方塊中，鍵入 "cfd"
    
    ![](./images/03-Search_marketplace.png " ")
    
4.  按一下「映像檔」，然後選取該映像檔，然後按一下**啟動堆疊**按鈕。請確認您位於正確的區間中。
    
    ![](./images/04-Launch_Stack.png " ")
    
5.  啟動堆疊之後，請填入詳細資訊以建立資源，包括 HPC 工作節點、堡壘主機伺服器、網路元件、VCN 伺服器。此堆疊也會要求輸入值部署套裝軟體 (例如 openMPI 和 openFoam)。
    
6.  請在下方提供堆疊的名稱
    
    ![](./images/05-Create_Stack01.png " ")
    
7.  選取有可供佈建之 HPC 節點的可用性網域。提供連線至堡壘主機伺服器所需的 ssh 公開金鑰。
    
    ![](./images/06-Create_Stack02.png " ")
    
8.  選取可用性網域以佈建堡壘主機伺服器。可以與 HPC 節點上的可用性網域不同。選取您選擇的堡壘主機資源配置。勾選此項以在堡壘主機伺服器上安裝 VNC 伺服器，並提供之後要進行 VNC 連線所需的密碼。
    
    ![](./images/07-Create_Stack03.png " ")
    
9.  對於「工作者」節點，選取的唯一選項為 BM.HPC2.36。您至少需要 2 個 HPC 運算資源配置，才能完成佈建。請在下方畫面中選取 2 個或更多個運算節點。將所有選項還原為預設值。
    
    ![](./images/08-Create_Stack04.png " ")
    
10.  在「網路 (Network)」選項下，使用堆疊中傳送的預設值建立新 VCN。此示範會透過此堆疊建立新的 VCN。不過，您也可以選擇使用現有的 VCN。
    
    ![](./images/09-Create_Stack05.png " ")
    
11.  對於檔案系統區段，請使用下列選項建立 NFS 掛載並在上面安裝 gluster 檔案系統。
    
    ![](./images/10-Create_Stack06.png " ")
    
12.  按一下「INSTALL OPENFOAM」核取方塊即可在此 HPC 堆疊中安裝 OpenFoam 應用程式。
    
    ![](./images/11-Create_Stack07.png " ")
    
13.  在堆疊的「最後一頁」驗證輸入的詳細資訊，如果所有詳細資訊都正確無誤，請按一下「建立」按鈕開始佈建基礎架構和軟體安裝。
    
    ![](./images/12-Create_Stack08.png " ")
    
14.  驗證 HPC 堆疊安裝進度
    
    ![](./images/13-StackJob.png " ")
    
15.  在大約 20 分鐘的時間內完成佈建之後，日誌檔應該包含下列訊息。您可以在「功能表 -> 資源管理程式 -> 堆疊」(按一下您的堆疊名稱) -> 工作 (按一下工作名稱連結以檢視完整日誌) 找到日誌
    
    ![](./images/14-StackJobDetails.png " ")
    

## 作業 2：驗證設定

1.  按一下畫面左側的「關聯資源」連結，即可查看此堆疊所佈建的基礎架構資源。您可以在下列位置找到關聯資源：**功能表** -> **資源管理程式** -> **堆疊** (按一下堆疊名稱) -> **工作** (按一下工作名稱連結) -> **關聯資源**
    
    ![](./images/15-Stack_Resources.png " ")
    
2.  在**運算** -> **執行處理**底下，將佈建 3 個運算執行處理、專用網路中的 2 個 HPC 節點，以及配備公用 IP 位址的 1 個堡壘主機伺服器。
    
    ![](./images/16-Compute_Details.png " ")
    
3.  使用公開金鑰上傳在上方的堆疊中的 SSH 私密金鑰，以 opc 使用者身分連線至 Bastion 伺服器。使用此方法從 Linux/Mac 桌面連線。若為 Windows 桌面，請使用 Putty 並提供 ppk 私密金鑰以進行連線。
    
    ![](./images/17-Bastion_ssh.png " ")
    
4.  確認掛載於 2 個 HPC 節點和堡壘主機伺服器上的共用 NFS 儲存體。此 NFS 儲存體包含用來執行工作負載的 OpenFoam 軟體。
    
    ![](./images/18-Bastion_storage.png " ")
    
5.  OpenFoam 應用程式是以 "/mnt/gluster-share/work" 資料夾代管。檢查包含將執行工作負載之 2 個 HPC 節點的優先順序 IP 位址的主機檔案。
    
    ![](./images/19-Bastion_openfoam.png " ")
    
6.  以下的輸出顯示一個執行中的 VNC 伺服器使用連接埠 5901 。此連接埠將用於建立堡壘主機的 VNC 連線。
    
    ![](./images/20-Bastion_VNCdetails.png " ")
    
7.  請從「堡壘主機」伺服器的安全清單檢查允許的傳入規則連接埠 5901. 如果 VNC 在任何其他連接埠上執行，且該連接埠不允許用於安全清單中的傳入流量，請為該連接埠建立一個新的安全規則。
    
    ![](./images/21-SecList_OpenVNC_Port.png " ")
    
8.  驗證 HPC 節點的名稱。
    
    ![](./images/22-ListHPCNodes.png " ")
    

## 作業 3：執行模擬工作負載並轉譯輸出

1.  在堡壘主機伺服器上，執行下列使用 Paraview 套裝程式呈現輸出所需的命令。
    
        <copy>$ sudo yum install -y mesa-libGLU
        $ cd /mnt/gluster-share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" https://www.paraview.org/paraview-downloads/download.php >file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  從堡壘主機伺服器連線至其中一個 HPC 節點並執行工作負載
    
        <copy>$ ssh 172.16.1.2
        $ cd /mnt/gluster-share/work/
        $ ./Allrun 36</copy>
        
    
        ./Allrun 36
        Cleaning /mnt/gluster-share/work case
        Mesh Dimensions: (40 16 16)
        Cores:36: 6, 6, 1
        Running surfaceFeatures on /mnt/gluster-share/work
        Running blockMesh on /mnt/gluster-share/work
        Running decomposePar on /mnt/gluster-share/work
        Running snappyHexMesh
        Running patchsummary
        Running potentialFoam
        Running simpleFoam
        Running reconstructParMesh on /mnt/gluster-share/work
        Running reconstructPar on /mnt/gluster-share/work
        219.95
        [opc@inst-quqyz-accurate-swan work]$
        
3.  順利完成工作負載之後，請在您的機器上設定 VNC 用戶端，就像這樣。提供堡壘主機伺服器和 VNC 連接埠的公用 IP
    
    ![](./images/24-VNC_Connection.png " ")
    
4.  選擇性，如果您不允許開啟 VNC 連接埠 5901，或是基於安全理由想讓 SSH 通道使用這個連接埠，請使用下列命令在連接埠 5901 上執行 SSH 通道，而不開啟安全清單中的連接埠
    
    使用終端機視窗中的下列指令，從筆記型電腦 / 桌面建立通道。連接埠 5901 的此處通訊將透過 ssh 連接埠 22 進行，IP 位址 150.136.41.3 則是堡壘主機伺服器的公用 IP 位址。
    
        <copy>$ ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
5.  請勿關閉上述 ssh 通道終端機視窗。現在起，開始 VNC 工作階段，這次不使用 IP 位址，也就是使用 port 5901 上的 "localhost"，即使這個連接埠並未在子網路的安全清單中開啟 。
    
    ![](./images/28-ssh_Tunnel.png " ")
    
6.  從堡壘主機伺服器內啟動 Paraview 應用程式
    
        <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        ./paraview</copy>
        
7.  在 Paraview 應用程式視窗中，\[File (檔案) -> Open (開啟) -> Path "/mnt/gluster-share/work"\] 並選取檔案名稱 motorbike.foam。檔案必須是 0 位元組，而且應該正確 。
    
    ![](./images/25-Paraview_Menu.png " ")
    
8.  在視窗的左邊，在「特性」頁籤下，選取「網格區域」以選取所有值，然後取消選取開頭不是 motorBike\_ 前置碼的頂端值。確定已選取所有以 motorBike\_ 開頭的值。點擊 套用 鍵，有些錯誤會跳出，忽略彈出視窗來檢視 VNC 主控台中的影像顯示結果 。
    
    ![](./images/26-Mesh_Regions.png " ")
    
9.  螢幕上將顯示如下的影像。根據某些顯示設定，畫面上的影像看起來可能有點不同。
    
    ![](./images/27-Image_Rendering.png " ")
    

全部完成！這會完成在 HPC 叢集上執行 OpenFoam 應用程式的示範。

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者 / 日期** - Harrison Dvoor，2021 年 1 月