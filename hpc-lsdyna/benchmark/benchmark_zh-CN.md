## **基准示例**

![](images/lemans.png " ")

STAR-CCM+ 的表现通常使用具有 17 和 105 百万个单元的 LeMans 基准来衡量。下图显示了使用更多节点如何影响运行时，缩放真正接近 100%。此运行簿中未讨论的 RDMA 网络，仅当单元/核心比率开始下降时，才开始区分与常规 TCP 运行。

**1700 万个单元**

![](images/RunTime_17M.png " ")

**1.05 亿个单元格**

![](images/Scaling_105M.png " ")

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月