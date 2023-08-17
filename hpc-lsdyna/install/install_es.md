# Instalación de LS-DYNA

## Introducción

En este laboratorio, instalará LS-DYNA.

Tiempo estimado de laboratorio: 25 minutos

## Tarea 1: Descarga de binarios

Puede descargar los binarios LS-DYNA desde el [sitio web de LSTC](http://www.lstc.com/download/ls-dyna) o transferirlos a su máquina mediante scp.

Tome la versión creada para mpi y compilada para RedHat Ent Srv 5.4. Según nuestras conclusiones, IntelMPI funciona más rápido que la plataforma MPI en OCI. (ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1).tar.gz)

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

Otra posibilidad es cargar el instalador en Object Storage.

1.  En el menú principal de la consola, seleccione Object Storage.
2.  Seleccione la región correcta en la parte superior derecha
3.  Seleccione el compartimento correcto en el lado izquierdo
4.  Cree un cubo si no lo ha creado
5.  En el cubo, seleccione el objeto de carga y especifique la ruta del instalador.
6.  Seleccione los 3 puntos del lado derecho del objeto de instalador y seleccione Crear solicitud autenticada previamente
7.  Si pierde la URL, no puede recuperarla, pero puede volver a generar una nueva solicitud autenticada previamente

Descargue el almacenamiento de objetos de formulario del instalador con

    <copy>
        wget PAR_URL
    </copy>
    

Descomprima o descomprima el instalador según su versión

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## Tarea 2: Instalación de LS-DYNA

1.  Destar los binarios en una ubicación compartida. Por defecto, un cluster de HPC tiene un recurso compartido NFS o un recurso compartido Gluster montado en todos los nodos de cálculo.
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## Tarea 3: Instalación de bibliotecas MPI

**Intel MPI 2018**

Ejecute esos comandos en cada nodo.

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**MPI de plataforma**

Instale esas bibliotecas:

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

Descargue el archivo tar del sitio web de IBM y ejecute:

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

A continuación, siga las instrucciones de la pantalla. Si instala la plataforma en una unidad de recurso compartido, todos los nodos de cálculo podrán acceder a ella.

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020