# LiveLabs 中的表

## 表太酷了！

您可以按如下方式在 Markdown 中定义表：

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

结果如下所示：

| 表 | 属于 | 太棒了 |
| --- | :-: | --: |
| **列 3 是** | 右对齐 | $1600 |
| 列 2 是 | _居中_ | $12 |
| 斑马纹 | 整齐 | $1 |

您可以看到，提供了默认的表标题，默认情况下，它是研习会标题和实验室标题的串联。

如果您不喜欢默认值，还可以通过在表定义下方添加表标题来提供自己的表标题：

    {: title="My table title"}
    

完整的下拉列表如下所示：

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

现在我们的表如下所示：

| 表 | 属于 | 太棒了 |
| --- | :-: | --: |
| **列 3 是** | 右对齐 | $1600 |
| 列 2 是 | _居中_ | $12 |
| 斑马纹 | 整齐 | $1 |
| ｛：title=“我的表标题”｝ |  |  |

如您所见，编号会自动添加。

那不酷吗？

您也可以参阅 [LiveLabs Markdown Cheatsheet](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf)