# Taller con un único conjunto de prácticas

## Instrucciones - Elimine este archivo cuando finalice

1.  Abra la plantilla de ejemplo-taller en Atom o Visual Studio Code
2.  Hemos creado previamente 5 carpetas. Se crea un taller a partir de varias prácticas.
3.  Elimine los comentarios como este: _Mostrar objetivos para esta práctica_
4.  Asegúrese de utilizar la carpeta en minúscula y el nombre de archivo y guiones para espacios (setup-adb NOT Setup\_ADB)
5.  Los nombres de imagen deben tener nombres descriptivos. No solo adb1, adb2, adb3. Para facilitar la accesibilidad, necesitamos descripciones de imágenes para explicar el aspecto de la imagen. Recuerde todas las minúsculas y guiones.
6.  Descargue nuestro documento de control de calidad de WMS. Encontramos talleres en producción más rápido cuando sabes lo que se necesita para pasar a producción por delante y usas el esqueleto.

PS: no necesita un Readme.md. Los Léame sólo existen en los niveles superiores de la biblioteca. Dirigimos todo el tráfico a LiveLabs ya que no podemos realizar un seguimiento del uso en GitHub. No cree ningún enlace directo a GitHub, su taller puede ser muy popular, pero no podemos rastrearlo para que nadie lo sepa.

## Ruta de acceso absoluta para la navegación del menú de Oracle Cloud

**Laboratorio 1: Aprovisionamiento de una instancia -> Paso 0: uso de estas imágenes estandarizadas para la navegación por Oracle Cloud (normalmente para el aprovisionamiento)**: hemos incluido una lista de capturas de pantalla comunes para navegar por el menú de Oracle Cloud. Lea esta sección y utilice las imágenes de ruta absoluta relevantes cuando corresponda. Esto probará en el futuro su taller en caso de actualizaciones de la interfaz de usuario de Oracle Cloud.

## Estructura de Carpeta

En este ejemplo, el objetivo es crear varios talleres "secundarios" a partir de un taller "principal" más largo. Los hijos están formados por partes del padre.

ejemplo-taller/ -- laboratorios individuales

        provision/
        setup/
        dataload/
        query/
        introduction/
          introduction.md       -- description of the everything workshop, note that it is a "lab" since there is only one
    
    workshops/
       freetier/                -- freetier version of the workshop
        index.html
        manifest.json
       livelabs/                -- livelabs version of the workshop
        index.html
        manifest.json
    

### FreeTier frente a LiveLabs

*   "FreeTier": incluye pruebas gratuitas, cuentas pagadas y, para algunos talleres, cuentas siempre gratis (botón marrón)
*   "LiveLabs": estos son talleres que utilizan arrendamientos proporcionados por Oracle (botón verde)
*   "Escritorio": este es un nuevo despliegue en el que el taller está encapsulado en un entorno NoVNC que se ejecuta en una instancia informática

### Acerca del Taller

El taller incluye los 6 laboratorios individuales en una sola secuencia.

La estructura de carpetas incluye un "laboratorio" de introducción que describe el taller como un conjunto completo de 6 prácticas. Nota: puede que no necesite tener una introducción diferente para cada versión principal y secundaria de los talleres, esto es solo ilustrativo.

Consulte la carpeta product-name-workshop/freetier y consulte el archivo manifest.json para ver la estructura.

> **Nota:** El uso de "Lab n:" en los títulos es opcional

El "lab" de requisito previo es la primera práctica de una carpeta común en el repositorio oracle/learning-library. Puesto que este laboratorio ya existe, podemos utilizar una URL RAW/absoluta en su lugar:

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

El archivo manifest.json debe conocer la ubicación de cada práctica relativa a dónde existe en la jerarquía. En esta estructura, los laboratorios se encuentran en dos niveles superiores, por ejemplo:

    "filename": "../../provision/provision.md"
    

### Por ejemplo:

Este [taller de APEX](https://oracle.github.io/learning-library/developer-library/apex/spreadsheet/workshops/freetier/) es un buen ejemplo de un taller con un único juego de prácticas: [https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).