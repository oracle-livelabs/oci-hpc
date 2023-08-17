# Auf GPU-Knoten zugreifen

## Einführung

In dieser Übung greifen Sie auf den GPU-Knoten zu.

Geschätzte Laborzeit: 5 Minuten

## Aufgabe: Auf den GPU-Knoten zugreifen

1.  Nachdem Ihr Job erfolgreich in Resource Manager abgeschlossen wurde, finden Sie die öffentlichen IP-Adressen für den GPU-Knoten und den Private Key im unteren linken Menü unter **Ausgaben**.
    
2.  Kopieren Sie den Private Key auf Ihren lokalen Rechner, ändern Sie die Berechtigungen des Schlüssels, und melden Sie sich bei der Instanz an:
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  Nachdem Sie sich bei Ihrem GPU-Knoten angemeldet haben, können Sie Gromacs über /mnt/block ausführen. In der Datei README.md finden Sie spezifische Befehle zum Ausführen von Gromacs auf Ihrer GPU-Instanz.
    

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020