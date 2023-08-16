# LiveLabs 中的表格

## 表格很酷！

您可以在「減價」中定義表格的方式如下：

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

結果如下：

| 表格 | 是 | 酷 |
| --- | :-: | --: |
| **欄位 3 為** | 靠右對齊 | $1600 |
| 欄位 2 為 | _中心_ | $12 |
| 斑馬條 | 是 neat | $1 |

您可以看到提供的預設表格標題，預設會串連研討會標題與實驗室標題。

如果您不喜歡預設值，也可以在表格定義下方新增表格標題：

    {: title="My table title"}
    

完整的減價看起來如下：

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

現在我們的表格如下所示：

| 表格 | 是 | 酷 |
| --- | :-: | --: |
| **欄位 3 為** | 靠右對齊 | $1600 |
| 欄位 2 為 | _中心_ | $12 |
| 斑馬條 | 是 neat | $1 |
| {：title=「我的表格標題」} |  |  |

如您所見，會自動新增編號。

這不是很酷？

您也可以參閱 [LiveLabs Markdown 速查表](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf)