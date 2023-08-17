# 啟動 HPC 叢集網路

## 簡介

有許多方式可以啟動 HPC 叢集網路，本解決方案指南涵蓋兩種不同的方法：

*   透過市集
    
*   根據作業系統的不同，您要以特定方法執行。如果使用 HPC Cluster Network 市集映像檔或 OCI HPC CN Terraform 命令檔，這僅適用於 Oracle Linux 7。如果要使用 CentOS、Ubuntu 或其他作業系統，則必須手動配置。
    

預估實驗室時間：30 分鐘

## **OPTION 1** ：透過 Marketplace 建立叢集網路

Marketplace 保留了可在我們的基礎架構中部署的應用程式和映像檔。對於想要使用 Oracle Linux 的客戶，可使用 HPC 叢集網路映像檔，可從市場內直接啟動。建議您啟動 [CFD Ready Cluster](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) ，此叢集包含 CFD 所需的程式庫。

1.  在市集中，選取右上方的**取得 App** 。
    
2.  選取 OCI 區域，然後按一下**登入**。
    
3.  登入之後，請驗證 HPC 叢集映像檔的版本，然後選取將啟動叢集的區間。接受條款與條件，然後**啟動堆疊**。
    
4.  填寫堆疊的其餘詳細資訊：
    
    *   為運算資源配置和堡壘主機選取所需的 **AD** 。
    *   複製並貼上您的公用 **ssh 金鑰**
    *   輸入叢集的**運算執行處理**數目
    *   取消勾選安裝 OpenFOAM
    *   如果您需要超過 6TB 的共用磁碟空間，請檢查 GlusterFS 並選取您需要的伺服器數目。(每部伺服器 6TB)
5.  按一下**建立**。
    
6.  瀏覽至「Terraform 動作」，然後按一下**套用 (Apply)** 。這會啟動 CN 佈建。
    
7.  請等待工作顯示「成功」，然後瀏覽至**輸出**以取得堡壘主機和運算節點專用 IP。
    

## **OPTION 2** ：透過手動組態建立叢集網路

Marketplace 保留了可在我們的基礎架構中部署的應用程式和映像檔。對於使用 Oracle Linux 的客戶，您可以手動建立叢集網路，如下所示：

1.  選取右上角的 OCI 區域。
    
2.  在主功能表中，選取**網路**和**虛擬雲端網路**
    
3.  按一下「啟動 VCN 精靈」，然後選取**具備網際網路連線的 VCN**
    
4.  選擇並命名正確的區間，然後使用 172.16.0.0/16 作為 **VCN CIDR** 、172.16.0.0/24 作為公用子網路，以及 172.16.1.0/24 作為專用子網路。按一下**下一步 (Next)** ，然後按一下**建立 (Create)** 。
    
5.  在主功能表中，依序選取**運算、執行處理**和建立執行處理
    
6.  變更影像並選取 **Oracle Image** 頁籤，選取 **Oracle Linux 7 - HPC Cluster Networking Image**
    
7.  選取可用來啟動 BM.HPC2.36 執行處理的**可用性網域**
    
8.  將**資源配置**變更為裸機和專業領域下的 BM.HPC2.36
    
9.  選取 VCN 和您建立的公用子網路。
    
10.  新增公開金鑰以連線至執行處理。此金鑰將用於所有運算執行處理。
    
11.  機器啟動後，請按一下建立的執行處理。在**其他動作**下方，選取**建立執行處理組態**。您現在可以在**其他動作**底下**終止**執行處理。
    
12.  在主功能表中，依序選取**運算**和**叢集網路**。
    
13.  按一下**建立叢集網路**並填入所有選項。請使用剛剛建立的 VCN、專用子網路和執行處理組態。選取您可以在其中啟動 BM.HPC2.36 執行處理的 AD。
    
14.  啟動叢集網路。
    
15.  載入時，請在**主功能表、運算和執行處理**底下建立另一個執行處理。
    
16.  將其放到剛才建立的公用子網路中，使用您的公用金鑰和資源配置應該是 VM.Standard2.1 或類似資源。這將是我們用來連線至叢集的堡壘主機。
    
17.  將金鑰暫存至 /home/opc/.ssh/cluster\_key 堡壘主機上的叢集，並一併將它複製到 /home/opc/.ssh/id\_rsa
    
18.  請使用下列命令在堡壘主機上安裝「佈建工具」：
    
        <copy>
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        </copy>
        
        
19.  依序瀏覽至「主控台」中的**運算**和**執行處理集區**，並收集叢集網路集區的所有 IP 位址。或在專用子網路上執行任何其他動作時，在堡壘主機上使用此命令。
    
        <copy>
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1}>>tmp/ips; done
        </copy>
        
20.  透過下列指令安裝佈建工具：
    
        <copy>
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        </copy>
        

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月