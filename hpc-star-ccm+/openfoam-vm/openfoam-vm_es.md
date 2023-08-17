# Ejecute el proyecto OpenFoam en la máquina virtual estándar en OCI

## Introducción

Este laboratorio está diseñado para facilitar la evaluación del software de CFD OpenFOAM en Oracle Cloud Infrastructure. Descarga y configura automáticamente OpenFOAM. OpenFOAM es el software libre de código abierto CFD lanzado y desarrollado principalmente por OpenCFD Ltd desde 2004. Cuenta con una gran base de usuarios en la mayoría de las áreas de ingeniería y ciencia, tanto de organizaciones comerciales como académicas. OpenFOAM tiene una amplia gama de características para resolver cualquier cosa, desde flujos de fluidos complejos que implican reacciones químicas, turbulencias y transferencia de calor, hasta acústica, mecánica sólida y electromagnética.

En este laboratorio se explica cómo instalar OpenFoam en una unidad VM.Standard 2.1 básica, pero para un despliegue real, sería adecuada una unidad más grande, como BM.HPC2.36. Algunas unidades de alto rendimiento solo están disponibles en regiones y dominios de disponibilidad específicos.

Crearemos dos nodos para esta práctica, el nodo principal del cluster en la subred pública y un nodo de cálculo de trabajador en la subred privada. Para acceder a los nodos de trabajador, primero crearemos el nodo principal y, a continuación, generaremos una clave SSH en el nodo principal, y usaremos esa clave pública al crear el nodo de trabajador.

Para **incidencias de log**, haga clic [aquí](https://github.com/oracle/learning-library/issues/new) para ir al formulario de envío de problemas del repositorio de oracle de github.

Tiempo estimado de laboratorio: 60 minutos

### Objetivos

Como desarrollador, ingeniero de datos,

1.  Despliegue manualmente las instancias informáticas de alto rendimiento
2.  Creación de una red de cluster
3.  Creación de un sistema de archivos de red
4.  Configurar VNC
5.  Instale OpenFoam y Paraview

### Requisitos previos

1.  Cuenta de Oracle Cloud Infrastructure con privilegios para crear una unidad de computación estándar de máquina virtual de instancia 2.1 o BM.HPC2.36.

### Terminología de la infraestructura

*   Nodo de trabajador: instancias informáticas de HPC que proporcionan la potencia de procesamiento para ejecutar la carga de trabajo de simulaciones computacionales u otra carga de trabajo de ingeniería. En esta demostración, los nodos de la unidad de computación BM.HPC2.36 son los nodos de trabajador.
*   Nodo principal: instancia informática desde la que se pueden programar y enviar todos los trabajos computacionales para que se ejecuten en nodos de trabajador. Muchas veces, el nodo de cabecera y el nodo de bastión pueden ser la misma máquina. Para esta demostración se está aprovisionando el nodo Bastion.
*   Nodo bastión: una instancia informática con IP pública actúa como punto de entrada para conectarse a los nodos de trabajador que normalmente están en una red privada.
*   Red RoCE: RDMA sobre Ethernet convergente (RoCE) es un protocolo de red que permite el acceso directo a memoria remota (RDMA) a través de una red Ethernet.

## Tarea 1: Aprovisionamiento de la red virtual en la nube de Oracle

1.  Antes de crear una instancia, debemos configurar una red virtual en la nube. Seleccione el menú de la parte superior izquierda y, a continuación, seleccione Redes y Redes virtuales en la nube ![](./images/vcn.png) ![](./images/create_vcn.png) ![](./images/vcn_content.png)
    
2.  En la página siguiente, seleccione lo siguiente para la VCN:
    
    *   Nombre
    *   Compartimento
    *   Bloque de CIDR (ejemplo: 10.0.0.0/16)
3.  Desplácese a la parte inferior y haga clic en Create VCN (Crear VCN).
    
4.  Crear subredes
    
    1.  subred pública
        *   Nombre: hpc\_public
        *   Tipo de subred: regional
        *   Bloque de CIDR: 10.0.0.0/24
        *   Tabla de rutas: "Default Route table"
        *   Acceso a subred: subred pública
        *   Lista de seguridad: "Default Security List"
    2.  subred privada
        *   Nombre: hpc\_private
        *   Tipo de subred: regional
        *   Bloque de CIDR: 10.0.3.0/24
        *   Tabla de rutas: seleccione la tabla de rutas creada en el paso anterior
        *   Acceso a subred: subred privada
        *   Lista de seguridad: seleccione la lista de seguridad creada en el paso anterior
5.  Haga clic en **crear subred** ![](./images/create_subnet.png) ![](./images/create_subnet_content.png)
    
6.  Crear gateway de Internet
    
    1.  Haga clic en `hpc_vcn` que ha creado y en el menú **Recursos** de la parte izquierda de la página, seleccione **Gateway de Internet** y cree el gateway de Internet. ![](./images/create_IG.png) ![](./images/create_IG_content.png)

Nota: Esto creará el gateway de Internet y deberá estar asociado a una tabla de rutas. En este caso, dado que la tabla de rutas por defecto se utilizará para la subred pública, el gateway de Internet se debe asociar a esa tabla de rutas.

7.  Agregue reglas de ruta a la tabla de rutas. En el menú **Recursos** de la parte izquierda de la página, seleccione **Tabla de rutas por defecto para `hpc_vcn`** y haga clic en **Agregar reglas de ruta**.
    *   Tipo de destino: gateway de internet
    *   Bloque de CIDR de destino: 0.0.0.0/0
    *   Gateway de Internet de destino en el compartimento: gateway de Internet que ha creado ![](./images/route_table.png) ![](./images/route_rules.png)

## Tarea 2: Creación de nodos de cluster

Crearemos dos nodos para este ejercicio práctico: el **código de cabecera** para el cluster en la subred pública y **un nodo de cálculo de trabajador** en la subred privada. Para acceder a los nodos de trabajador, primero crearemos el nodo principal y, a continuación, generaremos una clave SSH en el nodo principal, y usaremos esa clave pública al crear el nodo de trabajador.

Nota: En este laboratorio solo utilizaremos la unidad VM.Standard2.1 básica, pero para un despliegue real sería adecuada una unidad más grande como BM.HPC2.36. Algunas unidades de alto rendimiento solo están disponibles en regiones y dominios de disponibilidad específicos.

1.  #### Crear nodo de cabecera
    
    *   Nombre: hpc\_head
    *   Imagen o sistema operativo: última versión de Oracle Linux (por defecto)
    *   Dominio de disponibilidad: dominio disponible para las unidades VM.Standard 2.1
    *   Unidad de instancia:
        *   VM.Standard 2.1
        *   cualquier otra forma (BM.HPC2.36 preferida si está disponible)
    *   Red virtual en la nube: VCN creada antes
    *   Subred: subred pública creada antes
    *   Clave SSH: asocie su archivo de clave pública
    
    1.  En el menú superior izquierdo, seleccione Compute y cree la instancia. ![](./images/compute.png) ![](./images/compute_bm.png) ![](./images/compute_vm.png)
        
    2.  Establezca una conexión SSH en el nodo de cabecera y genere una clave ssh específica para que el cluster se comunique.
        
            ```
            $ ssh -i <ssh_key> opc@<headnode_ip_address>
            
            $ ssh-keygen
            ```
            Note: Do not change the ssh key file location (/home/opc/.ssh/id_rsa) and hit enter when asked about a passphrase (twice).
            
            1. Run and Copy the whole string, which will be used in creating the worker node
            
            ```
            $ cat ~/.ssh/id_rsa.pub
            ```
            
2.  **Crear nodo de trabajador**
    
    1.  En el menú superior izquierdo, seleccione Compute y cree una instancia con los siguientes detalles:
        
        *   Nombre: hpc\_worker1
        *   Imagen o sistema operativo: última versión de Oracle Linux (por defecto)
        *   Dominio de disponibilidad: dominio disponible para las unidades VM.Standard 2.1
        *   Unidad de instancia:
            *   VM.Standard 2.1
            *   cualquier otra forma (BM.HPC2.36 preferida si está disponible)
        *   Red virtual en la nube: VCN creada antes
        *   Subred: subred privada creada antes
        *   Clave SSH: cadena de clave pública copiada de un paso anterior.
    2.  Establezca SSH en el nodo de trabajador.  
        Vuelva a la consola que ha iniciado sesión en el nodo principal y tome la dirección IP privada y ssh en el nodo de trabajador del nodo principal
        
            ```
            $ ssh opc@10.x.x.x
            ```
            

## Tarea 3: Configurar gateway de NAT

\*\* Tenga en cuenta que esto es solo para el nodo de trabajador \*\*  

1.  Seleccione el nodo de trabajador y haga clic en **VNIC asociadas** en el menú **recursos** de la izquierda
2.  Seleccione **Editar VNIC**
3.  Desactive **Omitir comprobación de origen/destino** si está activada y haga clic en **Actualizar VNIC**. ![](./images/nat_gateway.png)

## Tarea 4: Montaje de una unidad

Nota: Solo si la unidad de nodo tiene un NVMe conectado (BM.HPC2.36 tiene uno, no VM.Standard2.1), las máquinas HPC tienen almacenamiento NVMe local pero no está montado por defecto. Vaya al paso 5 si utiliza VM.Standard2.1

1.  SSH en el nodo de cabecera y ejecute los siguientes comandos

    ```
     $ lsblk
    ```
    The drive should be listed with the NAME on the left (Probably nvme0n1, if it is different, change it in the next commands)
    

El nodo principal tendrá la unidad compartida con la instalación y el modelo. Se compartirá entre los distintos nodos de trabajador. Cada nodo de trabajador también montará la unidad para que se ejecute localmente en una unidad NVMe. En este ejemplo, el directorio de recursos compartidos será de 500 GB, pero no dude en cambiarlo.

2.  Partición de la unidad en el nodo de trabajo (opcional)

    ```
     $ sudo yum -y install gdisk
    ```
    ```
     $ sudo gdisk /dev/nvme0n1
     $ > n    # Create new partition
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
    ```
    

3.  Formatear unidad en los nodos de trabajador

    ```
    $ sudo mkfs -t ext4 /dev/nvme0n1
    ```
    

4.  Formatee las particiones en el nodo de cabecera `sudo mkfs -t ext4 /dev/nvme0n1p1 sudo mkfs -t ext4 /dev/nvme0n1p2`
    
5.  Crear un directorio y montar la unidad
    
6.  Headnode(local y share): seleccione /mnt/share como directorio de montaje para la partición 500G y /mnt/local para la más grande.
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1p1 /mnt/share
         sudo chmod 777 /mnt/share
         sudo mount /dev/nvme0n1p2 /mnt/local
         sudo chmod 777 /mnt/local
        
        
7.  Nodo de cabecera (compartir):
    
         sudo mkdir /mnt/share
         sudo mount /dev/nvme0n1 /mnt/share
         sudo chmod 777 /mnt/share
        
8.  Nodos de trabajador: seleccione /mnt/local como directorio de montaje de toda la unidad.
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1 /mnt/local
         sudo chmod 777 /mnt/local
        

## Tarea 5: Creación de un sistema de archivos de red

1.  Crear destino de montaje. En el menú superior izquierdo, haga clic en **Almacenamiento de archivos** y **Destino de montaje**.

    - New Mount Target Name: Enter a name (example: openfoam_fs)
    - Virtual Cloud Network: Select the VCN created above
    - Subnet: Select the public VCN
    

2.  El nodo principal está en una subred pública. Mantendremos el firewall activo y agregaremos una excepción a través de:  
    

    ```
    sudo firewall-cmd --permanent --zone=public --add-service=nfs
    sudo firewall-cmd --reload
    ```
    

3.  Haga clic en el destino de montaje que se ha creado y, a continuación, haga clic en la ruta de exportación. A continuación, haga clic en los comandos de montaje y copie estos comandos que se deben parecer a los siguientes: `sudo yum install nfs-utils sudo mkdir -p /mnt/share sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share`
    
4.  En los nodos de trabajador, puesto que se encuentran en una subred privada con una lista de seguridad que restringe el acceso, podemos desactivar el firewall por completo. A continuación, podemos instalar las herramientas de nfs-utils y montar los nfs del mismo modo que lo hicimos anteriormente.
    

    ```
    sudo systemctl stop firewalld
    sudo yum -y install nfs-utils
    sudo mkdir  -p /mnt/share
    sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share
    ```
    

## Tarea 6: Instalar OpenFOAM

1.  **Conexión de todos los nodos de trabajador**  
    

    Each worker node needs to be able to talk to all the worker nodes. SSH communication works but most applications have issues if all the hosts are not in the known host file. To disable the known host check for nodes with address in the VCN, you can deactivate with the following commands. You may need to modify it slightly if your have different addresses in your subnets. Put the following code block in a shell script and run the script. 
    
        ```
        for i in 0 1 2 3
        do
            echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
            echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done
        ```
    

2.  **Creación de una lista de máquinas**  
    

    OpenFOAM on the headnode does not automatically know which compute nodes are available. You can create a machinefile at `/mnt/share/machinelist.txt with` the private IP address of all the nodes along with the number of CPUs available. The headnode should also be included. The format for the entries is `private-ip-address cpu=number-of-cores`
    
        ```
        10.0.0.2 cpu=1
        10.0.3.2 cpu=1
        ```
    

3.  **Nodo de cabecera**  
    

    Install from sources, modify the path to the tarballs in the next commands. This example has the foundation OpenFOAM sources. OpenFOAM from ESI has also been tested. To share the installation between the different compute nodes, install on the network file system.
    
    
        ```
        sudo yum groupinstall -y 'Development Tools'
        sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        cd /mnt/share
        wget -O - http://dl.openfoam.org/source/7 | tar xvz
        wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        mv OpenFOAM-7-version-7 OpenFOAM-7
        mv ThirdParty-7-version-7 ThirdParty-7
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        cd /mnt/share/OpenFOAM-7
        ./Allwmake -j
    
        ```
    

4.  **Nodo de trabajador**  
    
        sudo yum -y install openmpi openmpi-devel
        cd /mnt/share
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        
        

## Tarea 7: Ejecutar la carga de trabajo de simulación y representar la salida

1.  En Headnode, ejecute los siguientes comandos que serán necesarios para presentar la salida mediante el paquete Paraview.
    
         ```
         $ sudo yum install -y mesa-libGLU
         $ cd /mnt/share
         $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php > file.tar.gz
         $ tar -xf file.tar.gz
         ```
        
2.  En headnonde, ejecute estos comandos para configurar el servidor VNC:  
    
        $ sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver
        
3.  Descargue este zip con los [scripts](../scripts/motorbike_RDMA.tgz) de /mnt/share/work en uno de los nodos de trabajador. Descomprima el archivo con `tar -xf motorbike_RDMA.tgz`
    
4.  Antes de ejecutar la carga de trabajo, debemos editar el archivo allrun para que coincida con la arquitectura que hemos creado. En primer lugar, moveremos la carpeta de la carpeta del instalador OpenFOAM
    
         ```
         $ model_drive=/mnt/share
         $ sudo mkdir $model_drive/work
         $ sudo chmod 777 $model_drive/work
         $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
         $ cd /mnt/share/work/motorBike/system
         ```
        
5.  Edite el sistema de archivos/decomposeParDict y cambie esta línea numberOfSubdomains 6; a numberOfSubdomains 12; o cuántos procesos necesitará. A continuación, en el bloque hierarchicalCoeffs, cambie n de n (3 2 1); a n (4 3 1); si multiplica esos 3 valores, debe obtener numberOfSubdomains
    

Para ejecutar con una configuración de 1 nodo de trabajador VM.Standard2.1:

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
    

6.  A continuación, edite el archivo de Allrun en /mnt/share/work/motorBike para que tenga un aspecto similar al siguiente: \`\`\` #!/bin/sh cd ${0%/\*} || exit 1 # Run from this directory NP=$1 install\_drive=/mnt/share # Source tutorial run Functions. $WM\_PROJECT\_DIR/bin/tools/RunFunctions
    
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
         touch motorbike.foam
         ```
        
7.  Asegúrese de que está en el nodo de trabajador y ejecute la carga de trabajo: `$ ssh worker_node_IP $ cd /mnt/share/work/ $ ./Allrun 2`
    
         ```
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
         219.95
        
         ```
        
8.  Una vez que la carga de trabajo finaliza correctamente, configure el cliente VNC en su equipo de la siguiente manera. Proporcione una IP pública del servidor Bastion y el puerto VNC ![](./images/24-VNC_Connection.png)
    
9.  OPCIONALMENTE, En caso de que no se le permita abrir el puerto VNC 5901 o debido a un motivo de seguridad, desee crear un túnel ssh para este puerto, utilice el siguiente comando para crear un túnel ssh en el puerto 5901 sin abrir el puerto en la lista de seguridad
    
10.  Cree un túnel desde su portátil o escritorio con el siguiente comando desde la ventana de terminal. Aquí, la comunicación para el puerto 5901 se realizará en el puerto ssh 22 y la dirección IP 150.136.41.3 es la dirección IP pública del servidor bastión.
    
        ```
        ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3
        ```
        
11.  No cierre la ventana de terminal del túnel ssh anterior. Ahora inicie la sesión de VNC y esta vez, en lugar de la dirección IP, utilice "localhost" en el puerto 5901, aunque este puerto no esté abierto en la lista de seguridad de la subred. ![](./images/28-ssh_Tunnel.png)
    
12.  Iniciar la aplicación Paraview desde el servidor bastión
    
        ```
        $ cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview
        ```
        
13.  En la ventana de la aplicación Paraview, File -> Open -> Path "/mnt/gluster-share/work" y seleccione el nombre de archivo motorbike.foam. Será un archivo de cero bytes y debería estar bien. ![](./images/25-Paraview_Menu.png)
    
14.  A la izquierda de la ventana, en el separador Propiedades, seleccione Regiones de malla para seleccionar todos los valores y, a continuación, anule la selección de los valores superiores que no empiecen por el prefijo motorBike\_. Asegúrese de que todos los valores que empiecen por motorBike\_ están seleccionados. Haga clic en el botón Apply (Aplicar), aparecerán algunos errores, ignore la ventana de error que aparece para ver la representación de la imagen en la consola VNC. ![](./images/26-Mesh_Regions.png)
    
15.  En la pantalla se presentará una imagen como la siguiente. En función de algunos valores de visualización, la imagen de la pantalla puede parecer un poco diferente. ![](./images/27-Image_Rendering.png)
    

¡Todo listo! De esta forma, se completa la demostración para ejecutar la aplicación OpenFoam en una máquina virtual estándar en OCI.

_¡Felicidades! Ha finalizado correctamente el taller_

Estos son información detallada sobre la gestión de instancias informáticas de alto rendimiento. Para obtener una referencia completa de los comandos, consulte la documentación de OCI [aquí](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Fecha/fecha de última actualización**: Harrison Dvoor, octubre de 2020

# <<<<<<<<<< CABECERA

> > > > > > > ascendente/maestro