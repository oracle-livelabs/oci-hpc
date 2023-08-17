# Configurar visualización

## Introducción

Las cargas de trabajo de HPC a menudo requieren herramientas de visualización para programar, supervisar o analizar la salida de las simulaciones. En estos escenarios, a menudo se desea crear un nodo de visualización de GPU para una resolución óptima y un procesamiento posterior. Una GUI no se instala por defecto en instancias de OCI; sin embargo, se puede configurar fácilmente mediante el protocolo de visualización remota VNC o X11. Las siguientes subsecciones mostrarán cómo crear un nodo de visualización de GPU en la subred pública mediante TurboVNC y OpenGL.

Tiempo estimado de laboratorio: 25 minutos

## Tarea 1: Configuración de una VNC en el bastión

Por defecto, el único acceso a la máquina de Oracle Linux es mediante SSH en modo de consola. Si desea ver la interfaz gráfica, deberá configurar una conexión VNC. El siguiente script funcionará para el usuario opc por defecto. La contraseña de la sesión de VNC se define como "HPC\_oci1", pero se puede editar en el siguiente juego de comandos. Si no está conectado actualmente al nodo de cabecera mediante SSH, hágalo ya que estos comandos deben ejecutarse en el nodo de cabecera.

        sudo yum -y groupinstall "Server with GUI"
        sudo yum -y install tigervnc-server mesa-libGL
        sudo systemctl set-default graphical.target
        sudo cp /usr/lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
        sudo sed -i 's/<USER>/opc/g' /etc/systemd/system/vncserver@:1.service
        sudo sed -ie '/^ExecStart=/a PIDFile=/home/opc/.vnc/%H%i.pid' /etc/systemd/system/vncserver@:1.service
        sudo mkdir /home/opc/.vnc/
        sudo chown opc:opc /home/opc/.vnc
        echo "password" | vncpasswd -f > /home/opc/.vnc/passwd
        chown opc:opc /home/opc/.vnc/passwd
        chmod 600 /home/opc/.vnc/passwd
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
    

## Tarea 2: adición de una instancia de GPU

Los siguientes pasos se realizan con OpenGL para mejorar los casos de uso de GPU en OCI. Consulte el blog para obtener más información.

1.  En la consola, vaya a Compute y, a continuación, a Instances.
    
2.  Cree una instancia informática para el nodo de visualización:
    
    *   Seleccionar el dominio de disponibilidad deseado
    *   Seleccione la unidad de GPU deseada (máquina virtual o hardware dedicado)
    *   Especifique una imagen de Oracle Linux compatible con GPU. La última imagen de Oracle Linux se activará automáticamente con GPU.
    *   Seleccionar la VCN de red de cluster y la subred pública
    *   Copie y pegue su clave ssh pública
    *   Haga clic en Crear.
3.  Espere a que la instancia aprovisione y, a continuación, conéctese a la instancia mediante:
    
        ssh opc@<public ip> -i <private key> 
        
4.  Instale X Window System, un gestor de visualización (GNOME/GDM) y un entorno de escritorio (MATE):
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  Instale el servidor VNC y VirtualGL. Tenga en cuenta que VirtualGL es un kit de herramientas de código abierto que permite a cualquier consola de Linux o Unix ejecutar aplicaciones OpenGL con una aceleración de hardware completa.
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  Configure el servidor X para activar el uso compartido de GPU para sesiones virtuales. Ejecute los siguientes comandos:
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  Configure el servidor X para activar el uso compartido de GPU para sesiones virtuales. Ejecute los siguientes comandos:
    
        sudo vglserver_config -config -s -f -t
        
8.  Para evitar que se bloquee cuando se inicie el protector de pantalla, establezca la contraseña de usuario local en algo que pueda utilizar más adelante:
    
        sudo passwd opc
        
9.  Cambie su contraseña de VNC por algo que pueda utilizar para conectarse:
    
        vncpasswd
        
10.  Reinicie el servidor X:
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  Activar e iniciar GDM:
    
        systemctl enable gdm --now
        
12.  Inicie el servidor VNC:
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  Si desea acceder al servidor VNC directamente sin reenvío SSH, asegúrese de que la lista de seguridad permita conexiones en el puerto 5901/tcp.
    
    *   En la consola, vaya a Networking y, a continuación, a Virtual Cloud Networks.
    *   Seleccione Subredes y, a continuación, la subred pública.
    *   En la lista de seguridad por defecto, agregue una regla de entrada con los siguientes detalles:
        *   Sin estado: no
        *   Tipo de origen: CIDR
        *   CIDR de origen: 0.0.0.0/0
        *   Protocolo IP: TCP
        *   Rango de puertos de origen: Todos
        *   Rango de puertos de destino: 5901 Nota: El puerto VNC estándar es 5900 más un número de visualización (por ejemplo, 5901 para :1, 5902 para :2)
14.  Permita el acceso en la configuración del firewall local, de la siguiente manera:
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020