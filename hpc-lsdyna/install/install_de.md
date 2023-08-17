# LS-DYNA installieren

## Einführung

In dieser Übung installieren Sie LS-DYNA.

Geschätzte Laborzeit: 25 Minuten

## Aufgabe 1: Binärdateien herunterladen

Sie können die LS-DYNA-Binärdateien von der [LSTC-Website](http://www.lstc.com/download/ls-dyna) herunterladen oder mithilfe von scp auf Ihren Rechner übertragen.

Nehmen Sie die Version auf, die für mpi erstellt und für RedHat Ent Srv 5.4 kompiliert wurde. Nach unseren Ergebnissen ist IntelMPI schneller als Plattform-MPI auf OCI (ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1).tar.gz)

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

Eine weitere Möglichkeit besteht darin, das Installationsprogramm in den Objektspeicher hochzuladen.

1.  Wählen Sie im Hauptmenü der Konsole "Object Storage".
2.  Wählen Sie oben rechts die richtige Region
3.  Wählen Sie das richtige Compartment auf der linken Seite aus
4.  Bucket erstellen, wenn noch kein Bucket erstellt wurde
5.  Wählen Sie im Bucket das Objekt zum Hochladen aus, und geben Sie den Pfad des Installationsprogramms an.
6.  Wählen Sie die 3 Punkte auf der rechten Seite des Installationsprogrammobjekts, und wählen Sie "Vorab authentifizierte Anforderung erstellen" aus
7.  Wenn Sie die URL verlieren, können Sie sie nicht zurückholen. Sie können jedoch eine neue vorab authentifizierte Anforderung neu generieren.

Laden Sie den Objektspeicher des Installationsprogrammformulars herunter mit

    <copy>
        wget PAR_URL
    </copy>
    

Entpacken oder entpacken Sie das Installationsprogramm je nach Version

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## Aufgabe 2: LS-DYNA installieren

1.  Entpacken Sie die Binärdateien an einem gemeinsamen Speicherort. Standardmäßig ist für ein HPC-Cluster ein NFS-Share oder ein Gluster-Share auf allen Compute Nodes gemountet.
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## Aufgabe 3: MPI-Bibliotheken installieren

**Intel MPI 2018**

Führen Sie diese Befehle auf jedem Knoten aus.

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**Plattform-MPI**

Installieren Sie diese Bibliotheken:

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

Laden Sie die tar-Datei von der IBM-Website herunter, und führen Sie folgenden Befehl aus:

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

Befolgen Sie dann die Anweisungen auf dem Bildschirm. Wenn Sie die Plattform auf einem Share-Laufwerk installieren, ist sie für alle Compute Nodes zugänglich.

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020