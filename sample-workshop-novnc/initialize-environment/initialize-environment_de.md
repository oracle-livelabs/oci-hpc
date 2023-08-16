# Umgebung initialisieren

## Einführung

In dieser Übung prüfen und starten wir alle Komponenten, die für die erfolgreiche Ausführung dieses Workshops erforderlich sind.

_Geschätzte Laborzeit:_ 30 Minuten.

### Ziele

*   Initialisieren Sie die Workshop-Umgebung.

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Oracle Cloud-Account
*   Sie haben abgeschlossen:
    *   Übung: Setup vorbereiten (nur _Free-Tier_ und _Kostenpflichtige Mandanten_)
    *   Übung: Umgebungssetup

> **Hinweis:** _Beim Kopieren/Einfügen mit der praktischen_ **Kopieren**\-_Funktion, die im gesamten Handbuch verwendet wird, müssen Sie nach dem Einfügen die Taste_ **EINGABETASTE** _klicken. Andernfalls bleibt die letzte Zeile im Puffer, bis Sie die EINGABETASTE drücken_ **!**

## Aufgabe 1: Prüfen, ob die erforderlichen Prozesse hochgefahren und gestartet sind.

1.  Fahren Sie jetzt mit dem Zugriff auf Ihre Remote-Desktopsitzung wie unten angegeben fort, um Ihre Umgebung zu validieren, bevor Sie die nachfolgenden Übungen ausführen. Die folgenden Prozesse müssen hochgefahren und gestartet sein:
    
    *   Datenbank-Listener
        *   LISTENER (1521)
        *   LISTCDB2 (1522)
    *   Datenbankserverinstanzen
        *   CDB1
        *   CDB2
    
    Sie können die Datenbankkonnektivität testen, indem Sie auf das _+_\-Zeichen neben den Datenbanken klicken (siehe unten im Bereich _SQL Developer Oracle-Verbindungen_).
    
    ![](./images/19c_hol_landing.png " ")
    
2.  Klicken Sie auf dem Desktop auf das Symbol _Terminal_, um eine Session zu starten. Führen Sie dann Folgendes aus, um zu prüfen, ob die erwarteten Prozesse hochgefahren sind.
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    Wenn alle erwarteten Prozesse in der Ausgabe wie oben dargestellt angezeigt werden, ist Ihre Umgebung für die nächste Aufgabe bereit.
    
3.  Wenn fragwürdige Ausgaben, Fehler oder Komponenten angezeigt werden, starten Sie den Service entsprechend neu
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Aufgabe 2: Datenbank für mehrmandantenfähige Anwendungsfälle initialisieren

1.  Führen Sie als Benutzer _oracle_ in Ihrer Remote-Desktopsession den folgenden Block aus, um Lab-Artefakte zu aktualisieren
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

Sie können jetzt [mit der nächsten Übung fortfahren](#next).

## Anhang 1: Startservices verwalten

1.  Datenbankservice (alle Datenbanken und Standard-Listener).
    
    *   Start
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   Beenden
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   Status
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   Neustart
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  Datenbankservice (nicht standardmäßige Listener).
    
    *   Start
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   Beenden
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   Status
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   Neustart
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Bestätigungen

*   **Autor** - Andy Rivenes, Senior Principal Product Manager, Oracle Database In-Memory
*   **Mitwirkende** - Kay Malcolm, Didi Han, Rene Fontcha
*   **Zuletzt aktualisiert von/Datum** - Rene Fontcha, LiveLabs Platform Lead, NA Technology, Oktober 2021