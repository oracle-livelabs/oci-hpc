# Introducción

## Introducción

La computación de alto rendimiento está cambiando el desarrollo de productos y la investigación, lo que permite a los clientes resolver problemas complejos más rápido. Esto significa menos prototipos, acelera las pruebas y reduce el tiempo de comercialización. Oracle ofrece infraestructura de HPC a demanda adecuada para cualquier carga de trabajo de HPC y basada en las tecnologías más avanzadas de recursos informáticos, almacenamiento, redes y software. Todo esto se obtiene por una fracción del costo de crearlo usted mismo y evitar problemas de utilización de la capacidad.

Oracle proporciona la infraestructura en la nube más flexible y ampliable para ejecutar sus aplicaciones de HPC. Con una capacidad prácticamente ilimitada, los ingenieros, los investigadores y los propietarios de sistemas de HPC pueden innovar más allá de las limitaciones de la infraestructura de HPC local. Oracle ofrece un conjunto integrado de servicios que proporciona todo lo necesario para crear y gestionar clusters de HPC en la nube de forma rápida y sencilla para ejecutar las cargas de trabajo más intensivas en recursos informáticos en varios mercados verticales del sector. Estas cargas de trabajo abarcan las aplicaciones de recursos informáticos de alto rendimiento tradicionales, como genómica, química computacional, modelado de riesgos financieros, ingeniería asistida por computadora, imágenes sísmicas y predicción meteorológica, así como aplicaciones emergentes, como aprendizaje automático, aprendizaje profundo y conducción autónoma.

Con Oracle Cloud Infrastructure, las empresas pueden ejecutar cargas de trabajo de recursos informáticos de alto rendimiento que requieren millones de operaciones IOP, una latencia de milisegundos y muchos GB/s de ancho de banda, en un modelo de pago por uso o no medido, lo que ahorra un 32 % en un costo total de propiedad de 3 años.

Estas guías prácticas de laboratorio proporcionan instrucciones paso a paso para configurar y utilizar su plataforma de recursos informáticos de alto rendimiento en Oracle Cloud Infrastructure.

Tiempo estimado del taller: 4 horas

### Acerca de los recursos informáticos de alto rendimiento (HPC)

HPC, o supercomputación, es como la computación diaria, solo más potente. Es una forma de procesar grandes volúmenes de datos a velocidades muy altas utilizando varios equipos y dispositivos de almacenamiento como tejido cohesivo. HPC permite explorar y encontrar respuestas a algunos de los mayores problemas del mundo en ciencia, ingeniería y negocios.

Una red de cluster es un pool de instancias informáticas de alto rendimiento (HPC) conectadas con una red de latencia ultrabaja y de gran ancho de banda. Cada nodo del cluster es una máquina con hardware dedicado que se encuentra cerca física de los otros nodos. Una red de acceso directo a memoria remota (RDMA) entre nodos proporciona latencia tan baja como microsegundos de un solo dígito, comparable a los clusters de HPC locales.

Los recursos informáticos de alto rendimiento se ofrecen en Oracle Cloud Infrastructure, en regiones de OCI.

Instancia informática de alto rendimiento disponible en Oracle Marketplace Image y BM.HPC2.36 en OCI.

El rack de recursos informáticos de alto rendimiento en la imagen de Oracle Marketplace incluye nodos de cluster de HPC, red de cluster y recurso compartido NFS.

Los nodos de cálculo se conectan mediante una red de cluster que proporciona acceso de almacenamiento basado en RDMA a los nodos de cálculo.

Actualmente, se admite una sola BM por nodo de cálculo. Permite el acceso raíz a los clientes a la vez que protege el hardware y la red, los nodos de cálculo se virtualizan con BM.HPC2.36.

### Objetivos

En este laboratorio:

*   Prepare su red privada en Oracle Cloud Infrastructure
*   Aprovisionamiento de una instancia informática de alto rendimiento en una red privada de OCI
*   Configurar un sistema de desarrollo para utilizarlo con la instancia informática de alto rendimiento
*   Uso de comandos de la CLI de OCI para trabajar con la instancia informática de alto rendimiento
*   Automatización con Terraform
*   Ejecute proyectos OpenFoam con su instancia informática de alto rendimiento

### Requisitos previos

*   Conocimientos útiles sobre los términos de la nube y la base de datos
*   Estar familiarizado con Oracle Cloud Infrastructure (OCI) resulta útil
*   Tener conocimientos de redes resulta útil

## Acerca de este taller

*   Práctica 1: Configuración de la instancia informática de alto rendimiento en Oracle Cloud Infrastructure.
    
*   Práctica 2: Gestión de la instancia informática de alto rendimiento mediante herramientas de la CLI de OCI.
    
*   Laboratorio 3: Diseñado para ayudar en la evaluación del software de CFD OpenFOAM en Oracle Cloud Infrastructure.
    

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Fecha/fecha de última actualización**: Harrison Dvoor, octubre de 2020