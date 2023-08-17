# Preparar configuración

## Introducción

En este laboratorio se muestra cómo descargar el archivo zip de la pila de Oracle Resource Manager (ORM) necesario para configurar el recurso necesario para ejecutar este taller.

_Tiempo de laboratorio estimado:_ 10 minutos

### Objetivos

*   Descargar pila de ORM
*   Configurar una red virtual en la nube (VCN) existente

### Requisitos previos

En esta práctica se asume que tiene:

*   Una cuenta de Oracle Cloud

## Tarea 1: Descargar archivo zip de pila de Oracle Resource Manager (ORM)

1.  Haga clic en el siguiente enlace para descargar el archivo zip del gestor de recursos que necesita para crear el entorno:
    
    _Nota 1:_ Si proporciona una descarga de pila única para el taller, utilice esta expresión simple.
    
    *   [ejemplo-mkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _Nota 2:_ Si proporciona la descarga de varias pilas para el mismo taller, utilice una expresión condicional similar a la siguiente. Tenga en cuenta que la condición o _type_ debe asociarse con una entrada válida en el archivo _manifest.json_. Consulte _freetier-advanced_ y _freetier-basics._
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  Guardar en la carpeta de descargas.

Recomendamos utilizar esta pila para crear una VCN autónoma/dedicada con sus instancias. Vaya a la _Tarea 3_ para seguir nuestras recomendaciones. Si prefiere utilizar una VCN existente, continúe con la siguiente tarea para actualizar la VCN existente con las reglas de entrada necesarias.

## Tarea 2: Adición de reglas de seguridad a una VCN existente

Este taller necesita que un determinado número de puertos esté disponible, un requisito que se puede cumplir mediante la ejecución de la pila ORM por defecto que crea una VCN dedicada. Para utilizar una VCN/subred existente, se deben agregar las siguientes reglas a la lista de seguridad.

| Tipo | Puerto de origen | CIDR de origen | Puerto de destino | Protocolo | Descripción |
| :-- | :-: | :-: | :-: | :-: | :-- |
| Entrada | Todos | 0.0.0.0/0 | 22 | TCP | SSH |
| Entrada | Todos | 0.0.0.0/0 | 80 | TCP | Escritorio remoto mediante noVNC |
| Salida | Todos | N/D | 80 | TCP | Acceso HTTP saliente |
| Salida | Todos | N/D | 443 | TCP | Acceso HTTPS de salida |
| {: title="Lista de reglas de seguridad de red necesarias"} |  |  |  |  |  |

1.  Vaya a _Networking >> Virtual Cloud Networks_
2.  Elegir red
3.  En Recursos, seleccione Listas de seguridad
4.  Haga clic en Listas de seguridad por defecto en el botón Crear lista de seguridad.
5.  Haga clic en el botón Add Ingress Rule.
6.  Introduzca lo siguiente:
    *   Tipo de origen: CIDR
    *   CIDR de origen: 0.0.0.0/0
    *   Protocolo IP: TCP
    *   Rango de puertos de origen: Todo (Mantener por defecto)
    *   Rango de puertos de destino: _seleccione de la tabla anterior_
    *   Descripción: _Seleccione la descripción correspondiente de la tabla anterior_
7.  Haga clic en el botón Add Ingress Rules.
8.  Repita los pasos \[5-7\] hasta que se cree una regla para cada puerto mostrado en la tabla.

## Tarea 3: Configuración de Compute

Con los detalles de las dos tareas anteriores, vaya a la práctica _Configuración del entorno_ para configurar el entorno del taller mediante Oracle Resource Manager (ORM) y una de las siguientes opciones:

*   Crear pila: _Recursos informáticos + redes_
*   Crear pila: _solo recursos informáticos_ con una VCN existente en la que las listas de seguridad se han actualizado según la _tarea 2_ anterior

Ahora puede continuar con la siguiente práctica.

## Acuses de recibo

*   **Autor**: Rene Fontcha, responsable de plataforma LiveLabs, tecnología NA
*   **Colaboradores** - Meghana Banka
*   **Fecha/fecha de última actualización**: Rene Fontcha, responsable de plataforma LiveLabs, tecnología NA, agosto de 2022