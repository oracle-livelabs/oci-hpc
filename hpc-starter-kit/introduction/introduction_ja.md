# 概要

## 概要

High Performance Computingは、製品開発と調査を変え、複雑な問題をより迅速に解決できるようにしています。これにより、プロトタイプが減少し、テストが促進され、タイムツーマーケットが短縮されます。Oracleは、最も高度なコンピュート、ストレージ、ネットワーキングおよびソフトウェア・テクノロジに基づいて、あらゆるHPCワークロードに適したオンデマンドHPCインフラストラクチャを提供します。これらすべてを自分で構築するためのわずかなコストで実現し、容量使用率の問題を回避できます。

Oracleは、HPCアプリケーションを実行するための最も柔軟でスケーラブルなクラウド・インフラストラクチャを提供します。ほぼ無制限の容量を持つエンジニア、研究者およびHPCシステム所有者は、オンプレミスのHPCインフラストラクチャの制限を超えて革新できます。Oracleは、クラウド内のHPCクラスタをすばやく簡単に構築および管理するために必要なものすべてを提供する統合サービス・スイートであり、様々な業種にわたって最もコンピュート集中型のワークロードを実行します。これらのワークロードは、ゲノミクス、計算化学、財務リスク・モデリング、コンピュータ支援エンジニアリング、地震イメージング、天気予報などの従来のHPCアプリケーションにまたがり、機械学習、ディープ・ラーニング、自律運転などの新しいアプリケーションにも及びます。

Oracle Cloud Infrastructureを使用すると、企業は数百万のIOP、ミリ秒のレイテンシ、および多くのGB/sの帯域幅を必要とするパフォーマンス集中型のHPCワークロードを従量課金制または非従量制モデルで実行できるため、3年間のTCOで32%節約できます。

これらのハンズオン・ラボ・ガイドでは、Oracle Cloud InfrastructureでHigh Performance Computingプラットフォームを設定して使用するための手順を紹介しています。

見積ワークショップ時間: 4時間

### High Performance Computing(HPC)について

HPC、つまりスーパーコンピューティングは、日常のコンピューティングと同様で、より強力です。複数のコンピュータとストレージ・デバイスをまとまりのあるファブリックとして使用して、大量のデータを非常に高速に処理する方法です。HPCは、科学、エンジニアリング、ビジネスにおける世界最大の問題のいくつかについて調査し、答えを見つけることを可能にします。

クラスタ・ネットワークは、高帯域幅の超低レイテンシ・ネットワークに接続された高パフォーマンス・コンピューティング(HPC)インスタンスのプールです。クラスタ内の各ノードは、他のノードに近い物理的に配置されているベア・メタル・マシンです。ノード間のリモート・ダイレクト・メモリー・アクセス(RDMA)ネットワークは、オンプレミスHPCクラスタに匹敵する、1桁のマイクロ秒という低レイテンシを提供します。

High Performance Computingは、OCIリージョン内のOracle Cloud Infrastructureで提供されています。

Oracle MarketplaceイメージおよびOCIのBM.HPC2.36で使用可能なHigh Performance Computingインスタンス。

Oracle Marketplace ImageのHigh Performance Computingラックには、HPCクラスタ・ノード、クラスタ・ネットワークおよびNFS共有が含まれます。

計算ノードは、計算ノードへのRDMAベースのストレージアクセスを提供するクラスタネットワーク経由で接続されます。

現在、計算ノードごとに1つのBMがサポートされています。ハードウェアとネットワークを保護しながら顧客にルート・アクセスを許可し、コンピュート・ノードはBM.HPC2.36を使用して仮想化されます。

### 目的

この演習では、次のことを行います。

*   Oracle Cloud Infrastructureでのプライベート・ネットワークの準備
*   プライベートOCIネットワークでのHigh Performance Computeインスタンスのプロビジョニング
*   High Performance Computeインスタンスで使用する開発システムを構成します
*   OCI CLIコマンドを使用して、High Performance Computeインスタンスを操作します
*   Terraformによる自動化
*   High Performance ComputeインスタンスでOpenFoamプロジェクトを実行します

### 前提条件

*   クラウドとデータベースの用語を理解する
*   Oracle Cloud Infrastructure(OCI)の知識が役に立つ
*   ネットワークの知識が役に立つ

## このワークショップについて

*   演習1: Oracle Cloud InfrastructureでのHigh Performance Computeインスタンスの設定。
    
*   演習2: OCI CLIツールを使用したHigh Performance Computeインスタンスの管理。
    
*   演習3: Oracle Cloud InfrastructureのOpenFOAM CFDソフトウェアの評価を支援するように設計されています。
    

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **最終更新者/日付** - Harrison Dvoor、2020年10月