# Instalación de STAR-CCM+

## Introducción

En esta práctica, instalará STAR-CCM+.

Tiempo estimado de laboratorio: 25 minutos

## Tarea 1: Adición de bibliotecas específicas

_**Si usó el cluster de CFD Ready desde Marketplace, este paso no es necesario.**_

1.  Hay un par de bibliotecas que se deben agregar a la imagen de Oracle Linux en todos los nodos de cálculo.
    
            sudo yum -y install libSM libX11 libXext libXt
        

## Tarea 2: Descargar los archivos binarios

Puede descargar el instalador STAR-CCM+ desde el sitio web de Siemens PLM o enviarlo a su máquina utilizando scp.

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

Otra posibilidad es cargar el instalador en Object Storage.

1.  En el menú principal de la consola, seleccione Object Storage.
2.  Seleccione la región correcta en la parte superior derecha
3.  Seleccione el compartimento correcto en el lado izquierdo
4.  Cree un cubo si no lo ha creado
5.  En el cubo, seleccione el objeto de carga y especifique la ruta del instalador.
6.  Seleccione los 3 puntos del lado derecho del objeto de instalador y seleccione Crear solicitud autenticada previamente
7.  Si pierde la URL, no puede recuperarla, pero puede volver a generar una nueva solicitud autenticada previamente

Descargue el almacenamiento de objetos de formulario del instalador con

        wget PAR_URL
    

Descomprima o descomprima el instalador según su versión

        tar -xf installer.tgz
        unzip installer.tgz
    

## Tarea 3: Instalación

1.  Inicie el instalador en una ubicación compartida. Por defecto, un cluster de HPC tiene un recurso compartido NFS montado en todos los nodos de cálculo.
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020