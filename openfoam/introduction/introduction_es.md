# Ejecución de proyectos OpenFOAM en clusters de HPC y máquinas virtuales estándar en OCI

## Introducción

Este libro de ejecución está diseñado para facilitar la evaluación del software de CFD OpenFOAM en Oracle Cloud Infrastructure. Descarga y configura automáticamente OpenFOAM. OpenFOAM es el software libre de código abierto CFD lanzado y desarrollado principalmente por OpenCFD Ltd desde 2004. Cuenta con una gran base de usuarios en la mayoría de las áreas de ingeniería y ciencia, tanto de organizaciones comerciales como académicas. OpenFOAM tiene una amplia gama de características para resolver cualquier cosa, desde flujos de fluidos complejos que implican reacciones químicas, turbulencias y transferencia de calor, hasta acústica, mecánica sólida y electromagnética.

### Acerca de los recursos informáticos de alto rendimiento (HPC)

HPC, o supercomputación, es como la computación diaria, solo más potente. Es una forma de procesar grandes volúmenes de datos a velocidades muy altas utilizando varios equipos y dispositivos de almacenamiento como tejido cohesivo. HPC permite explorar y encontrar respuestas a algunos de los mayores problemas del mundo en ciencia, ingeniería y negocios.

Una red de cluster es un pool de instancias informáticas de alto rendimiento (HPC) conectadas con una red de latencia ultrabaja y de gran ancho de banda. Cada nodo del cluster es una máquina con hardware dedicado que se encuentra cerca física de los otros nodos. Una red de acceso directo a memoria remota (RDMA) entre nodos proporciona latencia tan baja como microsegundos de un solo dígito, comparable a los clusters de HPC locales.

Los recursos informáticos de alto rendimiento se ofrecen en Oracle Cloud Infrastructure, en regiones de OCI.

Instancia informática de alto rendimiento disponible en Oracle Marketplace Image y BM.HPC2.36 en OCI.

El rack de recursos informáticos de alto rendimiento en la imagen de Oracle Marketplace incluye nodos de cluster de HPC, red de cluster y recurso compartido NFS.

Los nodos de cálculo se conectan mediante una red de cluster que proporciona acceso de almacenamiento basado en RDMA a los nodos de cálculo.

Actualmente, se admite una sola BM por nodo de cálculo. Permite el acceso raíz a los clientes a la vez que protege el hardware y la red, los nodos de cálculo se virtualizan con BM.HPC2.36.

_Tiempo estimado_: 2 horas

### Objetivos

*   Ejecute proyectos OpenFOAM mediante instancias de HPC interconectadas mediante acceso directo a memoria remoto (RDMA).
    
*   Ejecute proyectos de OpenFOAM con máquinas virtuales (VM) estándar normales en Oracle Cloud Infrastructure (OCI)
    

### Requisitos previos

*   Cuenta de Oracle Cloud Infrastructure con privilegios para crear un compartimento y una pila.
    
*   Es útil estar familiarizado con la consola de OCI y los recursos de Oracle Cloud (es decir, redes virtuales en la nube, recursos informáticos y almacenamiento).
    
*   Supongamos que ha completado el examen de OCI Cloud Architect Associate.
    

## Acerca de este taller

*   Ejecución de proyectos OpenFOAM con cluster de HPC
    
*   Ejecute proyectos OpenFOAM con la máquina virtual estándar en OCI
    

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Fecha/fecha de última actualización**: Harrison Dvoor, enero de 2021