# Taller con un único conjunto de prácticas

## Instrucciones - Elimine este archivo cuando finalice

1.  Abra la plantilla de ejemplo-taller en Atom o Visual Studio Code
2.  Hemos creado previamente 5 carpetas. Se crea un taller a partir de varias prácticas.
3.  Elimine los comentarios como este: _Mostrar objetivos para esta práctica_
4.  Asegúrese de utilizar la carpeta en minúscula y el nombre de archivo y guiones para espacios (setup-adb NOT Setup\_ADB)
5.  Los nombres de imagen deben tener nombres descriptivos. No solo adb1, adb2, adb3. Para facilitar la accesibilidad, necesitamos descripciones de imágenes para explicar el aspecto de la imagen. Recuerde todas las minúsculas y guiones.
6.  Descargue nuestro documento de comentarios sobre control de calidad en WMS. Encontramos talleres en producción más rápido cuando sabes lo que se necesita para pasar a producción por delante y usas el esqueleto.

PS: no necesita un Readme.md. Los Léame sólo existen en los niveles superiores de la biblioteca. Dirigimos todo el tráfico a LiveLabs ya que no podemos realizar un seguimiento del uso en GitHub. No cree ningún enlace directo a GitHub, su taller puede ser muy popular, pero no podemos rastrearlo para que nadie lo sepa.

## Ruta de acceso absoluta para la navegación del menú de Oracle Cloud

**Laboratorio 1: Aprovisionamiento de una instancia -> Paso 0: uso de estas imágenes estandarizadas para la navegación por Oracle Cloud (normalmente para el aprovisionamiento)**: hemos incluido una lista de capturas de pantalla comunes para navegar por el menú de Oracle Cloud. Lea esta sección y utilice las imágenes de ruta absoluta relevantes cuando corresponda. Esto probará en el futuro su taller en caso de actualizaciones de la interfaz de usuario de Oracle Cloud.

## Estructura de Carpeta

En este ejemplo, el objetivo es crear varios talleres "secundarios" a partir de un taller "principal" más largo. Los hijos están formados por partes del padre.

*   ejemplo-taller/
    *   laboratorios individuales
        *   prepare-setup/ -- contiene el enlace para descargar la pila de ORM/Terraform para el taller
        *   initialize-environment/ -- tareas para inicializar el entorno después del aprovisionamiento
        *   provision/ -- primer laboratorio ficticio
        *   dataload/ -- segundo laboratorio ficticio
        *   query/ -- tercer laboratorio ficticio
        *   introducción/
            *   introduction.md -- descripción del taller de todo, tenga en cuenta que es un "laboratorio" ya que solo hay uno
    *   talleres/
        *   escritorio/ -- _requerido_ versión de escritorio del taller (todos los laboratorios)
            *   index.html
            *   manifest.json
        *   escritorio-avanzado/ -- versión de escritorio del taller (introducción, inicialización-ambiente, carga de datos, consulta)
            *   index.html
            *   manifest.json
        *   base de escritorio/ -- versión de escritorio del taller (introducción, inicialización-entorno, provisión)
            *   index.html
            *   manifest.json
        *   arrendamiento/ -- _requerida_ versión de arrendamiento del taller (todos los laboratorios)
            *   index.html
            *   manifest.json
        *   arrendamiento-avanzado/ -- versión de arrendamiento del taller (introducción, inicialización-entorno, carga de datos, consulta)
            *   index.html
            *   manifest.json
        *   tenancy-basics/ -- versión de arrendamiento del taller (introducción, inicialización-entorno, provisión)
            *   index.html
            *   manifest.json
        *   sandbox/ -- _requerida_ versión de sandbox del taller (todos los laboratorios)
            *   index.html
            *   manifest.json
        *   sandbox-advanced/ -- versión sandbox del taller (introducción, inicialización-ambiente, carga de datos, consulta)
            *   index.html
            *   manifest.json
        *   sandbox-basics/ -- versión sandbox del taller (introducción, inicialización-medio ambiente, provisión)
            *   index.html
            *   manifest.json

### arrendamiento frente a sandbox frente a escritorio

*   "escritorio": guía del taller que se muestra dentro de la sesión de escritorio remota noVNC _necesaria_
*   "arrendamiento": taller aprovisionado con arrendamiento de cliente _necesario_
*   "sandbox" - Taller aprovisionado mediante "Reserve Workshop on LiveLabs Sandbox" (botón verde) _required_
*   "desktop-xxxxxxx": subjuego o guía de mini-taller que se muestra dentro de la sesión de escritorio remota noVNC
*   "tenancy-xxxxxxx": subconjunto o mini-taller aprovisionado con arrendamiento de cliente (botón marrón)
*   "sandbox-xxxxxxx" - Subconjunto o mini-taller aprovisionado mediante "Reserve Workshop on LiveLabs Sandbox" (botón verde)

### Acerca del Taller

El taller incluye los 6 laboratorios individuales en una sola secuencia.

La estructura de carpetas incluye un "laboratorio" de introducción que describe el taller como un conjunto completo de 6 prácticas. Nota: puede que no necesite tener una introducción diferente para cada versión principal y secundaria de los talleres, esto es solo ilustrativo.

Consulte la carpeta product-name-workshop/tenancy y consulte el archivo manifest.json para ver la estructura.

> **Nota:** El uso de "Lab n:" en los títulos es opcional

El "lab" de requisito previo es la primera práctica de una carpeta común en el repositorio oracle/learning-library. Puesto que este laboratorio ya existe, podemos utilizar una URL RAW/absoluta en su lugar:

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

El archivo manifest.json debe conocer la ubicación de cada práctica relativa a dónde existe en la jerarquía. En esta estructura, los laboratorios se encuentran en dos niveles superiores, por ejemplo:

    "filename": "../../provision/provision.md"
    

### Por ejemplo:

Este [taller de APEX](https://oracle-livelabs.github.io/apex/spreadsheet/) es un buen ejemplo de un taller con un único juego de prácticas: [https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).