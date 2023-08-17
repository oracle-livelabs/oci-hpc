# WRF-Setup, -Installation und -Ausführung für HPC-Cluster

## Einführung

In dieser Übung lernen Sie, wie Sie ein WRF-Cluster auf OCI-Infrastruktur einrichten und ausführen. Dieses Handbuch soll Ihnen zeigen, wie Sie ein HPC-Cluster mit zwei Knoten erstellen, um eine Umgebung einzurichten, in der Sie Experimente und Wettersimulationen ausführen können.

Geschätzte Laborzeit: 90 Minuten

### Ziele

In dieser Übung lernen Sie Folgendes kennen:

*   HPC-Cluster mit zwei Knoten bereitstellen
*   Abhängigkeiten und WRF/WPS herunterladen
*   Librarys für WRF/WPS werden kompiliert
*   WRF und WPS werden kompiliert
*   WRF-Domain mit Geogrid erstellen
*   GFS-Daten herunterladen und Ungrib und Metgrid ausführen
*   WRF mit echten Daten ausführen

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Ein Oracle-Test- oder kostenpflichtiger Cloud-Account
*   Hinweis für diese Übung verwenden wir zwei **BM.HPC2.36**\-Knoten für unser Cluster und einen VM.Standard2.4 für unsere Bastion.

## Aufgabe 1: HPC-Infrastruktur einrichten

1.  Verwenden Sie das Navigationsmenü auf der linken Seite, und navigieren Sie zu **Lösungen und Plattform** > Marketplace > Anwendungen.
2.  Drücken Sie im Suchleistentyp HPC die EINGABETASTE/Eingabetaste.
3.  Wählen Sie im Abschnitt "Alle Anwendungen" die Option "HPC-Cluster", und starten Sie den Stack. Sie sollte das Bild auf der linken Seite und das Bild auf der rechten Seite anzeigen.  
    ![Screenshot des HPC-Bildes](images/hpc2.png)
4.  Geben Sie im Menü "Stack erstellen" einen Namen und eine Beschreibung an, und klicken Sie dann auf "Weiter".
5.  Wählen Sie eine AD aus, fügen Sie den SSH-Schlüssel ein, und lassen Sie das Kontrollkästchen "KONFIGURE NFS SHARE" aktiviert.
6.  Wählen Sie für die Bastion dieselbe AD wie das Cluster aus, und wählen Sie die Ausprägung **VM.Standard.2.4** aus, und klicken Sie auf "Weiter".
7.  Prüfen Sie auf dem Prüfbildschirm alle eingegebenen Informationen, und klicken Sie auf "Erstellen".

## Aufgabe 2: Gnome-Desktop einrichten

1.  Es gibt eine Reihe von verschiedenen Desktop-Technologien, die Sie verwenden können. In diesem Handbuch wird jedoch das Setup und die Konfiguration von Gnome-Desktop überschrieben, da es der Standard für Oracle Linux ist. Mit den folgenden Schritten können Sie Gnome Desktop für unsere Oracle Linux 7 Bastion-Instanz herunterladen, installieren und konfigurieren.
    
        <copy>
        sudo yum groupinstall "server with gui"
        Y
        sudo yum install tigervnc-server
        Y
        vncserver # DO NOT MAKE READ ONLY and password should be no greater than 8 characters
        </copy>
        
2.  Öffnen Sie ein lokales Terminal  
    Führen Sie den folgenden Befehl mit Informationen zur Instanz aus: ssh -L 5901:localhost:5901 opc@IPADDRESS ![Screenshot von SSH](images/ssh.png)
    
3.  Öffnen Sie TigerVNC Viewer-Typ `localhost:1` im VNC-Server: Abschnitt  
    . Klicken Sie dann auf "Connect".  
    ![Screenshot des TigerVNC-Viewers](images/tigervnc.png)
    
4.  Geben Sie Ihr VNC-Kennwort ein.  
    Klicken Sie dann auf "OK".  
    ![Screenshot der VNC-Authentifizierung](images/vncauth.png)
    
5.  Abschließend wird ein Gnome-Desktop angezeigt. Hier können Sie weiterarbeiten.  
    ![Screenshot von Gnome Desktop](images/gnomedesktop.png)
    

## Aufgabe 3: Oracle Linux konfigurieren

1.  Da Sie jetzt auf die Desktopumgebung unserer Instanz zugreifen können, können Sie sie zur Ausführung von WRF konfigurieren. Damit Sie die Abhängigkeiten installieren können, müssen Sie fortfahren. Klicken Sie oben links auf "Applications" und dann auf "System Tools". Klicken Sie dann auf "Terminal". Führen Sie im Terminal Folgendes aus.
    
        <copy>
        sudo yum update -y
        sudo yum upgrade -y
        sudo yum group install "Development Tools" -y
        sudo yum install ncview -y
        sudo yum install mc -y
        </copy>
        

## Aufgabe 4: Librarys für WRF herunterladen und kompilieren

Nachdem wir die meisten Abhängigkeiten installiert haben, die wir für WRF benötigen, können Sie mit der Kompilierung der Bibliotheken beginnen, die WRF ausführen muss.

### Ordnerstruktur erstellen und Bibliotheken herunterladen

1.  Geben Sie im Remote-Terminal die folgenden Befehle ein, um mit der Einrichtung der erforderlichen Ordnerstruktur zu beginnen.
    
        <copy>  
        cd /mnt/nfs-share
        mkdir WRF
        cd WRF
        mkdir downloads
        cd downloads
        </copy>
        
2.  Im Verzeichnis "downloads" können Sie alle benötigten Bibliotheken mit den folgenden Befehlen herunterladen.
    
        <copy>
        curl -O ftp://ftp.unidata.ucar.edu/pub/netcdf/old/netcdf-4.1.2.tar.gz  
        curl -O https://www.zlib.net/zlib-1.2.11.tar.gz  
        curl -O ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng16/libpng-1.6.37.tar.gz  
        curl -O https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz  
        curl -O https://www.mpich.org/static/downloads/3.3.2/mpich-3.3.2.tar.gz
        </copy>
        
3.  Anstatt die Dateien einzeln zu dekomprimieren, können Sie dazu eine **Schleife** verwenden und anschließend zum WRF-Verzeichnis zurückkehren.
    
        <copy>
        for i in *.gz ; do tar -xzf $i ; done
        cd /mnt/nfs-share/WRF  
        </copy>
        
4.  Wenn Sie jetzt wieder im WRF-Verzeichnis sind, können Sie die Ordnerstruktur für unsere Bibliotheken für WRF einrichten. Außerdem erstellen wir eine Umgebungsvariable zur Referenzierung des Bibliothekspfads, der später in der Anleitung verwendet werden soll.
    
        <copy>
        mkdir libs  
        cd libs  
        mkdir netcdf  
        mkdir mpich  
        mkdir grib2 # (this will be used for the jasper, libpng, and zlib libraries)  
        export LIBDIR=/mnt/nfs-share/WRF/libs
        </copy>
        

### grib2-Bibliothek wird kompiliert

Die grib2-Bibliothek ist tatsächlich eine Zusammenstellung aus drei separaten Librarys, insbesondere zlib, jasper und libpng.

5.  Zlib wird kompiliert
    
        <copy> 
        cd /mnt/nfs-share/WRF/downloads
        cd zlib-1.2.11
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        
6.  libpng wird kompiliert
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd libpng-1.6.37/
        ./configure --prefix=$LIBDIR/grib2 LDFLAGS="-L$LIBDIR/grib2/lib" CPPFLAGS="-I$LIBDIR/grib2/include"
        make
        make install
        </copy>
        
7.  Jasper wird kompiliert
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd jasper-1.900.1/
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        

### Kompilieren von Netcdf- und Mpich-Bibliotheken

8.  netcdf-Bibliothek wird kompiliert
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd netcdf-4.1.2/
        ./configure --prefix=$LIBDIR/netcdf --disable-dap --disable-netcdf-4
        make
        make install
        </copy>
        
9.  mpich-Bibliothek wird kompiliert
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd mpich-3.3.2/
        ./configure --prefix=$LIBDIR/mpich
        make
        make install
        </copy>
        

## Aufgabe 5: WRF und WPS kompilieren

1.  Nachdem wir die Ordnerstruktur für die Bibliotheken eingerichtet haben, können wir WRF und WPS herunterladen und kompilieren. Der folgende Codeblock lädt die Programme herunter und platziert sie an den entsprechenden Orten.
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        wget https://github.com/wrf-model/WRF/archive/v4.1.5.tar.gz
        wget https://github.com/wrf-model/WPS/archive/v4.1.tar.gz
        for i in *.gz ; do tar -xzf $i ; done
        mv WRF-4.1.5/ /mnt/nfs-share/WRF/
        mv WPS-4.1/ /mnt/nfs-share/WRF/
        </copy>
        
2.  Bevor wir WRF kompilieren können, müssen wir einige Umgebungsvariablen einrichten, damit das Programm die von uns kompilierten Bibliotheken finden und verwenden kann.
    
        <copy>
        cd .. or /mnt/nfs-share/WRF
        cd WRF-4.1.5/
        export NETCDF=$LIBDIR/netcdf
        export PATH=$LIBDIR/mpich/bin:$PATH
        export JASPERLIB=$LIBDIR/grib2/lib
        export JASPERINC=$LIBDIR/grib2/include
        </copy>
        
3.  Nachdem wir nun alle Bibliotheken auf Umgebungsvariablen verwiesen haben, können wir WRF endlich kompilieren. Für dieses Labor werden wir WRF zusammenstellen, um echte meteorologische Daten verwenden zu können, die gesammelt wurden. Sie werden in zwei Abschnitte unterteilt: eine Konfiguration und einen Kompilierungsabschnitt.
    
    **Konfigurieren:**  
    Sie können mit Option 34 "gfortran/gcc" und Option 1 wählen, da die Verschachtelung in diesem Handbuch nicht abgedeckt wird.
    
        <copy>  
        ./configure
        34 (dmpar)
        1
        vi ~/.bashrc  
            export LIBDIR=/mnt/nfs-share/WRF/libs  
            export LD_LIBRARY_PATH=$LIBDIR/netcdf/lib:$LIBDIR/grib2/lib   
            export PATH=$LIBDIR/mpich/bin:$PATH
        source ~/.bashrc
        </copy>
        
    
    **Kompilieren:**  
    Hier können Sie WRF kompilieren und testen, ob es ausgeführt wird.
    
        <copy>
        ./compile em_real
        cd main
        ./wrf.exe
        ./real.exe
        </copy>
        
4.  Nachdem WRF kompiliert wurde, müssen wir WPS kompilieren. WRF muss zuerst kompiliert werden. Hier wählen wir Option 3 aus, weil wir das Linux-Betriebssystem (Oracle Linux) auf der x86-Infrastruktur (OCI BM.HPC2.36) zusammen mit dem Gfortran-Compiler verwenden.
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        export WRF_DIR=/mnt/nfs-share/WRF/WRF-4.1.5/
        ./configure
        3
        ./compile
        </copy>
        
5.  Nachdem WPS kompiliert ist, müssen wir die Daten herunterladen, die wir zur Simulation der Geographie der Welt verwenden werden. Wir erstellen einen neuen Ordner für diese Informationen. Die Komprimierung dauert eine Weile, da wir die empfohlenen Dateien mit hoher Auflösung verwenden. Daher verwenden wir den PV-Dialog, um eine Fortschrittsleiste für uns zu generieren.
    
        <copy>
        cd /mnt/nfs-share/WRF/
        mkdir GEOG
        cd GEOG
        wget https://www2.mmm.ucar.edu/wrf/src/wps_files/geog_high_res_mandatory.tar.gz  
        tar xzf geog_high_res_mandatory.tar.gz
        </copy>
        
6.  Nachdem wir die Geografiedaten heruntergeladen und extrahiert haben, müssen wir einige Dateien in das richtige Verzeichnis verschieben und einige zusätzliche Dateien löschen, die nicht benötigt werden. Dafür verwenden wir den Mitternacht-Kommandanten, aber Sie können Terminal-Befehle nur verwenden, wenn Sie möchten. Im Folgenden wird die Mitternachtkommander-Schnittstelle verwendet, also **`DO NOT`** `copy the following entire code block into terminal`.
    
        <copy>
        mc    #Opens Midnight Commander
        Click on the left WPS_GEOG
        Use CTRL + T to highlight all the folders
        F6    #This will move the files to the GEOG directory
        ENTER #This will confirm the choice
        F10   #This is used to exit Midnight commander
        sudo rm -r WPS_GEOG   #This will delete the additional GEOG folder.
        mc    #Opens Midnight Commander
        Highlight *._WPS_GEOG
        F8    #This deletes the highlighted file
        ENTER #This will confirm the choice
        F10 #This is used to exit Midnight commander
        </copy>
        
7.  Sie müssen die Datei namelist.wps an einem beliebigen Speicherort auf null setzen. Ich werde ein kleines Netz (10.000 x 10.000 Meter) mit Woburn Massachusetts USA als Mittelpunkt stehen.
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **Ändern Sie basierend auf Ihrem Experiment alle folgenden Werte nach Ihren Wünschen.**
    
    *   `max_dom`: Eine Ganzzahl, die in der Simulation die Gesamtanzahl der Domains angibt, einschließlich der übergeordneten Domain. Der Standardwert ist 1.
    *   `e_we`: Ganzzahlen zur Angabe der West-Ost-Dimension Ihres Rasters. Kein Standardwert.
    *   `e_sn`: Ganzzahlen zur Angabe der gesamten südnordischen Dimension Ihres Rasters. Kein Standardwert.
    *   `dx`: Ein realer Wert, der den Rasterabstand (in Metern) in x-Richtung angibt, wobei der Skalierungsfaktor der Karte 1 ist.
    *   `dy`: Ein realer Wert, der den Rasterabstand (in Metern) in y-Richtung angibt, wobei der Skalierungsfaktor der Karte 1 ist.
    *   `ref_lat`: Ein echter Wert, der den Breitengradteil eines (Breitengrad, Längengrad-)Standorts angibt, dessen (i,j)-Standort in der Simulationsdomain bekannt ist.
    *   `ref_lon`: Ein echter Wert, der den Längengradteil eines (Breitengrad, Längengrad-)Standorts angibt, dessen (i, j) Standort in der Simulationsdomain bekannt ist.
    *   `truelat1`: Ein echter Wert, der den ersten wahren Breitengrad für die Lambert-konforme Projektion angibt, oder der einzige wahre Breitengrad für den Mercator und polare stereografische Projektionen.
    *   `truelat2`: Ein echter Wert, der den zweiten wahren Breitengrad für die Lambert-konische Projektion angibt.
    *   `stand_lon`: Ein echter Wert, der den Längengrad angibt, der parallel zur y-Achse in den Lambert-konformen und polaren stereografischen Projektionen liegt. Bei der regelmäßigen Breiten-Längenprojektion gibt dieser Wert die Drehung um die geographischen Pole der Erde.
    *   `geog_data_path`: Eine Zeichenfolge, die den Pfad (relativ oder absolut) zu dem Verzeichnis angibt, in dem die geografischen Datenverzeichnisse gefunden werden können.
    
        <copy>
            &share  
            wrf_core = 'ARW',  
            max_dom = 1,  
            start_date = '2006-08-16_12:00:00','2006-08-16_12:00:00',  
            end_date   = '2006-08-16_18:00:00','2006-08-16_12:00:00',  
            interval_seconds = 21600  
            io_form_geogrid = 2,  
            /  
            &geogrid  
            parent_id         =   1,   1,  
            parent_grid_ratio =   1,   3,  
            i_parent_start    =   1,  31,  
            j_parent_start    =   1,  17,  
            e_we              =  100, 112,  
            e_sn              =  100,  97,  
            !  
            !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!  
            ! The default datasets used to produce the MAXSNOALB and ALBEDO12M  
            ! fields have changed in WPS v4.0. These fields are now interpolated  
            ! from MODIS-based datasets.  
            !  
            ! To match the output given by the default namelist.wps in WPS v3.9.1,  
            ! the following setting for geog_data_res may be used:  
            !  
            ! geog_data_res = 'maxsnowalb_ncep+albedo_ncep+default','maxsnowalb_ncep+albedo_ncep+default',  
            !  
            !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!  
            !  
            geog_data_res = 'default','default',  
            dx = 10000,  
            dy = 10000,  
            map_proj = 'lambert',  
            ref_lat   =  42.48,  
            ref_lon   = -71.15,  
            truelat1  =  42.48,  
            truelat2  =  42.48,  
            stand_lon = -71.15,  
            geog_data_path = '/mnt/nfs-share/WRF/GEOG/'  
            / 
        </copy>     
        
    
    **Drücken Sie zum Beenden einfach die Esc-Taste, dann die Umschalttaste: gefolgt von der QK-Eingabe**  
    **Alle diese Änderungen beziehen sich auf einen geografischen Bereich mit Woburn MA als Epizentrum.**
    
8.  Nachdem wir nun die Informationen für unser geografisches Interessengebiet eingegeben haben (Dieses Handbuch verwendet Woburn MA USA als Mittelpunkt), können wir mit ncview überprüfen, ob wir nach der Ausführung des geogrid-Programms den richtigen Standort haben.
    
        <copy>
        ./geogrid.exe
        sudo ncview geo_em.d01.nc
        </copy>
        
    
    Prüfen Sie den Standort mit der 2D-Varstellung. Wenn das Image zufrieden ist, wird die Domain erstellt.
    
    ![Screenshot von ncview](images/nc1.png)
    
9.  Nach der Einrichtung unseres geografischen Bereichs oder unserer Domäne. Wir müssen nun meteorologische Daten erhalten, um die Domain zu überlagern.
    
    1.  Navigieren Sie in einem Webbrowser zu https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/.
    2.  Klicken Sie auf den Link mit dem neuesten Datum (z.B. gfs.20210120/ wie heute der 20. Januar 2021 ist)
    3.  Wählen Sie 00  
        Nicht wirklich etwas herunterladen. Dafür erstellen wir ein Skript. Wir gehen mit der geringeren Auflösung für einen einfacher zu handhabenden Dataset für diese Übung, also 0p50 anstelle von 0p25. Verwenden Sie 0p25, wenn Sie zusätzlichen Speicher haben und Daten mit höherer Auflösung/zu zuverlässigerer Daten benötigen. Sie müssen die Anzahl der Dateien für die Dauer herunterladen, die Sie ausführen möchten. Jede Datei ist eine Stunde der Daten in einem bestimmten Intervall. EX, wenn Sie sechs Stunden lang ausführen möchten, benötigen Sie gfs.t00z.pgrb2.0p50.f000, gfs.t00z.pgrb2.0p50.f003 und gfs.t00z.pgrb2.0p50.f006. 0p25 ist in einer Stunde und 0p50 befindet sich in 3-Stunden-Schritten. Für ein Tutorial werden wir nur 6 Stunden Daten verwenden. Fühlen Sie sich frei, mehr zu verwenden, wenn Sie später mit WRF bequemer werden.
10.  Mit dieser Option können Sie zum richtigen Verzeichnis navigieren und das Skript zum Herunterladen von Daten erstellen.
    
        <copy>
        cd /mnt/nfs-share/WRF
        mkdir scripts
        mkdir GFS
        cd scripts
        vi download_gfs.sh
        </copy>
        
11.  Das Skript lautet:
    
        <copy>
        #!/bin/bash  
        
        inputdir=/mnt/nfs-share/WRF/GFS  
        rm -rf $inputdir  
        mkdir $inputdir  
        
        year=2021  
        month=01  
        day=20  
        cycle=00  
        
        for ((i=000; i<=006; i+=3))  
        do  
                ftime=`printf "%03d\n" "${i}"`  
                server=https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod  
                directory=gfs.${year}${month}${day}/${cycle}  
                file=gfs.t${cycle}z.pgrb2.0p50.f${ftime}  
                url=${server}/${directory}/${file}  
                echo $url  
                wget -O ${inputdir}/${file} ${url}  
        done 
        </copy> 
        
    
    **Um den Vorgang zu beenden, drücken Sie einfach die Esc-Taste und dann die Umschalttaste: gefolgt von der QK-Eingabe**  
    **Dieses Skript lädt SIX-Datenstunden für das Datum 01/20/21 in der Auflösung von 0p50 herunter. Passen Sie sich an Ihre Bedürfnisse an.**
    
12.  Mit den folgenden Befehlen kann das Skript ausgeführt werden und zum Herunterladen der Daten ausgeführt werden:
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
13.  Nachdem wir unsere Daten heruntergeladen haben, können Sie durch den Prozess der Überlagerung über unsere Domain gehen. Dazu verwenden wir Ungrib und Metgrid. Sie werden in zwei Abschnitte unterteilt: eine Konfiguration und einen Ausführungsabschnitt.
    
    **Konfigurieren:**
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh /mnt/nfs-share/WRF/GFS/
        vi namelist.wps
        </copy>  
        
    
    **Ändern Sie alle Werte unten basierend auf den heruntergeladenen Dateien**.
    
    *   `start_date` im Format: Year-Month-Day\_hour:minute:second
    *   `end_date` im Format: Year-Month-Day\_hour:minute:second
    *   `interval seconds`: Die Ganzzahl von Sekunden zwischen zeitabhängigen meteorologischen Eingabedateien. Kein Standardwert.
    
        <copy>
        &share  
        wrf_core = 'ARW',  
        max_dom = 1,  
        start_date = '2021-01-20_00:00:00',         # start time  
        end_date   = '2021-01-20_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        / 
        </copy>
        
    
    **Um den Vorgang zu beenden, drücken Sie einfach Esc und dann Shift: gefolgt von wq enter**
    
    **Ausführen:**
    
        <copy>
        ./ungrib.exe
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
14.  Wir können uns die Ergebnisse ansehen, indem wir ncview verwenden und skintemp ansehen, um den Wert der heruntergeladenen Daten zu sehen.
    
        <copy>
        sudo ncview met_em.d01.2021-01-20_00:00:00.nc 
        </copy>
        
    
    ![Screenshot von ncview](images/nc2.png)
    

## Aufgabe 6: Real und WRF ausführen

1.  Wir haben Daten zur Darstellung eines geografischen Standortes herabgestuft und wir haben ihm meteorologische Daten hinzugefügt; es ist endlich an der Zeit, WRF mit echten Daten auszuführen.
    
        <copy>
        cd /mnt/nfs-share/WRF/WRF-4.1.5
        cd run
        ln -s ../../WPS-4.1/met_em* .
        vi namelist.input
        </copy>
        
2.  In der Datei namelist.input müssen wir den Inhalt bearbeiten, um unsere geografische Region/Domain und unser Datum und Intervall widerzuspiegeln.  
    **Ändern Sie basierend auf Ihrem Experiment alle Werte nach Ihren Wünschen.**
    
    *   `run_days`: Ausführungszeit in Tagen.
    *   `run_hours`: Laufzeit in Stunden. **Hinweis**: Wenn der Wert größer als 1 Tag ist, können Sie sowohl run\_days als auch run\_hours oder nur run\_hours verwenden. Beispiel: Wenn die Gesamtlänge 36 Stunden beträgt, können Sie run\_days = 1 und run\_hours = 12 oder run\_days = 0 und run\_hours = 36 festlegen.
    *   `run_minutes`: Laufzeit in Minuten.
    *   `run_seconds`: Ausführungszeit in Sekunden.
    *   `start_year`: Ein 4-stelliges Jahr der Startzeit.
    *   `start_month`: Ein 2-stelliger Monat mit der Startzeit.
    *   `start_day`: Ein 2-stelliger Tag der Startzeit.
    *   `start_hour`: Eine 2-stellige Startzeit.
    *   `end_year`: Ein 4-stelliges Jahr der Endzeit.
    *   `end_month`: Ein 2-stelliger Monat mit der Endzeit.
    *   `end_day`: Ein 2-stelliger Tag der Endzeit.
    *   `end_hour`: Eine 2-stellige Stunde mit der Endzeit.
    *   `interval_seconds`: Das Zeitintervall zwischen den eingehenden realen Daten, das das Intervall zwischen der lateralen Randbedingungsdatei sein wird.
    *   `e_we`: Der Endindex in x (west\_east)-Richtung (gestaffelte Dimension).
    *   `e_sn`: Der Endindex in y-(Südnord-)Richtung (gestaffelte Dimension).
    *   `e_vert`: Endindex in z-Richtung (vertikal) (gestaffelte Dimension - dies bezieht sich auf vollständige Ebenen).
    *   `dx`: Die Rasterlänge in x-Richtung (in Metern).
    *   `dy`: Rasterlänge in y-Richtung (in Metern).
    *   `frames_per_outfile`: Wie oft Sie die Ausgabedatei aufteilen.
    *   `num_metgrid_levels`: Die Anzahl der vertikalen Ebenen in der WPS-Ausgabe.
        *   Geben Sie ncdump -h für eine der met\_em\*-Dateien ein, um diese Zahl zu ermitteln.
    *   `w_damping`: Hochgeschwindigkeitsdämpfungskennzeichen (zur betrieblichen Verwendung). Auf 1 setzen.
    
        <copy>
        &time_control
        run_days                            = 0,
        run_hours                           = 6,
        run_minutes                         = 0,
        run_seconds                         = 0,
        start_year                          = 2021, 2000, 2000,
        start_month                         = 01,   01,   01,
        start_day                           = 20,   24,   24,
        start_hour                          = 00,   12,   12,
        end_year                            = 2021, 2000, 2000,
        end_month                           = 01,   01,   01,
        end_day                             = 20,   25,   25,
        end_hour                            = 06,   12,   12,
        interval_seconds                    = 10800
        input_from_file                     = .true.,.true.,.true.,
        history_interval                    = 60,  60,   60,
        frames_per_outfile                  = 1, 1000, 1000,                                        
        restart                             = .false.,
        restart_interval                    = 7200,
        io_form_history                     = 2
        io_form_restart                     = 2
        io_form_input                       = 2
        io_form_boundary                    = 2
        /
        
        &domains
        time_step                           = 60,
        time_step_fract_num                 = 0,
        time_step_fract_den                 = 1,
        max_dom                             = 1,
        e_we                                = 100,    112,   94,
        e_sn                                = 100,    97,    91,
        e_vert                              = 40,    33,    33,
        p_top_requested                     = 5000,
        num_metgrid_levels                  = 34,
        num_metgrid_soil_levels             = 4,
        dx                                  = 10000, 10000,  3333.33,
        dy                                  = 10000, 10000,  3333.33,
        grid_id                             = 1,     2,     3,
        parent_id                           = 0,     1,     2,
        i_parent_start                      = 1,     31,    30,
        j_parent_start                      = 1,     17,    30,
        parent_grid_ratio                   = 1,     3,     3,
        parent_time_step_ratio              = 1,     3,     3,
        feedback                            = 1,
        smooth_option                       = 0
        /
        
        &physics
        physics_suite                       = 'CONUS'
        mp_physics                          = -1,    -1,    -1,
        cu_physics                          = -1,    -1,     0,
        ra_lw_physics                       = -1,    -1,    -1,
        ra_sw_physics                       = -1,    -1,    -1,
        bl_pbl_physics                      = -1,    -1,    -1,
        sf_sfclay_physics                   = -1,    -1,    -1,
        sf_surface_physics                  = -1,    -1,    -1,
        radt                                = 30,    30,    30,
        bldt                                = 0,     0,     0,
        cudt                                = 5,     5,     5,
        icloud                              = 1,
        num_land_cat                        = 21,
        sf_urban_physics                    = 0,     0,     0,
        /
        
        &dynamics
        hybrid_opt                          = 2,
        w_damping                           = 1,
        diff_opt                            = 1,      1,      1,
        km_opt                              = 4,      4,      4,
        diff_6th_opt                        = 0,      0,      0,
        diff_6th_factor                     = 0.12,   0.12,   0.12,
        base_temp                           = 290.
        damp_opt                            = 3,
        zdamp                               = 5000.,  5000.,  5000.,
        dampcoef                            = 0.2,    0.2,    0.2
        khdif                               = 0,      0,      0,
        kvdif                               = 0,      0,      0,
        non_hydrostatic                     = .true., .true., .true.,
        moist_adv_opt                       = 1,      1,      1,
        scalar_adv_opt                      = 1,      1,      1,
        gwd_opt                             = 0,
        /
        
        &bdy_control
        spec_bdy_width                      = 5,
        specified                           = .true.
        /
        
        &namelist_quilt
        nio_tasks_per_group = 0,
        nio_groups = 1,
        /
        </copy>
        
    
    **Um den Vorgang zu beenden, drücken Sie einfach Esc und dann Shift: gefolgt von wq enter**
    
3.  Jetzt können Sie endlich real.exe und wrf.exe ausführen. Die Ausführung dieses Programms dauert eine Weile. Um dies zu beschleunigen, verwenden wir MPI, um die Programme auf mehreren Cores und Knoten unseres HPC-Clusters auszuführen. Denken Sie daran, dass dieses Handbuch mit einer VM.Standard2.4-Ausprägungen für den Bastion-Server geschrieben wurde. Daher wird real.exe auf 2 Cores ausgeführt.
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  Mit ncview können Sie unsere Eingabedatei ansehen.
    
        <copy>
        sudo ncview wrfinput_d01
        </copy>
        
    
    Hier sehen Sie die Temperatur auf zwei Metern für den Zielbereich. ![Screenshot von ncview](images/nc3.png)
    
5.  Wir haben unsere Beiträge, können sie jetzt verwenden, um eine Vorhersage zu generieren.
    
        <copy>
        mpirun -np 72 -hosts 172.16.1.2,172.16.1.3 ./wrf.exe #This will run on 72 cores on two nodes with ipaddesses of 172.16.1.2 and 172.16.1.3
        tail -F rsl.out.0000 #can be used to check for errors and progress. 
        </copy>
        
6.  Wir können unsere Vorhersage überprüfen mit:
    
        <copy>
        sudo ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![Screenshot von ncview](images/nc4.png)  
    Aus unserer Vorhersage kann man sehen, dass einige Änderungen aufgetreten sind, wenn wir in derselben Gegend sechs Stunden später betrachten. Es gibt viele Variablen, die Sie erkunden können, also haben Sie Spaß.
    
    Hiermit haben Sie gelernt, wie Sie WRF einrichten und konfigurieren, um Wettervorhersagen mit echten Daten auf einem HPC-Cluster auszuführen. Es gibt viele Variablen und Konfigurationen, die untersucht werden können. Viel Spaß also.
    

## Bestätigungen

*   **Autor** - Brian Bennett, Solution Engineer, Big Compute
*   **Zuletzt aktualisiert von/Datum** - Brian Bennett, Big Compute, Januar 2021