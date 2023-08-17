# 运行 Faceswap

## 简介

现在我们已经准备好了计算实例（请参阅练习 4），我们可以继续运行 Faceswap。在实验室中，您将学习将培训数据复制到计算实例、从培训数据中提取面孔、使用提取面孔训练 GAN 以及使用交换面孔转换视频。

估计研讨会时间：60 分钟（不包括模型培训）

### 目标

*   将您的培训数据（照片或具有要交换面孔的视频）复制到计算实例
*   [从照片或视频中提取](https://forum.faceswap.dev/viewtopic.php?f=25&t=27)面孔
*   [训练](https://forum.faceswap.dev/viewtopic.php?f=27&t=146)具有提取面孔的 GAN
*   [转换](https://forum.faceswap.dev/viewtopic.php?f=24&t=1083)并用脸 B 在源视频中交换面 A

### 你需要什么？

*   完成实验室 1 到实验室 4

## 任务 1：连接到 VNC 桌面

1.  使用以下命令创建到 VNC 服务器的 SSH 隧道（其中，private-key 是您的专用 OpenSSH 密钥，public\_ip 是来自 Ubuntu 计算实例的专用 IP 地址）：

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  使用 VNC 客户机连接到 **127.0.0.1：5901** 。 ![](images/tigervnc.PNG " ")
    
3.  当提示输入口令时，输入在 VNC 服务器配置期间创建的口令。 ![](images/tigervncpwd.PNG " ")
    

您的远程 Ubuntu 桌面将打开。 ![](images/vncdesktop.PNG " ")

## 任务 2：在 VNC 桌面上打开 Faceswap

1.  通过右键单击桌面并选择**此处打开终端**来打开终端。 ![](images/open-terminal.PNG " ")
    
    终端将在 VNC 桌面上打开。
    
    ![](images/vnc-terminal.PNG " ")
    

如果无法打开终端，请在本地计算机上的终端中运行以下命令。

    <copy>sudo apt remove gnome-terminal</copy>
    

    <copy>sudo apt install gnome-terminal</copy>
    

2.  导航到您的 facewap 文件夹。

    <copy>cd cd ../faceswap/</copy>
    

通过执行以下命令启动 Faceswap GUI：

    <copy>bash faceswap_gui_launcher.sh</copy>
    

Faceswap GUI 将打开。

![](images/faceswapGUI.PNG " ")

## 任务 3：将培训数据复制到计算实例

要培训 Faceswap GAN，您需要提供人员 A 和人员 B 的图片或视频，以供您交换面孔。在本示例中，我们将使用 WinSCP 将人员 A 和人员 B 中的两个视频从本地计算机复制到远程 Ubuntu 实例。

1.  在本地计算机上下载并安装 [WinSCP](https://winscp.net/eng/download.php) 。
    
2.  在本地计算机上打开 WinSCP，然后使用左侧的视频打开文件夹 side.F ![](images/open-winscp.PNG " ")
    
3.  单击**新建站点**。此时将弹出一个新窗口。 ![](images/winscp-newsite.PNG " ")
    
4.  在 _hostname_ 字段中填写实例的 **IP address** ，在 _user name_ 字段中填写 **Ubuntu** ，如下所示。 ![](images/winscp-hostname-username.PNG " ")
    
5.  单击**高级 ...** 按钮。
    
6.  单击窗口左侧 **SSH** 下的**验证**。
    
7.  单击三个点，然后选择文件夹中的 SSH 私有密钥文件，如下所示。 ![](images/winscp-ppk.PNG " ")
    
8.  单击**确定**
    
9.  单击**登录**
    
10.  此时将弹出一个警告屏幕。单击**是**。 ![](images/winscp-warning.PNG " ")
    
11.  您可以在左侧查看本地文件文件夹，在 WinSCP 工具的右侧查看远程文件文件夹。 ![](images/winscp-auth-success.PNG " ")
    
12.  通过右键单击右侧的字段，选择 **New -> Directory** 并键入文件夹 **src** 的名称，在远程文件夹中创建文件夹 **src** 。
    
13.  选择左侧的两个视频（本地计算机），然后将其拖到右侧的 **src** 文件夹（远程计算机）。
    

## 任务 4：从照片和视频中提取面孔

第一步，是从视频 A 和视频 B 中提取面孔。

1.  在 Faceswap GUI 中，选择 **Extract** 选项卡。
    
2.  在**数据**字段中，您可以看到**输入目录**，在其中可以选择输入视频或输入图像所在的目录。我们将通过单击视频按钮（第一个，左侧）并从人员 A 中从 **src** 文件夹中选择输入视频（我们在步骤 3 中创建和填充），选择输入视频。
    

![](images/GUI-inputdir.PNG " ")

**注：**如果从输入图像中提取面孔，请确保具有介于 1000 和 10.000 之间的图像。

3.  单击**输出目录**旁边的文件夹按钮。选择将保存源视频的已提取面图像的文件夹。您可以调用目录，例如 _faceswap/faces/personA_ ，然后单击 **OK** 。

![](images/GUI-outputdir.PNG " ")

4.  可选：如果您希望阅读有关提取插件的更多信息并使用它们，请访问 [Faceswap 提取指南](https://forum.faceswap.dev/viewtopic.php?t=27)。在本示例中，我们将使用默认插件。
    
5.  单击**提取**。您将在右侧的**预览**选项卡中看到视频中提取的面孔。在底部字段中，您会看到 Python 提取脚本的状态，例如已初始化的插件、找到图像、检测到的面，以及在出现错误时。
    

![](images/GUI-extract.PNG " ")

6.  从**第 4 步**重复 1.-5。
    
7.  可选：如果不希望使用 GUI，可以使用以下命令在终端中执行面部提取（仅调整输入和输出目录和插件，以防要使用其他插件）：
    

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py extract -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/faces/personA -D s3fd -A fan -nm none -rf 0 -min 0 -l 0.4 -sz 512 -een 1 -si 0 -L INFO</copy>
    

![](images/extract-terminal.PNG " ")

## 任务 5：培训 GAN

现在我们已经从视频中提取了 A 和 B 人的脸，我们可以训练 Faceswap GAN。

1.  在 Faceswap GUI 中，选择 **Train（训练）**选项卡。
    
2.  在 **Faces（空间）**字段中，您可以看到 **Input A（输入 A）**，在其中选择人员 A 的提取面目录（在本例中为 _faceswap/faces/personA_ ）。
    

![](images/GUI-faces-input.PNG " ")

3.  下面是**对齐 A** 行。对齐文件保存每个框架中找到的所有面的信息，特别是面部所在的位置以及标志点的位置（例如眼睛、鼻子、嘴唇）。它是在提取阶段创建的，并存储在人员 A 的视频的源文件夹中。单击“文件”按钮并在 **src** 文件夹中选择人员 A 的对齐文件，如下所示。

![](images/GUI-alignmentA.PNG " ")

4.  对人员 B 重复点 1. 和 2。
    
5.  在**模型**字段中，单击**模型目录**旁边的文件夹按钮。选择将保存培训模型的文件夹。您可以调用目录，例如 _faceswap/model_ ，然后单击 **OK** 。
    
6.  可选：如果要阅读有关培训师的更多信息或“数据扩充”选项并使用，请访问 [Faceswap 培训指南](https://forum.faceswap.dev/viewtopic.php?f=27&t=146)。在本示例中，我们将使用默认插件。
    
7.  单击**培训**按钮以开始培训。
    

![](images/GUI-train.PNG " ")

在右侧的**预览**选项卡中，您将在视频帧中看到人员 A 和人员 B 的交换面。

**图形**选项卡包含一个显示一段时间内损失的图形。它每次保存模型时都会更新，但可以通过单击“刷新”按钮刷新。对于每批注入模型的面孔，GAN 将观察它试图重新创建并比较它与实际的脸。根据它认为它的表现，它将给自己一个得分（损失值）。

8.  可选：如果不希望使用 GUI，可以使用以下命令在终端执行模型训练（仅调整输入目录、对齐文件、模型目录和训练器选项，以防您希望使用其他训练器选项）：

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py train -A /home/ubuntu/faceswap/faces/personA -ala /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -B /home/ubuntu/faceswap/faces/personB -alb /home/ubuntu/faceswap/src/personB/personB_alignments.fsa -m /home/ubuntu/faceswap/model -t original -bs 16 -it 1000000 -s 250 -ss 25000 -ps 100 -L INFO</copy>
    

![](images/terminal-train.PNG " ")

了解模型是否已完成培训的最佳方法是观看预览。最终，这些显示实际交换的外观。您还可以检查损失值。损失值越接近零，完成培训的概率就越高。

![](images/graph-loss-iterations.PNG " ")

在**分析**选项卡中，您可以检查最后一列中的值 _EGs/sec_ ，该值将提供每秒通过模型处理的面数。您的 (GPU) 核心越强大， _EGs/sec_ 越高，模型的聚合速度越快。

9.  使用 nvidia-smi 命令在训练期间检查 GPU 使用情况。 ![](images/nvidia-smi-GPU.PNG " ")
    
    它将显示易变的 GPU 占用率。
    
10.  可选：可以通过提高 GPU 核心和内存时钟速率来提升应用性能。有关详细信息，请访问有关 [Nvidia GPU 提升](https://developer.nvidia.com/blog/increase-performance-gpu-boost-k80-autoboost/)的 Nvidia 博客文章。
    

## 任务 6：使用交换的脸转换您的视频

如果培训结束后，您可以使用面部 B 在源视频中转换视频并交换面部 A。

1.  在 Faceswap GUI 中，选择 **Convert** 选项卡。
    
2.  在**数据**字段中，您可以看到**输入目录**，在该目录中，您可以选择输入视频所在的目录，以及要从中交换面孔的人员。在本例中，输入视频存储在 _/faceswap/src/personA_ 下
    
3.  单击**输出目录**旁边的文件夹按钮。选择将保存输出视频的文件夹。您可以调用目录，例如 _/faceswap/outputvideo_ ，然后单击 **OK** 。
    
4.  单击**对齐**旁边的“文件”按钮，然后从要交换面的人员中选择对齐文件（在本例中为人员 A）。如果对齐文件与**输入目录**位于同一目录中，则可以将其留空。
    
5.  可选：仅当您将图像转换为视频时，才需要**参考视频**。在这种情况下，我们将其留空。
    
6.  在**模型**字段中，单击**模型目录**旁边的文件夹按钮。选择保存培训模型的文件夹。在这种情况下，即目录 _faceswap/model_ 。单击**确定**。
    
7.  单击**转换**。
    

![](images/GUI-convert.PNG " ")

8.  可选：如果不希望使用 GUI，可以使用以下命令在终端中进行转换（仅调整输入和输出目录、对齐文件、模型目录和插件，以防要使用其他插件）：

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py convert -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/outputvideo -al /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -m /home/ubuntu/faceswap/model -c avg-color -M extended -w opencv -osc 100 -l 0.4 -j 0 -L INFO</copy>
    

![](images/convert-terminal.PNG " ")

9.  在目录 _/faceswap/outputvideo_ 中，将找到人员 A 的输入视频的框架，该视频具有人员 B 的交换面。 ![](images/outputvideo-dir.PNG " ")
    
    我们将使用 ffmpeg 从单个帧构建视频。
    
    让我们先安装 ffmpeg。
    
        <copy>sudo apt install ffmpeg</copy>
        
    
    现在，我们可以使用 ffmpeg 构建视频，将单个帧组合到一个视频中，在此视频中，我们将循环遍历 _outputvideo_ 目录中包含的所有 personA 帧，其中 _%nd_ 表示连续 n 位数字。在本例中，n=6。
    
        <copy>ffmpeg -i personA_%6d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4</copy>
        
10.  观看[输出视频](https://www.youtube.com/watch?v=b-uKJ89QSnE)
    

## **确认**

*   **创建者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月
*   **上次更新者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月