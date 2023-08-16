# Variables dans LiveLabs

## Présentation

Vous pouvez spécifier des variables dans un autre fichier et les référencer dans votre démarque.

## Tâche 1 :

Ajoutez ce qui suit à votre manifest.json dans la section supérieure :

       "variables": ["../../variables/variables.json",
                     "../../variables/variables-in-another-file.json"],
    

## Tâche 2

Indiquez les variables dans le fichier .json comme suit :

_Exemple : variables.json_

    {
        "var1": "Variable 1 value",
        "var2": "Variable 2 value",
        "random_name": "LiveLabs rocks!",
        "number_of_ocpu_paid": "24"
        "number_of_ocpu_always_free": "2"
     }
    

Vous pouvez également ajouter plusieurs fichiers qui spécifient des variables (voir l'exemple de la tâche 1).

_Exemple : variables\_in\_another\_file.json_

    {
        "var11": "Variable 1 value but yet different",
        "var22": "Variable 2 value is different",
        "random_name2": "LiveLabs rocks & rules!",
        "name_of_database": "My-Database-Name-is-the-best",
        "magic": "What is 2*2?"
     }
    

## Tâche 3

Vous pouvez maintenant vous reporter à ces variables à l'aide de la syntaxe suivante (**Notez que vous ne pouvez voir la syntaxe que dans Markdown**) :

[](var:var1)

ou

[](var:magic)

### Exemples

(Cochez la case pour voir la syntaxe - le texte en gras est la valeur de la variable)

*   Tu sais math ? Ceci est le suivant :**[](var:magic)**
    
*   Combien d'OCPU dois-j'exécuter ce service dans ma location payante ? Vous avez besoin de**[](var:number_of_ocpu_paid)**
    
*   Mais que se passe-t-il si vous utilisez "Toujours gratuit" ? Ensuite, vous avez besoin de**[](var:number_of_ocpu_always_free)**
    
*   Quel est le meilleur nom pour ma base de données ? Il s'agit de**[](var:name_of_database)**
    
*   Pour plus d'informations :**[](var:doc_link)**