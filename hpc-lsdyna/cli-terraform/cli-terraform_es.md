# Uso de comandos de la CLI de OCI y Terraform para trabajar con HPC

## Introducción

`ocihpc` es una herramienta para simplificar los despliegues de aplicaciones de HPC en Oracle Cloud Infrastructure (OCI). La interfaz de línea de comandos (CLI) de HPC le permite utilizar un solo comando para desplegar clusters de HPC de cualquier tamaño con nuestras instancias informáticas de HPC dedicadas y redes RDMA de Mellanox. No necesita conocer Terraform u Oracle Cloud Infrastructure Resource Manager para utilizar la CLI de HPC. Se basa en la CLI principal de Oracle Cloud Infrastructure e incluye scripts de Terraform incorporados.

Entre las ventajas de utilizar HPC CLI se incluyen:

1.  Inicie rápidamente un cluster de recursos informáticos completo con RDMA sobre Ethernet convergente (RoCE) v2 rápidamente mediante un único comando.
2.  No se necesita ningún conocimiento de Terraform u Oracle Cloud Infrastructure Resource Manager para iniciar una red de cluster básica.
3.  Admite la implementación de interfaz de paso de mensajes (MPI) incluye un conjunto completo de paquetes de software para ejecutar el procesamiento paralelo con RDMA, incluido Mellanox OFED con MPI abierto, MPI de Intel y MPI de plataforma.
4.  Personalizable Puede ejecutar sus propios scripts de terraform o agregarlos a los scripts existentes proporcionados con la herramienta para instalar sus propias aplicaciones.

Tiempo estimado de laboratorio: 60 minutos

### Objetivos

Como desarrollador o ingeniero de datos,

1.  Despliegue de la red de cluster de HPC con la interfaz de línea de comandos de Oracle
2.  Ver, ejecutar y gestionar la pila a través de Cli

### Requisitos previos

1.  Una cuenta de Oracle Cloud Infrastructure con credenciales de usuario suficientes para ejecutar una pila de Terraform.
2.  Clave SSH generada

### Políticas

La cuenta de usuario de OCI que utilice en `ocihpc` debe tener las políticas necesarias configuradas para OCI Resource Manager. Consulte [este enlace](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm) para obtener información sobre las políticas necesarias.

## Tarea 1: Instalación de ocihpc en macOS/Linux

1.  Descargue la última versión con el siguiente comando y extráigalo:
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  descomprima el archivo que ha descargado en el paso 1.
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  Convierta el ejecutable binario ocihpc.
    
        $ chmod +x ./ocihpc 
        
4.  Mueva el binario ocihpc a su RUTA.
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  Compruebe que funciona.
    
        $ ocihpc version 
        

## Tarea 2: Instalación de ocihpc en Windows

1.  Descargue la última versión de [este enlace](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip) y extráigalo.
    
2.  Agregue el binario ocihpc a su RUTA.
    
3.  Compruebe que funciona.
    
        $ ocihpc.exe version 
        

## Tarea 3: Creación de un par de claves ssh en la línea de comandos

Consulte [este enlace](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm) si está utilizando Windows.

1.  Abra un shell o un terminal para introducir los comandos.
    
2.  Navegue a la carpeta .oci.
    
        $ cd Users/enjli/.ssh
        
3.  Si aún no ha creado la carpeta, cree un directorio .ssh para almacenar las credenciales:
    
        $ mkdir Users/enjli/.ssh
        
4.  Si no ve ningún par de claves id\_rsa en la carpeta, introduzca el siguiente comando y proporcione un nombre y una frase de contraseña cuando se le solicite. Las claves se crearán con los valores predeterminados: claves RSA de 2048 bits.
    
        $ ssh-keygen
        

## Tarea 4: Generación de una clave de firma de API

Las solicitudes de API se firmarán con su clave privada y Oracle utilizará la clave pública para verificar la autenticación de la solicitud. Consulte [este enlace](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other) si está utilizando Windows.

1.  En caso contrario, cree un directorio .oci para almacenar las credenciales:
    
        $ mkdir Users/enjli/.oci
        
2.  Genere la clave privada con los siguientes comandos.
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  Compruebe que solo usted pueda leer el archivo de clave privada:
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  Genere la clave pública:
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## Tarea 5: adición de clave pública a Oracle Cloud Infrastructure

Ahora que tiene un combinado de clave privada/pública, debe agregarlo a la consola de OCI con la configuración de usuario:

1.  Navegue a la carpeta .oci que contiene `oci_api_key_public.pem` y copie la clave pública.
    
2.  Conéctese a la consola de OCI y haga clic en Menú y seleccione Identidad y usuarios. Seleccione un usuario y vaya a la página Detalles de usuario.
    
3.  Haga clic en Agregar clave pública en la sección Claves de API. ![](./images/ResourcesMenu.png) ![](./images/APIKeys.png)
    
4.  Pegue la clave pública que copió de la CLI en Add Public Key ![](./images/AddPublicKey.png)
    

## Tarea 6: Configurar

En este paso se describe la configuración necesaria para la CLI e incluye configuraciones opcionales que permiten ampliar la funcionalidad de CLI.

1.  Antes de utilizar la CLI, debe crear un archivo de configuración en la carpeta .oci que contenga las credenciales necesarias para trabajar con su cuenta de Oracle Cloud Infrastructure. Puede crear este archivo mediante un cuadro de diálogo de configuración o manualmente mediante un editor de texto.
    
2.  Ejecute _ocihpc configure_ para comprobar si tiene una configuración válida para acceder a OCI. La herramienta le guiará a través de la creación de una configuración.
    

Se le notificará dónde está escrito su archivo de configuración:

_Archivo de configuración guardado en: /Users/enjli/.oci/config_

Para crear el archivo de configuración, necesitará: 1. Su OCID de usuario (se encuentra en la sección de perfil en la parte superior derecha de la pantalla en > configuración de usuario > separador de información de usuario), 2. OCID de arrendamiento (Administración > Detalles de arrendamiento > separador Información de arrendamiento), 3. La región de la que está trabajando (por ejemplo, us-phoenix-1, us-ashburn-1, etc.), 4. Huella del usuario (que se encuentra en la ficha de información del usuario), 5. Ruta a su clave de firma privada de API.

Un archivo `config` de ejemplo tendría este aspecto: ![](./images/oci_config.png)

## Tarea 7: lista

Puede obtener la lista de pilas disponibles ejecutando `ocihpc list`.

Ejemplo:

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## Tarea 8: Inicializar

1.  Cree una carpeta que utilizará como origen del despliegue.

IMPORTANTE: Utilice una carpeta diferente por pila. No inicialice más de una pila en la misma carpeta. De lo contrario, la herramienta sobrescribirá la anterior.

2.  Cambie a esa carpeta y ejecute `ocihpc init <stack name>`. `ocihpc` descargará los archivos necesarios en esa carpeta.
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**IMPORTANTE**: edite el contenido del archivo /Users/enjli/ocihpc-test/config.json antes de ejecutar el comando de despliegue de ocihpc.

## Tarea 9: Despliegue

1.  Antes de realizar el despliegue, debe cambiar los valores en el archivo `config.json`. Las variables dependen de la pila que despliegue. Un ejemplo de `config.json` para Cluster Network sería el siguiente:
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  Para modificar el archivo `config.json`, navegue hasta el directorio que acaba de crear (ocihpc-test en este caso) y abra el archivo "config.json" con texteditor o bloc de notas. ![](./images/config_json.png)
    

Tenga en cuenta que este no es el mismo archivo de configuración que hemos configurado en el paso 1.

Para este archivo de configuración, necesitaremos:

1.  La información del dominio de disponibilidad que contiene los recursos de HPC de nuestro arrendamiento (Administración > Detalles de arrendamiento > Desplácese hasta la sección Límites de servicio > Recursos informáticos > y desplácese hacia abajo para encontrar "BM.HPC2.36"). En la siguiente captura de pantalla, podemos ver que tenemos un total de 6 máquinas BM.HPC2.36 que usar en AD-2, 0 de las cuales están actualmente en uso. ![](./images/hpc_resource.png)
    
2.  Bastion AD puede ser cualquier dominio de disponibilidad que haya elegido siempre que haya recursos (unidad VM.standard2.1) La unidad Bastion ya debe estar rellena - VM.Standard2.1
    
3.  Recuento de nodos - para los fines de este laboratorio, vamos a ir con 2 para utilizar todos los recursos de HPC
    
4.  Nuestra clave pública ssh
    

**Notas**

1.  Después de cambiar los valores en `config.json`, puede desplegar la pila con `ocihpc deploy <arguments>`. Este comando creará una pila en Oracle Cloud Resource Manager y desplegará la pila con ella.
    
2.  Para las pilas soportadas, puede definir el número de nodos que desea desplegar agregándolo al comando `ocihpc deploy`. Si la pila no la soporta o si no proporciona un valor, la herramienta se desplegará con los números por defecto.
    
3.  Por ejemplo, el siguiente comando desplegará una red de cluster con 5 nodos:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  La herramienta generará un nombre de despliegue que consta de `<stack name>-<current directory>-<random-number>`.
    
    Ejemplo:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## Tarea 10: Conectar

Una vez completado el despliegue, verá la IP de bastión/código de nodo de cabecera a la que puede conectarse:

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

También puede obtener los detalles de conexión ejecutando el comando `ocihpc get ip`.

## Tarea 11: Gestionar

Además, puede utilizar comandos cli para gestionar y mantener fácilmente el seguimiento de los recursos:

1.  Para generar una lista de todas las pilas desplegadas en un compartimento específico: `oci resource-manager job list -c [OCID OF COMPARTMENT]`
    
2.  Para generar una lista de trabajos en una pila o compartimento, ordenados por tiempo creado: `oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  Para revisar un trabajo junto con los detalles del trabajo: `oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  Para ver las entradas de log del trabajo especificado en formato JSON: `oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`
    
5.  Para mover una pila y sus trabajos asociados a otro compartimento: `oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## Tarea 12: Suprimir

Cuando haya terminado con el despliegue, puede suprimirlo cambiando a la carpeta de pila y ejecutando `ocihpc delete --stack <stack name>`.

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

¡Todo listo! Ha desplegado correctamente su primera instancia informática de alto rendimiento con la herramienta CLI de OCI.

_Ahora puede continuar con la siguiente práctica_

Esta es información detallada sobre la gestión de instancias informáticas de alto rendimiento. Para obtener una referencia completa de los comandos, consulte la documentación de OCI [aquí](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Fecha/fecha de última actualización**: Harrison Dvoor, octubre de 2020

# <<<<<<<<<< CABECERA

> > > > > > > ascendente/maestro