# VNCを介した設定およびアクセス

## 概要

このラボでは、VNCクライアント(TigerVNC)を介してUbuntuインスタンスを設定およびアクセスするために必要なステップについて説明します。

予想ワークショップ時間: 15分

### 目的

*   SSH経由でコンピュート・インスタンスに接続します
*   UbuntuインスタンスでVNCを設定および構成します
*   VNCクライアント経由でコンピュート・インスタンスにアクセスします(例: TigerVNC)

### 必要なもの

*   完全なラボ1とラボ2
*   [イングレス・ルールをVCNセキュリティ・リストに追加](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using)して、ポート・ポート5900+N (Nは表示番号)のTCPトラフィックを許可します(通常は、物理表示の場合は:0)。![](images/vncseclist.PNG " ")

## タスク1: SSHを使用したコンピュート・インスタンスへの接続

1.  任意の端末(コマンドラインまたはコンソールとも呼ばれる)を開きます。この例では、ローカルのWindowsマシンで作業し、Windows PowerShellを端末として使用します。
    
2.  UbuntuインスタンスのパブリックIPアドレスを確認します。これは、コンパートメントのインスタンス・リストの**「コンピュート」→「インスタンス」**にあります。
    
3.  端末で、Ubuntuコンピュート・インスタンスの作成中に使用したSSHキー・ペアが存在するフォルダに移動します。この場合、フォルダは _SSH_と呼ばれます。
    

       <copy>cd .ssh</copy>
    

4.  次のコマンドを入力して、_private-key_をOpenSSH秘密キーおよび_public IP_をUbuntuインスタンスのパブリックIPアドレスに置き換える必要があるUbuntuコンピュート・インスタンスにアクセスします。

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## タスク2: UbuntuインスタンスでのVNCの設定および構成

1.  端末で次のコマンドを実行して、VNCサーバーをインストールします。

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  デスクトップ環境をインストールします。次のコマンドを入力して、Xfceデスクトップ環境をインストールします(ただし、ほかのデスクトップ環境を使用することもできます)。

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  vncpasswdコマンドを実行して、VNCサーバーのパスワードを設定します。

       <copy>vncpasswd</copy>
    

プロンプトが表示されたら、パスワードを入力し、2回目を入力して確認します。

4.  次のコマンドを使用してVNCサーバーを起動します。

       <copy>vncserver :1</copy>
    

次の出力が表示されます。 ![](images/vncserver.PNG " ")

5.  次のコマンドを使用してVNCサーバーを強制終了します。

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  VNC構成ファイルは、~/.VNC/xstartupにあります。お気に入りのテキストエディタで編集します。vimを使用します。

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  ファイル末尾に次の行を追加します。

       <copy>exec /usr/bin/startxfce4 &</copy>
    

vimで作業している場合は、**A**を押して最後にテキストを追加し、上記のコマンドをコピーします。ESCキーを押して、コマンドモードに切り替えます。**:wq** - 保存して終了します。

8.  次のコマンドを実行して、VNCサーバーを起動します。

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  次のコマンドを実行して、インスタンスへのSSH接続を閉じます。

       <copy>exit</copy>
    

## タスク3: VNCデスクトップへの接続

VNCクライアント(TigerVNCなど)およびSSHトンネリングを使用して、リモート・デスクトップに接続できます。

1.  次のコマンドを使用して、VNCサーバーへのSSHトンネルを作成します(private-keyはプライベートOpenSSHキーで、Ubuntuコンピュート・インスタンスからプライベートIPアドレスをpublic\_ipします):

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  vncviewerクライアント(ここではTigerVNC)をインストールします。

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  ローカル・マシンにVNCクライアント(TigerVNCなど)をダウンロードしてインストールします。VNCクライアントを使用して**127.0.0.1:5901**に接続します。 ![](images/tigervnc.PNG " ")
    
4.  パスワードの入力を求められたら、VNCサーバーの構成中に作成されたパスワードを入力します。 ![](images/tigervncpwd.PNG " ")
    

リモートのUbuntuデスクトップが開きます。 ![](images/vncdesktop.PNG " ")

## **確認**

*   **作成者/日付** - Oracle Proposal to Production Program、 Maria Patelkou氏、HPCソリューション・アーキテクト、2021年3月
*   **最終更新者/日付** - Oracle Proposal to Production Program、 Maria Patelkou氏、HPCソリューション・アーキテクト、2021年3月