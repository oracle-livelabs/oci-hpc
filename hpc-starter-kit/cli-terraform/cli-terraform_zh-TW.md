# 使用 OCI CLI 命令和 Terraform 搭配 HPC 使用

## 簡介

`ocihpc` 是一種可簡化 Oracle Cloud Infrastructure (OCI) 中 HPC 應用系統部署的工具。HPC 命令行介面 (CLI) 可讓您透過專用的裸機 HPC 運算執行處理和 Mellanox RDMA 網路，使用單一命令來部署任何大小的 HPC 叢集。您不需要瞭解 Terraform 或 Oracle Cloud Infrastructure Resource Manager 即可使用 HPC CLI。它建置在核心 Oracle Cloud Infrastructure CLI 上，並包含預先建置的 Terraform 命令檔。

使用 HPC CLI 的優點包括：

1.  透過單一命令，快速啟動具備 RDMA over converged Ethernet (RoCE) v2 的完整運算叢集。
2.  易於使用 - 不知啟動基本叢集網路所需的 Terraform 或 Oracle Cloud Infrastructure Resource Manager。
3.  支援訊息傳遞介面 (MPI) 部署包含一組完整的軟體套裝，可搭配 RDMA 執行平行處理，包括 Mellanox OFED with Open MPI、Intel MPI 和 Platform MPI。
4.  可自訂 您可以執行自己的 terraform 指令碼，或新增至工具隨附的現有指令碼以安裝您自己的應用程式。

預估實驗室時間：60 分鐘

### 目標

身為開發人員或資料工程師，

1.  使用 Oracle 命令行介面部署 HPC 叢集網路
2.  透過從屬端檢視、執行及管理堆疊

### 必要條件

1.  具備足夠使用者證明資料以執行 Terraform 堆疊的 Oracle Cloud Infrastructure 帳戶。
2.  已產生的 SSH 金鑰

### 政策

您在 `ocihpc` 中使用的 OCI 使用者帳戶必須要有為 OCI 資源管理程式設定的必要原則。請參考 [此連結](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm) 以瞭解必要原則的相關資訊。

## 作業 1：在 macOS/Linux 上安裝 ocihpc

1.  使用下列指令下載最新版本，並加以解壓縮：
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  將您在步驟 1 中下載的檔案解壓縮。
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  讓 ocihpc 二進位檔可執行。
    
        $ chmod +x ./ocihpc 
        
4.  將 ocihpc 二進位檔移至您的 PATH。
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  測試它是否有效。
    
        $ ocihpc version 
        

## 作業 2：在 Windows 上安裝 ocihpc

1.  從[此連結](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip)下載最新版本並進行解壓縮。
    
2.  將 ocihpc 二進位檔案加到您的 PATH。
    
3.  測試它是否有效。
    
        $ ocihpc.exe version 
        

## 作業 3：在命令行建立 ssh 金鑰組

如果您使用視窗，請參考[此連結](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm)。

1.  開啟 shell 或終端機來輸入指令。
    
2.  瀏覽至您的 .oci 資料夾
    
        $ cd Users/enjli/.ssh
        
3.  如果尚未建立資料夾，請建立一個 .ssh 目錄以儲存證明資料：
    
        $ mkdir Users/enjli/.ssh
        
4.  如果您在資料夾中看不到任何 id\_rsa 金鑰組，請輸入下列命令並在提示時提供名稱和密碼詞組。將使用預設值建立金鑰：2048 位元的 RSA 金鑰。
    
        $ ssh-keygen
        

## 作業 4：產生 API 簽署金鑰

您的 API 要求將會使用您的私密金鑰簽署，Oracle 會使用公開金鑰來驗證要求的真實性。如果您使用視窗，請參考[此連結](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other)。

1.  如果沒有，請建立一個 .oci 目錄以儲存證明資料：
    
        $ mkdir Users/enjli/.oci
        
2.  使用下列指令產生私密金鑰。
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  確定只有您能夠讀取此私密金鑰檔案：
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  產生公開金鑰 ：
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## 作業 5：將公開金鑰新增至 Oracle Cloud Infrastructure

現在有私密 / 公開金鑰組合，您必須在使用者設定底下將其新增至 OCI 主控台：

1.  瀏覽至包含 `oci_api_key_public.pem` 的 .oci 資料夾，複製公開金鑰。
    
2.  登入您的 OCI 主控台，然後按一下「功能表」，然後選取「身分識別與使用者」。選取使用者並瀏覽至「使用者詳細資料」頁面。
    
3.  按一下「API 金鑰」區段下的「新增公開金鑰」。![市場](images/ResourcesMenu.png) ![市場](images/APIKeys.png)
    
4.  貼上您在「新增公開金鑰」中從 CLI 複製的公開金鑰 ![市場](images/AddPublicKey.png)
    

## 作業 6：設定

此步驟描述 CLI 所需的配置，並包括可讓您擴充 CLI 功能的選擇性配置。

1.  在使用 CLI 之前，您必須先在 .oci 資料夾中建立一個組態檔，其中包含使用 Oracle Cloud Infrastructure 帳戶所需的證明資料。您可以使用設定對話方塊或者使用文字編輯器手動建立此檔案。
    
2.  執行 _ocihpc configure_ 檢查您是否有存取 OCI 的有效組態。此工具將逐步引導您建立組態。
    

您將會收到配置檔寫入位置的通知：

_配置檔案儲存至：/Users/enjli/.oci/config_

若要建立配置檔案，您需要：1。您的使用者 OCID (在畫面右上角的設定檔區段中找到，使用者設定值 > 使用者資訊頁籤)，2. 租用戶 OCID (管理 > 租用戶詳細資訊 > 租用戶資訊頁籤)，3. 您工作的區域 (例如 us-phoenix-1、us-ashburn-1 等) 4. 使用者的指紋 (可在使用者資訊頁標中找到)，5. 您 API 私密簽署金鑰的路徑。

範例 `config` 檔案如下所示： ![市場](images/oci_config.png)

## 作業 7：清單

您可以執行 `ocihpc list` 取得可用堆疊的清單。

範例：

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## 作業 8：初始化

1.  建立您要用作部署來源的資料夾。

重要：每個堆疊使用不同的資料夾。請勿在相同的資料夾中初始化多個堆疊。否則，此工具將會覆寫先前的工具。

2.  變更至該資料夾並執行 `ocihpc init <stack name>`。`ocihpc` 會將必要的檔案下載至該資料夾。
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**重要事項**：執行 ocihpc deploy 命令之前，請先編輯 /Users/enjli/ocihpc-test/config.json 檔案的內容

## 作業 9：建置

1.  部署之前，您必須變更 `config.json` 檔案中的值。變數取決於您部署的堆疊。叢集網路的 `config.json` 範例如下：
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  若要修改您的 `config.json` 檔案，請瀏覽至新建立的目錄 (此例中為 ocihpc-test) 並使用 texteditor 或 notepad 開啟 "config.json" 檔案。 ![市場](images/config_json.png)
    

請注意，這與步驟 1 中設定的組態檔不同。

此設定檔需要：

1.  可用性網域資訊包含我們租用戶中的 HPC 資源 (管理 > 租用戶詳細資訊 > 向下捲動至「服務限制」區段 > \[ 運算 \] > \[ 並向下捲動以尋找 "BM.HPC2.36") - 在下面的螢幕擷取畫面中，我們共有 6 台 BM.HPC2.36 台機器可於目前使用的 AD-2、0 中使用。 ![市場](images/hpc_resource.png)
    
2.  只要有資源 (VM.standard2.1 資源配置) 堡壘主機資源配置應該已經填入 - VM.Standard2.1，堡壘主機 AD 可以是您選擇的任何 AD
    
3.  節點數目 - 在此實驗室中，我們將使用 2 個節點，以啟動所有 HPC 資源
    
4.  我們的公開 SSH 金鑰
    

**備註**

1.  變更 `config.json` 中的值之後，可以使用 `ocihpc deploy <arguments>` 部署堆疊。此命令會在 Oracle Cloud Resource Manager 建立堆疊，然後使用堆疊進行部署。
    
2.  對於支援的堆疊，您可以新增節點至 `ocihpc deploy` 指令來設定要部署的節點數目。如果堆疊不支援此堆疊，或您未提供值，工具將會使用預設數字進行部署。
    
3.  例如，下列命令將會部署含有 5 個節點的叢集網路：
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  此工具將產生包含 `<stack name>-<current directory>-<random-number>` 的部署名稱。
    
    範例：
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## 工作 10：連線

部署完成後，您會看到可以連線到的堡壘主機 / 標頭節點 IP：

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

您也可以執行 `ocihpc get ip` 指令來取得連線詳細資訊。

## 任務 11：管理

此外，您可以使用剪輯指令輕鬆管理和保留資源的追蹤：

1.  產生部署在特定區間中的所有堆疊清單：`oci resource-manager job list -c [OCID OF COMPARTMENT]`
    
2.  若要產生堆疊或區間中的工作清單，請依建立時間排序：`oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  複查工作與工作詳細資訊：`oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  以 JSON 格式檢視指定工作的日誌項目：`oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`
    
5.  若要將堆疊及其關聯的工作移至其他區間：`oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## 作業 12：刪除

完成部署後，您可以變更堆疊資料夾並執行 `ocihpc delete --stack <stack name>` 來刪除該資料夾。

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

全部完成！您已使用 OCI CLI 工具順利部署第一個高效能運算執行處理。

_您現在可以繼續進行下一個實驗室_

這是有關管理高效能運算執行處理的詳細資訊。如需完整的命令參考資訊，請參閱[此處](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)的 OCI 文件。

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者 / 日期** - Harrison Dvoor，2020 年 10 月

# <<<<<<< 標頭

> > > > > > > 上游 / 主軸