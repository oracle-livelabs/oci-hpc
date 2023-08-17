# Experimentdaten mit WRF ändern

## Einführung

Dies ist eine kleinere Anleitung im Vergleich zu WRF Setup, Installation und Run Guide. Es soll verwendet werden, nachdem Sie WRF Setup, Installation und Run Guide abgeschlossen haben oder den Downloadlink verwendet haben, um mit WRF 4.1.5 und WPS 4.1 bereits ein benutzerdefiniertes Image zu verwenden.

Geschätzte Laborzeit: 45 Minuten

### Ziele

In dieser Übung lernen Sie Folgendes kennen:

*   WRF-Domain mit Geogrid erstellen
*   GFS-Daten herunterladen und Ungrib und Metgrid ausführen
*   WRF mit echten Daten ausführen

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Free Tier- oder kostenpflichtiger Cloud-Account von Oracle
*   Zugriff auf eine Ubuntu 18.04-CPU-Instanz gemäß den Übungen 1 und 2
    *   Hinweis für diese Übung verwenden wir eine **VM.Standard2.16-Ausprägung**. Sie können eine kleinere Form verwenden, wenn Sie möchten.
*   Sie haben den WRF-Setup, die Installation und den Run Guide durchlaufen oder das dort bereitgestellte benutzerdefinierte Image [hier](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2) heruntergeladen.
    *   **Hinweis**: Das Ubuntu-Kennwort ist ppkkego und das vnc-Kennwort ist ppkkego  
        Sie können mit den folgenden Befehlen im Remote-Terminal geändert werden:
        
            <copy>
            sudo passwd ubuntu
            sudo vncpasswd
            </copy> 
            

## Aufgabe 1: Verbindung zum Gnome-Desktop herstellen

Wir verwenden [TigerVNC Viewer](https://tigervnc.org/), um eine Verbindung zur Instanz herzustellen.

1.  Lokales Terminal öffnen
    
    Führen Sie den folgenden Befehl mit Informationen zur Instanz aus: ssh -L 5901:localhost:5901 ubuntu@IPADDRESS ![Screenshot von ssh](images/ssh.png)
    
2.  VNC-Server-Session starten  
    vncserver  
    ![Screenshot von ssh](images/vncstart.png)
    
3.  TigerVNC-Viewer öffnen
    
    VNC-Server: localhost:1  
    Klicken Sie dann auf "Connect".  
    ![Screenshot des TigerVNC-Viewers](images/tigervnc.png)
    
4.  Geben Sie Ihr VNC-Kennwort ein.  
    Klicken Sie dann auf "OK".  
    ![Screenshot der VNC-Authentifizierung](images/vncauth.png)
    
5.  Abschließend wird ein Gnome-Desktop angezeigt. Hier können Sie weiterarbeiten.  
    ![Screenshot von Gnome Desktop](images/gnomedesktop.png)
    

## Aufgabe 2: Geografisches Gebiet/Domäne erstellen

1.  Sie müssen die Datei namelist.wps an einem beliebigen Speicherort auf null setzen. Es wird ein kleines Netz (10.000 x 10.000 Meter) mit Texas Houston USA als Mittelpunkt.
    
        <copy>
        cd ~/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **Ändern Sie basierend auf Ihrem Experiment alle folgenden Werte nach Ihren Wünschen.**
    
    *   `max_dom` Eine Ganzzahl, die in der Simulation die Gesamtanzahl der Domains angibt, einschließlich der übergeordneten Domain. Der Standardwert ist 1.
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
        ref_lat   =  41.87,  
        ref_lon   = -87.62,  
        truelat1  =  41.87,  
        truelat2  =  41.87,  
        stand_lon = -87.62,  
        geog_data_path = '~/WRF/GEOG/'  
        /  
        </copy>
        
    
    **Drücken Sie zum Beenden einfach die Esc-Taste, dann die Umschalttaste: gefolgt von der QK-Eingabe**  
    **Alle diese Änderungen beziehen sich auf einen geografischen Bereich mit Houston TX als Epizentrum.**
    
2.  Nachdem wir nun die Informationen für unser geografisches Interessengebiet eingegeben haben (Dieser Leitfaden verwendet Houston TX USA als Mittelpunkt), können wir mit ncview überprüfen, ob wir nach der Ausführung des geogrid-Programms den richtigen Standort haben.
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
3.  Prüfen Sie den Standort mit der 2D-Varstellung. Wenn das Image zufrieden ist, wird die Domain erstellt.
    
    ![Screenshot von ncview](images/nc21.png)
    

## Aufgabe 3: Meteorologische Daten zu geografischem Gebiet/Domäne hinzufügen

Nach der Einrichtung unseres geografischen Bereichs oder unserer Domäne. Wir müssen nun meteorologische Daten erhalten, um die Domain zu überlagern.

1.  Navigieren Sie in einem Webbrowser zu https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/.
    
2.  Klicken Sie auf den Link mit dem neuesten Datum (z.B. gfs.20201120/ wie heute der 20. November 2020 ist)
    
3.  Wählen Sie 00  
    `Don’t actually download anything.` aus. Hier wird ein Skript erstellt. Wir gehen mit der geringeren Auflösung für einen einfacher zu handhabenden Dataset für diese Übung, also 0p50 anstelle von 0p25. Verwenden Sie 0p25, wenn Sie zusätzlichen Speicher haben und Daten mit höherer Auflösung/zu zuverlässigerer Daten benötigen. Sie müssen die Anzahl der Dateien für die Dauer herunterladen, die Sie ausführen möchten. Jede Datei ist eine Stunde der Daten in einem bestimmten Intervall. EX, wenn Sie sechs Stunden lang ausführen möchten, benötigen Sie gfs.t00z.pgrb2.0p50.f000, gfs.t00z.pgrb2.0p50.f003 und gfs.t00z.pgrb2.0p50.f006. 0p25 ist in einer Stunde und 0p50 befindet sich in 3-Stunden-Schritten. Für ein Tutorial werden wir nur 6 Stunden Daten verwenden. Fühlen Sie sich frei, mehr zu verwenden, wenn Sie später mit WRF bequemer werden.
    
4.  Mit dieser Option können Sie zum richtigen Verzeichnis navigieren und das vorab erstellte Skript zum Herunterladen von Daten bearbeiten.
    
        <copy>
        cd ~/WRF
        cd scripts
        vi download_gfs.sh
        </copy>
        
    
    Das Skript lautet:  
    Sie müssen nur `year`, `month` und `day` für die Daten bearbeiten, die Sie herunterladen möchten.
    
        <copy>
        #!/bin/bash  
        
        inputdir=/home/ubuntu/WRF/GFS  
        rm -rf $inputdir  
        mkdir $inputdir  
        
        year=2020  
        month=12  
        day=09  
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
    **Dieses Skript lädt SIX-Datenstunden für das Datum 12/09/20 in der Auflösung von 0p50 herunter. Passen Sie sich an Ihre Bedürfnisse an.**
    
5.  Mit den folgenden Befehlen kann das Skript ausgeführt werden und zum Herunterladen der Daten ausgeführt werden:
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
6.  Nachdem wir unsere Daten heruntergeladen haben, können Sie durch den Prozess der Überlagerung über unsere Domain gehen. Dazu verwenden wir Ungrib und Metgrid. Sie werden in zwei Abschnitte unterteilt: eine Konfiguration und einen Ausführungsabschnitt.
    
    **Konfigurieren:**
    
        <copy>
        cd ~/WRF/WPS-4.1
        rm ./Vtable
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh ~/WRF/GFS/
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
        start_date = '2020-12-09_00:00:00',         # start time  
        end_date   = '2020-12-09_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        /
        </copy>
        
    
    **Um den Vorgang zu beenden, drücken Sie einfach Esc und dann Shift: gefolgt von wq enter**
    
    **Ausführen:**
    
        <copy>
        ./ungrib.exe
        rm ./METGRID.TBL 
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
7.  Wir können uns die Ergebnisse ansehen, indem wir ncview verwenden und skintemp ansehen, um den Wert der heruntergeladenen Daten zu sehen.
    
        <copy>
        ncview met_em.d01.2020-12-09_00\:00\:00.nc 
        </copy>
        
    
    ![Screenshot von ncview](images/nc22.png)
    

## Aufgabe 4: Real und WRF ausführen

1.  Wir haben Daten zur Darstellung eines geografischen Standortes herabgestuft und wir haben ihm meteorologische Daten hinzugefügt; es ist endlich an der Zeit, WRF mit echten Daten auszuführen.
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
        rm met_em*
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
    
        <copy>
        &time_control
        run_days                            = 0,
        run_hours                           = 6,
        run_minutes                         = 0,
        run_seconds                         = 0,
        start_year                          = 2020, 2000, 2000,
        start_month                         = 12,   01,   01,
        start_day                           = 09,   24,   24,
        start_hour                          = 00,   12,   12,
        end_year                            = 2020, 2000, 2000,
        end_month                           = 12,   01,   01,
        end_day                             = 09,   25,   25,
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
    
3.  Jetzt können Sie endlich real.exe und wrf.exe ausführen. Die Ausführung dieses Programms dauert eine Weile. Um dies zu beschleunigen, werden wir MPI verwenden, um die Programme auf mehreren Kernen unserer Form auszuführen. Denken Sie daran, dass dieses Handbuch mit einer VM.Standard2.16-Ausprägungen geschrieben wurde. Daher wird real.exe auf 2 Cores ausgeführt.
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  Wir können die Ergebnisse mit ncview anzeigen und T2 prüfen, um den Wert der heruntergeladenen Daten anzuzeigen.
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    Hier sehen Sie die Temperatur auf zwei Metern für den Zielbereich. ![Screenshot von ncview](images/nc23.png)
    
5.  Wir haben unsere Beiträge, können sie jetzt verwenden, um eine Vorhersage zu generieren. Wir führen real.exe mit 10 Cores aus.
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress.
        </copy>
        
6.  Wir können uns die Ergebnisse unserer Vorhersage mit ncview ansehen mit:
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![Screenshot von ncview](images/nc24.png)  
    Aus unserer Vorhersage kann man sehen, dass einige Änderungen aufgetreten sind, wenn wir in derselben Gegend sechs Stunden später betrachten. Es gibt viele Variablen, die Sie erkunden können, also haben Sie Spaß.
    

## Bestätigungen

*   **Autor** - Brian Bennett, Solution Engineer, Big Compute
*   **Zuletzt aktualisiert von/Datum** - Brian Bennett, Big Compute, Dezember 2020