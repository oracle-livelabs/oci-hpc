# Variables en LiveLabs

## Introducción

Puede especificar variables en otro archivo y hacer referencia a ellas en su Markdown.

## Tarea 1:

Agregue lo siguiente a manifest.json en la sección superior:

       "variables": ["../../variables/variables.json",
                     "../../variables/variables-in-another-file.json"],
    

## Tarea 2

Especifique las variables en el archivo .json de la siguiente forma:

_Ejemplo: variables.json_

    {
        "var1": "Variable 1 value",
        "var2": "Variable 2 value",
        "random_name": "LiveLabs rocks!",
        "number_of_ocpu_paid": "24"
        "number_of_ocpu_always_free": "2"
     }
    

También puede agregar varios archivos que especifiquen variables (consulte el ejemplo de la tarea 1).

_Ejemplo: variables\_in\_another\_file.json_

    {
        "var11": "Variable 1 value but yet different",
        "var22": "Variable 2 value is different",
        "random_name2": "LiveLabs rocks & rules!",
        "name_of_database": "My-Database-Name-is-the-best",
        "magic": "What is 2*2?"
     }
    

## Tarea 3

Ahora, puede hacer referencia a esas variables mediante la siguiente sintaxis (**Tenga en cuenta que solo puede ver la sintaxis en Markdown**):

[](var:var1)

o bien

[](var:magic)

### Ejemplos

(Compruebe la rebaja para ver la sintaxis: el texto en negrita es el valor de la variable)

*   ¿Conoces matemáticas? Es**[](var:magic)**
    
*   ¿Cuántas OCPU necesito para ejecutar este servicio en mi arrendamiento de pago? Necesita**[](var:number_of_ocpu_paid)**
    
*   Pero, ¿qué pasa si estoy usando 'Siempre gratis'? A continuación, necesita**[](var:number_of_ocpu_always_free)**
    
*   ¿Cuál es el mejor nombre para mi base de datos? Es**[](var:name_of_database)**
    
*   Aquí puede encontrar más información:**[](var:doc_link)**