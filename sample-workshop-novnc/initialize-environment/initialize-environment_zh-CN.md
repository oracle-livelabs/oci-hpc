# 初始化环境

## 简介

在本练习中，我们将复习并启动成功运行此研习会所需的所有组件。

_估计的实验室时间：_ 30 分钟。

### 目标

*   初始化研习会环境。

### 前提条件

本练习假定您具有：

*   Oracle Cloud 账户
*   您已完成：
    *   实验室：准备设置（仅限_免费套餐_和_付费租户_）
    *   实验室：环境设置

> **注：** _使用方便的_ **复制** _在执行复制/粘贴时，您必须在粘贴后命中_ **ENTER** _密钥。否则，最后一行将保留在缓冲区中，直到您命中_ **ENTER！**

## 任务 1：验证所需的流程是否已启动且正在运行。

1.  现在，在访问远程桌面会话时，请按以下所示继续验证环境，然后再开始执行后续练习。以下进程应该已启动且正在运行：
    
    *   数据库监听程序
        *   监听程序 (1521)
        *   LISTCDB2 (1522)
    *   数据库服务器实例
        *   CDB1
        *   CDB2
    
    您可以通过单击数据库旁边的 _+_ 符号测试数据库连接，如下面的 _SQL Developer Oracle 连接_面板中所示。
    
    ![](./images/19c_hol_landing.png " ")
    
2.  单击桌面上的 _Terminal（终端）_图标以启动会话，然后运行以下命令以验证预期进程是否已启动。
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    如果所有预期进程都如上所示显示在输出中，则您的环境已准备好执行下一个任务。
    
3.  如果看到可疑的输出、故障或关闭的组件，请相应地重新启动服务
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## 任务 2：为多租户用例初始化数据库

1.  从远程桌面会话中，以 _oracle_ 用户身份运行以下块以刷新练习构件
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

您现在可以[进入下一个实验](#next)。

## 附录 1：管理启动服务

1.  数据库服务（所有数据库和标准监听程序）。
    
    *   启动
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   停止
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   状态
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   重新启动
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  数据库服务（非标准监听程序）。
    
    *   启动
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   停止
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   状态
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   重新启动
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## 确认

*   **作者** - Oracle Database In-Memory 高级产品经理 Andy Rivenes
*   **贡献者** - Kay Malcolm、Didi Han、Rene Fontcha
*   **上次更新者/日期** - NA Technology 的 LiveLabs 平台主管 Rene Fontcha，2021 年 10 月