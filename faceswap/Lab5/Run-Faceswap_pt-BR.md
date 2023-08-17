# Run Faceswap

## Introdução

Agora que preparamos nossa instância de computação (consulte o Laboratório 4), podemos prosseguir e executar o Faceswap. Em um laboratório, você aprenderá a copiar seus dados de treinamento para a instância de computação, extrair as faces dos dados de treinamento, treinar o GAN com as faces extraídas e converter seu vídeo com a face trocada.

Tempo estimado do workshop: 60 minutos (treinamento do modelo de estudo)

### Objetivos

*   Copie seus dados de treinamento (fotos ou vídeos com os rostos que você deseja trocar) para a instância de computação
*   [Extraia](https://forum.faceswap.dev/viewtopic.php?f=25&t=27) os rostos das fotos ou vídeos
*   [Treinar](https://forum.faceswap.dev/viewtopic.php?f=27&t=146) o GAN com as faces extraídas
*   [Converter](https://forum.faceswap.dev/viewtopic.php?f=24&t=1083) e trocar o rosto A em seu vídeo de origem com o rosto B

### O que você precisa?

*   Preencha o Laboratório 1 até o Laboratório 4

## Tarefa 1: Estabelecer conexão com o VNC Desktop

1.  Crie um túnel SSH para o servidor VNC usando o seguinte comando (em que a chave privada é sua chave OpenSSH privada e public\_ip o endereço IP privado da sua instância de computação Ubuntu):

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  Conecte-se a **127.0.0.1:5901** usando seu cliente VNC. ![](images/tigervnc.PNG " ")
    
3.  Quando for solicitada a senha, informe a senha criada durante a configuração do servidor VNC. ![](images/tigervncpwd.PNG " ")
    

Seu desktop Ubuntu remoto será aberto. ![](images/vncdesktop.PNG " ")

## Tarefa 2: Abrir Faceswap no VNC Desktop

1.  Abra um terminal clicando com o botão direito do mouse na área de trabalho e selecionando **Abrir Terminal aqui**. ![](images/open-terminal.PNG " ")
    
    O terminal será aberto na sua área de trabalho VNC.
    
    ![](images/vnc-terminal.PNG " ")
    

Se não for possível abrir um terminal, execute os comandos a seguir no terminal da sua máquina local.

    <copy>sudo apt remove gnome-terminal</copy>
    

    <copy>sudo apt install gnome-terminal</copy>
    

2.  Navegue até a pasta faceswap.

    <copy>cd cd ../faceswap/</copy>
    

Inicie a GUI do Faceswap executando o seguinte comando:

    <copy>bash faceswap_gui_launcher.sh</copy>
    

A GUI do Faceswap é aberta.

![](images/faceswapGUI.PNG " ")

## Tarefa 3: Copiar seus dados de treinamento para a instância de computação

Para treinar o Faceswap GAN, você precisa fornecer fotos ou um vídeo da pessoa A e da pessoa B de quem você deseja trocar de rostos. Neste exemplo, copiaremos dois vídeos da pessoa A e da pessoa B da nossa máquina local para a instância remota do Ubuntu usando WinSCP.

1.  Faça download e instale [WinSCP](https://winscp.net/eng/download.php) na sua máquina local.
    
2.  Abra WinSCP na sua máquina local e abra a pasta com os vídeos à esquerda side.F ![](images/open-winscp.PNG " ")
    
3.  Clique em **Novo Site**. Uma nova janela será exibida. ![](images/winscp-newsite.PNG " ")
    
4.  Preencha o _endereço IP_ da sua instância no campo **hostname** e _Ubuntu_ no campo **nome do usuário**, conforme mostrado abaixo. ![](images/winscp-hostname-username.PNG " ")
    
5.  Clique no botão **Advanced...**.
    
6.  Clique em **Autenticação** em **SSH** na parte esquerda da janela.
    
7.  Clique nos três pontos e selecione seu arquivo de chave privada SSH na sua pasta, conforme mostrado abaixo. ![](images/winscp-ppk.PNG " ")
    
8.  Clique em **OK**
    
9.  Clique em **Login**.
    
10.  Uma tela de aviso é exibida. Clique em **Sim**. ![](images/winscp-warning.PNG " ")
    
11.  Você pode ver sua pasta de arquivos locais à esquerda e sua pasta de arquivos remotos na parte direita da ferramenta WinSCP. ![](images/winscp-auth-success.PNG " ")
    
12.  Crie a pasta **src** na sua pasta remota clicando com o botão direito do mouse no campo à direita, selecionando **New -> Directory** e digitando o nome da sua pasta **src**.
    
13.  Selecione seus dois vídeos no lado esquerdo (máquina local) e arraste-os para a pasta **src** no lado direito (máquina remota).
    

## Tarefa 4: Extraia os rostos das fotos e vídeos

O primeiro passo é extrair as faces do vídeo A e do vídeo B.

1.  Na GUI do Faceswap, selecione a guia **Extrair**.
    
2.  No campo **Dados**, você vê **Dir de Entrada**, no qual pode selecionar o diretório no qual suas imagens de entrada ou vídeo de entrada estão localizadas. Selecionaremos um vídeo de entrada clicando no botão de vídeo (primeiro à esquerda) e selecionando o vídeo de entrada da pessoa A na nossa pasta **src**, recém-criamos e preenchemos na etapa 3.
    

![](images/GUI-inputdir.PNG " ")

**Observação:** Se você extrair faces de imagens de entrada, certifique-se de ter entre 1000 e 10.000 imagens.

3.  Clique no botão de pasta ao lado de **Dir de Saída**. Escolha a pasta, onde as imagens de rosto extraídas do vídeo de origem serão salvas. Você pode chamar o diretório, por exemplo, _faceswap/faces/personA_ e clicar em **OK**.

![](images/GUI-outputdir.PNG " ")

4.  Opcional: Se você quiser ler mais sobre os plug-ins de extração e usá-los, visite o [Faceswap Extraction Guide](https://forum.faceswap.dev/viewtopic.php?t=27). Neste exemplo, manteremos os plug-ins padrão.
    
5.  Clique em **Extrair**. Você verá os rostos extraídos do vídeo na guia **Visualizar** à direita. No campo inferior, você vê o estado do script de extração do Python, por exemplo, os plug-ins inicializados, imagens encontradas, rostos detectados e, caso haja erros.
    

![](images/GUI-extract.PNG " ")

6.  Repita 1.-5. da **etapa 4** para a extração de cara da pessoa B.
    
7.  Opcional: Caso não queira usar a GUI, você poderá executar a extração de face no terminal com o seguinte comando (apenas ajustar os diretórios e plug-ins de entrada e saída, caso deseje usar outros plug-ins):
    

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py extract -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/faces/personA -D s3fd -A fan -nm none -rf 0 -min 0 -l 0.4 -sz 512 -een 1 -si 0 -L INFO</copy>
    

![](images/extract-terminal.PNG " ")

## Tarefa 5: Treinar o GAN

Agora que extraímos os rostos da pessoa A e da pessoa B dos vídeos, podemos treinar o GAN Faceswap.

1.  Na GUI do Faceswap, selecione a guia **Treinar**.
    
2.  No campo **Faces**, você vê **Entrada A**, onde seleciona o diretório das faces extraídas da pessoa A (no nosso caso, _faceswap/faces/personA_).
    

![](images/GUI-faces-input.PNG " ")

3.  Abaixo, você vê a linha **Alinhamentos A**. O arquivo de alinhamentos contém informações sobre todas as faces que encontra em cada quadro, especificamente onde está o rosto e onde estão os pontos de referência (por exemplo, olhos, nariz, lábios). Ele é criado durante a fase de extração e é armazenado na pasta de origem do vídeo da pessoa A. Clique no botão do arquivo e selecione o arquivo de alinhamentos da pessoa A na pasta **src**, conforme mostrado abaixo.

![](images/GUI-alignmentA.PNG " ")

4.  Repita os pontos 1. e 2. para a pessoa B.
    
5.  No campo **Modelo**, clique no botão de pasta ao lado de **Dir do Modelo**. Escolha a pasta em que o modelo de treinamento será salvo. Você pode chamar o diretório, por exemplo, _faceswap/model_ e clicar em **OK**.
    
6.  Opcional: Se você quiser ler mais sobre o instrutor ou as opções de "Aumento de Dados" e usá-las, visite o [Guia de Treinamento do Faceswap](https://forum.faceswap.dev/viewtopic.php?f=27&t=146). Neste exemplo, manteremos os plug-ins padrão.
    
7.  Clique no botão **Treinar** para iniciar o treinamento.
    

![](images/GUI-train.PNG " ")

Na guia **Visualizar** à direita, você verá as faces trocadas nos quadros de vídeo da pessoa A e da pessoa B.

A guia **Gráfico** contém um gráfico que mostra a perda ao longo do tempo. Ele é atualizado toda vez que o modelo é salvo, mas pode ser atualizado pressionando o botão "Atualizar". Para cada lote de rostos alimentados no modelo, o GAN analisará o rosto que tentou recriá-lo e compará-lo com o rosto real que foi alimentado. Com base no quão bem ele acha que fez, ele dará a si mesmo uma pontuação (o valor da perda).

8.  Opcional: Caso não queira usar a GUI, você poderá executar o treinamento de modelo no terminal com o seguinte comando (ajuste somente os diretórios de entrada, os arquivos de alinhamento, o diretório de modelo e a opção de instrutor caso deseje usar outra opção de instrutor):

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py train -A /home/ubuntu/faceswap/faces/personA -ala /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -B /home/ubuntu/faceswap/faces/personB -alb /home/ubuntu/faceswap/src/personB/personB_alignments.fsa -m /home/ubuntu/faceswap/model -t original -bs 16 -it 1000000 -s 250 -ss 25000 -ps 100 -L INFO</copy>
    

![](images/terminal-train.PNG " ")

A melhor maneira de saber se um modelo terminou o treinamento é assistir às visualizações. Em última análise, eles mostram como será a permuta real. Também é possível verificar os valores de perda. Quanto mais próximos os valores de perda forem de zero, maior a probabilidade de um treinamento concluído.

![](images/graph-loss-iterations.PNG " ")

Na guia **Análise**, você pode verificar o valor _EGs/sec_ na última coluna que fornecerá o número de faces processadas pelo modelo por segundo. Quanto mais poderosos forem seus núcleos (GPU), mais altos serão os _EGs/sec_ e mais rápido o modelo será convergido.

9.  Verifique o uso da GPU durante o treinamento com o comando nvidia-smi. ![](images/nvidia-smi-GPU.PNG " ")
    
    Ele mostrará a utilização volátil de GPU.
    
10.  Opcional: é possível aumentar o desempenho do aplicativo aumentando as taxas de núcleo de GPU e de clock de memória. Visite a publicação no blog da Nvidia sobre [aumento de GPU da Nvidia](https://developer.nvidia.com/blog/increase-performance-gpu-boost-k80-autoboost/) para obter mais informações.
    

## Tarefa 6: Converta seu vídeo com o rosto trocado

Se seu treinamento estiver concluído, você poderá converter seu vídeo e trocar o rosto A em seu vídeo de origem com o rosto B.

1.  Na GUI do Faceswap, selecione a guia **Converter**.
    
2.  No campo **Dados**, você vê o **Dir de Entrada** no qual seleciona o diretório em que o vídeo de entrada está localizado com a pessoa de quem você deseja trocar o rosto. No nosso caso, o vídeo de entrada é armazenado em _/faceswap/src/personA_
    
3.  Clique no botão de pasta ao lado de **Dir de Saída**. Escolha a pasta na qual o vídeo de saída será salvo. Você pode chamar o diretório, por exemplo, _/faceswap/outputvideo_ e clicar em **OK**.
    
4.  Clique no botão de arquivo ao lado de **Alinhamentos** e escolha o arquivo de alinhamentos da pessoa de quem você deseja trocar o rosto (no nosso caso, essa é a pessoa A). Se o arquivo de alinhamentos estiver no mesmo diretório do **Dir de Entrada**, você poderá deixá-lo em branco.
    
5.  Opcional: o **Vídeo de Referência** só será necessário se você estiver convertendo de imagens em vídeo. No nosso caso, deixaremos em branco.
    
6.  No campo **Modelo**, clique no botão de pasta ao lado de **Dir do Modelo**. Escolha a pasta em que o modelo de treinamento será salvo. No nosso caso, esse é o diretório _faceswap/model_. Clique em **OK**.
    
7.  Clique em **Converter**.
    

![](images/GUI-convert.PNG " ")

8.  Opcional: Caso você não queira usar a GUI, poderá fazer a conversão no terminal com o seguinte comando (somente ajustar os diretórios de entrada e saída, o arquivo de alinhamentos, o diretório de modelos e os plug-ins, caso deseje usar outros plug-ins):

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py convert -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/outputvideo -al /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -m /home/ubuntu/faceswap/model -c avg-color -M extended -w opencv -osc 100 -l 0.4 -j 0 -L INFO</copy>
    

![](images/convert-terminal.PNG " ")

9.  No diretório _/faceswap/outputvideo_ você encontrará os quadros do vídeo de entrada da pessoa A com a face trocada da pessoa B. ![](images/outputvideo-dir.PNG " ")
    
    Usaremos o ffmpeg para construir um vídeo a partir dos quadros únicos.
    
    Vamos instalar o ffmpeg primeiro.
    
        <copy>sudo apt install ffmpeg</copy>
        
    
    Agora podemos construir um vídeo com ffmpeg para combinar os quadros únicos em um vídeo em que percorreremos todos os quadros personA contidos no diretório _outputvideo_ em que _%nd_ representa um número sequencial de n dígitos. No nosso caso n=6.
    
        <copy>ffmpeg -i personA_%6d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4</copy>
        
10.  Assista ao [vídeo de saída](https://www.youtube.com/watch?v=b-uKJ89QSnE)
    

## **Confirmação**

*   **Criado por/Data** - Maria Patelkou, Arquiteto de Soluções HPC, programa Oracle Proposal to Production, março de 2021
*   **Data da Última Atualização** - Maria Patelkou, Arquiteto de Soluções HPC, programa Proposta para Produção da Oracle, março de 2021