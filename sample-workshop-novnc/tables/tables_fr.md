# Tables dans LiveLabs

## Les tables sont cool !

Vous pouvez définir une table dans Markdown comme suit :

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

Le résultat est le suivant :

| Tables | Sont | Cool |
| --- | :-: | --: |
| **col 3 est** | aligné à droite | $1600 |
| col 2 est | _centré_ | $12 |
| rayures zèbre | sont nets | $1 |

Vous pouvez constater qu'une légende de tableau par défaut est fournie, qui correspond par défaut à une concaténation du titre de l'atelier et du titre de l'atelier.

Si vous n'aimez pas la valeur par défaut, vous pouvez également fournir votre propre titre de tableau en ajoutant la définition de tableau ci-dessous :

    {: title="My table title"}
    

La démarque complète se présente comme suit :

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

Maintenant, notre table ressemble à ceci :

| Tables | Sont | Cool |
| --- | :-: | --: |
| **col 3 est** | aligné à droite | $1600 |
| col 2 est | _centré_ | $12 |
| rayures zèbre | sont nets | $1 |
| { : title="Mon titre de table"} |  |  |

Comme vous pouvez le voir, la numérotation est ajoutée automatiquement.

C'est pas cool ?

Vous pouvez également vous reporter à la [feuille de calcul Markdown LiveLabs](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf)