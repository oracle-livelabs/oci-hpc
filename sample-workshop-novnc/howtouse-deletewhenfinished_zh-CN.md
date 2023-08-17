# 具有一组实验室的研讨会

## 说明 - 完成后删除此文件

1.  在 Atom 或 Visual Studio Code 中打开示例研讨会模板
2.  我们预先创建了 5 个文件夹。研讨会由多个实验组成。
3.  删除类似以下评论：_列出此实验室的目标_
4.  请确保为空格使用小写文件夹和文件名和短划线 (setup-adb NOT Setup\_ADB)
5.  您的图像名称应该具有说明性名称。不仅 adb1、adb2、adb3。为了方便访问，我们需要图像说明来解释图像的外观。请记住所有小写和短划线。
6.  下载 WMS 上的 QA 反馈文档。如果您知道在前沿生产和使用骨架需要什么，我们可以发现研讨会更快地投入生产。

PS：不需要 Readme.md。只在顶级库级别存在自述文件。我们会将所有流量定向到 LiveLabs，因为我们无法跟踪 GitHub 上的使用量。不要直接创建到 GitHub 的链接，因为您的研讨会可能非常流行，但我们无法跟踪它，因此没有人会知道。

## Oracle Cloud 菜单导航的绝对路径

**练习 1：预配实例 -> 步骤 0：为 Oracle Cloud 导航使用这些标准化图片（通常用于预配）** - 我们提供了用于导航 Oracle Cloud 菜单的常用屏幕截图列表。请阅读本节并酌情使用相关的绝对路径图像。在更新 Oracle Cloud 用户界面时，这将为您的研讨会提供未来验证。

## 文件夹结构

在本例中，目标是从一个更长的“父母”研讨会创建几个“儿童”研讨会。子项由父项中的部分组成。

*   样品研讨会/
    *   个人实验室
        *   prepare-setup/ -- 包含用于下载研讨会的 ORM/Terraform 堆栈的链接
        *   initialize-environment/ -- 预配后初始化环境的任务
        *   预配/ -- 第一个虚构实验室
        *   dataload/ -- 第二个虚构实验室
        *   查询/ -- 第三虚构实验室
        *   简介/
            *   introduction.md - 关于所有研讨会的说明，请注意，这是一个“练习”，因为只有一个
    *   研讨会/
        *   桌面/ -- 研讨会的_必需_桌面版本（所有实验）
            *   index.html
            *   manifest.json
        *   桌面高级/ -- 研讨会的桌面版本（简介、初始化环境、数据加载、查询）
            *   index.html
            *   manifest.json
        *   桌面基础/- 研讨会的桌面版本（介绍、初始化环境、配置）
            *   index.html
            *   manifest.json
        *   tenancy/ -- 研讨会的_必需_租户版本（所有实验）
            *   index.html
            *   manifest.json
        *   租户高级/ -- 研讨会的租户版本（简介、初始化环境、数据加载、查询）
            *   index.html
            *   manifest.json
        *   tenancy-basics/ -- 研讨会的租户版本（简介、初始化环境、提供）
            *   index.html
            *   manifest.json
        *   沙盒/ -- 研讨会的_必需_沙盒版本（所有实验）
            *   index.html
            *   manifest.json
        *   沙盒高级/ -- 研讨会的沙盒版本（简介、初始化环境、数据加载、查询）
            *   index.html
            *   manifest.json
        *   沙盒基本/ -- 研讨会的沙盒版本（介绍、初始化环境、提供）
            *   index.html
            *   manifest.json

### 租户、沙盒与桌面

*   "desktop"- 在 noVNC 远程桌面会话中显示的工作室指南_必需_
*   “租户”- 使用客户租户_必需_预配的研讨会
*   "sandbox" - _需要_使用“在 LiveLabs 沙盒上保留研习会”预配的研习会
*   "desktop-xxxxxxx"-noVNC 远程桌面会话中显示的子集或小型工坊指南
*   "tenancy-xxxxxxx" - 使用客户租户预配的子集或小型工坊（棕色按钮）
*   "sandbox-xxxxxxx" - 使用 "Reserve Workshop on LiveLabs Sandbox" 预配的子集或小型研讨会（绿色按钮）

### 关于研习会

研习会按一个序列列出所有 6 个实验室。

文件夹结构包括一个介绍“练习”，将研习会描述为一组完整的 6 个实验。注：对于研讨会的每个父版本和子版本，您可能不需要进行不同的介绍，这仅供说明。

查看 product-name-workshop/tenancy 文件夹并查看 manifest.json 文件以查看结构。

> **注：**标题中使用 "Lab n:" 是可选的

先决条件“lab”是 oracle/learning-library repo 上公用文件夹中的第一个练习。由于此实验已经存在，因此我们可以改用 RAW/absolute URL：

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

manifest.json 文件需要知道每个实验相对于分层结构中的位置的位置。在此结构中，实验室位于两个级别上，例如：

    "filename": "../../provision/provision.md"
    

### 例如：

本 [APEX 研讨会](https://oracle-livelabs.github.io/apex/spreadsheet/)是包含一组实验室的研讨会的一个很好的示例：[https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet) 。