# Exécuter Faceswap

## Présentation

Maintenant que nous avons préparé notre instance de calcul (voir l'atelier 4), nous pouvons continuer et exécuter Faceswap. Au cours de l'exercice, vous allez apprendre à copier les données d'entraînement vers l'instance de calcul, à extraire les faces des données d'entraînement, à entraîner le GAN avec les faces extraites et à convertir votre vidéo avec la face permutée.

Durée estimée de l'atelier : 60 minutes (exclus l'entraînement du modèle)

### Objectifs

*   Copier vos données de formation (photos ou vidéos avec les faces à échanger) vers l'instance de calcul
*   [Extraire](https://forum.faceswap.dev/viewtopic.php?f=25&t=27) les visages des photos ou vidéos
*   [Entraîner](https://forum.faceswap.dev/viewtopic.php?f=27&t=146) le GAN avec les faces extraites
*   [Convertir](https://forum.faceswap.dev/viewtopic.php?f=24&t=1083) et échanger la face A dans votre vidéo source avec la face B

### Eléments requis

*   Exercice 1 terminé à l'atelier 4

## Tâche 1 : connexion au bureau VNC

1.  Créez un tunnel SSH vers le serveur VNC à l'aide de la commande suivante (où la clé privée est votre clé OpenSSH privée et public\_ip l'adresse IP privée de votre instance de calcul Ubuntu) :

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  Connectez-vous à **127.0.0.1 :5901** à l'aide de votre client VNC. ![](images/tigervnc.PNG " ")
    
3.  Lorsque vous êtes invité à saisir un mot de passe, saisissez le mot de passe créé lors de la configuration du serveur VNC. ![](images/tigervncpwd.PNG " ")
    

Votre bureau Ubuntu distant s'ouvrira. ![](images/vncdesktop.PNG " ")

## Tâche 2 : ouverture de Faceswap sur le bureau VNC

1.  Ouvrez un terminal en cliquant avec le bouton droit de la souris sur le bureau et en sélectionnant **Open Terminal here** (Ouvrir le terminal ici). ![](images/open-terminal.PNG " ")
    
    Le terminal s'ouvre sur le bureau VNC.
    
    ![](images/vnc-terminal.PNG " ")
    

S'il n'est pas possible d'ouvrir un terminal, exécutez les commandes suivantes dans le terminal sur votre ordinateur local.

    <copy>sudo apt remove gnome-terminal</copy>
    

    <copy>sudo apt install gnome-terminal</copy>
    

2.  Accédez au dossier faceswap.

    <copy>cd cd ../faceswap/</copy>
    

Lancez l'interface graphique Faceswap en exécutant la commande suivante :

    <copy>bash faceswap_gui_launcher.sh</copy>
    

L'interface graphique de Faceswap s'ouvre.

![](images/faceswapGUI.PNG " ")

## Tâche 3 : copie des données de formation dans l'instance de calcul

Pour former le Faceswap GAN, vous devez fournir des images ou une vidéo de la personne A et de la personne B de qui vous voulez échanger des visages. Dans cet exemple, nous allons copier deux vidéos de la personne A et de la personne B de notre machine locale vers l'instance Ubuntu distante à l'aide de WinSCP.

1.  Téléchargez et installez [WinSCP](https://winscp.net/eng/download.php) sur votre ordinateur local.
    
2.  Ouvrez WinSCP sur votre ordinateur local et ouvrez le dossier avec les vidéos sur la gauche side.F ![](images/open-winscp.PNG " ")
    
3.  Cliquez sur **Nouveau site**. Une nouvelle fenêtre apparaît. ![](images/winscp-newsite.PNG " ")
    
4.  Renseignez l'_adresse IP_ de votre instance dans le champ **hostname** et _Ubuntu_ dans le champ **user name** comme indiqué ci-dessous. ![](images/winscp-hostname-username.PNG " ")
    
5.  Cliquez sur le bouton **Avancé...**.
    
6.  Cliquez sur **Authentication** sous **SSH** dans la partie gauche de la fenêtre.
    
7.  Cliquez sur les trois points et sélectionnez votre fichier de clés privées SSH dans votre dossier comme indiqué ci-dessous. ![](images/winscp-ppk.PNG " ")
    
8.  Cliquez sur **OK**.
    
9.  Cliquez sur **Connexion**.
    
10.  Un écran d'avertissement s'affiche. Cliquez sur **Yes**. ![](images/winscp-warning.PNG " ")
    
11.  Vous pouvez voir votre dossier de fichiers locaux à gauche et votre dossier de fichiers distants à droite de l'outil WinSCP. ![](images/winscp-auth-success.PNG " ")
    
12.  Créez le dossier **src** dans votre dossier distant en cliquant avec le bouton droit de la souris dans le champ situé à droite, en sélectionnant **New -> Directory** et en saisissant le nom de votre dossier **src**.
    
13.  Sélectionnez vos deux vidéos sur le côté gauche (ordinateur local) et faites-les glisser vers le dossier **src** sur le côté droit (ordinateur distant).
    

## Tâche 4 : Extraire les visages des photos et des vidéos

La première étape consiste à extraire les faces de la vidéo A et de la vidéo B.

1.  Dans l'interface graphique de Faceswap, sélectionnez l'onglet **Extract**.
    
2.  Dans le champ **Données**, vous voyez **Dir d'entrée** où vous pouvez sélectionner le répertoire dans lequel se trouve votre vidéo d'entrée ou vos images d'entrée. Nous allons sélectionner une vidéo d'entrée en cliquant sur le bouton vidéo (premier, gauche) et en sélectionnant la vidéo d'entrée de la personne A dans notre dossier **src**, nous venons de la créer et de la remplir à l'étape 3.
    

![](images/GUI-inputdir.PNG " ")

**Remarque :** si vous extrayez des faces d'images d'entrée, assurez-vous que vous avez entre 1000 et 10 000 images.

3.  Cliquez sur le bouton de dossier en regard de **Répertoire de sortie**. Choisissez le dossier dans lequel les images faciales extraites de la vidéo source seront enregistrées. Vous pouvez appeler le répertoire, par exemple _faceswap/faces/personA_, puis cliquer sur **OK**.

![](images/GUI-outputdir.PNG " ")

4.  Facultatif : si vous voulez en savoir plus sur les plugins d'extraction et en utiliser, veuillez visiter le [Guide d'extraction de Faceswap](https://forum.faceswap.dev/viewtopic.php?t=27). Dans cet exemple, nous allons nous en tenir aux plugins par défaut.
    
5.  Cliquez sur l'**extraction**. Les faces extraites de la vidéo apparaissent dans l'onglet **Aperçu** à droite. Dans le champ inférieur, vous voyez l'état du script d'extraction Python, par exemple les plug-ins initialisés, les images trouvées, les faces détectées et en cas d'erreurs.
    

![](images/GUI-extract.PNG " ")

6.  Répétez 1 à 5 à partir de l'**étape 4** pour l'extraction de visage de la personne B.
    
7.  Facultatif : si vous ne souhaitez pas utiliser l'interface graphique, vous pouvez exécuter l'extraction de la face dans le terminal à l'aide de la commande suivante (ajustez uniquement les répertoires d'entrée et de sortie et les plug-ins, si vous souhaitez utiliser d'autres plug-ins) :
    

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py extract -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/faces/personA -D s3fd -A fan -nm none -rf 0 -min 0 -l 0.4 -sz 512 -een 1 -si 0 -L INFO</copy>
    

![](images/extract-terminal.PNG " ")

## Tâche 5 : Entraînement du GAN

Maintenant que nous avons extrait les visages pour la personne A et la personne B des vidéos, nous pouvons former le Faceswap GAN.

1.  Dans l'interface graphique de Faceswap, sélectionnez l'onglet **Train**.
    
2.  Dans le champ **Faces**, vous voyez **Input A** où vous sélectionnez le répertoire des faces extraites de la personne A (dans notre cas, _faceswap/faces/personA_).
    

![](images/GUI-faces-input.PNG " ")

3.  La ligne **Alignements A** s'affiche ci-dessous. Le fichier d'alignements contient des informations sur tous les visages qu'il trouve dans chaque cadre, en particulier l'endroit où se trouve le visage et l'endroit où se trouvent les repères des points (par exemple les yeux, le nez, les lèvres). Il est créé pendant la phase d'extraction et est stocké dans le dossier source de la vidéo de la personne A. Cliquez sur le bouton de fichier et sélectionnez le fichier d'alignements pour la personne A dans le dossier **src** comme indiqué ci-dessous.

![](images/GUI-alignmentA.PNG " ")

4.  Répétez les points 1. et 2 pour la personne B.
    
5.  Dans le champ **Modèle**, cliquez sur le bouton de dossier en regard de **Répertoire du modèle**. Choisissez le dossier dans lequel le modèle d'entraînement sera enregistré. Vous pouvez appeler le répertoire, par exemple _faceswap/model_, puis cliquer sur **OK**.
    
6.  Facultatif : si vous souhaitez en savoir plus sur les options Formateur ou "Augmentation des données" et en utiliser, reportez-vous au [Guide de formation Faceswap](https://forum.faceswap.dev/viewtopic.php?f=27&t=146). Dans cet exemple, nous allons nous en tenir aux plugins par défaut.
    
7.  Cliquez sur le bouton **Formation** pour démarrer la formation.
    

![](images/GUI-train.PNG " ")

Dans l'onglet **Aperçu** à droite, vous verrez les faces échangées dans les cadres vidéo de la personne A et de la personne B.

L'onglet **Graphique** contient un graphique indiquant la perte dans le temps. Il est mis à jour à chaque enregistrement du modèle, mais peut être actualisé en cliquant sur le bouton "Actualiser". Pour chaque lot de faces alimentées dans le modèle, le GAN examinera le visage qu'il a tenté de recréer et de le comparer au visage réel qui a été alimenté. En fonction de la façon dont il pense qu'il a fait, il se donnera un score (la valeur de perte).

8.  Facultatif : si vous ne souhaitez pas utiliser l'interface graphique, vous pouvez exécuter l'entraînement du modèle dans le terminal à l'aide de la commande suivante (ajustez uniquement les répertoires d'entrée, les fichiers d'alignements, le répertoire du modèle et l'option d'entraînement si vous souhaitez utiliser une autre option d'entraînement) :

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py train -A /home/ubuntu/faceswap/faces/personA -ala /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -B /home/ubuntu/faceswap/faces/personB -alb /home/ubuntu/faceswap/src/personB/personB_alignments.fsa -m /home/ubuntu/faceswap/model -t original -bs 16 -it 1000000 -s 250 -ss 25000 -ps 100 -L INFO</copy>
    

![](images/terminal-train.PNG " ")

La meilleure façon de savoir si un modèle a terminé l'entraînement est de regarder les aperçus. En fin de compte, ils montrent à quoi ressemblera le swap réel. Vous pouvez également vérifier les valeurs de perte. Plus les valeurs de perte sont proches de zéro, plus la probabilité d'une formation terminée est élevée.

![](images/graph-loss-iterations.PNG " ")

Dans l'onglet **Analyse**, vous pouvez vérifier la valeur _EG/sec_ dans la dernière colonne qui vous donnera le nombre de faces traitées par le modèle par seconde. Plus vos coeurs (GPU) sont puissants, plus les _EG/s_ seront élevées et plus le modèle convergera rapidement.

9.  Vérifiez l'utilisation du GPU pendant l'entraînement à l'aide de la commande nvidia-smi. ![](images/nvidia-smi-GPU.PNG " ")
    
    Elle indique l'utilisation volatile des GPU.
    
10.  Facultatif : il est possible d'améliorer les performances de l'application en augmentant les taux d'horloge du coeur de GPU et de la mémoire. Pour plus d'informations, visitez le billet de blog Nvidia sur [l'augmentation du GPU Nvidia](https://developer.nvidia.com/blog/increase-performance-gpu-boost-k80-autoboost/).
    

## Tâche 6 : Convertissez votre vidéo avec le visage échangé

Si votre formation est terminée, vous pouvez convertir votre vidéo et échanger le visage A dans votre vidéo source avec le visage B.

1.  Dans l'interface graphique de Faceswap, sélectionnez l'onglet **Convert**.
    
2.  Dans le champ **Données**, le répertoire **Entrée** s'affiche, dans lequel vous sélectionnez le répertoire dans lequel se trouve votre vidéo d'entrée avec la personne à partir de laquelle vous souhaitez échanger la face. Dans notre cas, la vidéo d'entrée est stockée sous _/faceswap/src/personA_.
    
3.  Cliquez sur le bouton de dossier en regard de **Répertoire de sortie**. Choisissez le dossier dans lequel la vidéo de sortie sera enregistrée. Vous pouvez appeler le répertoire, par exemple _/faceswap/outputvideo_, puis cliquer sur **OK**.
    
4.  Cliquez sur le bouton de fichier en regard d'**Alignements** et choisissez le fichier d'alignements de la personne à partir de laquelle vous souhaitez échanger le visage (dans notre cas, c'est la personne A). Si le fichier d'alignements se trouve dans le même répertoire que le répertoire **Input Dir**, vous pouvez le laisser vide.
    
5.  Facultatif : la **vidéo de référence** n'est requise que si vous convertissez des images en vidéo. Dans notre cas, nous le laisserons vide.
    
6.  Dans le champ **Modèle**, cliquez sur le bouton de dossier en regard de **Répertoire du modèle**. Choisissez le dossier dans lequel le modèle d'entraînement est enregistré. Dans notre cas, il s'agit du répertoire _faceswap/model_. Cliquez sur **OK**.
    
7.  Cliquez sur **Convertir**.
    

![](images/GUI-convert.PNG " ")

8.  Facultatif : si vous ne souhaitez pas utiliser l'interface graphique, vous pouvez effectuer la conversion dans le terminal à l'aide de la commande suivante (ajustez uniquement les répertoires d'entrée et de sortie, le fichier d'alignements, le répertoire de modèle et les plug-ins, si vous souhaitez utiliser d'autres plug-ins) :

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py convert -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/outputvideo -al /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -m /home/ubuntu/faceswap/model -c avg-color -M extended -w opencv -osc 100 -l 0.4 -j 0 -L INFO</copy>
    

![](images/convert-terminal.PNG " ")

9.  Dans le répertoire _/faceswap/outputvideo_, vous trouverez les cadres de la vidéo d'entrée de la personne A avec le visage échangé de la personne B. ![](images/outputvideo-dir.PNG " ")
    
    Nous utiliserons ffmpeg pour construire une vidéo à partir des images uniques.
    
    Installez ffmpeg en premier.
    
        <copy>sudo apt install ffmpeg</copy>
        
    
    Maintenant, nous pouvons construire une vidéo avec ffmpeg pour combiner les images uniques en une vidéo où nous allons parcourir toutes les images personA contenues dans le répertoire _outputvideo_ où _%nd_ représente un nombre séquentiel à n chiffres. Dans notre cas n=6.
    
        <copy>ffmpeg -i personA_%6d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4</copy>
        
10.  Regardez la [vidéo de sortie](https://www.youtube.com/watch?v=b-uKJ89QSnE)
    

## **Accusés de réception**

*   **Created By/Date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021
*   **Dernière mise à jour le/la date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021