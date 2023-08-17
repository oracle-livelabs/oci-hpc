# Configuración, instalación y ejecución de WRF en cluster de HPC

## Introducción

En este laboratorio se tratará y le guiará sobre cómo configurar y ejecutar un cluster de WRF en la infraestructura de OCI. Esta guía está destinada a mostrar cómo crear un cluster HPC de dos nodos para configurar un entorno en el que podrá ejecutar experimentos y simulaciones meteorológicas.

Tiempo estimado de laboratorio: 90 minutos

### Objetivos

En este laboratorio, aprenderá sobre:

*   Despliegue de un cluster de HPC de dos nodos
*   Descarga de dependencias y WRF/WPS
*   Compilación de bibliotecas para WRF/WPS
*   Compilando WRF y WPS
*   Creando dominio de WRF con cuadrícula
*   Descarga de datos de GFS y ejecución de Ungrib y Metgrid
*   Ejecución de WRF con datos reales

### Requisitos previos

En esta práctica se asume que tiene:

*   Una cuenta de prueba de Oracle o de pago en la nube
*   Tenga en cuenta que en esta práctica utilizaremos dos nodos **BM.HPC2.36** para nuestro cluster y una VM.Standard2.4 para nuestro bastión.

## Tarea 1: Configuración de la infraestructura de HPC

1.  Utilice el menú de navegación de la izquierda y vaya a **Soluciones y plataforma** > Marketplace > Aplicaciones.
2.  En la barra de búsqueda, escriba HPC y, a continuación, pulse la tecla Intro/Intro.
3.  En la sección Todas las aplicaciones, seleccione Cluster de HPC y, a continuación, inicie la pila. Debería ver la imagen a la izquierda seguida de la imagen a la derecha.  
    ![Captura de pantalla de la imagen HPC](images/hpc2.png)
4.  En el menú de creación de pila, proporcione un nombre y una descripción según corresponda y, a continuación, haga clic en Siguiente.
5.  Seleccione un dominio de disponibilidad, pegue la clave SSH y deje la casilla CONFIGURE NFS SHARE marcada.
6.  Para Bastion, seleccione el mismo dominio de disponibilidad que el cluster y elija la unidad **VM.Standard.2.4** y, a continuación, haga clic en NEXT.
7.  En la pantalla de revisión, vuelva a comprobar toda la información que ha introducido y, a continuación, haga clic en Crear.

## Tarea 2: Configuración de Gnome Desktop

1.  Hay una serie de tecnologías de escritorio diferentes que puede utilizar, pero esta guía pasará por la configuración y la congestión del escritorio de Gnome, ya que es la opción por defecto para Oracle Linux. Los siguientes pasos descargarán, instalarán y configurarán Gnome Desktop para nuestra instancia de Oracle Linux 7 Bastion.
    
        <copy>
        sudo yum groupinstall "server with gui"
        Y
        sudo yum install tigervnc-server
        Y
        vncserver # DO NOT MAKE READ ONLY and password should be no greater than 8 characters
        </copy>
        
2.  Abra un terminal local  
    Ejecute el siguiente comando con información relativa a su instancia: ssh -L 5901:localhost:5901 opc@IPADDRESS ![Captura de pantalla de SSH](images/ssh.png)
    
3.  Abra TigerVNC Viewer Type `localhost:1` en la sección VNC Server:  
    A continuación, haga clic en connect  
    ![Captura de pantalla del visor TigerVNC](images/tigervnc.png)
    
4.  Introduzca su contraseña de VNC  
    A continuación, haga clic en Aceptar  
    ![Captura de pantalla de autenticación VNC](images/vncauth.png)
    
5.  Finalmente, se le presentará un escritorio gnome y debe seguir trabajando desde aquí.  
    ![Captura de pantalla de Gnome Desktop](images/gnomedesktop.png)
    

## Tarea 3: Configuración de Oracle Linux

1.  Ahora que podemos acceder al entorno de escritorio de nuestra instancia, podemos empezar a configurarlo para que se ejecute WRF. Para empezar, podemos instalar las dependencias que necesitaremos. Haga clic en Aplicaciones en la parte superior izquierda, haga clic en Herramientas del sistema y, a continuación, en Terminal. Realice lo siguiente en el terminal.
    
        <copy>
        sudo yum update -y
        sudo yum upgrade -y
        sudo yum group install "Development Tools" -y
        sudo yum install ncview -y
        sudo yum install mc -y
        </copy>
        

## Tarea 4: Descarga y compilación de bibliotecas para WRF

Ahora que hemos instalado la mayoría de las dependencias que necesitaremos para WRF, vamos a empezar a compilar las bibliotecas que WRF necesita para funcionar.

### Creación de la estructura de carpetas y descarga de bibliotecas

1.  Introduzca los siguientes comandos en el terminal remoto para comenzar a configurar la estructura de carpetas necesaria.
    
        <copy>  
        cd /mnt/nfs-share
        mkdir WRF
        cd WRF
        mkdir downloads
        cd downloads
        </copy>
        
2.  En el directorio de descargas, puede descargar todas las bibliotecas que necesitamos con los siguientes comandos.
    
        <copy>
        curl -O ftp://ftp.unidata.ucar.edu/pub/netcdf/old/netcdf-4.1.2.tar.gz  
        curl -O https://www.zlib.net/zlib-1.2.11.tar.gz  
        curl -O ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng16/libpng-1.6.37.tar.gz  
        curl -O https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz  
        curl -O https://www.mpich.org/static/downloads/3.3.2/mpich-3.3.2.tar.gz
        </copy>
        
3.  En lugar de descomprimir los archivos uno a uno, permite utilizar un **bucle** para hacerlo por nosotros y, a continuación, volver al directorio WRF.
    
        <copy>
        for i in *.gz ; do tar -xzf $i ; done
        cd /mnt/nfs-share/WRF  
        </copy>
        
4.  Ahora que volvemos al directorio WRF, permite configurar la estructura de carpetas para nuestras bibliotecas para WRF. También crearemos una variable ambiental para hacer referencia a la ruta de la biblioteca que se utilizará más adelante en la guía.
    
        <copy>
        mkdir libs  
        cd libs  
        mkdir netcdf  
        mkdir mpich  
        mkdir grib2 # (this will be used for the jasper, libpng, and zlib libraries)  
        export LIBDIR=/mnt/nfs-share/WRF/libs
        </copy>
        

### Compilando biblioteca grib2

La biblioteca grib2 es en realidad una compilación de tres bibliotecas separadas, específicamente, zlib, jasper y libpng.

5.  Compilando Zlib
    
        <copy> 
        cd /mnt/nfs-share/WRF/downloads
        cd zlib-1.2.11
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        
6.  Compilando libpng
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd libpng-1.6.37/
        ./configure --prefix=$LIBDIR/grib2 LDFLAGS="-L$LIBDIR/grib2/lib" CPPFLAGS="-I$LIBDIR/grib2/include"
        make
        make install
        </copy>
        
7.  Compilando jasper
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd jasper-1.900.1/
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        

### Compilación de bibliotecas Netcdf y Mpich

8.  Compilando biblioteca netcdf
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd netcdf-4.1.2/
        ./configure --prefix=$LIBDIR/netcdf --disable-dap --disable-netcdf-4
        make
        make install
        </copy>
        
9.  Compilando biblioteca mpich
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd mpich-3.3.2/
        ./configure --prefix=$LIBDIR/mpich
        make
        make install
        </copy>
        

## Tarea 5: Compilación de WRF y WPS

1.  Ahora que hemos configurado la estructura de carpetas para las bibliotecas, podemos comenzar a descargar y compilar WRF y WPS. El siguiente bloque de código descargará y colocará los programas en las ubicaciones adecuadas.
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        wget https://github.com/wrf-model/WRF/archive/v4.1.5.tar.gz
        wget https://github.com/wrf-model/WPS/archive/v4.1.tar.gz
        for i in *.gz ; do tar -xzf $i ; done
        mv WRF-4.1.5/ /mnt/nfs-share/WRF/
        mv WPS-4.1/ /mnt/nfs-share/WRF/
        </copy>
        
2.  Antes de poder compilar WRF necesitamos configurar algunas variables de entorno para que el programa pueda encontrar y utilizar las bibliotecas que hemos compilado para funcionar.
    
        <copy>
        cd .. or /mnt/nfs-share/WRF
        cd WRF-4.1.5/
        export NETCDF=$LIBDIR/netcdf
        export PATH=$LIBDIR/mpich/bin:$PATH
        export JASPERLIB=$LIBDIR/grib2/lib
        export JASPERINC=$LIBDIR/grib2/include
        </copy>
        
3.  Ahora que hemos hecho referencia a todas las bibliotecas en variables de entorno, finalmente podemos compilar WRF. Para este laboratorio vamos a compilar WRF para poder utilizar datos meteorológicos reales que se han recogido. Lo desglosaremos en dos secciones: una sección de configuración y una de compilación.
    
    **Configurar:**  
    Elegimos la opción 34 para continuar con nuestra elección de usar gfortran/gcc y la opción 1 porque no cubriremos el anidamiento en esta guía.
    
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
        
    
    **Compilar:**  
    aquí compilamos WRF y probamos que se ejecutará.
    
        <copy>
        ./compile em_real
        cd main
        ./wrf.exe
        ./real.exe
        </copy>
        
4.  Ahora que WRF se ha compilado, necesitamos compilar WPS. Los WRF se deben compilar primero. Aquí seleccionamos la opción 3 porque estamos utilizando un sistema operativo Linux (Oracle Linux) en la infraestructura x86 (OCI BM.HPC2.36) junto con el compilador gfortran.
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        export WRF_DIR=/mnt/nfs-share/WRF/WRF-4.1.5/
        ./configure
        3
        ./compile
        </copy>
        
5.  Ahora que WPS está compilado, necesitamos descargar los datos que usaremos para simular la geografía del mundo. Crearemos una nueva carpeta para alojar esta información. Se tarda un tiempo en descomprimir, ya que estamos utilizando los archivos de alta resolución recomendados, por lo que estamos utilizando el cuadro de diálogo PV para generar una barra de progreso para nosotros.
    
        <copy>
        cd /mnt/nfs-share/WRF/
        mkdir GEOG
        cd GEOG
        wget https://www2.mmm.ucar.edu/wrf/src/wps_files/geog_high_res_mandatory.tar.gz  
        tar xzf geog_high_res_mandatory.tar.gz
        </copy>
        
6.  Ahora que hemos descargado y extraído los datos de geografía que necesitamos mover algunos archivos a la ubicación correcta y eliminar algunos archivos adicionales que no necesitamos. Usaremos el comandante de medianoche para esto, pero solo puede utilizar los comandos de terminal si lo desea. El siguiente comando posterior a mc utilizará la interfaz del comandante de medianoche, de modo que **`DO NOT`** `copy the following entire code block into terminal`.
    
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
        
7.  Debemos ajustar el archivo namelist.wps a cero en la ubicación que desee. Seré una pequeña rejilla (10.000 x 10.000 metros) con Woburn Massachusetts USA como punto central.
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **Cambie todos los valores a continuación a su gusto según su experimento**.
    
    *   `max_dom`: entero que especifica el número total de dominios, incluido el dominio principal, en la simulación. El valor por defecto es 1.
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
            ref_lat   =  42.48,  
            ref_lon   = -71.15,  
            truelat1  =  42.48,  
            truelat2  =  42.48,  
            stand_lon = -71.15,  
            geog_data_path = '/mnt/nfs-share/WRF/GEOG/'  
            / 
        </copy>     
        
    
    **Para salir, simplemente presione Esc y luego cambie: seguido de wq enter**  
    **Todos estos cambios son para un área geográfica con Woburn MA como epicentro.**
    
8.  Ahora que hemos puesto en la información de nuestra área geográfica de interés (Esta guía utiliza Woburn MA USA como el punto central) permite usar ncview para verificar que tenemos la ubicación correcta después de ejecutar el programa de geogrid.
    
        <copy>
        ./geogrid.exe
        sudo ncview geo_em.d01.nc
        </copy>
        
    
    Use la var 2d para comprobar la máscara de tierra para verificar la ubicación. Si está satisfecho con la imagen, terminamos de crear el dominio.
    
    ![Captura de pantalla de ncview](images/nc1.png)
    
9.  Después de configurar nuestro área geográfica o dominio. Ahora necesitamos obtener datos meteorológicos para superponer al dominio.
    
    1.  En un explorador web, vaya a https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
    2.  Haga clic en el enlace con la fecha más reciente (por ejemplo, gfs.20210120/, como hoy es el 20 de enero de 2021).
    3.  Elija 00  
        No descargue nada. Crearemos un script para ello. Vamos a reducir la resolución para facilitar el manejo del tamaño de datos de esta práctica, por lo que 0p50 en lugar de 0p25. Utilice 0p25 si tiene almacenamiento adicional y desea datos de mayor resolución o más fiables. Es necesario descargar el número de archivos durante el tiempo que desea ejecutar. Cada archivo tiene una hora de datos en un intervalo determinado. EX si desea ejecutar durante seis horas, necesita gfs.t00z.pgrb2.0p50.f000, gfs.t00z.pgrb2.0p50.f003 y gfs.t00z.pgrb2.0p50.f006. 0p25 está en pasos de una hora y 0p50 en pasos de 3 horas. Para el tutorial utilizaremos solo 6 horas de datos. Siéntase libre de usar más a medida que se sienta más cómodo usando WRF más adelante.
10.  Permite navegar al directorio correcto y crear nuestro script para descargar datos.
    
        <copy>
        cd /mnt/nfs-share/WRF
        mkdir scripts
        mkdir GFS
        cd scripts
        vi download_gfs.sh
        </copy>
        
11.  El script será el siguiente:
    
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
        
    
    **Para salir, simplemente pulse Esc y luego Mayús: seguido de wq enter**  
    **Este script descargará seis horas de datos para la fecha 20/01/21 en la resolución 0p50. Ajuste para ajustarse a sus necesidades.**
    
12.  Los siguientes comandos hacen que la secuencia de comandos sea ejecutable y la ejecutan para descargar los datos:
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
13.  Ahora que hemos descargado nuestros datos, veamos el proceso de superponerlos sobre nuestro dominio. Utilizaremos ungrib y metgrid para lograr esto. Lo desglosaremos en dos secciones: una sección de configuración y una de ejecución.
    
    **Configuración:**
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh /mnt/nfs-share/WRF/GFS/
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
        start_date = '2021-01-20_00:00:00',         # start time  
        end_date   = '2021-01-20_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        / 
        </copy>
        
    
    **Para salir, simplemente pulse Esc y luego cambie: seguido de wq enter**
    
    **Ejecución:**
    
        <copy>
        ./ungrib.exe
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
14.  Podemos ver los resultados utilizando ncview y observando el modo cuttemp para ver el valor de los datos que descargamos.
    
        <copy>
        sudo ncview met_em.d01.2021-01-20_00:00:00.nc 
        </copy>
        
    
    ![Captura de pantalla de ncview](images/nc2.png)
    

## Tarea 6: Ejecución de Real y WRF

1.  Hemos reducido los datos para representar una ubicación geográfica y le hemos agregado datos meteorológicos; finalmente es hora de ejecutar WRF utilizando datos reales.
    
        <copy>
        cd /mnt/nfs-share/WRF/WRF-4.1.5
        cd run
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
    *   `frames_per_outfile`: frecuencia con la que se divide el archivo de salida.
    *   `num_metgrid_levels`: número de niveles verticales en la salida de WPS.
        *   Escriba ncdump -h en uno de los archivos met\_em\* para averiguar este número.
    *   `w_damping`: indicador de amortiguación de velocidad vertical (para uso operativo). Establecido en 1.
    
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
        
    
    **Para salir, simplemente pulse Esc y luego cambie: seguido de wq enter**
    
3.  Ahora, finalmente podemos ejecutar real.exe y wrf.exe. Este programa tarda un tiempo en ejecutarse. Para acelerar esto, utilizaremos MPI para ejecutar los programas en varios núcleos y nodos de nuestro cluster de HPC. Recuerde que esta guía se ha escrito con una unidad VM.Standard2.4 para el servidor bastión, por lo que ejecutaremos real.exe en 2 núcleos.
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  Vamos a utilizar ncview para echar un vistazo a nuestro archivo de entrada.
    
        <copy>
        sudo ncview wrfinput_d01
        </copy>
        
    
    Aquí puede ver la temperatura a dos metros para el área objetivo. ![Captura de pantalla de ncview](images/nc3.png)
    
5.  Tenemos nuestra opinión, ahora podemos usarla para generar una predicción.
    
        <copy>
        mpirun -np 72 -hosts 172.16.1.2,172.16.1.3 ./wrf.exe #This will run on 72 cores on two nodes with ipaddesses of 172.16.1.2 and 172.16.1.3
        tail -F rsl.out.0000 #can be used to check for errors and progress. 
        </copy>
        
6.  Podemos comprobar nuestra predicción con lo siguiente:
    
        <copy>
        sudo ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![Captura de pantalla de ncview](images/nc4.png)  
    Desde nuestra predicción se puede ver que algunos cambios han ocurrido cuando observamos la misma área seis horas más tarde. Hay muchas variables que explorar, así que divertirse.
    
    Con esto, ha aprendido a configurar WRF para ejecutar predicciones meteorológicas con datos reales en un cluster de HPC. Hay muchas variables y configuraciones que explorar, por lo que divertirse.
    

## Acuses de recibo

*   **Autor**: Brian Bennett, ingeniero de soluciones, recursos informáticos de gran tamaño
*   **Fecha/fecha de última actualización**: Brian Bennett, Big Compute, enero de 2021