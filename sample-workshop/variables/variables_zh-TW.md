# LiveLabs 中的變數

## 簡介

您可以在其他檔案中指定變數，並在 Markdown 中參考這些變數。

## 工作 1：

將下列項目新增至頂端區段中的 manifest.json：

       "variables": ["../../variables/variables.json",
                     "../../variables/variables-in-another-file.json"],
    

## 任務 2

指定 .json 檔案中的變數，如下所示：

_範例：variables.json_

    {
        "var1": "Variable 1 value",
        "var2": "Variable 2 value",
        "random_name": "LiveLabs rocks!",
        "number_of_ocpu_paid": "24"
        "number_of_ocpu_always_free": "2"
     }
    

您也可以新增多個指定變數的檔案 (請參閱「任務 1」中的範例)。

_範例：variables\_in\_another\_file.json_

    {
        "var11": "Variable 1 value but yet different",
        "var22": "Variable 2 value is different",
        "random_name2": "LiveLabs rocks & rules!",
        "name_of_database": "My-Database-Name-is-the-best",
        "magic": "What is 2*2?"
     }
    

## 任務 3

現在，您可以使用下列語法參考這些變數 ( **請注意，您只能在 Markdown 中看到語法** )：

[](var:var1)

或者

[](var:magic)

### 範例

(檢查減價以查看語法 - 粗體文字是變數的值)

*   您知道數學嗎 ？這是**[](var:magic)**
    
*   在我付費租用戶中，需要執行此服務的 OCPU 數目為何？您需要**[](var:number_of_ocpu_paid)**
    
*   但如果使用「永遠免費」，該怎麼辦？接著您需要**[](var:number_of_ocpu_always_free)**
    
*   資料庫的最佳名稱是什麼？它是**[](var:name_of_database)**
    
*   您可以在此取得更多資訊：**[](var:doc_link)**