# 运行应用程序

## 简介

在本练习中，您将运行应用程序。

估计实验室时间：5 分钟

## 任务：运行应用程序

如果提供的 terraform 脚本用于启动应用程序，则使用 CUDA 的 NAMD 将安装在 /mnt/block/NAMD/NAMD\_2.13\_CUDA 文件夹中，/mnt/block/work/NAMD\_models 文件夹中提供两个示例模型。

1.  通过以下命令使用 CUDA 运行 NAMD：
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    其中：- +p - CPU 核心数 - +setcpuaffinity - 以循环方式将线程/进程分配给可用核心，其编号顺序为操作系统 - +devices - GPU 设备数 - +idlepoll - 轮询 GPU 以获得结果，而不是在空闲时休眠 - +commap - 与 GPU 的通信映射 - output.txt - 使用分析输出文件
    
    BM.GPU2.2 的示例：
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    BM.GPU3.8 的示例：
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  对输出文件运行 ns\_per\_day.py，以通过记录的计时语句计算平均每天的纳秒数。这用于使用当前的 CPU/GPU 配置标识应用程序的性能和效率。
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月