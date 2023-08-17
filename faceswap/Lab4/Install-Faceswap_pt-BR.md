# Instalar Faceswap

## Introdução

Neste laboratório, vamos instalar o Faceswap em nossa instância de computação. Vamos seguir de perto a documentação do Faceswap para [install Faceswap](https://forum.faceswap.dev/viewtopic.php?f=4&t=68).

Tempo estimado do workshop: 30 minutos

**Observação:** caso você esteja usando uma instância GPU, poderá instalar uma variedade de drivers e bibliotecas na **etapa 1** que são necessários para o suporte de GPU Tensorflow. As versões corretas do Cuda/cuDNN precisam ser instaladas para a versão necessária do Tensorflow. Consulte as [configurações de build testadas](https://www.tensorflow.org/install/source#linux) para saber quais versões do CUDA e cuDNN usar com as respectivas releases TensorFlow. Para tensorflow-2.4.0, precisamos instalar o CUDA 11.0.

### Objetivos

*   Opcional: Prepare sua instância de GPU para garantir o suporte a GPU Tensorflow
*   Instale o Faceswap em nossa máquina Ubuntu

### O que você precisa?

*   Preencha o Laboratório 1 até o Laboratório 3

## Tarefa 1: Instalar o CUDA Toolkit (opcional)

Nesta etapa, instalaremos o Kit de Ferramentas CUDA que inclui o SDK CUDA, as ferramentas GUI e o driver Nvidia.

Instalaremos o CUDA Toolkit 11.0 (incluindo o CUDA 11.0) necessário para o tensorflow-2.4.0 que será instalado na **etapa 2** durante a instalação do Faceswap.

**Observação:** Para releases futuras do Faceswap, sempre verifique qual versão do tensorflow será instalada e qual versão do CUDA é suportada para essa versão do tensorflow (consulte [configurações de build testadas](https://www.tensorflow.org/install/source#linux))

1.  Digite o comando a seguir em seu terminal para acessar sua instância de computação Ubuntu na qual você precisa substituir a _chave privada_ pela sua chave privada OpenSSH e o _ip público_ pelo endereço IP público da sua instância do Ubuntu.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

2.  Seguiremos de perto a documentação do desenvolvedor da Nvidia para [baixar e instalar o CUDA Toolkit 11.0](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

    <copy>
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
    sudo apt-get update
    sudo apt-get -y install cuda-11.0
    </copy>
    

3.  Verifique se as GPUs estão visíveis e se o CUDA 11.0 foi instalado com sucesso usando o comando:

       <copy>nvidia-smi</copy>
    

A saída de nvidia-smi terá esta aparência:

![](images/nvidia-smi.PNG " ")

## Tarefa 2: Instalar Faceswap

1.  Faça download da versão mais recente do instalador do Faceswap.

       <copy>wget https://github.com/deepfakes/faceswap/releases/latest/download/faceswap_setup_x64.sh</copy>
    

2.  Navegue até o local de download e insira o seguinte comando:

       <copy>bash ./faceswap_setup_x64.sh</copy>
    

Siga as etapas de configuração do [Faceswap Install Guide](https://forum.faceswap.dev/viewtopic.php?f=4&t=68)

## **Confirmação**

*   **Criado por/Data** - Maria Patelkou, Arquiteto de Soluções HPC, programa Oracle Proposal to Production, março de 2021
*   **Data da Última Atualização** - Maria Patelkou, Arquiteto de Soluções HPC, programa Proposta para Produção da Oracle, março de 2021