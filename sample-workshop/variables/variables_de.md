# Variablen in LiveLabs

## Einführung

Sie können Variablen in einer anderen Datei angeben und in der Preisabschrift darauf verweisen.

## Aufgabe 1:

Fügen Sie der manifest.json im oberen Abschnitt Folgendes hinzu:

       "variables": ["../../variables/variables.json",
                     "../../variables/variables-in-another-file.json"],
    

## Aufgabe 2

Geben Sie die Variablen in der JSON-Datei wie folgt an:

_Beispiel: variables.json_

    {
        "var1": "Variable 1 value",
        "var2": "Variable 2 value",
        "random_name": "LiveLabs rocks!",
        "number_of_ocpu_paid": "24"
        "number_of_ocpu_always_free": "2"
     }
    

Sie können auch mehrere Dateien hinzufügen, die Variablen angeben (siehe Beispiel in Aufgabe 1).

_Beispiel: variables\_in\_another\_file.json_

    {
        "var11": "Variable 1 value but yet different",
        "var22": "Variable 2 value is different",
        "random_name2": "LiveLabs rocks & rules!",
        "name_of_database": "My-Database-Name-is-the-best",
        "magic": "What is 2*2?"
     }
    

## Aufgabe 3

Nun können Sie diese Variablen mit der folgenden Syntax referenzieren (**Beachten Sie, dass die Syntax nur in Markup angezeigt wird**):

[](var:var1)

oder

[](var:magic)

### Beispiele

(Prüfen Sie die Markierung, um die Syntax anzuzeigen. Der fett formatierte Text ist der Wert der Variablen.)

*   Kennen Sie Mathe? Dies ist**[](var:magic)**
    
*   Wie viele OCPUs muss ich diesen Service in meinem bezahlten Mandanten ausführen? Sie benötigen**[](var:number_of_ocpu_paid)**
    
*   Aber was ist, wenn "Immer kostenlos" verwendet wird? Dann benötigen Sie**[](var:number_of_ocpu_always_free)**
    
*   Wie lautet der beste Name für meine Datenbank? Es ist**[](var:name_of_database)**
    
*   Hier finden Sie weitere Informationen:**[](var:doc_link)**