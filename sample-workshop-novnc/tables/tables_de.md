# Tabellen in LiveLabs

## Tische sind cool!

Sie können eine Tabelle in Markdown wie folgt definieren:

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

Das Ergebnis sieht wie folgt aus:

| Tabellen | Sind | Cool |
| --- | :-: | --: |
| **Spalte 3 ist** | rechtsbündig | $1600 |
| Spalte 2 ist | _Zentriert_ | $12 |
| Zebrastreifen | sind sauber | $1 |

Sie können sehen, dass eine Standard-Tabellentitel angegeben ist, die standardmäßig eine Verkettung des Workshop-Titels und des Labeltitels darstellt.

Wenn Ihnen der Standard nicht gefällt, können Sie auch Ihren eigenen Tabellentitel angeben, indem Sie die folgende Tabellendefinition hinzufügen:

    {: title="My table title"}
    

Die vollständige Preisabschrift sieht folgendermaßen aus:

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

Nun sieht unser Tisch wie folgt aus:

| Tabellen | Sind | Cool |
| --- | :-: | --: |
| **Spalte 3 ist** | rechtsbündig | $1600 |
| Spalte 2 ist | _Zentriert_ | $12 |
| Zebrastreifen | sind sauber | $1 |
| {: title="My table title"} |  |  |

Wie Sie sehen, wird die Nummerierung automatisch hinzugefügt.

Ist das nicht cool?

Weitere Informationen finden Sie auch im [LiveLabs Markdown-Übersichtsblatt](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf).