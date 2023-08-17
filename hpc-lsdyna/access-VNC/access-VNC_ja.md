# VNCへのアクセス

## 概要

この演習では、VNCにアクセスします。

予想ラボ時間: 10分

## タスク: VNCへのアクセス

1.  SSHトンネルを介してインスタンスに接続します。マシンでは、次のSSH PORTを使用して接続すると、5900 + Nの結果になります。Nは表示番号で、Nの出力が1の場合、PORTは5901で、出力が9の場合、PORTは5909で、public\_ipはVNCサーバーを実行しているヘッドノードのパブリックIPアドレスです。前述の手順を使用した場合、ポートは5901になります
    
        <copy>
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        </copy>
        
        
2.  これで、localhost:NをVNCサーバーとして使用し、VNCのインストール中に設定したパスワードを使用して、任意のVNCビューアを使用して接続できるようになりました。希望するVNCクライアントを選択するか、このガイドを使用してローカル・マシンにインストールできます:
    

*   [Windows - TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [MacOS/Windows - RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月