# 將 Gromacs 安裝程式新增至物件儲存

## 簡介

在此實驗室中，您將新增 Gromacs 安裝程式至物件儲存。

預估實驗室時間：5 分鐘

## 作業：新增 Gromacs 安裝程式至物件儲存

_請注意，此 github 中提供的 terraform 指令碼 [zip file](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip) 已包含 Gromacs 2020.1、VMD 1.9.3 的物件儲存 URL，以及來自 [Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench) 的範例基準模型。如果您要變更這些 URL，請修改 variable.tf 檔案，並將 gromacs\_url、model\_url 和 visualizer\_url 的值取代為您自己的預先認證要求 URL。_

1.  選取左上角的功能表 ![](./images/menu.png)，然後選取「物件儲存 --> 物件儲存」
2.  建立新儲存桶或選取現有儲存桶。若要建立，請按一下![](./images/create_bucket.png)
3.  保留預設選項：「標準」作為儲存層和 Oracle 管理的金鑰。按一下![](./images/create_bucket.png)
4.  按一下新建立的儲存桶名稱，然後選取![](./images/upload_object.png)
5.  選取您的 Gromacs 安裝程式 tar 檔案，然後按一下![](./images/upload_object.png)
6.  按一下您剛才上傳之物件右側的 3 個點，然後選取「建立預先認證要求」
7.  在下列功能表中，選擇預設選項，並為安裝程式的 URL 選取到期日。按一下![](./images/pre-auth.png)
8.  在下一個視窗中，複製 "PRE-AUTHENTICATED REQUEST URL" 並保留它。關閉此視窗後，您將無法擷取。如果遺失或過期，您可以重新建立另一個將產生不同 URL 的預先認證要求

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月