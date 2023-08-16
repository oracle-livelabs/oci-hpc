# LiveLabsの変数

## 概要

別のファイルに変数を指定し、その変数をマークダウンで参照できます。

## タスク1:

上部のセクションのmanifest.jsonに次を追加します:

       "variables": ["../../variables/variables.json",
                     "../../variables/variables-in-another-file.json"],
    

## タスク2

次のように.jsonファイルで変数を指定します。

_例: variables.json_

    {
        "var1": "Variable 1 value",
        "var2": "Variable 2 value",
        "random_name": "LiveLabs rocks!",
        "number_of_ocpu_paid": "24"
        "number_of_ocpu_always_free": "2"
     }
    

変数を指定する複数のファイルを追加することもできます(タスク1の例を参照)。

_例: variables\_in\_another\_file.json_

    {
        "var11": "Variable 1 value but yet different",
        "var22": "Variable 2 value is different",
        "random_name2": "LiveLabs rocks & rules!",
        "name_of_database": "My-Database-Name-is-the-best",
        "magic": "What is 2*2?"
     }
    

## タスク3

ここで、次の構文を使用してこれらの変数を参照できます(**構文はマークダウンでのみ表示されます**)。

[](var:var1)

または

[](var:magic)

### 例

(マークダウンをチェックして構文を確認します。太字のテキストは変数の値です)

*   数学を知っていますか。**[](var:magic)**
    
*   有料テナンシでこのサービスを実行するために必要なOCPUの数。**[](var:number_of_ocpu_paid)**が必要です
    
*   「Always free」を使っているとしたら?次に、**[](var:number_of_ocpu_always_free)**が必要です
    
*   データベースの名前として最も適切なものは何ですか。**[](var:name_of_database)**
    
*   詳細は、次を参照してください。**[](var:doc_link)**