# 執行 Faceswap

## 簡介

現在，我們準備了運算執行處理 (請參閱實驗室 4)，準備就緒，然後執行 Faceswap。在此實驗室中，您將學習將訓練資料複製到運算執行處理、從訓練資料擷取面貌、使用擷取的面向訓練 GAN，以及使用交換的面向轉換您的視訊。

預估研討會時間：60 分鐘 (研究模型訓練)

### 目標

*   將您的訓練資料 (包含您要交換的臉孔或影片) 複製到運算執行處理
*   [從相片或影片擷取](https://forum.faceswap.dev/viewtopic.php?f=25&t=27)面
*   [訓練](https://forum.faceswap.dev/viewtopic.php?f=27&t=146) GAN 與擷取的面
*   將您的原始影片中的 A 臉[轉換](https://forum.faceswap.dev/viewtopic.php?f=24&t=1083)並換成 B 臉

### 您需要什麼？

*   完成實驗室 1 至實驗室 4

## 作業 1：連線至 VNC 桌面

1.  使用下列命令建立 VNC 伺服器的 SSH 通道 (私密金鑰為您的私密 OpenSSH 金鑰，public\_ip 則為來自 Ubuntu 運算執行處理的專用 IP 位址)：

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  使用您的 VNC 用戶端連線至 **127.0.0.1：5901** 。 ![](images/tigervnc.PNG " ")
    
3.  在系統提示輸入密碼時，請輸入 VNC 伺服器組態期間建立的密碼。 ![](images/tigervncpwd.PNG " ")
    

您的遠端 Ubuntu 桌面將開啟。 ![](images/vncdesktop.PNG " ")

## 作業 2：在 VNC 桌面上開啟 Faceswap

1.  在桌面上按一下滑鼠右鍵，然後選取**在此開啟終端機**。 ![](images/open-terminal.PNG " ")
    
    終端會在您的 VNC 桌面上開啟 。
    
    ![](images/vnc-terminal.PNG " ")
    

如果無法開啟終端機，請在本機機器的終端機中執行下列指令。

    <copy>sudo apt remove gnome-terminal</copy>
    

    <copy>sudo apt install gnome-terminal</copy>
    

2.  瀏覽至您的 facewap 資料夾。

    <copy>cd cd ../faceswap/</copy>
    

執行下列指令來啟動 Faceswap GUI：

    <copy>bash faceswap_gui_launcher.sh</copy>
    

Faceswap GUI 會開啟。

![](images/faceswapGUI.PNG " ")

## 作業 3：將您的訓練資料複製到運算執行處理

若要訓練 Faceswap GAN，您需要提供個人 A 和個人 B 的圖片或影片，而您想要交換面貌。在此範例中，我們將使用 WinSCP 將人員 A 與人員 B 的兩個視訊從本端機器複製到遠端 Ubuntu 執行處理。

1.  下載安裝 [WinSCP](https://winscp.net/eng/download.php) 到您的本機機器。
    
2.  開啟本機機器上的 WinSCP，然後使用左側 side.F 的影片開啟資料夾 ![](images/open-winscp.PNG " ")
    
3.  按一下**新網站**。新視窗即會出現。 ![](images/winscp-newsite.PNG " ")
    
4.  請在 _hostname_ 欄位輸入執行處理的 **IP 位址**，並在_使用者名稱_欄位中輸入 **Ubuntu** ，如下所示。 ![](images/winscp-hostname-username.PNG " ")
    
5.  按一下**進階 ...** 按鈕。
    
6.  按一下視窗左側 **SSH** 下的**認證**。
    
7.  按一下這三個點，然後在資料夾中選取您的 SSH 私密金鑰檔案，如下所示。 ![](images/winscp-ppk.PNG " ")
    
8.  按一下**確定**。
    
9.  Click **Login**
    
10.  即會出現警告畫面。按一下**是 (Yes)** 。 ![](images/winscp-warning.PNG " ")
    
11.  您可以在 WinSCP 工具的左側與右側部分看到您的本機檔案資料夾。 ![](images/winscp-auth-success.PNG " ")
    
12.  在右側的欄位中按一下滑鼠右鍵，選取 **\[New\] (新) -> \[Directory\] (目錄)** ，然後輸入資料夾 **src** 的名稱，在您的遠端資料夾中建立資料夾 **src** 。
    
13.  在左側 (本機機器) 選取您的兩個影片，然後將它們拖曳至右側 (遠端機器) 的 **src** 資料夾。
    

## 工作 4：從相片和影片中抽出面

第一步是從視訊 A 和視訊 B 擷取面。

1.  在 Faceswap GUI 中，選取 \[ **擷取** \] 頁籤。
    
2.  在**資料**欄位中，您會看到**輸入目錄**，您可以在其中選取輸入視訊或輸入影像所在的目錄。我們將按一下視訊按鈕 (左上角) 並從 **src** 資料夾中的人員 A 選取輸入視訊，然後在步驟 3 中建立並填入。
    

![](images/GUI-inputdir.PNG " ")

**注意：**如果您從輸入影像中擷取臉孔，請確定您有介於 1000 到 10.000 個影像。

3.  按一下**輸出目錄**旁的資料夾按鈕。選擇要儲存擷取之來源視訊的臉部影像的資料夾。您可以呼叫目錄，例如 _faceswap/faces/personA_ ，然後按一下**確定**。

![](images/GUI-outputdir.PNG " ")

4.  選擇性：如果您想要閱讀擷取外掛程式的更多資訊並利用這些外掛程式，請造訪 [Faceswap Extraction Guide](https://forum.faceswap.dev/viewtopic.php?t=27) 。在此範例中，我們將取代預設外掛程式。
    
5.  按一下**擷取**。您會在右側的**預覽**標籤中看到從影片擷取的面。在底部欄位中，您會看到 Python 擷取指令碼的狀態，例如初始化的外掛程式、找到的影像、偵測到的面，以及是否發生錯誤。
    

![](images/GUI-extract.PNG " ")

6.  從**步驟 4** 重複 1.-5，以擷取人員 B 的面部。
    
7.  選擇性 ：如果您不想使用 GUI，您可以使用下列指令在終端機中執行臉部擷取 (只調整輸入及輸出目錄與外掛程式) ：
    

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py extract -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/faces/personA -D s3fd -A fan -nm none -rf 0 -min 0 -l 0.4 -sz 512 -een 1 -si 0 -L INFO</copy>
    

![](images/extract-terminal.PNG " ")

## 任務 5：訓練 GAN

現在我們已從影片中抽出個人 A 和個人 B 的面向，我們可以訓練 Faceswap GAN。

1.  在 Faceswap GUI 中，選取 \[ **訓練** \] 頁籤。
    
2.  在 **Faces** 欄位中，您會看到**輸入 A** ，您可以在其中選取人員 A 擷取面的目錄 (在 _faceswap/faces/personA_ 中)。
    

![](images/GUI-faces-input.PNG " ")

3.  下方會顯示**調整 A** 資料列。對齊檔案會保留其在各框架中發現之所有面的資訊，特別是面部所在的位置，以及指標地標的位置 (例如眼睛、鼻子、唇)。它是在擷取階段所建立，並儲存在人員 A 影片的來源資料夾中。按一下檔案按鈕，然後在 **src** 資料夾中選取人員 A 的對齊檔案，如下所示。

![](images/GUI-alignmentA.PNG " ")

4.  針對人員 B 重複點數 1. 與 2。
    
5.  在**模型**欄位中，按一下**模型目錄**旁的資料夾按鈕。選擇儲存訓練模型的資料夾。您可以呼叫目錄，例如 _faceswap/model_ ，然後按一下**確定**。
    
6.  選擇性：如果您想要閱讀有關訓練員的更多資訊，或是「資料擴增」選項並利用這些選項，請造訪 [Faceswap 訓練指南](https://forum.faceswap.dev/viewtopic.php?f=27&t=146)。在此範例中，我們將取代預設外掛程式。
    
7.  按一下**訓練**按鈕以開始訓練。
    

![](images/GUI-train.PNG " ")

在右側的**預覽**頁標中，您將會在人員 A 與人員 B 的影片框架中看到交換的面。

**圖表**頁籤包含顯示一段時間內損失的圖表。它會在每次儲存模型時更新，但只要按一下「重新整理」按鈕即可重新整理。針對每批送入模型的面額，GAN 會查看其嘗試重建的面額，並將其與被餵食的實際面額進行比較。根據其認為完成的程度，它將給予自己的分數 (損失值)。

8.  選擇性：如果您不想使用 GUI，可以使用下列指令在終端機中執行模型訓練 (如果想要使用其他訓練員選項，請只調整輸入目錄、對齊檔案、模型目錄和訓練員選項)：

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py train -A /home/ubuntu/faceswap/faces/personA -ala /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -B /home/ubuntu/faceswap/faces/personB -alb /home/ubuntu/faceswap/src/personB/personB_alignments.fsa -m /home/ubuntu/faceswap/model -t original -bs 16 -it 1000000 -s 250 -ss 25000 -ps 100 -L INFO</copy>
    

![](images/terminal-train.PNG " ")

瞭解模型是否完成訓練的最佳方式是觀看預覽。最後，這些顯示實際交換的外觀。您也可以檢查損失值。損失值越接近零，完成訓練的機率越高。

![](images/graph-loss-iterations.PNG " ")

在**分析**頁籤中，您可以檢查最後一個資料欄的值 _EGs/sec_ ，這樣就會提供每秒透過模型處理的面數。您的 (GPU) 核心越強大， _EG/ 秒_越高，模型的轉換速度也越快。

9.  使用 nvidia-smi 指令訓練時，請檢查 GPU 的使用狀況。 ![](images/nvidia-smi-GPU.PNG " ")
    
    它會顯示動態的 GPU 使用量。
    
10.  選擇性：增加 GPU 核心和記憶體時鐘速率，可以提升應用程式效能。如需詳細資訊，請造訪 Nvidia 部落格文章，瞭解 [Nvidia GPU Boost](https://developer.nvidia.com/blog/increase-performance-gpu-boost-k80-autoboost/) 。
    

## 任務 6：用置換的臉轉換您的影片

如果您的訓練已完成，您可以使用 B 形將原始影片中的視訊和交換 A 轉換成形。

1.  在 Faceswap GUI 中，選取 \[ **轉換** \] 頁籤。
    
2.  在**資料**欄位中，您會看到**輸入目錄**，您可以在其中選取輸入視訊所在的目錄，以及您要交換其外觀的人員。在我們的情況下，輸入視訊儲存在 _/faceswap/src/personA_ 之下
    
3.  按一下**輸出目錄**旁的資料夾按鈕。選擇要儲存輸出影像的資料夾 。您可以呼叫目錄，例如 _/faceswap/outputvideo_ ，然後按一下**確定**。
    
4.  按一下**調整**旁邊的檔案按鈕，然後從您要交換面的人中選擇調整檔案 (在我們的案例中為人員 A)。如果對齊檔案與**輸入目錄**位於相同的目錄中，您可以保留空白。
    
5.  選擇性：只有在您要從影像轉換成視訊時，才需要**參考視訊**。在我們的情況下，我們將保留空白。
    
6.  在**模型**欄位中，按一下**模型目錄**旁的資料夾按鈕。選擇儲存訓練模型的資料夾。在我們的狀況下，也就是 _faceswap/model_ 目錄。按一下**確定 (OK)** 。
    
7.  按一下**「轉換」**。
    

![](images/GUI-convert.PNG " ")

8.  選擇性 ：如果您不要使用 GUI，您可以用以下的指令在終端機中進行轉換 (只需調整輸入與輸出的目錄、 對齊檔案、 模型目錄與外掛程式) ，如果您想要使用其它外掛程式 ：

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py convert -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/outputvideo -al /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -m /home/ubuntu/faceswap/model -c avg-color -M extended -w opencv -osc 100 -l 0.4 -j 0 -L INFO</copy>
    

![](images/convert-terminal.PNG " ")

9.  在 _/faceswap/outputvideo_ 目錄中，您會發現人員 A 的輸入視訊框架，其置換面為人員 B。 ![](images/outputvideo-dir.PNG " ")
    
    我們會使用 ffmpeg 從單一影格建立影片。
    
    先安裝 ffmpeg 。
    
        <copy>sudo apt install ffmpeg</copy>
        
    
    Now we can construct a video with ffmpeg to combine the single frames into a video where we will loop through all the personA frames contained om the _outputvideo_ directory where _%nd_ stands for a a sequential n-digit number. In our case n=6.
    
        <copy>ffmpeg -i personA_%6d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4</copy>
        
10.  觀看[輸出影片](https://www.youtube.com/watch?v=b-uKJ89QSnE)
    

## **確認**

*   **建立者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月
*   **上次更新者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月