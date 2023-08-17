# Siemens STAR-CCM+ Introducción

## Introducción

Simcenter STAR-CCM+ es una solución multifísica completa para la simulación de productos y diseños. Este Runbook le guiará por el proceso de despliegue de un cluster de Simcenter STAR-CCM+ en Oracle Cloud con redes de baja latencia entre los nodos de cálculo. Ejecutar Simcenter STAR-CCM+ en Oracle Cloud es bastante sencillo, siga esta guía para obtener todos los consejos y trucos.

Tenga en cuenta que debe proporcionar su propia licencia para Simcenter STAR-CCM+!

![](images/simulation.png " ")

## Arquitectura

La arquitectura de este runbook es como sigue, tenemos una pequeña máquina (bastión) en la que se conectará. Los nodos de cálculo estarán en una red privada independiente enlazada con la red RDMA RoCE v2. Se podrá acceder al bastión a través de SSH desde cualquier usuario con la clave (o VNC si decide activarlo). Solo se podrá acceder a los nodos de cálculo a través del bastión dentro de la red. Esto es posible con 1 red virtual en la nube con 2 subredes, una pública y una privada.

### **Infraestructura base**

Las redes de cluster se admiten en las siguientes regiones. En cada caso, recomendamos utilizar la arquitectura de referencia de línea base y, a continuación, ajustarla, según sea necesario, para cumplir sus requisitos específicos:

*   VCN de viñeta
*   Subred pública, lista de seguridad, tabla de rutas
*   Subred privada, lista de seguridad, tabla de rutas
*   Gateway de Internet
*   Gateway de NAT
*   Nodos de Cálculo
*   Host de bastión en una subred pública
*   Nodos de recursos informáticos de HPC en subred privada

![](images/architecture.png " ")

La infraestructura base anterior proporciona las siguientes especificaciones:

*   Redes
    *   1 RDMA de 100 Gbps sobre Ethernet convergente (ROCE) v2
    *   Latencia tan baja como 1,5 μs
*   Nodos de cálculo de HPC (BM.HPC2.36)
    *   Almacenamiento local en unidades de estado sólido NVME de 6,4 TB por nodo
    *   36 núcleos por nodo
    *   384 GB de memoria por nodo

### **Infraestructura opcional**

### Almacenamiento

Además del almacenamiento en unidades de estado sólido NVME que incluye la unidad de HPC, también puede asociar volúmenes en bloque con IOPS de 32 K por volumen, respaldado por el SLA de mayor rendimiento de Oracle. Si utiliza nuestras soluciones para iniciar la infraestructura, se instala por defecto un recurso compartido nfs en el almacenamiento SSD NVME en /mnt. También puede instalar su propio sistema de archivos paralelo sobre el almacenamiento en unidades de estado sólido NVME o el almacenamiento de bloques, según sus requisitos de rendimiento.

### Nodo de Visualizador

Puede crear un nodo del visualizador, como una máquina virtual de GPU o una máquina con hardware dedicado, según sus necesidades. Este nodo del visualizador puede ser el host bastión o puede ser independiente. Según los requisitos de seguridad para la carga de trabajo, el nodo del visualizador se puede colocar en la subred privada o pública.

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020