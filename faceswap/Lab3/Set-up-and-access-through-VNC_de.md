# Einrichtung und Zugriff über VNC

## Einführung

In dieser Übung führen wir die erforderlichen Schritte aus, um Ihre Ubuntu-Instanz über einen VNC-Client einzurichten und aufzurufen (hier: TigerVNC).

Geschätzte Workshopzeit: 15 Minuten

### Ziele

*   Stellen Sie über SSH eine Verbindung zur Compute-Instanz her
*   VNC auf Ihrer Ubuntu-Instanz einrichten und konfigurieren
*   Über einen VNC-Client auf die Compute-Instanz zugreifen (z.B. TigerVNC)

### Was benötigen Sie?

*   Vollständige Übung 1 und Übung 2
*   [Fügen Sie der VCN-Sicherheitsliste eine Ingress-Regel hinzu](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using), um TCP-Traffic für den Portport 5900+N zuzulassen, wobei N die Anzeigenummer ist (in der Regel :0 für eine physische Anzeige). ![](images/vncseclist.PNG " ")

## Aufgabe 1: SSH-Verbindung zur Compute-Instanz herstellen

1.  Öffnen Sie ein Terminal (auch als Befehlszeile oder Konsole bezeichnet) Ihrer Wahl. In diesem Beispiel arbeiten wir auf einem lokalen Windows-Rechner und verwenden Windows PowerShell als Terminal.
    
2.  Halten Sie die öffentliche IP-Adresse Ihrer Ubuntu-Instanz fest, die Sie unter **Compute -> Instances** in der Instanzliste Ihres Compartments finden.
    
3.  Navigieren Sie in Ihrem Terminal zu dem Ordner, in dem sich das SSH-Schlüsselpaar befindet, das Sie bei der Erstellung der Ubuntu-Compute-Instanz verwendet haben. In unserem Fall heißt der Ordner _SSH_.
    

       <copy>cd .ssh</copy>
    

4.  Geben Sie den folgenden Befehl ein, um auf die Ubuntu Compute-Instanz zuzugreifen, bei der Sie _private-key_ durch den Private Key OpenSSH und die _public IP_ durch die öffentliche IP-Adresse Ihrer Ubuntu-Instanz ersetzen müssen.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## Aufgabe 2: VNC auf der Ubuntu-Instanz einrichten und konfigurieren

1.  Installieren Sie den VNC-Server, indem Sie die folgenden Befehle im Terminal ausführen:

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  Desktopumgebung installieren Wir installieren die Xfce-Desktopumgebung (Sie können aber auch andere Desktopumgebungen verwenden), indem Sie den folgenden Befehl eingeben:

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  Führen Sie den vncpasswd-Befehl aus, um ein Kennwort für den VNC-Server festzulegen.

       <copy>vncpasswd</copy>
    

Wenn Sie dazu aufgefordert werden, geben Sie Ihr Kennwort ein, und prüfen Sie es, indem Sie ein zweites Mal eingeben.

4.  Starten Sie VNC Server mit dem folgenden Befehl:

       <copy>vncserver :1</copy>
    

Sie erhalten die folgende Ausgabe. ![](images/vncserver.PNG " ")

5.  Kill VNC Server mit dem folgenden Befehl:

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  Die VNC-Konfigurationsdatei befindet sich auf ~/.VNC/xstartup. Bearbeiten Sie sie mit Ihrem bevorzugten Texteditor. Wir werden vim verwenden.

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  Fügen Sie die folgende Zeile am Ende der Datei ein:

       <copy>exec /usr/bin/startxfce4 &</copy>
    

Falls Sie mit vim arbeiten: Drücken Sie **A**, um am Ende Text anzuhängen und den obigen Befehl einzufügen. Wechseln Sie in den Befehlsmodus, indem Sie die ESC-Taste drücken. Geben Sie **:wq** ein, um zu speichern und zu beenden.

8.  Starten Sie den VNC-Server, indem Sie den folgenden Befehl ausführen.

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  Schließen Sie die SSH-Verbindung zur Instanz, indem Sie den folgenden Befehl ausführen:

       <copy>exit</copy>
    

## Aufgabe 3: Verbindung zum VNC-Desktop herstellen

Sie können mit einem VNC-Client (z.B. TigerVNC) und einem SSH-Tunneling eine Verbindung zum Remotedesktop herstellen.

1.  Erstellen Sie einen SSH-Tunnel zum VNC-Server mit dem folgenden Befehl (bei dem der Private Key der OpenSSH-Schlüssel ist und die private IP-Adresse aus der Ubuntu-Compute-Instanz public\_ip lautet):

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  Installieren Sie den vncviewer-Client, der in unserem Fall TigerVNC lautet.

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  Laden Sie einen VNC-Client (z.B. TigerVNC) auf dem lokalen Rechner herunter, und installieren Sie ihn. Stellen Sie mit dem VNC-Client eine Verbindung zu **127.0.0.1:5901** her. ![](images/tigervnc.PNG " ")
    
4.  Wenn Sie zur Eingabe eines Kennwortes aufgefordert werden, geben Sie das Kennwort ein, das während der VNC-Serverkonfiguration erstellt wurde. ![](images/tigervncpwd.PNG " ")
    

Der Remote-Ubuntu-Desktop wird geöffnet. ![](images/vncdesktop.PNG " ")

## **Bestätigungen**

*   **Erstellt von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021
*   **Zuletzt aktualisiert von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021