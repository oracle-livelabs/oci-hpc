# Tablas en LiveLabs

## Las mesas son frescas!

Puede definir una tabla en Markdown de la misma forma:

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

El resultado es el siguiente:

| Tablas | Soy | Genial |
| --- | :-: | --: |
| **col 3 es** | alineado a la derecha | $1600 |
| col 2 es | _centrado_ | $12 |
| rayas de cebra | están limpios | $1 |

Puede ver que se proporciona un título de tabla por defecto, que es por defecto una concatenación del título del taller y el título de la práctica.

Si no le gusta el valor por defecto, también puede proporcionar su propio título de tabla agregando la siguiente definición de tabla:

    {: title="My table title"}
    

La rebaja completa tiene el siguiente aspecto:

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

Ahora nuestra mesa se ve así:

| Tablas | Soy | Genial |
| --- | :-: | --: |
| **col 3 es** | alineado a la derecha | $1600 |
| col 2 es | _centrado_ | $12 |
| rayas de cebra | están limpios | $1 |
| {: title="Mi tabla title"} |  |  |

Como puede ver, la numeración se agrega automáticamente.

¿No es tan genial?

También puede consultar la [LiveLabs Markdown Cheatsheet](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf)