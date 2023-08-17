# Faceswap installieren

## Einführung

In dieser Übung installieren wir Faceswap auf unserer Compute-Instanz. Wir befolgen die Faceswap-Dokumentation genau, um [Faceswap zu installieren](https://forum.faceswap.dev/viewtopic.php?f=4&t=68).

Geschätzte Workshopzeit: 30 Minuten

**Hinweis:** Wenn Sie eine GPU-Instanz verwenden, können Sie in **Schritt 1** ein Sortiment von Treibern und Librarys installieren, die für die Tensorflow-GPU-Unterstützung erforderlich sind. Für die erforderliche Version von Tensorflow müssen die korrekten Versionen von CUDA/cuDNN installiert werden. In den [getesteten Build-Konfigurationen](https://www.tensorflow.org/install/source#linux) erfahren Sie, welche CUDA- und cuDNN-Versionen mit den jeweiligen TensorFlow-Releases verwendet werden sollen. Für tensorflow-2.4.0 müssen wir CUDA 11.0 installieren.

### Ziele

*   Optional: GPU-Instanz vorbereiten, um Tensorflow-GPU-Unterstützung sicherzustellen
*   Faceswap auf unserem Ubuntu-Rechner installieren

### Was benötigen Sie?

*   Übung 1 zu Übung 3 abschließen

## Aufgabe 1: CUDA-Toolkit installieren (optional)

In diesem Schritt installieren wir das CUDA Toolkit, das das CUDA SDK, GUI-Tools und den Nvidia-Treiber enthält.

Wir installieren das CUDA Toolkit 11.0 (einschließlich CUDA 11.0), das für Tensorflow-2.4.0 erforderlich ist und während der Faceswap-Installation in **Schritt 2** installiert wird.

**Hinweis:** Bei zukünftigen Releases von Faceswap müssen Sie immer prüfen, welche Version von tensorflow installiert werden soll und welche CUDA-Version für diese Tensorflow-Version unterstützt wird (siehe [Tested Build-Konfigurationen](https://www.tensorflow.org/install/source#linux)).

1.  Geben Sie den folgenden Befehl in Ihrem Terminal ein, um auf die Ubuntu-Compute-Instanz zuzugreifen, bei der Sie _private-key_ durch den Private Key OpenSSH und die öffentliche IP-Adresse _public IP_ durch die öffentliche IP-Adresse Ihrer Ubuntu-Instanz ersetzen müssen.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

2.  Wir befolgen genau die Nvidia-Entwicklerdokumentation für den [Download und die Installation von CUDA Toolkit 11.0](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

    <copy>
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
    sudo apt-get update
    sudo apt-get -y install cuda-11.0
    </copy>
    

3.  Prüfen Sie, ob GPUs sichtbar sind und CUDA 11.0 erfolgreich mit dem folgenden Befehl installiert wurde:

       <copy>nvidia-smi</copy>
    

Die nvidia-smi-Ausgabe wird wie folgt aussehen:

![](images/nvidia-smi.PNG " ")

## Aufgabe 2: Faceswap installieren

1.  Laden Sie die neueste Version des Faceswap-Installationsprogramms herunter.

       <copy>wget https://github.com/deepfakes/faceswap/releases/latest/download/faceswap_setup_x64.sh</copy>
    

2.  Navigieren Sie zum Downloadverzeichnis, und geben Sie den folgenden Befehl ein:

       <copy>bash ./faceswap_setup_x64.sh</copy>
    

Befolgen Sie die Konfigurationsschritte im [Faceswap-Installationshandbuch](https://forum.faceswap.dev/viewtopic.php?f=4&t=68)

## **Bestätigungen**

*   **Erstellt von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021
*   **Zuletzt aktualisiert von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021