# Installer Faceswap

## Présentation

Dans cet exercice, nous allons installer Faceswap sur notre instance de calcul. Nous suivrons de près la documentation Faceswap pour [installer Faceswap](https://forum.faceswap.dev/viewtopic.php?f=4&t=68).

Durée estimée de l'atelier : 30 minutes

**Remarque :** si vous utilisez une instance de GPU, vous pouvez installer un ensemble de pilotes et de bibliothèques dans l'**étape 1** requis pour la prise en charge de GPU Tensorflow. Les versions correctes de Cuda/cuDNN doivent être installées pour la version requise de Tensorflow. Reportez-vous aux [configurations de build testées](https://www.tensorflow.org/install/source#linux) pour savoir quelles versions de CUDA et cuDNN utiliser avec les versions respectives de TensorFlow. Pour le flux de traction-2.4.0, nous devons installer CUDA 11.0.

### Objectifs

*   Facultatif : préparez votre instance de GPU pour assurer la prise en charge des GPU Tensorflow
*   Installez Faceswap sur notre machine Ubuntu.

### Eléments requis

*   Exercice 1 terminé à l'atelier 3

## Tâche 1 : installation du toolkit CUDA (facultatif)

Cette étape consiste à installer le toolkit CUDA incluant le kit SDK CUDA, les outils d'interface graphique et le pilote Nvidia.

Nous installerons la boîte à outils CUDA 11.0 (y compris CUDA 11.0) requise pour le flux de traction-2.4.0 qui sera installée à l'**étape 2** lors de l'installation de Faceswap.

**Remarque :** pour les prochaines versions de Faceswap, assurez-vous de toujours vérifier la version du tenseur à installer et la version de CUDA prise en charge pour cette version du tenseur (voir [configurations de build testées](https://www.tensorflow.org/install/source#linux)).

1.  Entrez la commande suivante dans votre terminal pour accéder à votre instance de calcul Ubuntu où vous devez remplacer _private-key_ par votre clé privée OpenSSH et votre _adresse IP publique_ par l'adresse IP publique de votre instance Ubuntu.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

2.  Nous suivrons de près la documentation destinée aux développeurs Nvidia pour [le téléchargement et l'installation du toolkit CUDA 11.0](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

    <copy>
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
    sudo apt-get update
    sudo apt-get -y install cuda-11.0
    </copy>
    

3.  Vérifiez que les GPU sont visibles et que CUDA 11.0 a été installé avec succès à l'aide de la commande :

       <copy>nvidia-smi</copy>
    

La sortie nvidia-smi se présente comme suit :

![](images/nvidia-smi.PNG " ")

## Tâche 2 : installation de Faceswap

1.  Téléchargez la dernière version du programme d'installation de Faceswap.

       <copy>wget https://github.com/deepfakes/faceswap/releases/latest/download/faceswap_setup_x64.sh</copy>
    

2.  Accédez à l'emplacement de téléchargement et entrez la commande suivante :

       <copy>bash ./faceswap_setup_x64.sh</copy>
    

Suivez les étapes de configuration du [Guide d'installation de Faceswap](https://forum.faceswap.dev/viewtopic.php?f=4&t=68).

## **Accusés de réception**

*   **Created By/Date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021
*   **Dernière mise à jour le/la date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021