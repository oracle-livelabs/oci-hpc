# OCIでのHPCクラスタおよびStandard VMでのOpenFOAMプロジェクトの実行

## 概要

このランブックは、Oracle Cloud InfrastructureのOpenFOAM CFDソフトウェアの評価を支援するように設計されています。OpenFOAMが自動的にダウンロードおよび構成されます。OpenFOAMは、2004年以降主にOpenCFD Ltdがリリースおよび開発した無料のオープン・ソースCFDソフトウェアです。商業組織と学術組織の両方から、エンジニアリングおよびサイエンスのほとんどの分野に大規模なユーザー基盤があります。OpenFOAMには、化学反応、乱流および熱伝達を含む複雑な流体フローから音響学、固体機械学、電磁学に至るまで、様々な機能があります。

### High Performance Computing(HPC)について

HPC、つまりスーパーコンピューティングは、日常のコンピューティングと同様で、より強力です。複数のコンピュータとストレージ・デバイスをまとまりのあるファブリックとして使用して、大量のデータを非常に高速に処理する方法です。HPCは、科学、エンジニアリング、ビジネスにおける世界最大の問題のいくつかについて調査し、答えを見つけることを可能にします。

クラスタ・ネットワークは、高帯域幅の超低レイテンシ・ネットワークに接続された高パフォーマンス・コンピューティング(HPC)インスタンスのプールです。クラスタ内の各ノードは、他のノードに近い物理的に配置されているベア・メタル・マシンです。ノード間のリモート・ダイレクト・メモリー・アクセス(RDMA)ネットワークは、オンプレミスHPCクラスタに匹敵する、1桁のマイクロ秒という低レイテンシを提供します。

High Performance Computingは、OCIリージョン内のOracle Cloud Infrastructureで提供されています。

Oracle MarketplaceイメージおよびOCIのBM.HPC2.36で使用可能なHigh Performance Computingインスタンス。

Oracle Marketplace ImageのHigh Performance Computingラックには、HPCクラスタ・ノード、クラスタ・ネットワークおよびNFS共有が含まれます。

計算ノードは、計算ノードへのRDMAベースのストレージアクセスを提供するクラスタネットワーク経由で接続されます。

現在、計算ノードごとに1つのBMがサポートされています。ハードウェアとネットワークを保護しながら顧客にルート・アクセスを許可し、コンピュート・ノードはBM.HPC2.36を使用して仮想化されます。

_推定時間_: 2時間

### 目的

*   リモート・ダイレクト・メモリー・アクセス(RDMA)を介して相互接続されたHPCインスタンスを使用して、OpenFOAMプロジェクトを実行します
    
*   Oracle Cloud Infrastructure (OCI)上で通常の標準仮想マシン(VM)を使用してOpenFOAMプロジェクトを実行します
    

### 前提条件

*   コンパートメントおよびスタックを作成する権限を持つOracle Cloud Infrastructureアカウント。
    
*   OCIコンソールおよびOracle Cloudリソース(Virtual Cloud Networks、ComputeおよびStorage)に精通していることが役立ちます。
    
*   OCI Cloud Architect Associate試験が完了したと仮定します。
    

## このワークショップについて

*   HPCクラスタを使用したOpenFOAMプロジェクトの実行
    
*   OCIでStandard VMを使用してOpenFOAMプロジェクトを実行
    

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **最終更新者/日付** - Harrison Dvoor、2021年1月