# 安裝 STAR-CCM+

## 簡介

在此實驗室中，您將安裝 STAR-CCM+。

預估實驗室時間：25 分鐘

## 作業 1：新增特定的程式庫

_**如果您使用市集的「CFD Ready 叢集」，則不需要此步驟**_

1.  所有運算節點上的 Oracle Linux 映像檔需要新增許多程式庫。
    
            sudo yum -y install libSM libX11 libXext libXt
        

## 作業 2：下載二進位檔

您可以從 Siemens PLM 網站下載 STAR-CCM+ 安裝程式，或使用 scp 將其推送至您的機器。

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

另一個方式是將安裝程式上傳到物件儲存體。

1.  在主控台的主功能表中，選取「物件儲存」。
2.  請在右上方選擇正確的區域
3.  選取左側的正確區間
4.  如果尚未建立儲存桶，請建立儲存桶
5.  在儲存桶中，選取上傳物件並指定安裝程式的路徑。
6.  選取安裝程式物件右側的 3 個點，然後選取「建立預先認證要求」
7.  如果您遺失 URL，便無法取回該 URL，但可以重新產生新的預先認證要求

下載安裝程式表單物件儲存

        wget PAR_URL
    

依據您的版本解壓縮或解壓縮安裝程式

        tar -xf installer.tgz
        unzip installer.tgz
    

## 作業 3：安裝

1.  在共用位置啟動安裝程式。在所有運算節點上，HPC 叢集預設會掛載 NFS 共用。
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月