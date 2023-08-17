# Cambio de datos de experimento con WRF

## Introducción

Esta es una guía más pequeña en comparación con la configuración, la instalación y la guía de ejecución de WRF. Está destinado a utilizarse después de haber completado la configuración, la instalación y la guía de ejecución de WRF, o bien utilizar el enlace de descarga para comenzar a utilizar una imagen personalizada con WRF 4.1.5 y WPS 4.1 ya configurados.

Tiempo estimado de laboratorio: 45 minutos

### Objetivos

En este laboratorio, aprenderá sobre:

*   Creando dominio de WRF con cuadrícula
*   Descarga de datos de GFS y ejecución de Ungrib y Metgrid
*   Ejecución de WRF con datos reales

### Requisitos previos

En esta práctica se asume que tiene:

*   Una cuenta gratuita de Oracle o de pago en la nube
*   Acceso a una instancia de CPU de Ubuntu 18.04 como se trata en los ejercicios prácticos 1 y 2
    *   Nota para este laboratorio utilizaremos una **unidad VM.Standard2.16**. Puede usar una forma más pequeña si lo desea.
*   Se ha realizado la Guía de configuración, instalación y ejecución de WRF o se ha descargado la imagen personalizada proporcionada allí o [aquí](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2).
    *   **Nota**: La contraseña de Ubuntu es ppkkego y la contraseña de vnc es ppkkego  
        Se pueden cambiar con los siguientes comandos en el terminal remoto:
        
            <copy>
            sudo passwd ubuntu
            sudo vncpasswd
            </copy> 
            

## Tarea 1: Conexión al escritorio de Gnome

Utilizaremos [TigerVNC Viewer](https://tigervnc.org/) para conectarse a nuestra instancia.

1.  Abrir un terminal local
    
    Ejecute el siguiente comando con información relacionada con su instancia: ssh -L 5901:localhost:5901 ubuntu@IPADDRESS ![Captura de pantalla de ssh](images/ssh.png)
    
2.  Inicio de sesión de vncserver  
    vncserver  
    ![Captura de pantalla de ssh](images/vncstart.png)
    
3.  Abra el visor TigerVNC
    
    Servidor VNC: localhost:1  
    A continuación, haga clic en connect  
    ![Captura de pantalla del visor TigerVNC](images/tigervnc.png)
    
4.  Introduzca su contraseña de VNC  
    A continuación, haga clic en Aceptar  
    ![Captura de pantalla de autenticación VNC](images/vncauth.png)
    
5.  Finalmente, se le presentará un escritorio gnome y debe seguir trabajando desde aquí.  
    ![Captura de pantalla de Gnome Desktop](images/gnomedesktop.png)
    

## Tarea 2: Creación de área geográfica/dominio

1.  Debemos ajustar el archivo namelist.wps a cero en la ubicación que desee. Será una pequeña rejilla (10.000 x 10.000 metros) con Houston Texas USA como punto central.
    
        <copy>
        cd ~/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **Cambie todos los valores a continuación a su gusto según su experimento**.
    
    *   `max_dom` Entero que especifica el número total de dominios, incluido el dominio principal, en la simulación. El valor por defecto es 1.
    *   `e_we`: números enteros que especifican la dimensión oeste este completa de la cuadrícula. No hay ningún valor por defecto.
    *   `e_sn`: números enteros que especifican la dimensión completa sur-norte de la cuadrícula. No hay ningún valor por defecto.
    *   `dx`: valor real que especifica la distancia de cuadrícula (en metros) en la dirección x, donde el factor de escala de mapa es 1.
    *   `dy`: valor real que especifica la distancia de cuadrícula (en metros) en la dirección y, donde el factor de escala de mapa es 1.
    *   `ref_lat`: valor real que especifica la parte de latitud de una ubicación (latitud, longitud) cuya ubicación (i,j) se conoce en el dominio de simulación.
    *   `ref_lon`: valor real que especifica la parte de longitud de una ubicación (latitud, longitud) cuya ubicación (i, j) se conoce en el dominio de simulación.
    *   `truelat1`: valor real que especifica la primera latitud verdadera para la proyección conformal Lambert, o la única latitud verdadera para el Mercator y las proyecciones estereográficas polares.
    *   `truelat2`: valor real que especifica la segunda latitud verdadera para la proyección cónica conforma Lambert.
    *   `stand_lon`: valor real que especifica la longitud que es paralela al eje y en las proyecciones estereográficas polares y conformales de Lambert. Para la proyección regular de latitud-longitud, este valor da la rotación alrededor de los polos geográficos de la tierra.
    *   `geog_data_path`: cadena de caracteres que proporciona la ruta, ya sea relativa o absoluta, al directorio donde se pueden encontrar los directorios de datos geográficos.
    
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
        
    
    **Para salir, simplemente pulse Esc y luego cambie: seguido de wq enter**  
    **Todos estos cambios son para un área geográfica con Houston TX como epicentro.**
    
2.  Ahora que hemos puesto en la información de nuestra área geográfica de interés (Esta guía utiliza Houston TX USA como punto central) permite utilizar ncview para verificar que tenemos la ubicación correcta después de ejecutar el programa de geogrid.
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
3.  Use la var 2d para comprobar la máscara de tierra para verificar la ubicación. Si está satisfecho con la imagen, terminamos de crear el dominio.
    
    ![Captura de pantalla de ncview](images/nc21.png)
    

## Tarea 3: Adición de datos meteorológicos al área geográfica/dominio

Después de configurar nuestro área geográfica o dominio. Ahora necesitamos obtener datos meteorológicos para superponer al dominio.

1.  En un explorador web, vaya a https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
    
2.  Haga clic en el enlace con la fecha más reciente (por ejemplo, gfs.20201120/, como hoy es el 20 de noviembre de 2020).
    
3.  Seleccione 00  
    `Don’t actually download anything.` Crearemos un script para ello. Vamos a reducir la resolución para facilitar el manejo del tamaño de datos de esta práctica, por lo que 0p50 en lugar de 0p25. Utilice 0p25 si tiene almacenamiento adicional y desea datos de mayor resolución o más fiables. Es necesario descargar el número de archivos durante el tiempo que desea ejecutar. Cada archivo tiene una hora de datos en un intervalo determinado. EX si desea ejecutar durante seis horas, necesita gfs.t00z.pgrb2.0p50.f000, gfs.t00z.pgrb2.0p50.f003 y gfs.t00z.pgrb2.0p50.f006. 0p25 está en pasos de una hora y 0p50 en pasos de 3 horas. Para el tutorial utilizaremos solo 6 horas de datos. Siéntase libre de usar más a medida que se sienta más cómodo usando WRF más adelante.
    
4.  Permite navegar al directorio correcto y editar el script creado previamente para descargar datos.
    
        <copy>
        cd ~/WRF
        cd scripts
        vi download_gfs.sh
        </copy>
        
    
    El script será el siguiente:  
    Solo tiene que editar `year`, `month` y `day` para los datos que desea descargar.
    
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
        
    
    **Para salir, simplemente pulse Esc y luego Mayús: seguido de wq enter**  
    **Este script descargará seis horas de datos para la fecha 12/09/20 en la resolución 0p50. Ajuste para ajustarse a sus necesidades.**
    
5.  Los siguientes comandos hacen que la secuencia de comandos sea ejecutable y la ejecutan para descargar los datos:
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
6.  Ahora que hemos descargado nuestros datos, veamos el proceso de superponerlos sobre nuestro dominio. Utilizaremos ungrib y metgrid para lograr esto. Lo desglosaremos en dos secciones: una sección de configuración y una de ejecución.
    
    **Configuración:**
    
        <copy>
        cd ~/WRF/WPS-4.1
        rm ./Vtable
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh ~/WRF/GFS/
        vi namelist.wps 
        </copy> 
        
    
    **Cambie todos los valores que aparecen a continuación según los archivos que haya descargado**.
    
    *   `start_date` con el formato: Año-Mes-Día\_hora:minuto:segundo
    *   `end_date` con el formato: Año-Mes-Día\_hora:minuto:segundo
    *   `interval seconds`: número entero de segundos entre archivos de entrada meteorológicos variables en el tiempo. No hay ningún valor por defecto.
    
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
        
    
    **Para salir, simplemente pulse Esc y luego cambie: seguido de wq enter**
    
    **Ejecución:**
    
        <copy>
        ./ungrib.exe
        rm ./METGRID.TBL 
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
7.  Podemos ver los resultados utilizando ncview y mirando Skintemp para ver el valor de los datos descargados.
    
        <copy>
        ncview met_em.d01.2020-12-09_00\:00\:00.nc 
        </copy>
        
    
    ![Captura de pantalla de ncview](images/nc22.png)
    

## Tarea 4: Ejecución de Real y WRF

1.  Hemos reducido los datos para representar una ubicación geográfica y le hemos agregado datos meteorológicos; finalmente es hora de ejecutar WRF utilizando datos reales.
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
        rm met_em*
        ln -s ../../WPS-4.1/met_em* .  
        vi namelist.input
        </copy>
        
2.  En el archivo namelist.input necesitamos editar los contenidos para reflejar nuestro área geográfica/dominio y nuestra fecha e intervalo.  
    **Cambie todos los valores a su gusto según su experimento**.
    
    *   `run_days`: tiempo de ejecución en días.
    *   `run_hours`: tiempo de ejecución en horas. **Nota**: si tiene más de 1 día, puede utilizar run\_days y run\_hours o solo run\_hours. Por ejemplo, si la longitud total de ejecución es de 36 horas, puede definir run\_days = 1, y run\_hours = 12, o run\_days = 0 y run\_hours = 36.
    *   `run_minutes`: tiempo de ejecución en minutos.
    *   `run_seconds`: tiempo de ejecución en segundos.
    *   `start_year`: año de inicio de 4 dígitos.
    *   `start_month`: mes de inicio de 2 dígitos.
    *   `start_day`: día de inicio de 2 dígitos.
    *   `start_hour`: hora de inicio de 2 dígitos.
    *   `end_year`: año de finalización de 4 dígitos.
    *   `end_month`: mes de 2 dígitos de la hora de finalización.
    *   `end_day`: día de finalización de 2 dígitos.
    *   `end_hour`: hora de finalización de 2 dígitos.
    *   `interval_seconds`: intervalo de tiempo entre los datos reales entrantes, que será el intervalo entre el archivo de condición de límite lateral.
    *   `e_we`: índice final en dirección x (west\_east) (dimensión escalonada).
    *   `e_sn`: índice final en la dirección y (sur-norte) (dimensión escalonada).
    *   `e_vert`: índice final en dirección z (vertical) (dimensión escalonada: se refiere a niveles completos).
    *   `dx`: longitud de la cuadrícula en dirección x (en metros).
    *   `dy`: longitud de cuadrícula en dirección y (en metros).
    
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
        
    
    **Para salir, simplemente pulse Esc y luego cambie: seguido de wq enter**
    
3.  Ahora, finalmente podemos ejecutar real.exe y wrf.exe. Este programa tarda un tiempo en ejecutarse. Para ayudar a acelerar esto, usaremos MPI para ejecutar los programas en múltiples núcleos de nuestra forma. Recuerde que esta guía se ha escrito con una unidad VM.Standard2.16, por lo que ejecutaremos real.exe en 2 núcleos.
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  Podemos ver los resultados utilizando ncview y consultando T2 para ver el valor de los datos descargados.
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    Aquí puede ver la temperatura a dos metros para el área objetivo. ![Captura de pantalla de ncview](images/nc23.png)
    
5.  Tenemos nuestra opinión, ahora podemos usarla para generar una predicción. Ejecutaremos real.exe con 10 núcleos.
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress.
        </copy>
        
6.  Podemos ver los resultados de nuestra predicción con ncview con lo siguiente:
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![Captura de pantalla de ncview](images/nc24.png)  
    Desde nuestra predicción se puede ver que algunos cambios han ocurrido cuando observamos la misma área seis horas más tarde. Hay muchas variables que explorar, así que divertirse.
    

## Acuses de recibo

*   **Autor**: Brian Bennett, ingeniero de soluciones, recursos informáticos de gran tamaño
*   **Fecha/fecha de última actualización**: Brian Bennett, Big Compute, diciembre de 2020