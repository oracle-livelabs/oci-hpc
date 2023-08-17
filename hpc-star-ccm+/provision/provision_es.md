# Aprovisionamiento del cluster de HPC desde la imagen de Oracle Marketplace

## Introducción

En este laboratorio, aprovisionará un cluster de HPC desde OCI Marketplace. OCI Marketplace es un centro integral para una amplia variedad de soluciones sectoriales que se pueden desplegar fácilmente en nuestra consola en la nube. Aquí podrá aprovechar este mercado para la implementación de clústeres HPC automatizados. La plantilla del mercado proporciona una base para una solución de recursos informáticos de alto rendimiento, incluidos todos los componentes necesarios para iniciar una carga de trabajo con el mínimo esfuerzo. A medida que progresen estas prácticas, verá cómo las aplicaciones de terceros pueden integrar y utilizar dicha línea base para acelerar el flujo de trabajo.

Tiempo estimado de laboratorio: 60 minutos

### Objetivos

Como desarrollador o ingeniero de datos,

*   Navegar a OCI Marketplace
*   Despliegue la imagen de cluster de HPC desde OCI Marketplace
*   Ver y ejecutar una pila en OCI

### Requisitos previos

*   Cuenta de Oracle Cloud Infrastructure con privilegios para crear una unidad de computación estándar de máquina virtual de instancia 2.1 o BM.HPC2.36.

## Tarea 1: Navegar a OCI Marketplace

1.  Haga clic en el menú de hamburguesa (o) en la esquina superior izquierda. ![](./images/click_hamburger.png)
    
2.  Haga clic en Marketplace. ![](./images/click_marketplace.png)
    
3.  Busque en Marketplace **HPC** y seleccione Cluster de HPC. ![](./images/marketplace.png)
    
4.  Revise las **condiciones y restricciones estándar de Oracle** y haga clic en **Iniciar pila**. ![](./images/launch_stack.png)
    

## Tarea 2: Configuración de la pila de HPC

Después de iniciar la pila, se le redirigirá al asistente Create Stack, donde rellenará la información de la pila.

1.  En **Información de pila**, asigne un nombre a la pila y _(opcionalmente)_ especifique el despliegue en un compartimento en el que no esté actualmente. ![](./images/stack_p1.png)
    
2.  Seleccione la **AD** deseada para las unidades de computación y el bastión.
    
3.  Copie y pegue su **clave SSH** pública. ![](./images/stack_p2_1.png)
    
4.  Introduzca el número de **instancias informáticas** para el cluster ![](./images/stack_p2_2.png)
    
5.  En **Opciones de red**, deje los valores por defecto ![](./images/stack_p2_3.png)
    
6.  Haga clic en **Siguiente** para revisar la configuración. ![](./images/stack_p3.png)
    
7.  Haga clic en **Crear**.
    

**Nota:** La pila tardará aproximadamente 15 minutos en aprovisionarse.

## Tarea 3: ver la pila en ejecución

Una vez que haya creado la pila, le redirigirá a una página en la que puede ver el trabajo de pila en ejecución. Debería tener un aspecto similar al siguiente. Aquí puede ver:

1.  **Registros**
2.  **Variables** transferidas o generadas por el script de Terraform
3.  **Recursos asociados** aprovisionados por el script de Terraform
4.  **Salidas**
5.  **Ver estado** del trabajo

![](./images/stack_detail_provisioning.png)

Para navegar a esta página donde puede ver todas las pilas del compartimento actual, haga clic en el menú de hamburguesa (o) en la parte superior izquierda y, en **Soluciones y plataforma**, desplácese sobre **Gestor de recursos** > **Pilas**.

![](./images/nav_resource_manager.png)

¡Todo listo! Esta acción finaliza la demostración para aprovisionar un cluster de HPC desde la imagen de Oracle Marketplace.

_Ahora puede continuar con la siguiente práctica_

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Fecha/fecha de última actualización**: Harrison Dvoor, octubre de 2020