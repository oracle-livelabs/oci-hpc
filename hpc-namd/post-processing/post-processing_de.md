# Nachverarbeitung

## Einführung

In dieser Übung analysieren Sie mit der Software Visual Molecular Dynamics (VMD) die Modelle für die Nachverarbeitung.

Geschätzte Laborzeit: 5 Minuten

## Aufgabe: Nachverarbeitung

1.  Für die Nachverarbeitung können Sie die Modelle mit der Software Visual Molecular Dynamics (VMD) analysieren. Führen Sie die folgenden Befehle aus, um VMD zu konfigurieren:
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  Wenn Sie vnc verwenden, starten Sie vncserver, und erstellen Sie ein vnc-Kennwort wie folgt:
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  Starten Sie eine VNC-Verbindung mit localhost:5901 (sicher, dass das Tunneling konfiguriert ist), und führen Sie die folgenden Befehle aus, um VMD zu starten:
    
        <copy>
        vmd
        </copy>
        
4.  Öffnen Sie die pdb-Dateien apoa1 und stmv in /mnt/block/work/NAMD\_models, und beginnen Sie mit der Wiedergabe mit Ihren Modellen.
    

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020