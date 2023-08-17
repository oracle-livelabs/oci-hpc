# NAMD 簡介

此 Runbook 提供在 Oracle Cloud Infrastructure 上部署 GPU 機器、安裝 NAMD 的步驟，以及使用 NAMD 軟體執行模型。

NAMD 是一種分子動力學軟體，模擬生物分子在預先定義的一組條件下移動原子 。用來識別這些生物分子在暴露於溫度、壓力與其他輸入變化時的行為，這些輸入可模擬生活有機體中實際的情況。NAMD 可用於建立蛋白質折疊、蛋白質膠結及細胞膜輸送的模式，使其成為藥物研究和發現非常有用的應用。

NAMD 建立在 Charm++ 和 Converse 上，可以在高效能電腦上執行平行處理。由伊利諾大學發展。如需詳細資訊，請參閱[這個網頁](http://charm.cs.illinois.edu/research/moldyn)。

![](./images/protein.gif)

## **架構**

此 Runbook 的架構相當簡單，單機是在具有公用子網路的 OCI VCN 內部執行的。由於使用 GPU 執行處理，因此區塊儲存體會連附至執行處理，並已安裝 NAMD 應用程式。執行處理位於公用子網路，並且會指定可透過 ssh 存取的公用 IP。

![](./images/arch-draft.png)

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月