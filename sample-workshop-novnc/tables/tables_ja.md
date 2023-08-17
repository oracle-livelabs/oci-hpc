# LiveLabsの表

## テーブルはクールです!

次のようにMarkdownで表を定義できます。

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

結果は次のようになります。

| テーブル | 次である | いいですね |
| --- | :-: | --: |
| **col 3は** | 右揃え | $1600 |
| col 2は | _中央揃え_ | $12 |
| ゼブラ・ストライプ | 中性子 | $1 |

デフォルトの表キャプション(デフォルトでは、ワークショップ・タイトルとラボ・タイトルの連結)が提供されていることがわかります。

デフォルトを好まない場合は、次の表定義を追加して、独自の表タイトルを指定することもできます。

    {: title="My table title"}
    

完全なマークダウンは次のようになります。

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

表は次のようになります。

| テーブル | 次である | いいですね |
| --- | :-: | --: |
| **col 3は** | 右揃え | $1600 |
| col 2は | _中央揃え_ | $12 |
| ゼブラ・ストライプ | 中性子 | $1 |
| {: title="表のタイトル"} |  |  |

ご覧のとおり、番号付けは自動的に追加されます。

涼しくないですか。

[LiveLabs Markdown Cheatsheet](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf)も参照できます。