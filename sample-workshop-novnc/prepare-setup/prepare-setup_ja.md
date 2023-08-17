# 設定の準備

## 概要

このラボでは、このワークショップの実行に必要なリソースの設定に必要なOracle Resource Manager (ORM)スタックのzipファイルをダウンロードする方法を示します。

_予想ラボ時間:_ 10分

### 目的

*   ORMスタックのダウンロード
*   既存のVirtual Cloudネットワーク(VCN)の構成

### 前提条件

この演習では、次のことを前提としています。

*   Oracle Cloudアカウント

## タスク1: Oracle Resource Manager (ORM)スタックのzipファイルのダウンロード

1.  次のリンクをクリックして、環境の構築に必要なResource Managerのzipファイルをダウンロードします。
    
    _ノート1:_ワークショップに単一のスタック・ダウンロードを提供する場合は、この単純な式を使用します。
    
    *   [サンプルmkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _Note 2:_ If providing multiple Stacks download for the same workshop, use a conditional expression similar to the below. Keep in mind that the condition or _type_ must be paired with a valid entry in the _manifest.json_ file. Refer to _freetier-advanced_ and _freetier-basics_
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  ダウンロード・フォルダに保存します。

このスタックを使用して、インスタンスを含む自己完結型/専用VCNを作成することを強くお薦めします。推奨事項に従うには、_タスク3_にスキップします。既存のVCNを使用する場合は、次のタスクに進み、必要なイングレス・ルールで既存のVCNを更新します。

## タスク2: 既存のVCNへのセキュリティ・ルールの追加

このワークショップでは、特定の数のポートを使用可能にする必要があります。専用VCNを作成するデフォルトのORMスタック実行を使用して満たすことができる要件です。既存のVCN/サブネットを使用するには、次のルールをセキュリティ・リストに追加する必要があります。

| タイプ | ソースポート | ソースCIDR | 宛先ポート | プロトコル | 摘要 |
| :-- | :-: | :-: | :-: | :-: | :-- |
| イングレス | すべて | 0.0.0.0/0 | 22 | TCP接続を使う | SSH |
| イングレス | すべて | 0.0.0.0/0 | 80 | TCP接続を使う | noVNCを使用したリモートデスクトップ |
| Egress | すべて | 該当なし | 80 | TCP接続を使う | アウトバウンドHTTPアクセス |
| Egress | すべて | 該当なし | 443 | TCP接続を使う | アウトバウンドHTTPSアクセス |
| {: title="必要なネットワーク・セキュリティ・ルールのリスト"} |  |  |  |  |  |

1.  _Networking >> Virtual Cloud Networks_にアクセスします
2.  ネットワークの選択
3.  「Resources」の下の「Security Lists」を選択します
4.  「Create Security List」ボタンの下にある「Default Security Lists」をクリックします。
5.  「Add Ingress Rule」ボタンをクリックします
6.  次を入力します。
    *   ソース・タイプ: CIDR
    *   ソースCIDR: 0.0.0.0/0
    *   IPプロトコル: TCP
    *   ソース・ポート範囲: すべて(デフォルトのまま)
    *   宛先ポート範囲: _上の表から選択_
    *   説明: _前述の表から対応する説明を選択します_
7.  「Add Ingress Rules」ボタンをクリックします
8.  表にリストされているポートごとにルールが作成されるまで、ステップ\[5-7\]を繰り返します

## タスク3: コンピュートの設定

前述の2つのタスクの詳細を使用して、演習の_環境設定_に進み、Oracle Resource Manager (ORM)および次のいずれかのオプションを使用してワークショップ環境を設定します。

*   スタックの作成: _コンピュート+ネットワーキング_
*   スタックの作成: 前述の_タスク2_に従ってセキュリティ・リストが更新された既存のVCNと_コンピュートのみ_

次の演習に進むことができます。

## 確認

*   **作成者** - Rene Fontcha、LiveLabs Platform Lead、NA Technology
*   **貢献者** - Meghana Banka
*   **最終更新者/日付** - Rene Fontcha、LiveLabs Platform Lead、NA Technology、2022年8月