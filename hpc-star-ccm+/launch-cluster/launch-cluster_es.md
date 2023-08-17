# Iniciar red de cluster de HPC

## Introducción

Existen muchas formas de iniciar una red de clusters de HPC. Esta guía de soluciones abarcará dos métodos diferentes:

*   A través de Marketplace
*   En función del sistema operativo, deberá utilizar un método específico. Si se utiliza la imagen del mercado de red de cluster de HPC o nuestros scripts de CN Terraform de HPC de OCI, esto es solo para Oracle Linux 7. Si desea utilizar CentOS, Ubuntu u otro sistema operativo, se necesita la configuración manual.

Tiempo estimado de laboratorio: 30 minutos

## **OPCIÓN 1**: creación de una red de cluster mediante Marketplace

Marketplace incluye aplicaciones e imágenes que se pueden desplegar con nuestra infraestructura. Para los clientes que desean utilizar Oracle Linux, hay una imagen de HPC Cluster Network disponible y se puede iniciar directamente desde el mercado. Sugerimos iniciar el [cluster CFD Ready](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) que contendrá las bibliotecas necesarias para CFD.

1.  En Marketplace, seleccione Obtener aplicación en la parte superior derecha. ![](images/get-app.png " ")
    
2.  Seleccione la región de OCI y, a continuación, haga clic en Conectar. ![](images/sign-in.png " ")
    
3.  Después de conectarse, verifique la versión de la imagen de cluster de HPC y, a continuación, seleccione el compartimento en el que se iniciará el cluster. Acepte las condiciones y, a continuación, inicie la pila.
    
    ![](images/launch-stack.png " ")
    
4.  Rellene los detalles restantes de la pila:
    
    *   Seleccione el dominio de disponibilidad deseado para las unidades de computación y el bastión.
    *   Copie y pegue su clave ssh pública
    *   Introduzca el número de instancias informáticas para el cluster
    *   Desmarque Install OpenFOAM (Instalar)
    *   Si necesita más de 6 TB de espacio en disco compartido, compruebe GlusterFS y seleccione cuántos servidores necesitaría. (6 TB por servidor)
5.  Haga clic en Crear.
    
6.  Vaya a Terraform Actions y haga clic en Apply. De esta forma se iniciará el aprovisionamiento de CN.
    
    ![](images/apply.png " ")
    
7.  Espere hasta que el trabajo muestre 'Succeeded' y, a continuación, navegue hasta Outputs para obtener las IP privadas de nodo de cálculo y bastión.
    

## **OPCIÓN 2**: creación de una red de cluster mediante la configuración manual

Marketplace incluye aplicaciones e imágenes que se pueden desplegar con nuestra infraestructura. Para los clientes que desean utilizar Oracle Linux, puede crear manualmente una red de cluster de la siguiente manera:

1.  Seleccione la región de OCI en la parte superior derecha.
    
2.  En el menú principal, seleccione Red y redes virtuales en la nube.
    
3.  Haga clic en Iniciar asistente de VCN y seleccione la VCN con conexión a Internet
    
4.  Elija y asigne un nombre, el compartimento correcto y utilice 172.16.0.0/16 como CIDR de VCN, 172.16.0.0/24 para subred pública y 172.16.1.0/24 para subred privada. Haga clic en **Siguiente** y, a continuación, en **Crear**.
    
5.  En el menú principal, seleccione Compute, Instances y, a continuación, Create Instance.
    
6.  Seleccione **Cambiar imagen** y Oracle Linux 7: imagen de red de cluster de HPC, en el separador Imagen de Oracle.
    
7.  Seleccione el dominio de disponibilidad en el que puede poner en marcha una instancia BM.HPC2.36
    
8.  Cambie la forma a BM.HPC2.36 con hardware dedicado y especialidad
    
9.  Seleccione la VCN y la subred pública que ha creado.
    
10.  Agregue una clave pública para conectarse a la instancia. Esta clave se utilizará en todas las instancias informáticas.
    
11.  Una vez que la máquina esté activa, haga clic en la instancia creada. En Más acciones, seleccione Crear configuración de instancia. Ahora puede terminar la instancia en Más acciones.
    
12.  En el menú principal, seleccione Compute y, a continuación, Cluster Networks
    
13.  Haga clic en Create Cluster Network y complete todas las opciones. Utilice la configuración de instancia, subred privada y VCN que acaba de crear. Seleccione el dominio de disponibilidad en el que puede iniciar instancias de BM.HPC2.36.
    
14.  Inicie la red de cluster.
    
15.  Mientras se carga, cree otra instancia en el menú principal, recursos informáticos e instancias.
    
16.  Colóquelo en la subred pública que acaba de crear, utilizando su clave pública y unidad debe ser VM.Standard2.1 o similar. Este será el bastión que utilizaremos para conectarse al cluster.
    
17.  SCP la clave del cluster en el bastión en /home/opc/.ssh/cluster\_key y cópiela también en /home/opc/.ssh/id\_rsa
    
18.  Instale la herramienta de aprovisionamiento en el bastión mediante el siguiente comando:
    
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        
19.  Navegue a Compute y, a continuación, a los pools de instancias en la consola y recopile todas las direcciones IP del pool de red de cluster. O utilice este comando en el bastión si no tiene nada más en ejecución en su subred privada.
    
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done
        
20.  Instale la herramienta de aprovisionamiento mediante el siguiente comando:
    
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020