# Introducción de NAMD

En este Runbook se proporcionan los pasos para desplegar una máquina de GPU en Oracle Cloud Infrastructure, instalar NAMD y ejecutar un modelo con el software NAMD.

NAMD es un software de dinámica molecular que simula los movimientos de los átomos en biomoléculas bajo un conjunto predefinido de condiciones. Se utiliza para identificar el comportamiento de estas biomoléculas cuando se expone a cambios de temperatura, presión y otras entradas que imitan las condiciones reales encontradas en un organismo vivo. La NAMD puede usarse para establecer patrones en el plegado deproteínas, la unión proteína-ligando y el transporte demembrana celular, lo que la hace una aplicación muy útilpara la investigación y el descubrimiento de fármacos.

NAMD se basa en Charm++ y Converse, y puede ejecutarse en computadoras de alto rendimiento para ejecutar el procesamiento paralelo. Fue desarrollado por la Universidad de Illinois. Puede obtener más información [aquí](http://charm.cs.illinois.edu/research/moldyn).

![](./images/protein.gif)

## **Arquitectura**

La arquitectura de este libro de ejecución es simple, una única máquina que se ejecuta dentro de una VCN de OCI con una subred pública. Como se utiliza una instancia de GPU, el almacenamiento de bloques se asocia a la instancia y se instala con la aplicación NAMD. La instancia se encuentra en una subred pública y tiene asignada una IP pública, a la que se puede acceder mediante ssh.

![](./images/arch-draft.png)

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020