# Auf VNC zugreifen

## Einführung

In dieser Übung greifen Sie auf Ihre VNC zu.

Geschätzte Laborzeit: 10 Minuten

## Aufgabe: Auf VNC zugreifen

1.  Sie stellen eine Verbindung über einen SSH-Tunnel zur Instanz her. Auf Ihrem Rechner ist die Verbindung mit SSH PORT unten die Zahl, die aus 5900 + N resultiert. N ist die Anzeigenummer, wenn die Ausgabe für N 1 war, PORT ist 5901, wenn die Ausgabe 9 war, PORT ist 5909 public\_ip ist die öffentliche IP-Adresse des Headnodes, auf dem der VNC-Server ausgeführt wird. Wenn Sie die vorherigen Anweisungen verwendet haben, lautet der PORT 5901.
    
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        
2.  Sie können jetzt mit einem beliebigen VNC-Viewer eine Verbindung über localhost:N als VNC-Server und das Kennwort herstellen, das Sie während der VNC-Installation festlegen. Sie können einen VNC-Client wählen, den Sie bevorzugen, oder dieses Handbuch zur Installation auf Ihrem lokalen Rechner verwenden:
    

*   [Windows-TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [MacOS/Windows - RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020