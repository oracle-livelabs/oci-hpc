# Faceswapの実行

## 概要

コンピュート・インスタンスの準備が完了したので(演習4を参照)、Faceswapを実行できます。演習では、トレーニング・データをコンピュート・インスタンスにコピーし、トレーニング・データから面を抽出し、抽出された面でGANをトレーニングし、スワップされた面でビデオを変換する方法について学習します。

推定ワークショップ時間:60分(試験モデル・トレーニング)

### 目的

*   トレーニング・データ(交換する面を含む写真またはビデオ)をコンピュート・インスタンスにコピーします
*   写真またはビデオから顔を[抽出](https://forum.faceswap.dev/viewtopic.php?f=25&t=27)する
*   抽出された面でGANを[トレーニング](https://forum.faceswap.dev/viewtopic.php?f=27&t=146)する
*   ソース・ビデオの顔Aを面Bと[変換](https://forum.faceswap.dev/viewtopic.php?f=24&t=1083)してスワップします

### 必要なもの

*   完全なラボ1からラボ4

## タスク1: VNCデスクトップへの接続

1.  次のコマンドを使用して、VNCサーバーへのSSHトンネルを作成します(private-keyはプライベートOpenSSHキーで、Ubuntuコンピュート・インスタンスからプライベートIPアドレスをpublic\_ipします):

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  VNCクライアントを使用して**127.0.0.1:5901**に接続します。 ![](images/tigervnc.PNG " ")
    
3.  パスワードの入力を求められたら、VNCサーバーの構成中に作成されたパスワードを入力します。 ![](images/tigervncpwd.PNG " ")
    

リモートのUbuntuデスクトップが開きます。 ![](images/vncdesktop.PNG " ")

## タスク2: VNCデスクトップでのFaceswapのオープン

1.  デスクトップを右クリックし、**「ここでターミナルを開く」**を選択して、ターミナルを開きます。 ![](images/open-terminal.PNG " ")
    
    VNCデスクトップで端末が開きます。
    
    ![](images/vnc-terminal.PNG " ")
    

端末を開けない場合は、ローカル・マシンのターミナルで次のコマンドを実行します。

    <copy>sudo apt remove gnome-terminal</copy>
    

    <copy>sudo apt install gnome-terminal</copy>
    

2.  Faceswapフォルダにナビゲートします。

    <copy>cd cd ../faceswap/</copy>
    

次のコマンドを実行して、Faceswap GUIを起動します。

    <copy>bash faceswap_gui_launcher.sh</copy>
    

Faceswap GUIが開きます。

![](images/faceswapGUI.PNG " ")

## タスク3: トレーニング・データのコンピュート・インスタンスへのコピー

Faceswap GANをトレーニングするには、顔を交換する人Aと人Bの写真またはビデオを指定する必要があります。この例では、WinSCPを使用して、ローカル・マシンからリモートUbuntuインスタンスに2つのビデオを個人Aおよび個人Bからコピーします。

1.  ローカル・マシンで[WinSCP](https://winscp.net/eng/download.php)をダウンロードし、インストールします。
    
2.  ローカル・マシンでWinSCPを開き、左側のビデオを含むフォルダを開きます side.F ![](images/open-winscp.PNG " ")
    
3.  **「新規サイト」**をクリックします。新しいウィンドウが開きます。 ![](images/winscp-newsite.PNG " ")
    
4.  次に示すように、インスタンスの_IPアドレス_を**ホスト名**フィールドに、_Ubuntu_を**ユーザー名**フィールドに入力します。 ![](images/winscp-hostname-username.PNG " ")
    
5.  **「詳細設定...」**ボタンをクリックします。
    
6.  ウィンドウの左側の**「SSH」**の下にある**「認証」**をクリックします。
    
7.  3つのドットをクリックし、次に示すようにフォルダ内のSSH秘密キー・ファイルを選択します。 ![](images/winscp-ppk.PNG " ")
    
8.  **「OK」**をクリックします
    
9.  **「ログイン」**をクリックします
    
10.  警告画面がポップアップします。**「はい」**をクリックします。 ![](images/winscp-warning.PNG " ")
    
11.  ローカル・ファイル・フォルダは左側に表示され、リモート・ファイル・フォルダはWinSCPツールの右側に表示されます。 ![](images/winscp-auth-success.PNG " ")
    
12.  リモートフォルダ内にフォルダ **src**を作成するには、右側のフィールドを右クリックし、「**新規->ディレクトリ**」を選択して、フォルダ **src**の名前を入力します。
    
13.  左側にある2つのビデオ(ローカル・マシン)を選択し、右側にある**src**フォルダ(リモート・マシン)にドラッグします。
    

## タスク4: 写真およびビデオからの面の抽出

最初の手順は、ビデオAとビデオBから面を抽出することです。

1.  Faceswap GUIで、**「抽出」**タブを選択します。
    
2.  **「データ」**フィールドに**入力ディレクトリ**が表示され、入力ビデオまたは入力イメージが配置されているディレクトリを選択できます。ビデオ・ボタン(左)をクリックして入力ビデオを選択し、**src**フォルダから個人Aから入力ビデオを選択して、ステップ3で作成および移入します。
    

![](images/GUI-inputdir.PNG " ")

**ノート:**入力イメージからフェースを抽出する場合は、1000から10.000イメージの間にあることを確認してください。

3.  **「出力ディレクトリ」**の横にあるフォルダ・ボタンをクリックします。ソースビデオの抽出された面イメージが保存されるフォルダを選択します。ディレクトリ(_faceswap/faces/personA_など)をコールし、**「OK」**をクリックします。

![](images/GUI-outputdir.PNG " ")

4.  オプション: 抽出プラグインの詳細を参照して使用する場合は、[Faceswap抽出ガイド](https://forum.faceswap.dev/viewtopic.php?t=27)を参照してください。この例では、デフォルトのプラグインを使用します。
    
5.  Click on the **Extract**. You will see the extracted faces from the video in the **Preview** tab on the right. In the bottom field, you see the state of the Python extraction script, e.g. the initialized plugins, found images, detected faces and in case there are errors.
    

![](images/GUI-extract.PNG " ")

6.  個人Bの顔の抽出について、**ステップ4**から1から5を繰り返します。
    
7.  オプション: GUIを使用しない場合は、次のコマンドを使用してターミナルで面の抽出を実行できます(他のプラグインを使用する場合に備えて、入力ディレクトリと出力プラグインのみを調整します)。
    

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py extract -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/faces/personA -D s3fd -A fan -nm none -rf 0 -min 0 -l 0.4 -sz 512 -een 1 -si 0 -L INFO</copy>
    

![](images/extract-terminal.PNG " ")

## タスク5: GANのトレーニング

ビデオから人Aと人Bの顔を抽出したので、Faceswap GANをトレーニングできます。

1.  Faceswap GUIで、**「トレイン」**タブを選択します。
    
2.  **「Faces」**フィールドには、個人Aの抽出されたフェースのディレクトリを選択する**「Input A」**が表示されます(ここでは、_faceswap/faces/personA_)。
    

![](images/GUI-faces-input.PNG " ")

3.  下には、**「連携A」**行が表示されます。線形ファイルは、各フレーム内で検出されたすべての面、特に面の位置、ポイントのランドマーク(目、鼻、唇など)に関する情報を保持します。これは抽出フェーズで作成され、個人Aのビデオのソース・フォルダに格納されます。ファイル・ボタンをクリックして、次に示すように、**src**フォルダで個人Aの整列ファイルを選択します。

![](images/GUI-alignmentA.PNG " ")

4.  個人Bについて、点1と2を繰り返します。
    
5.  In the **Model** field, click on folder button next to **Model Dir**. Choose the folder, where the training model will be saved. You can call the directory e.g. _faceswap/model_ and click on **OK**.
    
6.  オプション: トレーナまたは「データ拡張」オプションの詳細を参照し、それらを使用する場合は、[Faceswapトレーニング・ガイド](https://forum.faceswap.dev/viewtopic.php?f=27&t=146)を参照してください。この例では、デフォルトのプラグインを使用します。
    
7.  **「トレーニング」**ボタンをクリックしてトレーニングを開始します。
    

![](images/GUI-train.PNG " ")

右側の**「プレビュー」**タブに、人Aと人Bのビデオ・フレームにスワップされた面が表示されます。

**「グラフ」**タブには、時間の経過に伴う損失を示すグラフが表示されます。モデルが保存されるたびに更新されますが、「リフレッシュ」ボタンを押すとリフレッシュできます。モデルに塗りつぶされた面のバッチごとに、GANは再作成しようとした面を調べて、塗りつぶされた実際の面と比較します。どれだけうまくやったかに基づいて、スコア(損失値)が与えられます。

8.  オプション: GUIを使用しない場合は、次のコマンドを使用してターミナルでモデル・トレーニングを実行できます(別のトレーナ・オプションを使用する場合に備えて、入力ディレクトリ、整列ファイル、モデル・ディレクトリおよびトレーナ・オプションのみを調整します)。

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py train -A /home/ubuntu/faceswap/faces/personA -ala /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -B /home/ubuntu/faceswap/faces/personB -alb /home/ubuntu/faceswap/src/personB/personB_alignments.fsa -m /home/ubuntu/faceswap/model -t original -bs 16 -it 1000000 -s 250 -ss 25000 -ps 100 -L INFO</copy>
    

![](images/terminal-train.PNG " ")

モデルのトレーニングが終了したかどうかを知る最善の方法は、プレビューを見ることです。最終的に、これらは実際のスワップの外観を示します。損失値を確認することもできます。損失値が0(ゼロ)に近いほど、終了したトレーニングの確率が高くなります。

![](images/graph-loss-iterations.PNG " ")

**「分析」**タブで、最後の列の値_「EGs/sec」_を確認すると、1秒当たりのモデルで処理されたフェースの数が表示されます。(GPU)コアが強力になるほど、_EGs/sec_が高くなり、モデルの収束が速くなります。

9.  nvidia-smiコマンドを使用して、トレーニング中のGPU使用状況を確認します。 ![](images/nvidia-smi-GPU.PNG " ")
    
    揮発性GPU使用率が表示されます。
    
10.  オプション: GPUコアおよびメモリー・クロック・レートを増やすことで、アプリケーションのパフォーマンスを向上させることができます。詳細については、[Nvidia GPUブースト](https://developer.nvidia.com/blog/increase-performance-gpu-boost-k80-autoboost/)についてのNvidiaブログ記事を参照してください。
    

## タスク6: スワップした面でビデオを変換する

トレーニングが終了したら、ビデオを変換し、面Bのソース ビデオで面Aを交換できます。

1.  Faceswap GUIで、**「変換」**タブを選択します。
    
2.  **「データ」**フィールドには、**入力ディレクトリ**が表示され、ここで、入力ビデオが顔をスワップする個人とともに配置されるディレクトリを選択します。この場合、入力ビデオは_/faceswap/src/personA_に格納されます。
    
3.  **「出力ディレクトリ」**の横にあるフォルダ・ボタンをクリックします。出力ビデオが保存されるフォルダを選択します。_/faceswap/outputvideo_などのディレクトリをコールし、**「OK」**をクリックします。
    
4.  **「整列」**の横にあるファイル・ボタンをクリックして、フェースを入れ替えるユーザー(この場合は個人A)から整列ファイルを選択します。整列ファイルが**入力ディレクトリ**と同じディレクトリにある場合は、空白のままにできます。
    
5.  オプション: **参照ビデオ**は、イメージからビデオに変換する場合にのみ必要です。この場合は空白のままにします。
    
6.  In the **Model** field, click on folder button next to **Model Dir**. Choose the folder, where the training model is saved. In our case, that is the directory _faceswap/model_. Click on **OK**.
    
7.  **「変換」**をクリックします。
    

![](images/GUI-convert.PNG " ")

8.  オプション: GUIを使用しない場合は、次のコマンドを使用して端末で変換を実行できます(ほかのプラグインを使用する場合に備えて、入力ディレクトリと出力ディレクトリ、整列ファイル、モデルディレクトリ、およびプラグインのみを調整します)。

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py convert -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/outputvideo -al /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -m /home/ubuntu/faceswap/model -c avg-color -M extended -w opencv -osc 100 -l 0.4 -j 0 -L INFO</copy>
    

![](images/convert-terminal.PNG " ")

9.  ディレクトリ _/faceswap/outputvideo_には、人Aの入力ビデオと人Bの交換面のフレームがあります。 ![](images/outputvideo-dir.PNG " ")
    
    ffmpegを使用して、単一フレームからビデオを作成します。
    
    まずffmpegをインストールします。
    
        <copy>sudo apt install ffmpeg</copy>
        
    
    ここでffmpegを使用してビデオを構築し、単一のフレームをビデオに結合して、_outputvideo_ディレクトリに含まれるすべての personAフレームをループスルーします。ここで、_%nd_は連続したn桁の数字を表します。この場合、n=6です。
    
        <copy>ffmpeg -i personA_%6d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4</copy>
        
10.  [出力ビデオ](https://www.youtube.com/watch?v=b-uKJ89QSnE)を見る
    

## **確認**

*   **作成者/日付** - Oracle Proposal to Production Program、 Maria Patelkou氏、HPCソリューション・アーキテクト、2021年3月
*   **最終更新者/日付** - Oracle Proposal to Production Program、 Maria Patelkou氏、HPCソリューション・アーキテクト、2021年3月