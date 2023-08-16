# 環境の初期化

## 概要

この演習では、このワークショップを正常に実行するために必要なすべてのコンポーネントを確認して起動します。

_推定ラボ時間:_ 30分。

### 目的

*   ワークショップ環境を初期化します。

### 前提条件

この演習では、次のことを前提としています。

*   Oracle Cloudアカウント
*   完了しました:
    *   演習: 設定の準備(_Free-tier_および_Paid Tenants_のみ)
    *   演習: 環境設定

> **注意:** _ガイド全体で使用される便利な_ **コピー** _機能を使用してコピー/ペーストを行う場合は、ペースト後に_ **ENTER** _キーを押す必要があります。そうしないと、ENTERキーを押すまで最後の行がバッファに残ります。_

## タスク1: 必要なプロセスが稼働中であることの検証

1.  リモート・デスクトップ・セッションにアクセスし、次に示すように進んで環境を検証してから、後続の演習を開始します。次のプロセスが稼働している必要があります。
    
    *   データベース・リスナー
        *   リスナー(1521)
        *   LISTCDB2 (1522)
    *   データベース・サーバー・インスタンス
        *   CDB1
        *   CDB2
    
    データベース接続をテストするには、_「SQL Developer Oracle接続」_パネルの次に示すように、データベースの横にある_+_記号をクリックします。
    
    ![](./images/19c_hol_landing.png " ")
    
2.  デスクトップの_「ターミナル」_アイコンをクリックしてセッションを起動し、次を実行して必要なプロセスが稼働していることを検証します。
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    前述のように、予想されるすべてのプロセスが出力に表示された場合、環境は次のタスクの準備ができています。
    
3.  疑わしい出力、障害、またはコンポーネントの停止が表示された場合は、それに応じてサービスを再起動します
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## タスク2: マルチテナントのユースケースでのデータベースの初期化

1.  リモート・デスクトップ・セッションでユーザー_oracle_として、次のブロックを実行し、演習のアーティファクトをリフレッシュします
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

これで、[次のラボに進む](#next)ことができます。

## 付録1: 起動サービスの管理

1.  データベース・サービス(すべてのデータベースおよびStandardリスナー)。
    
    *   開始
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   停止
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   ステータス
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   再起動
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  Databaseサービス(非Standardリスナー)。
    
    *   開始
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   停止
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   ステータス
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   再起動
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## 確認

*   **作成者** - Oracle Database In-Memory担当シニア・プリンシパル・プロダクト・マネージャー、Andy Rivenes
*   **貢献者** - Kay Malcolm、Didi Han、Rene Fontcha
*   **最終更新者/日付** - Rene Fontcha、LiveLabs Platform Lead、NA Technology、2021年10月