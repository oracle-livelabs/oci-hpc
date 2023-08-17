# Configurar y acceder a través de VNC

## Introducción

En este laboratorio, realizaremos un recorrido por los pasos necesarios para configurar y acceder a su instancia de Ubuntu a través de un cliente VNC (aquí: TigerVNC).

Tiempo estimado del taller: 15 minutos

### Objetivos

*   Conéctese a su instancia informática mediante SSH
*   Configurar VNC en la instancia de Ubuntu
*   Acceda a su instancia informática a través de un cliente VNC (por ejemplo, TigerVNC)

### ¿Qué se necesita?

*   Laboratorio completo 1 y laboratorio 2
*   [Agregue una regla de entrada a la lista de seguridad de VCN](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using) para permitir el tráfico TCP para el puerto de puertos 5900+N donde N es el número de visualización (normalmente :0 para una visualización física). ![](images/vncseclist.PNG " ")

## Tarea 1: Conéctese a su instancia informática mediante SSH

1.  Abra un terminal (también conocido como línea de comandos o consola) de su elección. En este ejemplo, trabajamos en una máquina local de Windows y utilizamos Windows PowerShell como terminal.
    
2.  Tenga en cuenta la dirección IP pública de la instancia de Ubuntu que encontrará en **Recursos informáticos -> Instancias** en la lista de instancias del compartimento.
    
3.  En el terminal, navegue hasta la carpeta en la que reside el par de claves SSH que utilizó durante la creación de la instancia informática de Ubuntu. En nuestro caso, la carpeta se denomina _ssh_.
    

       <copy>cd .ssh</copy>
    

4.  Introduzca el siguiente comando para acceder a su instancia informática de Ubuntu en la que necesita sustituir _private-key_ por su clave privada OpenSSH e _public ip_ por la dirección IP pública de su instancia de Ubuntu.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## Tarea 2: Configurar VNC en la instancia de Ubuntu

1.  Instale el servidor VNC ejecutando los siguientes comandos en su terminal:

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  Instale un entorno de escritorio. Instalaremos el entorno de escritorio de Xfce (pero puede elegir utilizar otros entornos de escritorio) introduciendo el siguiente comando:

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  Ejecute el comando vncpasswd para definir la contraseña para su servidor VNC.

       <copy>vncpasswd</copy>
    

Cuando se le solicite, introduzca la contraseña y verifíquela introduciendo una segunda vez.

4.  Inicie el servidor VNC con el comando:

       <copy>vncserver :1</copy>
    

Recibirá lo siguiente. ![](images/vncserver.PNG " ")

5.  Termine el servidor VNC con el comando:

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  El archivo de configuración de VNC se encuentra en ~/.vnc/xstartup. Edítelo con su editor de texto favorito. Utilizaremos vim.

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  Agregue la siguiente línea al final del archivo:

       <copy>exec /usr/bin/startxfce4 &</copy>
    

En caso de que esté trabajando con vim: pulse **A** para agregar texto al final y copie el comando anterior. Cambie al modo de comando pulsando la tecla ESC. Escriba **:wq**: para guardar y salir.

8.  Inicie el servidor VNC ejecutando el siguiente comando.

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  Cierre la conexión SSH a la instancia ejecutando el siguiente comando:

       <copy>exit</copy>
    

## Tarea 3: Conexión al escritorio de VNC

Puede conectarse al escritorio remoto mediante un cliente VNC (por ejemplo, TigerVNC) y un túnel SSH.

1.  Cree un túnel SSH al servidor VNC con el siguiente comando (donde la clave privada es su clave OpenSSH privada y public\_ip la dirección IP privada de su instancia informática de Ubuntu):

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  Instale el cliente vncviewer, que será TigerVNC en nuestro caso.

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  Descargue e instale un cliente VNC (por ejemplo, TigerVNC) en la máquina local. Conéctese a **127.0.0.1:5901** mediante el cliente VNC. ![](images/tigervnc.PNG " ")
    
4.  Cuando se le solicite la contraseña, introduzca la contraseña creada durante la configuración del servidor VNC. ![](images/tigervncpwd.PNG " ")
    

Se abrirá el escritorio de Ubuntu remoto. ![](images/vncdesktop.PNG " ")

## **Acuses de recibo**

*   **Creado por/Fecha**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021
*   **Fecha/fecha de última actualización**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021