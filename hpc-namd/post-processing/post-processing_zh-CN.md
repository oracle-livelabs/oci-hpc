# 后处理

## 简介

在此实验室中，您将使用可视分子动力学 (Visual Molecular Dynamics，VMD) 软件分析后处理模型。

估计实验室时间：5 分钟

## 任务：后处理

1.  对于后处理，您可以使用可视分子动力学 (Visual Molecular Dynamics，VMD) 软件来分析模型。运行以下命令以配置 VMD：
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  如果使用 vnc，请启动 vncserver 并按如下方式创建 vnc 密码：
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  使用 localhost:5901 启动 vnc 连接（确保已配置隧道），然后运行以下命令启动 VMD：
    
        <copy>
        vmd
        </copy>
        
4.  在 /mnt/block/work/NAMD\_models 中打开 apoa1 和 stmv pdb 文件，然后开始使用模型。
    

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月