# 准备设置

## 简介

此实验室将向您展示如何下载设置运行此研习会所需的资源所需的 Oracle 资源管理器 (ORM) 堆栈 zip 文件。

_估计的实验室时间：_ 10 分钟

### 目标

*   下载 ORM 堆栈
*   配置现有虚拟云网络 (VCN)

### 前提条件

本练习假定您具有：

*   Oracle Cloud 账户

## 任务 1：下载 Oracle Resource Manager (ORM) 堆栈 zip 文件

1.  单击下面的链接下载构建环境所需的资源管理器 zip 文件：
    
    _注 1：_如果为研习会提供一次堆栈下载，请使用此简单表达式。
    
    *   [示例 -mkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _注 2：_如果为同一研习会提供多个堆栈下载，请使用如下所示的条件表达式。请注意，条件或 _type_ 必须与 _manifest.json_ 文件中的有效条目配对。请参阅 _freetier-advanced_ 和 _freetier-basics_
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  保存到您的下载文件夹中。

我们强烈建议使用此堆栈创建包含实例的自包含/专用 VCN。跳至_任务 3_ 以遵循我们的建议。如果您希望使用现有 VCN，请继续执行下一个任务，使用所需的入站规则更新现有 VCN。

## 任务 2：向现有 VCN 添加安全规则

此研讨会要求提供一定数量的端口，这是使用创建专用 VCN 的默认 ORM 堆栈执行可以满足的要求。要使用现有 VCN/子网，应将以下规则添加到安全列表中。

| 类型 | 源端口 | 源 CIDR | 目标端口 | 协议 | 说明 |
| :-- | :-: | :-: | :-: | :-: | :-- |
| 入站 | 全部 | 0.0.0.0/0 | 22 | TCP | SSH |
| 入站 | 全部 | 0.0.0.0/0 | 80 | TCP | 使用 noVNC 的远程桌面 |
| 出站 | 全部 | 不适用 | 80 | TCP | 出站 HTTP 访问 |
| 出站 | 全部 | 不适用 | 443 | TCP | 出站 HTTPS 访问 |
| ｛：title=“所需网络安全规则列表”｝ |  |  |  |  |  |

1.  转到 _Networking >> Virtual Cloud Networks_
2.  选择您的网络
3.  在“Resources（资源）”下，选择“Security Lists（安全列表）”
4.  单击“Create Security List（创建安全列表）”按钮下的“Default Security Lists（默认安全列表）”
5.  单击“Add Ingress Rule（添加入站规则）”按钮
6.  输入以下信息：
    *   源类型：CIDR
    *   源 CIDR：0.0.0.0/0
    *   IP 协议：TCP
    *   源端口范围：全部（保留默认值）
    *   目标端口范围：_从上表中选择_
    *   说明：_从上表中选择相应的说明_
7.  单击“Add Ingress Rules（添加入站规则）”按钮
8.  重复步骤 \[5-7\]，直到为表中列出的每个端口创建规则

## 任务 3：设置计算

使用上述两个任务中的详细信息，转到实验室_环境设置_，使用 Oracle Resource Manager (ORM) 和以下选项之一设置您的研习会环境：

*   创建堆栈：_计算 + 网络_
*   创建堆栈：使用现有 VCN _仅计算_，其中安全列表已根据上面的_任务 2_ 更新

您现在可以进入下一个实验室。

## 确认

*   **作者** - NA Technology 的 LiveLabs 平台主管 Rene Fontcha
*   **贡献者** - Meghana Banka
*   **上次更新日期** - Rene Fontcha，LiveLabs Platform Lead，NA Technology，2022 年 8 月