# OpenFoam-Projekt in Standard-VM auf OCI ausführen

## Einführung

Die folgenden Aufgaben unterstützen Sie bei der Bewertung der CFD-Software OpenFOAM in Oracle Cloud Infrastructure. Führen Sie die Aufgaben aus, um OpenFOAM automatisch herunterzuladen und zu konfigurieren. OpenFOAM ist die kostenlose Open-Source-CFD-Software, die seit 2004 in erster Linie von OpenCFD Ltd veröffentlicht und entwickelt wurde. Es verfügt über eine große Benutzerbasis in den meisten Bereichen Engineering und Wissenschaft, sowohl von kommerziellen als auch akademischen Organisationen. OpenFOAM verfügt über eine umfangreiche Palette von Funktionen, um alles von komplexen Flüssigkeitsströmen mit chemischen Reaktionen, Turbulenzen und Wärmeübertragung, Akustik, Festkörpermechanik und Elektromagnetik zu lösen.

Sie werden durch die Installation von OpenFoam in einer einfachen VM.Standard 2.1-Ausprägung geführt. Für ein tatsächliches Deployment ist jedoch eine größere Ausprägung wie BM.HPC2.36 angemessen. Einige High-Performance-Ausprägungen sind nur in bestimmten Regionen und Availability-Domains verfügbar.

Wir erstellen zwei Knoten, den Headnode für das Cluster im öffentlichen Subnetz und einen Worker-Compute Node im privaten Subnetz. Um auf die Worker-Knoten zuzugreifen, erstellen wir zuerst den Headnode, generieren dann einen SSH-Schlüssel im Headnode und verwenden diesen Public Key beim Erstellen des Worker-Knotens.

Um **Probleme protokollieren**, klicken Sie [hier](https://github.com/oracle/learning-library/issues/new), um zum Formular "Github - oracle - Repository-Problemweiterleitung" zu gelangen.

Geschätzte Zeit: 60 Minuten

### Ziele

Als Entwickler, Data Engineer,

1.  High Performance Computing-Instanzen manuell bereitstellen
    
2.  Clusternetzwerk erstellen
    
3.  Erstellen eines Netzwerkdateisystems
    
4.  VNC einrichten
    
5.  OpenFoam und Paraview installieren
    

### Voraussetzungen

*   Ein Oracle Cloud Infrastructure-Account mit Berechtigungen zum Erstellen einer Instanz-VM-Standardausprägung 2.1 oder BM.HPC2.36.

### Infrastrukturterminologie

*   Worker-Knoten: HPC-Compute-Instanzen, die Rechenleistung zur Ausführung der Workload von Computersimulationen oder anderen Engineering-Workloads bereitstellen. In dieser Demo-Compute-Ausprägung sind BM.HPC2.36-Knoten die Worker-Knoten.
    
*   Kopfknoten: Compute-Instanz, aus der alle Berechnungsjobs geplant und zur Ausführung auf Worker-Knoten weitergeleitet werden können. Oft können Head-Knoten und Bastion-Knoten identisch sein. Für diese Demo stellen wir den Bastionknoten bereit.
    
*   Bastionknoten: Eine Compute-Instanz mit öffentlicher IP fungiert als Einstiegspunkt für die Verbindung zu Worker-Knoten, die sich im Allgemeinen im privaten Netzwerk befinden.
    
*   RoCE-Netzwerk: RDMA über Converged Ethernet (RoCE) ist ein Netzwerkprotokoll, das den Remote-Direktspeicherzugriff (RDMA) über ein Ethernet-Netzwerk ermöglicht.
    

## Aufgabe 1: Virtuelles Cloud-Netzwerk von Oracle bereitstellen

1.  Bevor Sie eine Instanz erstellen, müssen Sie ein virtuelles Cloud-Netzwerk konfigurieren. Wählen Sie das Menü oben links aus, und wählen Sie **Networking** und **Virtuelle Cloud-Netzwerke** aus.
    
    ![](./images/vcn.png " ")
    
    ![](./images/create_vcn.png " ")
    
    ![](./images/vcn_content.png " ")
    
2.  Wählen Sie auf der nächsten Seite Folgendes für Ihr VCN aus:
    
    *   Name
        
    *   Compartment
        
    *   CIDR-Block (Beispiel: 10.0.0.0/16)
        
3.  Scrollen Sie nach unten, und klicken Sie auf "VCN erstellen".
    
4.  Subnetze erstellen
    
    1.  Öffentliches Subnetz
        
        *   Name: d.h. hpc\_public
            
        *   Subnetztyp: regional
            
        *   CIDR-Block: 10,0,0,0/24
            
        *   Routentabelle: "Standardroutentabelle"
            
        *   Subnetzzugriff: Öffentliches Subnetz
            
        *   Sicherheitsliste: "Default Security List"
            
    2.  Privates Subnetz
        
        *   Name: hpc\_private
            
        *   Subnetztyp: regional
            
        *   CIDR-Block: 10.0.3.0/24
            
        *   Routentabelle: Wählen Sie die im vorherigen Schritt erstellte Routentabelle aus
            
        *   Subnetzzugriff: Privates Subnetz
            
        *   Sicherheitsliste: Sicherheitsliste auswählen, die im vorherigen Schritt erstellt wurde
            
5.  Klicken Sie auf **Subnetz erstellen**.
    
    ![](./images/create_subnet.png " ")
    
    ![](./images/create_subnet_content.png " ")
    
6.  Klicken Sie auf `hpc_vcn`, das Sie erstellt haben, und wählen Sie im Menü **Ressourcen** auf der linken Seite **Internetgateway** aus, und erstellen Sie das Internetgateway.
    
    ![](./images/create_IG.png " ")
    
    ![](./images/create_IG_content.png " ")
    
    > **Hinweis:** Dadurch wird das Internetgateway erstellt, und es muss mit einer Routentabelle verknüpft werden. In diesem Fall muss das Internetgateway mit dieser Routentabelle verknüpft sein, da die Standardroutentabelle für das öffentliche Subnetz verwendet wird.
    
7.  Routingregeln zu Routentabelle hinzufügen. Wählen Sie im Menü **Ressourcen** auf der linken Seite die Option **Standardroutentabelle für `hpc_vcn`** aus, und klicken Sie auf **Routingregeln hinzufügen**.
    
    *   Zieltyp: Internetgateway
        
    *   Ziel-CIDR-Block: 0.0.0.0/0
        
    *   Ziel-Internetgateway im Compartment: Von Ihnen erstelltes Internetgateway
        
    
    ![](./images/route_table.png " ")
    
    ![](./images/route_rules.png " ")
    

## Aufgabe 2: Clusterknoten erstellen

Wir erstellen zwei Knoten für diese Aufgabe, den **Headnode** für das Cluster im öffentlichen Subnetz und **einen Worker-Compute-Knoten** im privaten Subnetz. Um auf die Worker-Knoten zuzugreifen, erstellen wir zuerst den Headnode, generieren dann einen SSH-Schlüssel im Headnode und verwenden diesen Public Key beim Erstellen des Worker-Knotens.

> **Hinweis:** Für diese Aufgabe verwenden wir nur die Basisausprägung VM.Standard2.1. Für ein tatsächliches Deployment ist jedoch eine größere Ausprägung wie BM.HPC2.36 angemessen. Einige High-Performance-Ausprägungen sind nur in bestimmten Regionen und Availability-Domains verfügbar.

1.  Headnode erstellen
    
    *   Name: d.h. hpc\_head
        
    *   Image oder Betriebssystem: neueste Version von Oracle Linux (Standard)
        
    *   Availability-Domain: Domain für VM.Standard 2.1-Ausprägungen verfügbar
        
    *   Instanzausprägung:
        
        *   VM.Standard 2.1
            
        *   andere Formen (BM.HPC2.36 bevorzugt, wenn verfügbar)
            
    *   Virtuelles Cloud-Netzwerk: VCN erstellt vor
        
    *   Subnetz: Öffentliches Subnetz erstellt vor
        
    *   SSH-Schlüssel: Anhängen der Public-Key-Datei
        
    
    1.  Wählen Sie im Menü oben links die Option "Compute" aus, und erstellen Sie eine Instanz.
        
        ![](./images/compute.png " ")
        
        ![](./images/compute_bm.png " ")
        
        ![](./images/compute_vm.png " ")
        
    2.  Greifen Sie mit SSH auf den Headnode zu, und generieren Sie einen SSH-Schlüssel für die Kommunikation zwischen dem Cluster.
        
            $ <copy>ssh -i <ssh_key> opc@<headnode_ip_address></copy>
            
            $ <copy>ssh-keygen</copy>
            
        
        > **Hinweis:** Ändern Sie nicht den Speicherort der SSH-Schlüsseldatei (/home/opc/.ssh/id\_rsa), und drücken Sie die Eingabetaste, wenn Sie nach einer Passphrase gefragt werden (zwei).
        
        Führen Sie die gesamte Zeichenfolge aus, und kopieren Sie sie, die beim Erstellen des Worker-Knotens verwendet wird.
        
            $ <copy>cat ~/.ssh/id_rsa.pub</copy>
            
2.  Worker-Knoten erstellen
    
    1.  Wählen Sie im Menü oben links die Option "Compute", und erstellen Sie eine Instanz mit den folgenden Details:
        
        *   Name: d.h. hpc\_worker1
            
        *   Image oder Betriebssystem: neueste Version von Oracle Linux (Standard)
            
        *   Availability-Domain: Domain für VM.Standard 2.1-Ausprägungen verfügbar
            
        *   Instanzausprägung:
            
            *   VM.Standard 2.1
                
            *   andere Formen (BM.HPC2.36 bevorzugt, wenn verfügbar)
                
        *   Virtuelles Cloud-Netzwerk: VCN erstellt vor
            
        *   Subnetz: vorher erstelltes privates Subnetz
            
        *   SSH-Schlüssel: Public-Key-Zeichenfolge wurde aus einem Schritt oben kopiert.
            
    2.  SSH in Worker-Knoten. Kehren Sie zur Konsole zurück, die beim Hauptknoten angemeldet ist, und nehmen Sie die private IP-Adresse und SSH vom Hauptknoten zum Worker-Knoten auf
        
            $ <copy>ssh opc@10.x.x.x</copy>
            

## Aufgabe 3: NAT-Gateway einrichten

> **Hinweis:** Dies gilt nur für den Worker-Knoten.

1.  Wählen Sie den Worker-Knoten aus, und klicken Sie auf **Angehängte VNICs** im Menü **Ressourcen** auf der linken Seite.
    
2.  Wählen Sie **VNIC bearbeiten** aus.
    
3.  Deaktivieren Sie die Option **Quell-/Zielprüfung überspringen**, wenn sie aktiviert ist, und klicken Sie auf **VNICs aktualisieren**.
    
    ![](./images/nat_gateway.png " ")
    

## Aufgabe 4: Laufwerk mounten

> **Hinweis:** Nur wenn der Knotenausprägung ein NVMe zugeordnet ist (BM.HPC2.36 verfügt über einen, nicht VM.Standard2.1), haben HPC-Rechner einen lokalen NVMe-Speicher, ist jedoch standardmäßig nicht gemountet. Fahren Sie mit Schritt 5 fort, wenn Sie VM.Standard2.1 verwenden

1.  Greifen Sie mit SSH auf den Headnode zu, und führen Sie die folgenden Befehle aus
    
        $ <copy>lsblk</copy>
        
    
    Das Laufwerk muss mit dem NAME auf der linken Seite aufgeführt werden (möglicherweise nvme0n1, wenn es anders ist, ändern Sie es in den nächsten Befehlen).
    
    Der Headnode verfügt über das gemeinsam genutzte Laufwerk mit der Installation und dem Modell. Diese wird von allen verschiedenen Worker-Knoten gemeinsam verwendet. Jeder Worker-Knoten mounten auch das Laufwerk, das lokal auf einem NVMe-Laufwerk ausgeführt wird. In diesem Beispiel beträgt das Share-Verzeichnis 500 GB. Sie können das aber auch ändern.
    
2.  Das Laufwerk auf dem Worker-Knoten partitionieren (optional)
    
        $ <copy>sudo yum -y install gdisk</copy>
        
    
        $ <copy>sudo gdisk /dev/nvme0n1</copy>     $ > n    # Create new partition
        $ > 1    # Partition Number
        $ >      # Default start of the partition
        $ > +500G # Size of the shared partition
        $ > 8300  # Type = Linux File System
        $ > n     # Create new partition
        $ > 2     # Partition Number
        $ >       # Default start of the partition
        $ >       # Default end of the partition, to fill the whole drive
        
        $ > 8300  # Type = Linux File System
        
        $ > w      # Write to file
        $ > Y      # Accept Changes
        
3.  Laufwerk auf den Worker-Knoten formatieren
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1</copy>
        
4.  Partitionen auf dem Headnode formatieren
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p1</copy>
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p2</copy>
        
5.  Verzeichnis erstellen und Laufwerk mounten
    
6.  Headnode (lokal und gemeinsam): Wählen Sie /mnt/share als Mount-Verzeichnis für die 500G-Partition und /mnt/local für die größere Partition.
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1p1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1p2 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        
7.  Kopfknoten (Freigabe):
    
        $ <copy>sudo mkdir /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        
8.  Worker-Knoten: Wählen Sie /mnt/local als Mount-Verzeichnis des gesamten Laufwerks aus.
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        

## Aufgabe 5: Netzwerkdateisysteme erstellen

1.  Mountziel erstellen. Klicken Sie im Menü oben links auf **File Storage** und **Mountziel**.
    
    *   Neuer Mount-Zielname: Geben Sie einen Namen ein (Beispiel: openfoam\_fs)
        
    *   Virtuelles Cloud-Netzwerk: Wählen Sie das oben erstellte VCN aus
        
    *   Subnetz: Öffentliches VCN auswählen
        
2.  Headnode befindet sich in einem öffentlichen Subnetz. Die Firewall bleibt hoch, und eine Ausnahme wird hinzugefügt über:  
    
        <copy>sudo firewall-cmd --permanent --zone=public --add-service=nfs</copy>
        <copy>sudo firewall-cmd --reload</copy>
        
3.  Klicken Sie auf das erstellte Mountziel, und klicken Sie dann auf den Exportpfad. Klicken Sie dann auf die Mountbefehle, und kopieren Sie die folgenden Befehle:
    
        <copy>sudo yum install nfs-utils</copy>
        <copy>sudo mkdir -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        
4.  Da sie sich auf den Worker-Knoten in einem privaten Subnetz mit einer Sicherheitsliste befinden, die den Zugriff einschränkt, können wir die Firewall vollständig deaktivieren. Dann können wir nfs-utils Tools installieren und die NFS wie oben montieren.
    
        <copy>sudo systemctl stop firewalld</copy>
        <copy>sudo yum -y install nfs-utils</copy>
        <copy>sudo mkdir  -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        

## Aufgabe 6: OpenFOAM installieren

1.  **Alle Worker-Knoten verbinden**  
    
    Jeder Worker-Knoten muss mit allen Worker-Knoten kommunizieren können. SSH-Kommunikation funktioniert, die meisten Anwendungen weisen jedoch Probleme auf, wenn sich nicht alle Hosts in der bekannten Hostdatei befinden. Um die bekannte Hostprüfung für Knoten mit Adresse im VCN zu deaktivieren, können Sie mit den folgenden Befehlen deaktivieren. Möglicherweise müssen Sie sie geringfügig ändern, wenn Ihre Subnetze unterschiedliche Adressen enthalten. Setzen Sie den folgenden Codeblock in ein Shellskript, und führen Sie das Skript aus.
    
        <copy>for i in 0 1 2 3
        do
             echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
             echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done</copy>
        
2.  **Bearbeitungsliste erstellen**  
    
    OpenFOAM auf dem Headnode weiß nicht automatisch, welche Compute Nodes verfügbar sind. Sie können unter `/mnt/share/machinelist.txt with` die private IP-Adresse aller Knoten sowie die Anzahl der verfügbaren CPUs erstellen. Der Kopfknoten sollte ebenfalls einbezogen werden. Das Format für die Einträge lautet `private-IP-address cpu=number-of-cores`.
    
        <copy>10.0.0.2 cpu=1
        10.0.3.2 cpu=1</copy>
        
3.  **Kopfknoten**
    
    Installieren Sie aus Quellen, ändern Sie den Pfad zu den Tarballs in den nächsten Befehlen. Dieses Beispiel enthält die Basisquellen OpenFOAM. OpenFOAM von ESI wurde ebenfalls getestet. Um die Installation zwischen den verschiedenen Compute Nodes gemeinsam nutzen zu können, installieren Sie sie im Netzwerkdateisystem.
    
        <copy>$ sudo yum groupinstall -y 'Development Tools'
        $ sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        $ cd /mnt/share
        $ wget -O - http://dl.openfoam.org/source/7 | tar xvz
        $ wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        $ mv OpenFOAM-7-version-7 OpenFOAM-7
        $ mv ThirdParty-7-version-7 ThirdParty-7
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        $ echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc
        $ cd /mnt/share/OpenFOAM-7
        $ ./Allwmake -j</copy>
        
4.  **Worker-Knoten**  
    
        $ <copy>sudo yum -y install openmpi openmpi-devel
        $ cd /mnt/share
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        $ echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc</copy>
        

## Aufgabe 7: Simulations-Workload ausführen und Ausgabe wiedergeben

1.  Führen Sie auf Headnode die folgenden Befehle aus, um die Ausgabe mit dem Paraview-Package wiederzugeben.
    
        $ <copy>sudo yum install -y mesa-libGLU
        $ cd /mnt/share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php >  file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  Führen Sie auf der Headnonde die folgenden Befehle aus, um den VNC-Server einzurichten:  
    
        $ <copy>sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver</copy>
        
3.  Laden Sie diese ZIP-Datei mit den [Skripten](../scripts/motorbike_RDMA.tgz) in /mnt/share/work in einem der Worker-Knoten herunter. Dekomprimieren Sie die Datei mit `tar -xf motorbike_RDMA.tgz`.
    
4.  Bevor Sie die Workload ausführen, müssen Sie die Datei allrun bearbeiten, um die von Ihnen erstellte Architektur abzugleichen. Zuerst verschieben wir den Ordner aus dem Installationsordner OpenFOAM.
    
        $ <copy>model_drive=/mnt/share
        $ sudo mkdir $model_drive/work
        $ sudo chmod 777 $model_drive/work
        $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
        $ cd /mnt/share/work/motorBike/system</copy>
        
5.  Bearbeiten Sie das Dateisystem/decomposeParDict, und ändern Sie diese Zeile numberOfSubdomains 6; in numberOfSubdomains 12; oder wie viele Prozesse Sie benötigen. Ändern Sie dann im hierarchicalCoeffs-Block den Wert n von n (3 2 1); in n (4 3 1). Wenn Sie diese 3 Werte multiplizieren, sollten Sie den Wert numberOfSubdomains abrufen.
    
    Für die Ausführung mit einer Konfiguration von 1 VM.Standard2.1-Worker-Knoten:
    
        =========                 |
        \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
         \\    /   O peration     | Website:  https://openfoam.org
          \\  /    A nd           | Version:  7
           \\/     M anipulation  |
        \*---------------------------------------------------------------------------*/
        FoamFile
        {
             version     2.0;
             format      ascii;
             class       dictionary;
             object      decomposeParDict;
        }
        
        // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
        
        numberOfSubdomains 2;
        
        method          hierarchical;
        // method          ptscotch;
        
        simpleCoeffs
        {
             n               (4 1 1);
             delta           0.001;
        }
        
        hierarchicalCoeffs
        {
             n               (2 1 1);
             delta           0.001;
             order           xyz;
        }
        
        manualCoeffs
        {
             dataFile        "cellDecomposition";
        }
        
        // ************************************************************************* //
        
6.  Bearbeiten Sie als Nächstes die Allrun-Datei in /mnt/share/work/motorBike.
    
        <copy>#!/bin/sh
        cd ${0%/*} || exit 1    # Run from this directory
        NP=$1
        install_drive=/mnt/share
        # Source tutorial run functions
        . $WM_PROJECT_DIR/bin/tools/RunFunctions
        
        # Copy motorbike surface from resources directory
        cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
        cp $install_drive/machinelist.txt hostfile
        
        runApplication surfaceFeatures
        
        runApplication blockMesh
        
        runApplication decomposePar -copyZero
        echo "Running snappyHexMesh"
        mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.snappyHexMesh
        ls -d processor* | xargs -I {} rm -rf ./{}/0
        ls -d processor* | xargs -I {} cp -r 0 ./{}/0
        echo "Running patchsummary"
        mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
        echo "Running potentialFoam"
        mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
        echo "Running simpleFoam"
        mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam
        
        runApplication reconstructParMesh -constant
        runApplication reconstructPar -latestTime
        
        foamToVTK
        touch motorbike.foam</copy>
        
7.  Stellen Sie sicher, dass Sie sich im Worker-Knoten befinden, und führen Sie die Workload aus:
    
        $ <copy>ssh worker_node_IP
        $ cd /mnt/share/work/
        $ ./Allrun 2
        
        $ ./Allrun 2
        $ Cleaning /mnt/share/work case
        $ Mesh Dimensions: (40 16 16)
        $ Cores:36: 6, 6, 1
        $ Running surfaceFeatures on /mnt/share/work
        $ Running blockMesh on /mnt/share/work
        $ Running decomposePar on /mnt/share/work
        $ Running snappyHexMesh
        $ Running patchsummary
        $ Running potentialFoam
        $ Running simpleFoam
        $ Running reconstructParMesh on /mnt/share/work
        $ Running reconstructPar on /mnt/share/work
        219.95</copy>
        
8.  Nachdem die Workload erfolgreich abgeschlossen wurde, konfigurieren Sie den VNC-Client wie folgt auf Ihrem Rechner. Öffentliche IP von Bastion-Server und VNC-Port angeben
    
    ![](./images/24-VNC_Connection.png " ")
    
9.  OPTIONALLY. Wenn Sie VNC-Port 5901 nicht öffnen dürfen oder aus Sicherheitsgründen SSH-Tunnel für diesen Port erstellen möchten, verwenden Sie den folgenden Befehl, um SSH-Tunnel auf Port 5901 zu erstellen, ohne den Port in der Sicherheitsliste zu öffnen
    
10.  Erstellen Sie einen Tunnel vom Laptop/Desktop mit dem folgenden Befehl aus dem Terminalfenster. Hier erfolgt die Kommunikation für Port 5901 auf SSH-Port 22, und die IP-Adresse 150.136.41.3 ist die öffentliche IP-Adresse des Bastion-Servers.
    
        $ <copy>ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
11.  Schließen Sie das obige SSH-Tunnel-Terminalfenster nicht. Starten Sie jetzt die VNC-Session, und verwenden Sie dieses Mal anstelle der IP-Adresse "localhost" auf Port 5901, obwohl dieser Port nicht in der Sicherheitsliste des Subnetzes geöffnet ist.
    
    ![](./images/28-ssh_Tunnel.png " ")
    
12.  Starten Sie die Paraview-Anwendung im Bastion-Server
    
        $ <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview</copy>
        
13.  Wählen Sie im Anwendungsfenster von Paraview "File -> Open -> Path "/mnt/gluster-share/work" (Datei -> Öffnen), und wählen Sie den Dateinamen motorbike.foam. Es wird null Byte-Datei und das sollte in Ordnung sein.
    
    ![](./images/25-Paraview_Menu.png " ")
    
14.  Wählen Sie links im Fenster auf der Registerkarte "Eigenschaften" die Option "Mesh-Regionen", um alle Werte auszuwählen, und heben Sie die Auswahl der obersten Werte auf, die nicht mit dem Präfix motorBike\_ beginnen. Stellen Sie sicher, dass alle Werte ausgewählt sind, die mit motorBike\_ beginnen. Klicken Sie auf die Schaltfläche "Anwenden", werden einige Fehler angezeigt. Ignorieren Sie das daraufhin angezeigte Fehlerfenster, um die Wiedergabe des Bildes in der VNC-Konsole anzuzeigen.
    
    ![](./images/26-Mesh_Regions.png " ")
    
15.  Ein Bild wie unten wird auf dem Bildschirm wiedergegeben. Basierend auf einigen Anzeigeeinstellungen sieht das Bild auf dem Bildschirm möglicherweise etwas anders aus.
    
    ![](./images/27-Image_Rendering.png " ")
    

Alles fertig! Damit ist die Demo zur Ausführung der OpenFoam-Anwendung auf einer Standard-VM auf OCI abgeschlossen.

_Glückwunsch! Sie haben den Workshop erfolgreich abgeschlossen_

Diese detaillierten Informationen zur Verwaltung von High Performance Compute-Instanzen. Eine vollständige Befehlsreferenz finden Sie in der OCI-Dokumentation [hier](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Zuletzt aktualisiert von/Datum** - Harrison Dvoor, Januar 2021