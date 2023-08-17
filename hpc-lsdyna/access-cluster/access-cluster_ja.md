# 自分のクラスタへのアクセス

## 概要

この演習では、クラスタにアクセスします。

予想ラボ時間: 10分

## タスク: クラスタへのアクセス

1.  要塞のパブリックIPアドレスは、左下の「出力」メニューにあります。メイン・メニューでインスタンスに移動すると、要塞インスタンスとパブリックIPも表示されます。
    
2.  マシンにアクセスするための秘密鍵もそこにあります。マシン上のファイルにテキストをコピーします。say/home/user/key:
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月