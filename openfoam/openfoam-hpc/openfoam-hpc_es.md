# Ejecutar proyecto OpenFoam en cluster de HPC

## Introducción

La configuración del hardware de HPC y la implementación de software de aplicación sobre él pueden llevar mucho tiempo e incluir varias operaciones complejas. Con esta demostración, todas estas tareas se automatizan en una imagen del mercado de OCI y hay disponible un entorno de HPC totalmente operativo junto con el software de simulación OpenFoam justo después del aprovisionamiento. Todo lo que se necesita es ejecutar la carga de trabajo en el entorno de HPC totalmente funcional y ejecutar la aplicación de visualización como Paraview para presentar la salida.

Tiempo estimado: 60 minutos

### Arquitectura de referencia

![](./images/29_Architecture.png " ")

### Acerca de OpenFoam

La Dinámica de Fluidos Computacional (DFC) es la simulación del movimiento de fluidos y la transferencia de calor mediante análisis numérico. El software CFD, como OpenFOAM, puede servir como una herramienta poderosa para muchas industrias de ingeniería para mejorar los diseños, visualizar el impacto y detectar ineficiencias en un sistema. Sin embargo, la potencia de cálculo necesaria para procesar una simulación puede ser muy intensa. Los superordenadores y los clusters de HPC a menudo se utilizan para ejecutar modelos grandes y complejos en un plazo razonable. Calculará y representará el modelo aerodinámico de una motocicleta con un software de código abierto CFD, OpenFOAM, dentro de un clúster de Oracle HPC.

### Terminología de la infraestructura

1.  Nodo de trabajador: instancias informáticas de HPC que proporcionan la potencia de procesamiento para ejecutar la carga de trabajo de simulaciones computacionales u otra carga de trabajo de ingeniería. En esta demostración, los nodos de la unidad de computación BM.HPC2.36 son los nodos de trabajador.
    
2.  Nodo principal: instancia informática desde la que se pueden programar y enviar todos los trabajos computacionales para que se ejecuten en nodos de trabajador. Muchas veces, el nodo de cabecera y el nodo de bastión pueden ser la misma máquina. Para esta demostración se está aprovisionando el nodo Bastion.
    
3.  Nodo de GPU: instancia informática especializada con procesadores de GPU para presentar la salida de la carga de trabajo computacional. Esta demostración no está aprovisionando una máquina de GPU.
    
4.  Nodo bastión: una instancia informática con IP pública actúa como punto de entrada para conectarse a los nodos de trabajador que normalmente están en una red privada.
    
5.  Red RoCE: RDMA sobre Ethernet convergente (RoCE) es un protocolo de red que permite el acceso directo a memoria remota (RDMA) a través de una red Ethernet.
    

### Objetivos

1.  Aprovisione un entorno de HPC totalmente funcional mediante la imagen de OCI Marketplace
    
2.  Despliegue de la aplicación de simulación basada en CFD denominada OpenFoam
    
3.  Ejecutar carga de trabajo de Openfoam
    
4.  Represente la salida mediante la aplicación de visualización de vista previa.
    

### Requisitos previos

Asegúrese de tener acceso a lo siguiente:

1.  Autorice al servicio Compute a gestionar espacios de nombres de etiquetas en su nombre mediante la siguiente política:
    
    "Permitir al servicio compute\_management utilizar tag-namespace en el arrendamiento"
    
2.  La unidad con hardware dedicado BM.HPC2.36 solo se permite en esta demostración. Se necesita un mínimo de 2 unidades de computación en el mismo dominio de disponibilidad.
    
3.  Imagen de recursos informáticos de VM para el servidor bastión. No tiene que estar en el mismo dominio de disponibilidad que los nodos de HPC.
    
4.  Las credenciales de usuario deben tener una cuota para crear o utilizar una VCN existente con una subred privada y pública.
    
5.  Acceso a la imagen de mercado "CFD Ready Cluster". La versión de la imagen de Marketplace utilizada en este DEMO es (Versión: 20200625).
    

## Tarea 1: Iniciar imagen de Marketplace

1.  Antes de que el usuario inicie la imagen de Marketplace, compruebe si hay 2 o más nodos de HPC con la unidad BM.HPC2.36 disponibles. Vaya a **Menú** -> **Gobernanza** -> **Límites**, **Cuotas y uso**.
    
    ![](./images/01-Service_Limits.png " ")
    
2.  Vaya a **Menú** -> **Marketplace** -> **Aplicaciones**.
    
    ![](./images/02-Marketplace.png " ")
    
3.  En el cuadro de búsqueda, escriba "cfd".
    
    ![](./images/03-Search_marketplace.png " ")
    
4.  Haga clic en la imagen, selecciónela y, a continuación, haga clic en el botón "**Iniciar pila**". Compruebe que está en el compartimento correcto.
    
    ![](./images/04-Launch_Stack.png " ")
    
5.  Una vez que se haya iniciado la pila, complete los detalles para crear recursos, incluidos los nodos de trabajador de HPC, el servidor bastión, los componentes de red y el servidor VCN. La pila también solicitará entradas para desplegar paquetes como openMPI y openFoam.
    
6.  Proporcione el nombre de la pila a continuación
    
    ![](./images/05-Create_Stack01.png " ")
    
7.  Seleccione el dominio de disponibilidad en el que tiene nodos de HPC disponibles para aprovisionamiento. Proporcione la clave pública ssh que se necesitará para conectarse al servidor Bastion.
    
    ![](./images/06-Create_Stack02.png " ")
    
8.  Seleccione el dominio de disponibilidad para aprovisionar el servidor de Bastion. Puede ser diferente del dominio de disponibilidad en los nodos de HPC. Seleccione la unidad de host Bastion que desee. Active la casilla de control para instalar el servidor VNC en el servidor Bastion y proporcione la contraseña necesaria para establecer la conexión VNC más adelante.
    
    ![](./images/07-Create_Stack03.png " ")
    
9.  Para los nodos de trabajador, la única opción seleccionada es BM.HPC2.36. Necesitará un mínimo de 2 unidades de computación HPC para completar el aprovisionamiento. Seleccione 2 o más nodos de cálculo en la siguiente pantalla. Puede dejar todas las opciones en el valor por defecto.
    
    ![](./images/08-Create_Stack04.png " ")
    
10.  En Opciones de red, cree una nueva VCN utilizando los valores por defecto transferidos en la pila. Esta demostración crea una nueva VCN creada a través de esta pila. Sin embargo, también existe la opción de utilizar la VCN existente.
    
    ![](./images/09-Create_Stack05.png " ")
    
11.  Para la sección Sistema de archivos, utilice las siguientes opciones que crearán el montaje NFS e instalarán el sistema de archivos gluster encima de él.
    
    ![](./images/10-Create_Stack06.png " ")
    
12.  Haga clic en la casilla de control "INSTALL OPENFOAM" para instalar la aplicación OpenFoam en esta pila de HPC.
    
    ![](./images/11-Create_Stack07.png " ")
    
13.  En la última página de la pila, verifique los detalles que se han introducido y, si todo es correcto, haga clic en el botón "Create" para iniciar el aprovisionamiento de la infraestructura y la instalación de software.
    
    ![](./images/12-Create_Stack08.png " ")
    
14.  Verificar el progreso de la instalación de la pila de HPC
    
    ![](./images/13-StackJob.png " ")
    
15.  Una vez que el aprovisionamiento se complete en unos 20 minutos, el archivo log debe contener el siguiente mensaje. Los logs se pueden encontrar en Menú -> Gestor de recursos -> Pilas (haga clic en el nombre de la pila) -> Trabajos (haga clic en el enlace del nombre del trabajo para ver el log completo)
    
    ![](./images/14-StackJobDetails.png " ")
    

## Tarea 2: Validar la configuración

1.  Haga clic en el enlace "Asociar recursos" situado a la izquierda de la pantalla para ver los recursos de infraestructura aprovisionados por esta pila. Los recursos asociados se pueden encontrar en **Menú** -> **Gestor de recursos** -> **Pilas** (haga clic en el nombre de la pila) -> **Trabajos** (haga clic en el enlace del nombre del trabajo) -> **Asociar recursos**.
    
    ![](./images/15-Stack_Resources.png " ")
    
2.  En **Recursos informáticos** -> **Instancias**, se aprovisionarán 3 instancias informáticas, 2 nodos de HPC en red privada y 1 servidor bastión con dirección IP pública.
    
    ![](./images/16-Compute_Details.png " ")
    
3.  Conéctese al servidor Bastion utilizando la dirección IP pública como usuario opc utilizando la clave privada ssh para la que se ha cargado una clave pública en la pila anterior. Utilice este método para conectarse desde Linux/Mac Desktop. Para el escritorio de Windows, utilice Putty y proporcione una clave privada ppk para realizar la conexión.
    
    ![](./images/17-Bastion_ssh.png " ")
    
4.  Verifique el almacenamiento NFS compartido que está montado en 2 nodos HPC y el servidor bastión. Este almacenamiento NFS contiene el software OpenFoam que se utilizará para ejecutar la carga de trabajo.
    
    ![](./images/18-Bastion_storage.png " ")
    
5.  La aplicación OpenFoam se aloja en la carpeta "/mnt/gluster-share/work". Compruebe el archivo de host que contiene la dirección IP priavte de los 2 nodos HPC en los que se ejecutará la carga de trabajo.
    
    ![](./images/19-Bastion_openfoam.png " ")
    
6.  La siguiente salida muestra un servidor VNC en ejecución en el puerto 5901. Este puerto se utilizará para establecer la conexión VNC con el host bastión.
    
    ![](./images/20-Bastion_VNCdetails.png " ")
    
7.  Compruebe en la lista de seguridad del servidor Bastion bajo las reglas de entrada que está permitido el puerto 5901. Si VNC se está ejecutando en cualquier otro puerto y ese puerto no está permitido para el tráfico de entrada en la lista de seguridad, cree una nueva regla de seguridad para el mismo.
    
    ![](./images/21-SecList_OpenVNC_Port.png " ")
    
8.  Verifique los nombres de los nodos de HPC.
    
    ![](./images/22-ListHPCNodes.png " ")
    

## Tarea 3: Ejecutar la carga de trabajo de simulación y representar la salida

1.  En el servidor Bastion, ejecute los siguientes comandos que serán necesarios para presentar la salida mediante el paquete Paraview.
    
        <copy>$ sudo yum install -y mesa-libGLU
        $ cd /mnt/gluster-share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" https://www.paraview.org/paraview-downloads/download.php >file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  Conéctese a uno de los nodos de HPC desde el servidor bastión y ejecute la carga de trabajo
    
        <copy>$ ssh 172.16.1.2
        $ cd /mnt/gluster-share/work/
        $ ./Allrun 36</copy>
        
    
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
        
3.  Una vez que la carga de trabajo finaliza correctamente, configure el cliente VNC en su equipo de la siguiente manera. Proporcione una IP pública del servidor Bastion y el puerto VNC
    
    ![](./images/24-VNC_Connection.png " ")
    
4.  OPCIONALMENTE, En caso de que no se le permita abrir el puerto VNC 5901 o debido a un motivo de seguridad, desee crear un túnel ssh para este puerto, utilice el siguiente comando para crear un túnel ssh en el puerto 5901 sin abrir el puerto en la lista de seguridad
    
    Cree un túnel desde su portátil o escritorio con el siguiente comando desde la ventana de terminal. Aquí, la comunicación para el puerto 5901 se realizará en el puerto ssh 22 y la dirección IP 150.136.41.3 es la dirección IP pública del servidor bastión.
    
        <copy>$ ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
5.  No cierre la ventana de terminal del túnel ssh anterior. Ahora inicie la sesión de VNC y esta vez, en lugar de la dirección IP, utilice "localhost" en el puerto 5901, aunque este puerto no esté abierto en la lista de seguridad de la subred.
    
    ![](./images/28-ssh_Tunnel.png " ")
    
6.  Iniciar la aplicación Paraview desde el servidor bastión
    
        <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        ./paraview</copy>
        
7.  En la ventana de la aplicación Paraview, File -> Open -> Path "/mnt/gluster-share/work" y seleccione el nombre de archivo motorbike.foam. Será un archivo de cero bytes y debería estar bien.
    
    ![](./images/25-Paraview_Menu.png " ")
    
8.  A la izquierda de la ventana, en el separador Propiedades, seleccione Regiones de malla para seleccionar todos los valores y, a continuación, anule la selección de los valores superiores que no empiecen por el prefijo motorBike\_. Asegúrese de que todos los valores que empiecen por motorBike\_ están seleccionados. Haga clic en el botón Apply (Aplicar), aparecerán algunos errores, ignore la ventana de error que aparece para ver la representación de la imagen en la consola VNC.
    
    ![](./images/26-Mesh_Regions.png " ")
    
9.  En la pantalla se presentará una imagen como la siguiente. En función de algunos valores de visualización, la imagen de la pantalla puede parecer un poco diferente.
    
    ![](./images/27-Image_Rendering.png " ")
    

¡Todo listo! Esta acción finaliza la demostración para ejecutar la aplicación OpenFoam en un cluster de HPC.

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Fecha/fecha de última actualización**: Harrison Dvoor, enero de 2021