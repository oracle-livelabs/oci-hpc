# Ejecutar intercambio de caras

## Introducción

Ahora que hemos preparado nuestra instancia informática (consulte el laboratorio 4), podemos seguir adelante y ejecutar Faceswap. En el laboratorio, aprenderá a copiar los datos de formación en la instancia informática, extraer las caras de los datos de formación, entrenar la GAN con las caras extraídas y convertir el vídeo con la cara intercambiada.

Tiempo estimado del taller: 60 minutos (excluyendo entrenamiento modelo)

### Objetivos

*   Copie los datos de formación (fotos o vídeos con las caras que desea intercambiar) en la instancia informática
*   [Extracción](https://forum.faceswap.dev/viewtopic.php?f=25&t=27) de las caras de las fotos o los vídeos
*   [Entrenar](https://forum.faceswap.dev/viewtopic.php?f=27&t=146) la GAN con las caras extraídas
*   [Convierta](https://forum.faceswap.dev/viewtopic.php?f=24&t=1083) e intercambie la cara A en el vídeo de origen con la cara B

### ¿Qué se necesita?

*   Laboratorio completo 1-Laboratorio 4

## Tarea 1: Conexión al escritorio de VNC

1.  Cree un túnel SSH al servidor VNC con el siguiente comando (donde la clave privada es su clave OpenSSH privada y public\_ip la dirección IP privada de su instancia informática de Ubuntu):

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  Conéctese a **127.0.0.1:5901** mediante el cliente VNC. ![](images/tigervnc.PNG " ")
    
3.  Cuando se le solicite la contraseña, introduzca la contraseña creada durante la configuración del servidor VNC. ![](images/tigervncpwd.PNG " ")
    

Se abrirá el escritorio de Ubuntu remoto. ![](images/vncdesktop.PNG " ")

## Tarea 2: Espacio libre abierto en el escritorio de VNC

1.  Abra un terminal haciendo clic con el botón derecho en el escritorio y seleccionando **Abrir terminal aquí**. ![](images/open-terminal.PNG " ")
    
    El terminal se abrirá en el escritorio de VNC.
    
    ![](images/vnc-terminal.PNG " ")
    

Si no es posible abrir un terminal, ejecute los siguientes comandos en el terminal de su máquina local.

    <copy>sudo apt remove gnome-terminal</copy>
    

    <copy>sudo apt install gnome-terminal</copy>
    

2.  Navegue hasta la carpeta de intercambio de caras.

    <copy>cd cd ../faceswap/</copy>
    

Ejecute el siguiente comando para iniciar la GUI de Faceswap:

    <copy>bash faceswap_gui_launcher.sh</copy>
    

Se abrirá la GUI de Faceswap.

![](images/faceswapGUI.PNG " ")

## Tarea 3: Copia de los datos de formación en la instancia informática

Para entrenar el Faceswap GAN, debe proporcionar imágenes o un video de la persona A y la persona B de quien desea intercambiar caras. En este ejemplo, copiaremos dos vídeos de la persona A y la persona B desde nuestra máquina local a la instancia remota de Ubuntu mediante WinSCP.

1.  Descargue e instale [WinSCP](https://winscp.net/eng/download.php) en el equipo local.
    
2.  Abra WinSCP en la máquina local y abra la carpeta con los vídeos de la izquierda side.F ![](images/open-winscp.PNG " ")
    
3.  Haga clic en **Nuevo sitio**. Aparecerá una nueva ventana. ![](images/winscp-newsite.PNG " ")
    
4.  Rellene la _dirección IP_ de su instancia en el campo **hostname** y _Ubuntu_ en el campo **user name**, como se muestra a continuación. ![](images/winscp-hostname-username.PNG " ")
    
5.  Haga clic en el botón **Opciones avanzadas...**.
    
6.  Haga clic en **Autenticación** en **SSH** en la parte izquierda de la ventana.
    
7.  Haga clic en los tres puntos y seleccione su archivo de clave privada SSH en la carpeta, como se muestra a continuación. ![](images/winscp-ppk.PNG " ")
    
8.  Haga clic en **Aceptar**.
    
9.  Haga clic en **Conectar**.
    
10.  Aparece una pantalla de advertencia. Haga clic en **Sí**. ![](images/winscp-warning.PNG " ")
    
11.  Puede ver la carpeta de archivos locales a la izquierda y la carpeta de archivos remotos en la parte derecha de la herramienta WinSCP. ![](images/winscp-auth-success.PNG " ")
    
12.  Cree la carpeta **src** en la carpeta remota haciendo clic con el botón derecho en el campo de la derecha, seleccionando **Nuevo -> Directorio** y escribiendo el nombre de la carpeta **src**.
    
13.  Seleccione los dos vídeos del lado izquierdo (máquina local) y arrástrelos hasta la carpeta **src** del lado derecho (máquina remota).
    

## Tarea 4: Extraer las caras de las fotos y videos

El primer paso es extraer las caras del vídeo A y del vídeo B.

1.  En la GUI de Faceswap, seleccione el separador **Extract** (Extracción).
    
2.  En el campo **Datos**, puede ver **Directorio de entrada**, donde puede seleccionar el directorio en el que se encuentran el vídeo de entrada o las imágenes de entrada. Seleccionaremos un vídeo de entrada haciendo clic en el botón de video (primero, izquierdo) y seleccionando el vídeo de entrada de la persona A de nuestra carpeta **src**, acabamos de crear y rellenar en el paso 3.
    

![](images/GUI-inputdir.PNG " ")

**Nota:** Si extrae caras de imágenes de entrada, asegúrese de tener entre 1000 y 10.000 imágenes.

3.  Haga clic en el botón de carpeta junto a **Directorio de salida**. Seleccione la carpeta donde se guardarán las imágenes faciales extraídas del vídeo de origen. Puede llamar al directorio, por ejemplo, _faceswap/faces/personA_ y hacer clic en **Aceptar**.

![](images/GUI-outputdir.PNG " ")

4.  Opcional: si quieres leer más sobre los plugins de extracción y hacer uso de ellos, visita la [Guía de extracción con intercambio de caras](https://forum.faceswap.dev/viewtopic.php?t=27). En este ejemplo, mantendremos los plugins por defecto.
    
5.  Haga clic en **Extraer**. Verá las caras extraídas del vídeo en el separador **Vista previa** de la derecha. En el campo inferior, verá el estado de la secuencia de comandos de extracción de Python, por ejemplo, los plugins inicializados, las imágenes encontradas, las caras detectadas y, en caso de que haya errores.
    

![](images/GUI-extract.PNG " ")

6.  Repita del 1 al 5. desde el **paso 4** para la extracción facial de la persona B.
    
7.  Opcional: En caso de que no desee utilizar la GUI, puede ejecutar la extracción facial en el terminal con el siguiente comando (sólo ajuste los directorios de entrada y salida y los plugins, en caso de que desee utilizar otros plugins):
    

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py extract -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/faces/personA -D s3fd -A fan -nm none -rf 0 -min 0 -l 0.4 -sz 512 -een 1 -si 0 -L INFO</copy>
    

![](images/extract-terminal.PNG " ")

## Tarea 5: Entrenamiento de la GAN

Ahora que hemos extraído las caras para la persona A y la persona B de los videos, podemos entrenar la GAN Faceswap.

1.  En la GUI de Faceswap, seleccione el separador **Train** (Entrenar).
    
2.  En el campo **Faces**, verá **Input A**, donde puede seleccionar el directorio de las caras extraídas de la persona A (en nuestro caso, _faceswap/faces/personA_).
    

![](images/GUI-faces-input.PNG " ")

3.  A continuación, verá la fila **Alineaciones A**. El archivo de alineaciones contiene información sobre todas las caras que encuentra en cada marco, específicamente dónde está la cara, y dónde están los puntos de referencia (por ejemplo, ojos, nariz, labios). Se crea durante la fase de extracción y se almacena en la carpeta de origen del vídeo de la persona A. Haga clic en el botón Archivo y seleccione el archivo de alineaciones de la persona A en la carpeta **src**, como se muestra a continuación.

![](images/GUI-alignmentA.PNG " ")

4.  Repita los puntos 1 y 2 para la persona B.
    
5.  En el campo **Modelo**, haga clic en el botón de carpeta junto a **Directorio de modelo**. Seleccione la carpeta donde se guardará el modelo de entrenamiento. Puede llamar al directorio, por ejemplo, _faceswap/model_ y hacer clic en **OK** (Aceptar).
    
6.  Opcional: si desea obtener más información sobre el entrenador o las opciones de "Aumento de datos" y hacer uso de ellas, visite la [Guía de formación de intercambio de tráfico](https://forum.faceswap.dev/viewtopic.php?f=27&t=146). En este ejemplo, mantendremos los plugins por defecto.
    
7.  Haga clic en el botón **Entrenar** para iniciar la formación.
    

![](images/GUI-train.PNG " ")

En el separador **Vista previa** de la derecha, verá las caras intercambiadas en los marcos de vídeo de la persona A y la persona B.

El separador **Gráfico** contiene un gráfico que muestra la pérdida a lo largo del tiempo. Se actualiza cada vez que el modelo se guarda, pero se puede refrescar pulsando el botón "Refresh". Por cada lote de caras alimentadas al modelo, la GAN mirará la cara que ha intentado recrear y compararlo con la cara real que fue alimentada. Basándose en lo bien que piensa que ha hecho, se dará una puntuación (el valor de pérdida).

8.  Opcional: en caso de que no desee utilizar la GUI, puede ejecutar el entrenamiento de modelo en el terminal con el siguiente comando (sólo ajuste los directorios de entrada, los archivos de alineaciones, el directorio de modelo y la opción de entrenador en caso de que desee utilizar otra opción de entrenador):

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py train -A /home/ubuntu/faceswap/faces/personA -ala /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -B /home/ubuntu/faceswap/faces/personB -alb /home/ubuntu/faceswap/src/personB/personB_alignments.fsa -m /home/ubuntu/faceswap/model -t original -bs 16 -it 1000000 -s 250 -ss 25000 -ps 100 -L INFO</copy>
    

![](images/terminal-train.PNG " ")

La mejor forma de saber si un modelo ha terminado el entrenamiento es ver las vistas previas. En última instancia, estos muestran cómo será el intercambio real. También puede comprobar los valores de pérdida. Cuanto más cerca estén los valores de pérdida a cero, mayor será la probabilidad de un entrenamiento terminado.

![](images/graph-loss-iterations.PNG " ")

En el separador **Análisis**, puede comprobar el valor _EGs/sec_ en la última columna, que le proporcionará el número de caras procesadas a través del modelo por segundo. Cuanto más potentes sean los núcleos (GPU), mayor será _EGs/sec_ y más rápido convergerá el modelo.

9.  Compruebe el uso de GPU durante el entrenamiento con el comando nvidia-smi. ![](images/nvidia-smi-GPU.PNG " ")
    
    Le mostrará la utilización de GPU volátil.
    
10.  Opcional: es posible aumentar el rendimiento de la aplicación aumentando las velocidades de reloj de memoria y núcleo de GPU. Para obtener más información, visita la publicación del blog de Nvidia sobre [Nvidia GPU](https://developer.nvidia.com/blog/increase-performance-gpu-boost-k80-autoboost/).
    

## Tarea 6: Convierte tu vídeo con la cara intercambiada

Si tu entrenamiento ha terminado, puedes convertir tu video e intercambiar la cara A en tu video de origen con la cara B.

1.  En la GUI de Faceswap, seleccione el separador **Convert** (Convertir).
    
2.  En el campo **Datos**, verá el **Directorio de entrada**, donde puede seleccionar el directorio donde se encuentra el vídeo de entrada con la persona de la que desea intercambiar la cara. En nuestro caso, el vídeo de entrada se almacena en _/faceswap/src/personA_
    
3.  Haga clic en el botón de carpeta junto a **Directorio de salida**. Seleccione la carpeta donde se guardará el vídeo de salida. Puede llamar al directorio, por ejemplo, _/faceswap/outputvideo_ y hacer clic en **Aceptar**.
    
4.  Haga clic en el botón de archivo junto a **Alineaciones** y seleccione el archivo de alineaciones de la persona de la que desea intercambiar la cara (en nuestro caso, es decir, la persona A). Si el archivo de alineaciones está en el mismo directorio que el **Directorio de entrada**, puede dejarlo en blanco.
    
5.  Opcional: el **Vídeo de referencia** solo es necesario si está convirtiendo de imágenes a vídeo. En nuestro caso, lo dejaremos en blanco.
    
6.  En el campo **Modelo**, haga clic en el botón de carpeta junto a **Directorio de modelo**. Seleccione la carpeta donde se guarda el modelo de entrenamiento. En nuestro caso, ese es el directorio _faceswap/model_. Haga clic en **Aceptar**.
    
7.  Haga clic en **Convertir**.
    

![](images/GUI-convert.PNG " ")

8.  Opcional: En caso de que no desee utilizar la GUI, puede realizar la conversión en el terminal con el siguiente comando (solo ajustar los directorios de entrada y salida, archivo de alineaciones, directorio de modelo y plugins, en caso de que desee utilizar otros plugins):

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py convert -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/outputvideo -al /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -m /home/ubuntu/faceswap/model -c avg-color -M extended -w opencv -osc 100 -l 0.4 -j 0 -L INFO</copy>
    

![](images/convert-terminal.PNG " ")

9.  En el directorio _/faceswap/outputvideo_ encontrará las tramas del vídeo de entrada de la persona A con la cara intercambiada de la persona B. ![](images/outputvideo-dir.PNG " ")
    
    Utilizaremos el ffmpeg para construir un video a partir de los fotogramas individuales.
    
    Primero instalemos ffmpeg.
    
        <copy>sudo apt install ffmpeg</copy>
        
    
    Ahora podemos construir un vídeo con ffmpeg para combinar las tramas únicas en un vídeo donde realizaremos un bucle en todas las tramas personA contenidas en el directorio _outputvideo_, donde _%nd_ representa un número secuencial de n dígitos. En nuestro caso n=6.
    
        <copy>ffmpeg -i personA_%6d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4</copy>
        
10.  Vea el [vídeo de salida](https://www.youtube.com/watch?v=b-uKJ89QSnE)
    

## **Acuses de recibo**

*   **Creado por/Fecha**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021
*   **Fecha/fecha de última actualización**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021