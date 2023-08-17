# STAR-CCM+ wird installiert

## Einführung

In dieser Übung installieren Sie STAR-CCM+.

Geschätzte Laborzeit: 25 Minuten

## Aufgabe 1: Bestimmte Bibliotheken hinzufügen

_**Wenn Sie das CFD Ready-Cluster vom Marketplace verwendet haben, ist dieser Schritt nicht erforderlich**_

1.  Es gibt einige Librarys, die dem Oracle Linux-Image auf allen Compute Nodes hinzugefügt werden müssen.
    
            sudo yum -y install libSM libX11 libXext libXt
        

## Aufgabe 2: Binärdateien herunterladen

Sie können das STAR-CCM+-Installationsprogramm von der Siemens PLM-Website herunterladen oder mit scp auf Ihren Rechner schieben.

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

Eine weitere Möglichkeit besteht darin, das Installationsprogramm in den Objektspeicher hochzuladen.

1.  Wählen Sie im Hauptmenü der Konsole "Object Storage".
2.  Wählen Sie oben rechts die richtige Region
3.  Wählen Sie das richtige Compartment auf der linken Seite aus
4.  Bucket erstellen, wenn noch kein Bucket erstellt wurde
5.  Wählen Sie im Bucket das Objekt zum Hochladen aus, und geben Sie den Pfad des Installationsprogramms an.
6.  Wählen Sie die 3 Punkte auf der rechten Seite des Installationsprogrammobjekts, und wählen Sie "Vorab authentifizierte Anforderung erstellen" aus
7.  Wenn Sie die URL verlieren, können Sie sie nicht zurückholen. Sie können jedoch eine neue vorab authentifizierte Anforderung neu generieren.

Laden Sie den Objektspeicher des Installationsprogrammformulars herunter mit

        wget PAR_URL
    

Entpacken oder entpacken Sie das Installationsprogramm je nach Version

        tar -xf installer.tgz
        unzip installer.tgz
    

## Aufgabe 3: Installieren

1.  Starten Sie das Installationsprogramm an einem freigegebenen Speicherort. Standardmäßig ist für ein HPC-Cluster ein NFS-Share auf allen Compute Nodes gemountet.
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020