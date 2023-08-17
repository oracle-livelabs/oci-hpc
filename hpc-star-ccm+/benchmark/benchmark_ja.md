## **ベンチマークの例**

![](images/lemans.png " ")

多くの場合、STAR-CCM+のパフォーマンスは、1,700万および1億500万のセルを含むLeMansベンチマークを使用して測定されます。次のグラフは、より多くのノードを使用するとランタイムにどのように影響するかを示しています。スケーリングは100%近くになります。このランブックでは説明されていないRDMAネットワークは、セル/コアの比率が低下し始めると、通常のTCP実行とを区別し始めます。

**1,700万セル**

![](images/RunTime_17M.png " ")

**1億500万セル**

![](images/Scaling_105M.png " ")

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月