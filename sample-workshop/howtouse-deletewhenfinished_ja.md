# 単一のラボ・セットを使用したワークショップ

## 指示- 終了時にこのファイルを削除します

1.  AtomまたはVisual Studio Codeでサンプル・ワークショップ・テンプレートを開きます。
2.  5つのフォルダがあらかじめ作成されています。ワークショップは複数の演習から作成されます。
3.  次のようなコメントを削除します: _この演習の目標のリスト_
4.  スペースには必ず小文字のフォルダとファイル名、ダッシュを使用してください(setup-adb NOT Setup\_ADB)
5.  イメージ名には説明的な名前が必要です。adb1、adb2、adb3だけでなく、使いやすいように、イメージの説明が必要です。すべての小文字とダッシュを覚えておいてください。
6.  WMSからQAドキュメントをダウンロードしてください。本番環境への移行に何が必要かを知っていて、スケルトンを使用すると、ワークショップの生産が速くなります。

PS: Readme.mdは必要ありません。Readmeは、ライブラリの最上位レベルにのみ存在します。GitHubでは使用状況を追跡できないため、すべてのトラフィックをLiveLabsに転送します。GitHubへの直接リンクを作成しないでください。ワークショップは人気が高いかもしれませんが、誰も知らないようにトラッキングできません。

## Oracle Cloudの絶対パス・メニュー・ナビゲーション

**ラボ1: インスタンスのプロビジョニング->ステップ0: Oracle Cloudナビゲーション用のこれらの標準化画像の使用(プロビジョニングに共通)** - Oracle Cloudメニューをナビゲートするための一般的なスクリーンショットのリストが含まれています。このセクションを読んで、必要に応じて関連する絶対パス・イメージを使用してください。これにより、Oracle Cloudユーザー・インタフェースが更新された場合に備えてワークショップが後から実証されます。

## フォルダ構造

この例では、より長い「親」ワークショップから複数の「子」ワークショップを作成することが目標です。子は親の部品で構成されます。

サンプルワークショップ/ -- 個別のラボ

        provision/
        setup/
        dataload/
        query/
        introduction/
          introduction.md       -- description of the everything workshop, note that it is a "lab" since there is only one
    
    workshops/
       freetier/                -- freetier version of the workshop
        index.html
        manifest.json
       livelabs/                -- livelabs version of the workshop
        index.html
        manifest.json
    

### FreeTierとLiveLabs

*   "FreeTier" - 無料トライアル、有料アカウント、一部のワークショップではAlways Freeアカウント(ブラウン・ボタン)が含まれます
*   LiveLabs - Oracle提供のテナンシ(緑色のボタン)を使用するワークショップ
*   デスクトップ- これは、コンピュート・インスタンスで実行されているNoVNC環境でワークショップをカプセル化する新しいデプロイメントです。

### ワークショップについて

ワークショップには、個々の6つのラボがすべて1つのシーケンスで含まれています。

フォルダ構造には、ワークショップを6つの演習の完全なセットとして説明する紹介ラボが含まれています。ノート: ワークショップの親バージョンと子バージョンごとに異なる紹介が必要ない場合があります。これは説明のみです。

product-name-workshop/freetierフォルダを確認し、manifest.jsonファイルを参照して構造を確認します。

> **ノート:**タイトルでの「Lab n: 」の使用はオプションです

前提条件のlabは、oracle/learning-libraryリポジトリの共通フォルダの最初の演習です。この演習はすでに存在するため、かわりにRAW/絶対URLを使用できます。

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

manifest.jsonファイルは、各ラボが階層内のどこに存在するかを相対的に把握している必要があります。この構造では、ラボは2つ上のレベルに配置されます。次に例を示します。

    "filename": "../../provision/provision.md"
    

### たとえば:

この[APEXワークショップ](https://oracle.github.io/learning-library/developer-library/apex/spreadsheet/workshops/freetier/)は、単一のラボ・セット[https://github.com/oracle-livelabs/APEX/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet)を使用したワークショップのよい例です。