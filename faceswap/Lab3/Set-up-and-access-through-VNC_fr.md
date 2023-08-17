# Configurer et accéder via VNC

## Présentation

Dans cet atelier, nous allons examiner les étapes requises pour configurer l'instance Ubuntu et y accéder via un client VNC (ici : TigerVNC).

Durée estimée de l'atelier : 15 minutes

### Objectifs

*   Connexion à votre instance de calcul via SSH
*   Configurer VNC sur votre instance Ubuntu.
*   Accéder à votre instance de calcul via un client VNC (par exemple, TigerVNC)

### Eléments requis

*   Exercice 1 et atelier 2
*   [Ajoutez une règle entrante à la liste de sécurité VCN](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using) pour autoriser le trafic TCP pour le port de ports 5900+N où N est le numéro d'affichage (généralement :0 pour un affichage physique). ![](images/vncseclist.PNG " ")

## Tâche 1 : connexion à votre instance de calcul via SSH

1.  Ouvrez un terminal (également appelé ligne de commande ou console) de votre choix. Dans cet exemple, nous travaillons sur une machine Windows locale et utilisons Windows PowerShell comme terminal.
    
2.  Notez l'adresse IP publique de l'instance Ubuntu que vous trouverez sous **Compute -> Instances** dans la liste des instances de votre compartiment.
    
3.  Dans votre terminal, accédez au dossier dans lequel réside votre paire de clés SSH que vous avez utilisé lors de la création de l'instance de calcul Ubuntu. Dans notre cas, le dossier est appelé _ssh_.
    

       <copy>cd .ssh</copy>
    

4.  Entrez la commande suivante pour accéder à votre instance de calcul Ubuntu où vous devez remplacer _private-key_ par votre clé privée OpenSSH et votre _adresse IP publique_ par l'adresse IP publique de votre instance Ubuntu.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## Tâche 2 : configurer VNC sur votre instance Ubuntu.

1.  Installez le serveur VNC en exécutant les commandes ci-dessous dans votre terminal :

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  Installez un environnement de bureau. Nous allons installer l'environnement de bureau Xfce (mais vous pouvez choisir d'utiliser d'autres environnements de bureau) en saisissant la commande suivante :

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  Exécutez la commande vncpasswd pour définir le mot de passe du serveur VNC.

       <copy>vncpasswd</copy>
    

Lorsque vous y êtes invité, entrez votre mot de passe et vérifiez-l'en saisissant une seconde fois.

4.  Démarrez le serveur VNC à l'aide de la commande suivante :

       <copy>vncserver :1</copy>
    

La sortie suivante apparaît : ![](images/vncserver.PNG " ")

5.  Arrêtez le serveur VNC à l'aide de la commande suivante :

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  Le fichier de configuration VNC se trouve sur ~/.vnc/xstartup. Modifiez-l'à l'aide de votre éditeur de texte favori. Nous utiliserons vim.

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  Ajoutez la ligne suivante à la fin du fichier :

       <copy>exec /usr/bin/startxfce4 &</copy>
    

Si vous utilisez vim : appuyez sur **A** pour ajouter du texte à la fin et copiez la commande ci-dessus. Passez en mode commande en appuyant sur la touche Echap. Saisissez **:wq** - pour enregistrer et quitter.

8.  Démarrez le serveur VNC en exécutant la commande suivante.

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  Fermez la connexion SSH à l'instance en exécutant la commande suivante :

       <copy>exit</copy>
    

## Tâche 3 : connexion au bureau VNC

Vous pouvez vous connecter au bureau distant à l'aide d'un client VNC (par exemple, TigerVNC) et de la mise en tunnel SSH.

1.  Créez un tunnel SSH vers le serveur VNC à l'aide de la commande suivante (où la clé privée est votre clé OpenSSH privée et public\_ip l'adresse IP privée de votre instance de calcul Ubuntu) :

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  Installez le client vncviewer, qui sera TigerVNC dans notre cas.

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  Téléchargez et installez un client VNC (par exemple, TigerVNC) sur votre ordinateur local. Connectez-vous à **127.0.0.1 :5901** à l'aide de votre client VNC. ![](images/tigervnc.PNG " ")
    
4.  Lorsque vous êtes invité à saisir un mot de passe, saisissez le mot de passe créé lors de la configuration du serveur VNC. ![](images/tigervncpwd.PNG " ")
    

Votre bureau Ubuntu distant s'ouvrira. ![](images/vncdesktop.PNG " ")

## **Accusés de réception**

*   **Created By/Date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021
*   **Dernière mise à jour le/la date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021