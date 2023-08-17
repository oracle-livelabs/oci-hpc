# 将 Gromacs 安装程序添加到对象存储

## 简介

在本练习中，您将向对象存储添加 Gromacs 安装程序。

估计实验室时间：5 分钟

## 任务：将 Gromacs 安装程序添加到对象存储

_请注意，此 github 中提供的 terraform 脚本 [zip file](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip) 已经包含 Gromacs 2020.1、VMD 1.9.3 的对象存储 URL 以及 [Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench) 中的示例基准测试模型。如果要更改这些 URL，请修改 variable.tf 文件并将 gromacs\_url、model\_url 和 visualizer\_url 的值替换为您自己的预先验证的请求 URL。_

1.  选择左上方的菜单 ![](./images/menu.png)，然后选择“Object Storage（对象存储）”>“Object Storage（对象存储）”。
2.  创建新存储桶或选择一个现有存储桶。要创建一个，请单击![](./images/create_bucket.png)
3.  保留默认选项：“Standard as Storage tiers（标准为存储层）”和“Oracle Managed Key（托管密钥）”。单击![](./images/create_bucket.png)
4.  单击新创建的存储桶名称，然后选择![](./images/upload_object.png)
5.  选择您的 Gromacs 安装程序 tar 文件并单击![](./images/upload_object.png)
6.  单击刚上载对象右侧的 3 个点，然后选择“Create Pre-Authenticated Request（创建预先验证的请求）”
7.  在以下菜单中，保留默认选项并为安装程序的 URL 选择到期日期。单击![](./images/pre-auth.png)
8.  在下一个窗口中，复制 "PRE-AUTHENTICATED REQUEST URL" 并保留该 URL。关闭此窗口后，您将无法检索它。如果丢失或过期，始终可以重新创建另一个预先验证的请求，这将生成不同的 URL

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月