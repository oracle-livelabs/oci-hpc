# Instalar Faceswap

## Introducción

En este laboratorio, instalaremos Faceswap en nuestra instancia informática. Seguiremos estrechamente la documentación de Faceswap para [instalar Faceswap](https://forum.faceswap.dev/viewtopic.php?f=4&t=68).

Tiempo estimado del taller: 30 minutos

**Nota:** En caso de que utilice una instancia de GPU, puede instalar una variedad de controladores y bibliotecas en el **paso 1** que sean necesarios para la compatibilidad con GPU de Tensorflow. Las versiones correctas de Cuda/cuDNN deben instalarse para la versión requerida de Tensorflow. Consulte las [configuraciones de compilación probadas](https://www.tensorflow.org/install/source#linux) para conocer qué versiones de CUDA y cuDNN utilizar con las respectivas versiones TensorFlow. Para tensorflow-2.4.0, necesitamos instalar CUDA 11.0.

### Objetivos

*   Opcional: prepare su instancia de GPU para garantizar el soporte de GPU de Tensorflow
*   Instalar Faceswap en nuestra máquina Ubuntu

### ¿Qué se necesita?

*   Laboratorio completo 1-Laboratorio 3

## Tarea 1: Instalación del kit de herramientas de CUDA (opcional)

En este paso, instalaremos el kit de herramientas de CUDA que incluye el SDK de CUDA, las herramientas de GUI y el controlador Nvidia.

Instalaremos el kit de herramientas de CUDA 11.0 (incluido CUDA 11.0) requerido para tensorflow-2.4.0 que se instalará en el **paso 2** durante la instalación Faceswap.

**Nota:** Para futuras versiones de Faceswap, asegúrese siempre de comprobar qué versión de tensorflow se va a instalar y qué versión de CUDA se admite para esa versión de tensorflow (consulte [configuraciones de compilación probadas](https://www.tensorflow.org/install/source#linux))

1.  Introduzca el siguiente comando en su terminal para acceder a su instancia informática de Ubuntu en la que necesita sustituir _private-key_ por su clave privada OpenSSH e _public ip_ por la dirección IP pública de su instancia de Ubuntu.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

2.  Seguiremos estrechamente la documentación del desarrollador de Nvidia para la [descarga e instalación del kit de herramientas de CUDA 11.0](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

    <copy>
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
    sudo apt-get update
    sudo apt-get -y install cuda-11.0
    </copy>
    

3.  Compruebe que las GPU estén visibles y que CUDA 11.0 se haya instalado correctamente mediante el comando:

       <copy>nvidia-smi</copy>
    

La salida de nvidia-smi se verá así:

![](images/nvidia-smi.PNG " ")

## Tarea 2: Instalación de Faceswap

1.  Descargue la última versión del instalador Faceswap.

       <copy>wget https://github.com/deepfakes/faceswap/releases/latest/download/faceswap_setup_x64.sh</copy>
    

2.  Navegue hasta la ubicación de descarga e introduzca el siguiente comando:

       <copy>bash ./faceswap_setup_x64.sh</copy>
    

Siga los pasos de configuración de la [Guía de instalación de intercambio de caras](https://forum.faceswap.dev/viewtopic.php?f=4&t=68)

## **Acuses de recibo**

*   **Creado por/Fecha**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021
*   **Fecha/fecha de última actualización**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021