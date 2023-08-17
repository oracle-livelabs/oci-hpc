# OpenFoam-Projekt in HPC-Cluster ausführen

## Einführung

Das Einrichten der HPC-Hardware und das Deployment der Anwendungssoftware kann lange dauern und verschiedene komplexe Vorgänge umfassen. Mit dieser Demo werden alle diese Aufgaben in einem OCI-Marketplace-Image automatisiert. Direkt nach dem Provisioning ist eine voll funktionsfähige HPC-Umgebung sowie eine OpenFoam-Simulationssoftware verfügbar. Sie müssen lediglich die Workload in der voll funktionsfähigen HPC-Umgebung ausführen und die Visualisierungsanwendung wie Paraview ausführen, um die Ausgabe wiederzugeben.

Geschätzte Laborzeit: 60 Minuten

### Referenzarchitektur

![Marktplatz](images/29_Architecture.png)

### Info zu OpenFoam:

Computational Fluid Dynamics (CFD) ist die Simulation von Fluidbewegung und Wärmeübertragung mittels numerischer Analyse. CFD-Software wie OpenFOAM kann als leistungsstarkes Tool für viele Engineering-Industrien dienen, um Designs zu verbessern, Auswirkungen zu visualisieren und Ineffizienzen in einem System zu erkennen. Die zur Verarbeitung einer Simulation erforderliche Rechenleistung kann jedoch sehr intensiv sein. Supercomputer und HPC-Cluster werden häufig verwendet, um große und komplexe Modelle innerhalb eines angemessenen Zeitrahmens auszuführen. In dieser Übung berechnen und rendern Sie das aerodynamische Modell eines Motorrads mit einer Open-Source-CFD-Software, OpenFOAM, in einem Oracle HPC-Cluster

### Infrastructure-Terminologie:

1.  Worker-Knoten: HPC-Compute-Instanzen, die Rechenleistung zur Ausführung der Workload von Computersimulationen oder anderen Engineering-Workloads bereitstellen. In dieser Demo-Compute-Ausprägung sind BM.HPC2.36-Knoten die Worker-Knoten.
2.  Kopfknoten: Compute-Instanz, aus der alle Berechnungsjobs geplant und zur Ausführung auf Worker-Knoten weitergeleitet werden können. Oft können Head-Knoten und Bastion-Knoten identisch sein. Für diese Demo stellen wir den Bastionknoten bereit.
3.  GPU-Knoten: Eine spezielle Compute-Instanz mit GPU-Prozessoren zur Wiedergabe der Ausgabe der Rechen-Workload. Diese Demo stellt keinen GPU-Rechner bereit.
4.  Bastionknoten: Eine Compute-Instanz mit öffentlicher IP fungiert als Einstiegspunkt für die Verbindung zu Worker-Knoten, die sich im Allgemeinen im privaten Netzwerk befinden.
5.  RoCE-Netzwerk: RDMA über Converged Ethernet (RoCE) ist ein Netzwerkprotokoll, das den Remote-Direktspeicherzugriff (RDMA) über ein Ethernet-Netzwerk ermöglicht.

### Ziele

1.  Stellen Sie eine voll funktionsfähige HPC-Umgebung mit dem OCI-Marketplace-Image bereit
2.  Bereitstellung der CFD-basierten Simulationsanwendung mit dem Namen OpenFoam
3.  Openfoam-Workload ausführen
4.  Ausgabe mit Paraview-Visualisierungsanwendung wiedergeben.

### Voraussetzungen

Bevor Sie mit der Verwendung dieser Übung beginnen, stellen Sie sicher, dass Sie Zugriff auf Folgendes haben:

1.  Autorisieren Sie den Compute-Service, um Tag-Namespaces in Ihrem Namen mit der folgenden Policy zu verwalten:
    
    "Verwenden von Tag-Namespace im Mandanten durch Service compute\_management zulassen"
    
2.  Die Bare-Metal-Ausprägungen BM.HPC2.36 ist nur in dieser Demo zulässig. In derselben Availability-Domain sind mindestens 2 Compute-Ausprägungen erforderlich.
    
3.  VM-Compute-Image für Bastion-Server. Muss sich nicht in derselben Availability-Domain wie HPC-Knoten befinden.
    
4.  Benutzerzugangsdaten müssen Quota haben, um ein vorhandenes VCN mit privatem und öffentlichem Subnetz zu erstellen oder zu verwenden.
    
5.  Zugriff auf Marketplace-Image "CFD Ready Cluster". Die in diesem DEMO verwendete Version des Marketplace-Images ist (Version: 20200625).
    

## Aufgabe 1: Marketplace-Image starten

1.  Prüfen Sie vor dem Start des Marketplace-Images auf die Availability-Domain, in der 2 oder mehr HPC-Knoten mit der Ausprägung BM.HPC2.36 verfügbar sind. Gehe zu Menü -> Governance -> Limits, Quota und Nutzung ![Marktplatz](images/01-Service_Limits.png)

2.  Gehen Sie zu "Menü -> Marketplace -> Anwendungen". ![Marktplatz](images/02-Marketplace.png)

3.  Im Suchfeldtyp "cfd" ![Marktplatz](images/03-Search_marketplace.png)

4.  Klicken Sie auf das Bild, wählen Sie es aus, und klicken Sie dann auf die Schaltfläche "Stack starten". Stellen Sie sicher, dass Sie sich im richtigen Compartment befinden. ![Marktplatz](images/04-Launch_Stack.png)

5.  Geben Sie nach dem Starten des Stacks die Details zum Erstellen von Ressourcen ein, einschließlich HPC-Worker-Knoten, Bastion-Server, Netzwerkkomponenten und VCN-Server. Der Stack fordert auch Eingaben zur Bereitstellung von Packages wie openMPI und openFoam an.
    
6.  Geben Sie den Namen des Stacks unten an ![Marktplatz](images/05-Create_Stack01.png)
    

7.  Wählen Sie die Availability-Domain aus, in der HPC-Knoten für das Provisioning verfügbar sind. Geben Sie den SSH-Public Key an, der für die Verbindung zum Bastion-Server erforderlich ist. ![Marktplatz](images/06-Create_Stack02.png)
    
8.  Wählen Sie die Availability-Domain für das Provisioning des Bastion-Servers aus. Er kann sich von der Availability-Domain auf HPC-Knoten unterscheiden. Wählen Sie die Bastionhostausprägung Ihrer Wahl aus. Aktivieren Sie das Kontrollkästchen, um den VNC-Server auf dem Bastion-Server zu installieren. Geben Sie das Kennwort an, das für eine spätere VNC-Verbindung erforderlich ist. ![Marktplatz](images/07-Create_Stack03.png)
    

9.  Bei Worker-Knoten ist nur BM.HPC2.36 ausgewählt. Sie benötigen mindestens 2 HPC-Compute-Ausprägungen, um das Provisioning abzuschließen. Wählen Sie im folgenden Bildschirm mindestens 2 Compute Nodes aus. Behalten Sie alle Optionen bei, um sie standardmäßig zu übernehmen. ![Marktplatz](images/08-Create_Stack04.png)

10.  Erstellen Sie unter "Netzwerkoptionen" ein neues VCN mit den im Stack übergebenen Standardwerten. Diese Demo erstellt ein neues VCN, das über diesen Stack erstellt wurde. Sie können aber auch ein vorhandenes VCN verwenden. ![Marktplatz](images/09-Create_Stack05.png)

11.  Verwenden Sie im Abschnitt "Dateisystem" die folgenden Optionen, mit denen das NFS-Mount erstellt und das gluster-Dateisystem darüber installiert wird. ![Marktplatz](images/10-Create_Stack06.png)

12.  Klicken Sie auf das Kontrollkästchen "INSTALL OPENFOAM", um die Anwendung OpenFoam in diesem HPC-Stack zu installieren. ![Marktplatz](images/11-Create_Stack07.png)

13.  Prüfen Sie auf der letzten Seite des Stacks die eingegebenen Details. Wenn alles in Ordnung ist, klicken Sie auf die Schaltfläche "Erstellen", um das Provisioning der Infrastruktur und der Softwareinstallation zu starten. ![Marktplatz](images/12-Create_Stack08.png)

14.  Fortschritt der Installation des HPC-Stacks prüfen ![Marktplatz](images/13-StackJob.png)

15.  Sobald das Provisioning in etwa 20 Minuten abgeschlossen ist, sollte die Logdatei die folgende Meldung enthalten. Logs finden Sie unter Menü -> Ressourcenmanager -> Stacks (klicken Sie auf Ihren Stapelnamen) -> Jobs (Klicken Sie auf den Link mit dem Jobnamen, um das vollständige Log anzuzeigen). ![Marktplatz](images/14-StackJobDetails.png)

## Aufgabe 2: Setup validieren

1.  Klicken Sie links auf dem Bildschirm auf den Link "Ressourcen zuordnen", um die Infrastrukturressourcen anzuzeigen, die von diesem Stack bereitgestellt wurden. Zugeordnete Ressourcen finden Sie unter Menü -> Ressourcenmanager -> Stacks (klicken Sie auf Ihren Stapelnamen) -> Jobs (Klicken Sie auf den Link für den Jobnamen) -> Ressourcen zuordnen ![Marktplatz](images/15-Stack_Resources.png)

2.  Unter "Compute" -> "Instanzen" werden 3 Compute-Instanzen bereitgestellt, 2 HPC-Knoten im privaten Netzwerk und 1 Bastionserver mit öffentlicher IP-Adresse. ![Marktplatz](images/16-Compute_Details.png)

3.  Stellen Sie mit der öffentlichen IP-Adresse als opc-Benutzer eine Verbindung zum Bastion-Server her, und verwenden Sie dabei den SSH-Private Key, für den im obigen Stack ein Public Key hochgeladen wurde. Verwenden Sie diese Methode, um eine Verbindung von Linux/Mac Desktop herzustellen. Verwenden Sie für Windows Desktop Putty, und stellen Sie den privaten Schlüssel ppk zur Verbindung bereit. ![Marktplatz](images/17-Bastion_ssh.png)

4.  Prüfen Sie den Shared NFS-Speicher, der auf 2 HPC-Knoten und dem Bastion-Server gemountet ist. Dieser NFS-Speicher enthält die OpenFoam-Software, mit der die Workload ausgeführt wird. ![Marktplatz](images/18-Bastion_storage.png)

5.  Die Anwendung OpenFoam wird im Ordner "/mnt/gluster-share/work" gehostet. Prüfen Sie, ob die Hostdatei die priavte-IP-Adresse der 2 HPC-Knoten enthält, auf denen die Workload ausgeführt wird. ![Marktplatz](images/19-Bastion_openfoam.png)

6.  Die folgende Ausgabe zeigt einen ausgeführten VNC-Server auf Port 5901. Dieser Port wird verwendet, um eine VNC-Verbindung zum Bastionhost herzustellen. ![Marktplatz](images/20-Bastion_VNCdetails.png)

7.  Prüfen Sie in der Sicherheitsliste des Bastion-Servers unter Ingress-Regeln, ob Port 5901 zulässig ist. Wenn VNC auf einem anderen Port ausgeführt wird und dieser Port für Ingress-Traffic in der Sicherheitsliste nicht zulässig ist, erstellen Sie eine neue Sicherheitsregel für diesen. ![Marktplatz](images/21-SecList_OpenVNC_Port.png)

8.  Prüfen Sie die Namen der HPC-Knoten ![Marktplatz](images/22-ListHPCNodes.png)

## Aufgabe 3: Simulations-Workload ausführen und Ausgabe wiedergeben

1.  Führen Sie auf dem Bastion-Server die folgenden Befehle aus, um die Ausgabe mit dem Paraview-Package wiederzugeben.
    
        sudo yum install -y mesa-libGLU
        cd /mnt/gluster-share
        curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" https://www.paraview.org/paraview-downloads/download.php > file.tar.gz
        tar -xf file.tar.gz
        

2.  Stellen Sie eine Verbindung zu einem der HPC-Knoten vom Bastion-Server her, und führen Sie die Workload aus
    
        ssh 172.16.1.2
        cd /mnt/gluster-share/work/
        ./Allrun 36
        
    
        ./Allrun 36
        Cleaning /mnt/gluster-share/work case
        Mesh Dimensions: (40 16 16)
        Cores:36: 6, 6, 1
        Running surfaceFeatures on /mnt/gluster-share/work
        Running blockMesh on /mnt/gluster-share/work
        Running decomposePar on /mnt/gluster-share/work
        Running snappyHexMesh
        Running patchsummary
        Running potentialFoam
        Running simpleFoam
        Running reconstructParMesh on /mnt/gluster-share/work
        Running reconstructPar on /mnt/gluster-share/work
        219.95
        [opc@inst-quqyz-accurate-swan work]$
        

3.  Nachdem die Workload erfolgreich abgeschlossen wurde, konfigurieren Sie den VNC-Client wie folgt auf Ihrem Rechner. Öffentliche IP von Bastion-Server und VNC-Port angeben ![Marktplatz](images/24-VNC_Connection.png)

4.  OPTIONALLY. Wenn Sie VNC-Port 5901 nicht öffnen dürfen oder aus Sicherheitsgründen SSH-Tunnel für diesen Port erstellen möchten, verwenden Sie den folgenden Befehl, um SSH-Tunnel auf Port 5901 zu erstellen, ohne den Port in der Sicherheitsliste zu öffnen

Erstellen Sie einen Tunnel vom Laptop/Desktop mit dem folgenden Befehl aus dem Terminalfenster. Hier erfolgt die Kommunikation für Port 5901 auf SSH-Port 22, und die IP-Adresse 150.136.41.3 ist die öffentliche IP-Adresse des Bastion-Servers.

      ```
      ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3
      ```
    

5.  Schließen Sie das obige SSH-Tunnel-Terminalfenster nicht. Starten Sie jetzt die VNC-Session, und verwenden Sie dieses Mal anstelle der IP-Adresse "localhost" auf Port 5901, obwohl dieser Port nicht in der Sicherheitsliste des Subnetzes geöffnet ist. ![Marktplatz](images/28-ssh_Tunnel.png)

6.  Starten Sie die Paraview-Anwendung im Bastion-Server
    
        cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        ./paraview
        
7.  Wählen Sie im Anwendungsfenster von Paraview "File -> Open -> Path "/mnt/gluster-share/work" (Datei -> Öffnen), und wählen Sie den Dateinamen motorbike.foam. Es wird null Byte-Datei und das sollte in Ordnung sein. ![Marktplatz](images/25-Paraview_Menu.png)
    

8.  Wählen Sie links im Fenster auf der Registerkarte "Eigenschaften" die Option "Mesh-Regionen", um alle Werte auszuwählen, und heben Sie die Auswahl der obersten Werte auf, die nicht mit dem Präfix motorBike\_ beginnen. Stellen Sie sicher, dass alle Werte ausgewählt sind, die mit motorBike\_ beginnen. Klicken Sie auf die Schaltfläche "Anwenden", werden einige Fehler angezeigt. Ignorieren Sie das daraufhin angezeigte Fehlerfenster, um die Wiedergabe des Bildes in der VNC-Konsole anzuzeigen. ![Marktplatz](images/26-Mesh_Regions.png)

9.  Ein Bild wie unten wird auf dem Bildschirm wiedergegeben. Basierend auf einigen Anzeigeeinstellungen sieht das Bild auf dem Bildschirm möglicherweise etwas anders aus. ![Marktplatz](images/27-Image_Rendering.png)

Alles fertig! Damit ist die Demo zur Ausführung der OpenFoam-Anwendung in einem HPC-Cluster abgeschlossen.

_Sie können jetzt mit der nächsten Übung fortfahren_

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Zuletzt aktualisiert von/Datum** - Harrison Dvoor, Oktober 2020

# <<<<<<<<

> > > > > > > Upstream/Master