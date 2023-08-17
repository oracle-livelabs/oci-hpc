# Adición del instalador de Gromacs a Object Storage

## Introducción

En esta práctica, agregará Gromacs Installer a Object Storage.

Tiempo estimado de laboratorio: 5 minutos

## Tarea: Agregar el instalador de Gromacs a Object Storage

_Tenga en cuenta que las secuencias de comandos de terraform [zip file](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip) proporcionadas en este github ya contienen URL de almacenamiento de objetos para Gromacs 2020.1, VMD 1.9.3 y un modelo de referencia de ejemplo de [Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench). Si desea cambiar estas URL, modifique el archivo variable.tf y sustituya los valores de gromacs\_url, model\_url y visualizer\_url por sus propias URL de solicitud autenticadas previamente._

1.  Seleccione el menú ![](./images/menu.png) en la parte superior izquierda y, a continuación, seleccione Object Storage --> Object Storage
2.  Cree un nuevo cubo o seleccione uno existente. Para crear una, haga clic en![](./images/create_bucket.png)
3.  Deje las opciones por defecto: Estándar como niveles de almacenamiento y claves gestionadas por Oracle. Haga clic en![](./images/create_bucket.png)
4.  Haga clic en el nombre del cubo recién creado y, a continuación, seleccione![](./images/upload_object.png)
5.  Seleccione su archivo tar del instalador de Gromacs y haga clic![](./images/upload_object.png)
6.  Haga clic en los 3 puntos situados a la derecha del objeto que acaba de cargar y seleccione "Crear solicitud autenticada previamente".
7.  En el siguiente menú, deje las opciones predeterminadas y seleccione una fecha de caducidad para la URL del instalador. Haga clic en![](./images/pre-auth.png)
8.  En la siguiente ventana, copie la "URL de solicitud PRE-AUTHENTICATED" y consérvela. No podrá recuperarlo después de cerrar esta ventana. Si lo suelta o caduca, siempre es posible volver a crear otra solicitud autenticada previamente que genere una URL diferente

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020