# Aprovisione una instancia de GPU

## Introducción

En este laboratorio, realizaremos los pasos necesarios para aprovisionar una instancia de GPU en OCI con Ubuntu ejecutándose en ella.

**Nota:** Se necesita una cuenta de PAYG para aprovisionar instancias de GPU en OCI. Si está utilizando la prueba gratuita de 30 días de Oracle, puede hacer uso de todas las instancias de VM y BM disponibles.

Tiempo estimado del taller: 15 minutos

### Objetivos

*   Conexión a Oracle Cloud
*   Aprovisionar una instancia de GPU en OCI

### ¿Qué se necesita?

*   Un usuario (con un nombre de usuario y una contraseña) que forma parte de un grupo de usuarios en OCI. Consulte la página [Gestión de usuarios](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm) para leer cómo **crear un usuario** y cómo **agregar un usuario a un grupo** mediante la consola.
*   Nombre de la cuenta en la nube: nombre de su arrendamiento (proporcionado por el administrador o en el correo electrónico de bienvenida de Oracle Cloud)
*   Un grupo de usuarios. Compruebe la página [Gestión de grupos](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managinggroups.htm#three) para leer cómo **Crear un grupo** mediante la consola.
*   Políticas asignadas al grupo que permiten a los usuarios iniciar instancias informáticas. Consulte la página [Políticas comunes](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm#top) para leer las políticas que necesita **Para permitir a los usuarios iniciar instancias informáticas** mediante la consola.
*   Un compartimento para el usuario. Compruebe la página [Gestión de compartimentos](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingcompartments.htm#uscons) para leer cómo **crear un compartimento** mediante la consola.
*   Compruebe los límites del servicio para el número máximo permitido de instancias de GPU por dominio de disponibilidad en la región en la que desea aprovisionar la instancia de GPU. Para aumentar el límite de servicio, siga los pasos de la página [Límites de servicio](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/servicelimits.htm#top).

## Tarea 1: Conexión a Oracle Cloud

Si se ha desconectado de Oracle Cloud, siga estos pasos para volver a conectarse.

1.  Vaya a [cloud.oracle.com](https://cloud.oracle.com), introduzca su nombre de cuenta en la nube y haga clic en **Siguiente**. Este es el nombre que eligió al crear su cuenta en la sección anterior. NO es su dirección de correo electrónico. Si ha olvidado el nombre, consulte el correo electrónico de confirmación.
    
    ![](images/cloud-oracle.png " ")
    
2.  Amplíe la flecha después de _"Inicio de sesión directo de Oracle Cloud Infrastructure"_ para mostrar los campos de entrada de conexión.
    
    ![](images/cloud-login-tenant.png " ")
    
3.  Introduzca sus credenciales de cuenta de Cloud y haga clic en **Conectar**. Tu nombre de usuario es tu dirección de correo electrónico. La contraseña es lo que eligió cuando se registró para una cuenta.
    
    ![](images/oci-signin.png " ")
    
4.  Ya ha iniciado sesión en Oracle Cloud.
    
    ![](images/oci-console-home-page.png " ")
    

## Tarea 2: Inicio de una instancia de GPU

1.  Después de conectarse, haga clic en el botón **Crear una instancia de VM** en la sección **Acciones rápidas**.
    
    ![](images/click-create-vm-instance.png " ")
    

También puede hacer clic en el menú de servicio de la parte superior izquierda y, a continuación, en **Recursos informáticos** -> **Instancias**. Se abre una nueva página con el botón azul **Crear instancia** en la que puede hacer clic.

2.  Rellene el nombre de la instancia informática.
    
    ![](images/fill-in-name.PNG " ")
    
3.  Seleccione el compartimento en la lista desplegable.
    
    ![](images/select-compartment.PNG " ")
    
4.  Antes de seleccionar el dominio de disponibilidad, consulte **Gobernanza** -> **Límites, cuotas y uso**, en el que el dominio de disponibilidad de la región seleccionada tiene instancias de GPU disponibles. Seleccione el dominio de disponibilidad en el que se aprovisionará la instancia informática.
    
    ![](images/select-AD.PNG " ")
    
5.  Haga clic en el botón **Cambiar imagen**.
    
    ![](images/change-image.PNG " ")
    
    Seleccione la imagen que desea utilizar para su instancia informática y haga clic en el botón **Seleccionar imagen**. Seleccionaremos Ubuntu 18.04, recomendado por Faceswap, pero cualquier otra distribución de Linux y sistema operativo Windows también podría funcionar.
    
    ![](images/select-image.PNG " ")
    
6.  Haga clic en el botón **Cambiar unidad**.
    
    ![](images/change-shape.PNG " ")
    
    Seleccione **Máquina virtual** y **Especialidad y heredado**. Allí encontrará la instancia de GPU y la VM.Standard.E2.1 **Siempre gratis**. Microunidad (en caso de que no sea propietario de una cuenta PAYG para ejecutar instancias de GPU).
    
    ![](images/select-VM-SL.PNG " ")
    
    Seleccione la instancia de GPU que desea aprovisionar, lea y active la casilla _He revisado y acepto los siguientes documentos: condiciones de uso de Oracle y Nvidia_ y haga clic en el botón **Seleccionar unidad**. En nuestro caso, es la VM.GPU2.1 basada en P100 (arquitectura de GPU NVIDIA Pascal) con 1 GPU. También puede solicitar límites de servicio para máquinas virtuales y con hardware dedicado mediante V100 (arquitectura de GPU NVIDIA Volta) y A100 (arquitectura de GPU NVIDIA Ampere).
    
    ![](images/select-GPU.PNG " ")
    
7.  Si ya ha creado una VCN en su compartimento, seleccione la VCN y la subred en las que desea colocar la instancia informática. Si no es así, puede hacer clic en **Crear nueva red virtual en la nube**, seleccionar un nombre y un compartimento para la VCN y un nombre para la subred pública. Puede mantener el bloque CIDR por defecto o ajustarlo. Puede elegir asignar una dirección IP pública a su instancia. La asignación de una dirección IP pública hace que se pueda acceder a esta instancia desde Internet. Si no está seguro de si necesita una dirección IP pública, siempre puede asignar una más tarde.
    
    ![](images/create-vcn.PNG " ")
    
8.  Si desea utilizar el par de claves SSH públicas y privadas que ha creado en el _laboratorio 1 - Crear claves SSH_, puede hacer clic en **Seleccionar archivos de claves públicas** y seleccionar los archivos de claves públicas SSH o hacer clic en **Pegar claves públicas** y pegar las claves públicas SSH en el campo. También puede generar una clave privada y pública SSH en OCI y guardarla en su máquina local. Tenga en cuenta que si no proporciona claves SSH, no podrá conectarse a la instancia mediante SSH.
    
    ![](images/add-SSH.PNG " ")
    
9.  El paso final es especificar el tamaño del volumen de inicio, elegir si se debe utilizar o no el cifrado en tránsito y cómo gestionar las claves de cifrado. Puede utilizar el tamaño del volumen de inicio por defecto, que es de 46,6 GB para sistemas Linux, o bien aumentarlo a un máximo de 32 TB. Puede dejar todas las casillas desactivadas y hacer clic en **Crear** (para finalizar la creación de la instancia informática).
    

![](images/boot-volume.PNG " ")

Tras un par de minutos, su instancia informática de GPU cambiará su estado a **EN EJECUCIÓN**

![](images/instance-running.PNG " ")

## **Acuses de recibo**

*   **Creado por/Fecha**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021
*   **Fecha/fecha de última actualización**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021