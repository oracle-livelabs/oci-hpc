# Tabelas em LiveLabs

## Mesas são legais!

Você pode definir uma tabela em Markdown da seguinte forma:

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

O resultado é assim:

| Tabelas | São | Frio |
| --- | :-: | --: |
| **col 3 é** | alinhados à direita | $1600 |
| col 2 é | _centralizado_ | $12 |
| listras zebra | são permitidos | $1 |

Você pode ver que há uma legenda de tabela padrão fornecida, que por padrão é uma concatenação do título do workshop e do título do laboratório.

Se você não gostar do padrão, também poderá fornecer seu próprio título de tabela adicionando a seguir a definição de tabela:

    {: title="My table title"}
    

O markdown completo tem esta aparência:

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

Agora nossa tabela está assim:

| Tabelas | São | Frio |
| --- | :-: | --: |
| **col 3 é** | alinhados à direita | $1600 |
| col 2 é | _centralizado_ | $12 |
| listras zebra | são permitidos | $1 |
| {: title="Meu título de tabela"} |  |  |

Como você pode ver, a numeração é adicionada automaticamente.

Não é legal?

Você também pode consultar a [LiveLabs Planilha de Referência em Cheats](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf)