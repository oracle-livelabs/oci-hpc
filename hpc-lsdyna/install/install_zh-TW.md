# 安裝 LS-DYNA

## 簡介

在此實驗室中，您將安裝 LS-DYNA。

預估實驗室時間：25 分鐘

## 作業 1：下載二進位檔

您可以從 [LSTC 網站](http://www.lstc.com/download/ls-dyna)下載 LS-DYNA 二進位檔案，或使用 scp 將其推送至您的機器。

取用為 mpi 建立的版本，並編譯為 RedHat Ent Srv 5.4。根據我們的發現，IntelMPI 的執行速度比 OCI 上的平台 MPI 快。(ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1) .tar.gz)

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

另一個方式是將安裝程式上傳到物件儲存體。

1.  在主控台的主功能表中，選取「物件儲存」。
2.  請在右上方選擇正確的區域
3.  選取左側的正確區間
4.  如果尚未建立儲存桶，請建立儲存桶
5.  在儲存桶中，選取上傳物件並指定安裝程式的路徑。
6.  選取安裝程式物件右側的 3 個點，然後選取「建立預先認證要求」
7.  如果您遺失 URL，便無法取回該 URL，但可以重新產生新的預先認證要求

下載安裝程式表單物件儲存

    <copy>
        wget PAR_URL
    </copy>
    

依據您的版本解壓縮或解壓縮安裝程式

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## 作業 2：安裝 LS-DYNA

1.  解壓縮共用位置的二進位檔案。HPC 叢集預設會掛載所有運算節點上的 NFS 共用或 Gluster 共用。
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## 作業 3：安裝 MPI 程式庫

**Intel MPI 2018**

在每個節點上執行這些命令。

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**平台 MPI**

安裝這些程式庫：

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

從 IBM 網站下載 tar 檔案並執行：

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

然後依照螢幕上的指示進行。如果您在共用磁碟機上安裝平台，所有運算節點都可以存取此平台。

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月