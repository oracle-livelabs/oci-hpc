# Visualisierung konfigurieren

## Einführung

HPC-Workloads erfordern häufig Visualisierungstools zur Planung, Überwachung oder Analyse der Ausgabe der Simulationen. In diesen Szenarios wird häufig ein GPU-Visualisierungsknoten für eine optimale Auflösung und Nachverarbeitung erstellt. Eine GUI ist nicht standardmäßig auf OCI-Instanzen installiert. Sie können jedoch einfach mit dem Remoteanzeigeprotokoll VNC oder X11 konfiguriert werden. In den folgenden Unterabschnitten wird beschrieben, wie Sie einen GPU-Visualisierungsknoten mit TurboVNC und OpenGL im öffentlichen Subnetz erstellen.

Geschätzte Laborzeit: 25 Minuten

## Aufgabe 1: VNC für Ihre Bastion einrichten

Standardmäßig erfolgt der einzige Zugriff auf den Oracle Linux-Rechner über SSH in einem Konsolenmodus. Wenn die grafische Oberfläche angezeigt werden soll, müssen Sie eine VNC-Verbindung einrichten. Das folgende Skript funktioniert für den Standardbenutzer "opc". Das Kennwort für die VNC-Session ist auf "HPC\_oci1" gesetzt, kann aber im nächsten Befehlsset bearbeitet werden. Wenn Sie aktuell nicht über SSH mit dem Headnode verbunden sind, führen Sie diese Befehle auf dem Headnode aus.

        sudo yum -y groupinstall "Server with GUI"
        sudo yum -y install tigervnc-server mesa-libGL
        sudo systemctl set-default graphical.target
        sudo cp /usr/lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
        sudo sed -i 's/<USER>/opc/g' /etc/systemd/system/vncserver@:1.service
        sudo sed -ie '/^ExecStart=/a PIDFile=/home/opc/.vnc/%H%i.pid' /etc/systemd/system/vncserver@:1.service
        sudo mkdir /home/opc/.vnc/
        sudo chown opc:opc /home/opc/.vnc
        echo "password" | vncpasswd -f > /home/opc/.vnc/passwd
        chown opc:opc /home/opc/.vnc/passwd
        chmod 600 /home/opc/.vnc/passwd
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
    

## Aufgabe 2: GPU-Instanz hinzufügen

Die folgenden Schritte werden mit OpenGL ausgeführt, um GPU-Anwendungsfälle auf OCI zu verbessern. Weitere Informationen finden Sie im Blog.

1.  Navigieren Sie in der Konsole zu "Compute" und dann zu "Instanzen".
    
2.  Compute-Instanz für den Visualisierungsknoten erstellen:
    
    *   Wählen Sie die gewünschte AD aus
    *   Wählen Sie die gewünschte GPU-Ausprägung (VM oder BM) aus
    *   Geben Sie ein GPU-kompatibles Oracle Linux-Image an. Das neueste Oracle Linux-Image wird automatisch für GPU aktiviert.
    *   Wählen Sie das Clusternetzwerk-VCN und das öffentliche Subnetz aus
    *   Kopieren - Fügen Sie Ihren öffentlichen SSH-Schlüssel ein
    *   Klicken Sie auf "Erstellen".
3.  Warten Sie, bis die Instanz bereitgestellt ist, und melden Sie sich dann über Folgendes bei der Instanz an:
    
        ssh opc@<public ip> -i <private key> 
        
4.  Installieren Sie X Window System, einen Display Manager (GNOME/GDM) und eine Desktopumgebung (MATE):
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  Installieren Sie VNC-Server und VirtualGL. Beachten Sie, dass VirtualGL ein Open-Source-Toolkit ist, mit dem jede Linux- oder Unix-Konsole OpenGL-Anwendungen mit vollständiger Hardwarebeschleunigung ausführen kann.
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  Konfigurieren Sie den X-Server, um GPU-Sharing für virtuelle Sessions zu aktivieren. Führen Sie die folgenden Befehle aus:
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  Konfigurieren Sie den X-Server, um GPU-Sharing für virtuelle Sessions zu aktivieren. Führen Sie die folgenden Befehle aus:
    
        sudo vglserver_config -config -s -f -t
        
8.  Um beim Start des Bildschirmschoners nicht gesperrt zu werden, legen Sie das lokale Benutzerkennwort auf etwas fest, das Sie später verwenden können:
    
        sudo passwd opc
        
9.  Ändern Sie das VNC-Kennwort in ein Kennwort, das Sie für die Anmeldung verwenden können:
    
        vncpasswd
        
10.  X-Server neu starten:
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  GDM aktivieren und starten:
    
        systemctl enable gdm --now
        
12.  Starten Sie den VNC-Server:
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  Wenn Sie direkt ohne SSH-Weiterleitung auf den VNC-Server zugreifen möchten, stellen Sie sicher, dass die Sicherheitsliste Verbindungen zu Port 5901/tcp zulässt.
    
    *   Navigieren Sie in der Konsole zu "Networking" (Netzwerk) und dann zu virtuellen Cloud-Netzwerken.
    *   Wählen Sie Subnetze und dann das öffentliche Subnetz aus.
    *   Fügen Sie in der Standardsicherheitsliste eine Ingress-Regel mit den folgenden Details hinzu:
        *   Zustandslos: Nein
        *   Quelltyp: CIDR
        *   Quell-CIDR: 0.0.0.0/0
        *   IP-Protokoll: TCP
        *   Quellportbereich: Alle
        *   Zielportbereich: 5901 Hinweis: Der Standard-VNC-Port beträgt 5900 plus eine Anzeigenummer (z.B. 5901 für :1, 5902 für :2)
14.  Zugriff in lokalen Firewalleinstellungen zulassen:
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020