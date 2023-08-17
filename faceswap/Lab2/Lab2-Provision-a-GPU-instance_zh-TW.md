# 佈建 GPU 執行處理

## 簡介

在此實驗室中，我們將逐步介紹在 OCI 上佈建執行 Ubuntu 的 GPU 執行處理所需的步驟。

**注意：**必須在 OCI 上佈建 GPU 執行處理時，必須要有 PAYG 帳戶。如果您使用 Oracle 30 天免費試用，便可以使用所有可用的 VM 和 BM 執行處理。

預估車間時間：15 分鐘

### 目標

*   登入 Oracle Cloud
*   在 OCI 上佈建 GPU 執行處理

### 您需要什麼？

*   使用者 (使用者名稱和密碼) 是 OCI 上使用者群組的一部分。勾選[管理使用者](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm)頁面，瞭解**建立使用者**的方式，以及使用主控台**新增使用者至群組**的方式。
*   雲端帳戶名稱 - 您的租用戶名稱 (由管理員提供或您的 Oracle Cloud 歡迎電子郵件提供)
*   使用者群組。勾選[管理群組](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managinggroups.htm#three)頁面，瞭解如何使用主控台**建立群組**。
*   指定給讓使用者啟動運算執行處理之群組的原則。請查看頁面[通用原則](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm#top)，閱讀哪些原則需要**讓使用者透過主控台啟動運算執行處理**。
*   使用者的區間。瀏覽[管理區間](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingcompartments.htm#uscons)頁面，瞭解如何使用主控台**建立區間**。
*   請檢查您想要佈建 GPU 執行處理之區域中每一可用性網域允許的 GPU 執行處理數目上限。若要提高服務限制，請依照[服務限制](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/servicelimits.htm#top)頁面中的步驟進行。

## 任務 1：登入 Oracle Cloud

如果您已登出 Oracle Cloud，請使用這些步驟重新登入。

1.  請前往 [cloud.oracle.com](https://cloud.oracle.com) 並輸入雲端帳戶名稱，然後按**下一步**。這是您在上一個區段建立帳戶時所選擇的名稱。不是您的電子郵件地址。如果您忘記名稱，請參閱確認電子郵件。
    
    ![](images/cloud-oracle.png " ")
    
2.  在 _Oracle Cloud Infrastructure Direct Sign-In_ 之後展開箭頭，即可顯示登入輸入欄位。
    
    ![](images/cloud-login-tenant.png " ")
    
3.  輸入您的雲端帳戶證明資料，然後按一下**登入**。您的使用者名稱即為您的電子郵件地址。密碼是您註冊帳戶時所選擇的密碼。
    
    ![](images/oci-signin.png " ")
    
4.  您已登入 Oracle Cloud！
    
    ![](images/oci-console-home-page.png " ")
    

## 作業 2：啟動 GPU 執行處理

1.  登入之後，按一下**快速動作**區段中的**建立 VM 執行處理**按鈕。
    
    ![](images/click-create-vm-instance.png " ")
    

或者，您也可以按一下左上角的服務功能表，然後按一下 \[ **運算** \] -> \[ **執行處理** \]。新頁面隨即開啟，並顯示藍色**建立執行處理**按鈕供您按一下。

2.  填寫您的運算執行處理名稱。
    
    ![](images/fill-in-name.PNG " ")
    
3.  請從下拉式清單中選取區間。
    
    ![](images/select-compartment.PNG " ")
    
4.  在選取可用性網域之前，請先前往**治理** -> **限制、配額和用量**底下，勾選您所選區域的可用性網域可供使用 GPU 執行處理。選取將在其中佈建運算執行處理的可用性網域。
    
    ![](images/select-AD.PNG " ")
    
5.  按一下**變更影像**按鈕。
    
    ![](images/change-image.PNG " ")
    
    選取要用於運算執行處理的映像檔，然後按一下**選取映像檔**按鈕。我們將選擇由 Faceswap 建議的 Ubuntu 18。04，但任何其他 Linux 發行版和 Windows 作業系統也可能有效。
    
    ![](images/select-image.PNG " ")
    
6.  按一下**變更資源配置**按鈕。
    
    ![](images/change-shape.PNG " ")
    
    選取**虛擬機器**和**專長和傳統**。您會看到 GPU 執行處理和**永遠免費** VM.Standard.E2.1。微型資源配置 (如果您並未擁有執行 GPU 執行處理的 PAYG 帳戶)。
    
    ![](images/select-VM-SL.PNG " ")
    
    選取要佈建的 GPU 執行處理，讀取並勾選_我已複查並接受下列文件：Oracle 和 Nvidia 使用條款_方塊，然後按一下**選取資源配置**按鈕。VM.GPU2.1 以 1 個 GPU 的 P100 (NVIDIA Pascal GPU 架構) 為基礎。您也可以根據 V100 (NVIDIA Volta GPU 架構) 和 A100 (NVIDIA Ampere GPU 架構) 要求虛擬機器和裸機機器的服務限制。
    
    ![](images/select-GPU.PNG " ")
    
7.  如果您已經在區間中建立 VCN，請選取要將運算執行處理放在哪個 VCN 和子網路。如果沒有，您可以按一下**建立新的虛擬雲端網路**，選取 VCN 的名稱和區間，然後選取公用子網路的名稱。您可以保留預設 CIDR 區塊，或調整它。您可以選擇將公用 IP 位址指定給執行處理。若指定公用 IP 位址，此執行處理便可從網際網路存取。如果您不確定是否需要公用 IP 位址，可以之後隨時指派一個位址。
    
    ![](images/create-vcn.PNG " ")
    
8.  如果您想要使用在_實驗室 1 - 建立 SSH 金鑰_中建立的 SSH 公開和私密金鑰組，可以按一下**選擇公開金鑰檔案**並選取 SSH 公開金鑰檔案，或者按一下**貼上公開金鑰**，然後在欄位中貼上 SSH 公開金鑰。您也可以建立 OCI 上的 SSH 公用金鑰和私密金鑰，並將其儲存至本機機器。請注意，如果您未提供 SSH 金鑰，就無法使用 SSH 連線至執行處理。
    
    ![](images/add-SSH.PNG " ")
    
9.  最後一步是指定開機磁碟區大小，選擇是否要使用傳輸中加密，以及如何管理加密金鑰。您使用的預設開機磁碟區大小為 Linux 內容的 46.6 GB，或最多增加為 32 TB。您可以將所有方塊保持未勾選，然後按一下**建立 (Create)** 以完成運算執行處理建立作業。
    

![](images/boot-volume.PNG " ")

在幾分鐘之後，您的 GPU 運算執行處理會將其狀態變更為**執行中**

![](images/instance-running.PNG " ")

## **確認**

*   **建立者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月
*   **上次更新者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月