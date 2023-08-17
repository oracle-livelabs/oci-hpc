# Auf das Cluster zugreifen

## Einführung

In dieser Übung greifen Sie auf das Cluster zu.

Geschätzte Laborzeit: 10 Minuten

## Aufgabe: Auf das Cluster zugreifen

1.  Die öffentliche IP-Adresse der Bastion finden Sie im unteren linken Menü unter "Ausgaben". Wenn Sie im Hauptmenü zu Ihren Instanzen navigieren, finden Sie auch Ihre Bastioninstanz und die öffentliche IP.
    
2.  Der Private Key für den Zugriff auf die Maschinen finden Sie auch dort. Kopieren Sie den Text in einer Datei auf Ihrem Computer, sagen wir/home/user/key:
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020