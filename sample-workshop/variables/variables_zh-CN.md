# LiveLabs 中的变量

## 简介

您可以在另一个文件中指定变量，然后在 Markdown 中引用它们。

## 任务 1：

将以下内容添加到顶部的 manifest.json 中：

       "variables": ["../../variables/variables.json",
                     "../../variables/variables-in-another-file.json"],
    

## 任务 2

按如下方式在 .json 文件中指定变量：

_示例：variables.json_

    {
        "var1": "Variable 1 value",
        "var2": "Variable 2 value",
        "random_name": "LiveLabs rocks!",
        "number_of_ocpu_paid": "24"
        "number_of_ocpu_always_free": "2"
     }
    

您还可以添加多个指定变量的文件（请参见任务 1 中的示例）。

_示例：variables\_in\_another\_file.json_

    {
        "var11": "Variable 1 value but yet different",
        "var22": "Variable 2 value is different",
        "random_name2": "LiveLabs rocks & rules!",
        "name_of_database": "My-Database-Name-is-the-best",
        "magic": "What is 2*2?"
     }
    

## 任务 3

现在，可以使用以下语法引用这些变量（**请注意，只能在 Markdown 中看到语法**）：

[](var:var1)

或者

[](var:magic)

### 示例

（检查标记以查看语法 - 粗体文本是变量的值）

*   你知道数学吗？这是**[](var:magic)**
    
*   在我的付费租户中需要运行此服务多少 OCPU？您需要**[](var:number_of_ocpu_paid)**
    
*   但如果使用“始终免费”怎么办？然后您需要**[](var:number_of_ocpu_always_free)**
    
*   数据库的最佳名称是什么？它是**[](var:name_of_database)**
    
*   您可以在此处查找更多信息：**[](var:doc_link)**