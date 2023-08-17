# Exécuter le projet OpenFoam dans un cluster HPC

## Présentation

La configuration du matériel HPC et le déploiement de logiciels d'application peuvent prendre beaucoup de temps et inclure diverses opérations complexes. Avec cette démonstration, toutes ces tâches sont automatisées au sein d'une image de Marketplace OCI et un environnement HPC entièrement opérationnel avec le logiciel de simulation OpenFoam est disponible immédiatement après le provisionnement. Tout ce qui est nécessaire est d'exécuter la charge de travail sur l'environnement HPC entièrement fonctionnel et d'exécuter l'application de visualisation comme Paraview pour afficher le résultat.

Temps de laboratoire estimé : 60 minutes

### Architecture de référence

![marché](images/29_Architecture.png)

### A propos de OpenFoam :

La dynamique des fluides numérique (CFD) est la simulation du mouvement de fluide et du transfert de chaleur à l'aide d'une analyse numérique. Les logiciels CFD, tels que OpenFOAM, peuvent servir d'outil puissant pour de nombreux secteurs de l'ingénierie afin d'améliorer la conception, de visualiser l'impact et de détecter les inefficacités d'un système. Cependant, la puissance de calcul requise pour traiter une simulation peut être très importante. Les superordinateurs et les clusters HPC sont souvent utilisés pour exécuter des modèles volumineux et complexes dans un délai raisonnable. Dans cet atelier, vous allez calculer et rendre le modèle aérodynamique d'une moto à l'aide d'un logiciel CFD open source, OpenFOAM, dans un cluster HPC Oracle.

### Terminologie de l'infrastructure :

1.  Noeud de processus actif : instances de calcul HPC fournissant la puissance de traitement nécessaire pour exécuter la charge de travail des simulations de calcul ou d'autres charges de travail d'ingénierie. Dans cette forme de calcul de démonstration, les noeuds BM.HPC2.36 sont les noeuds de processus actif.
2.  Noeud de tête : instance de calcul à partir de laquelle tous les travaux de calcul peuvent être programmés et soumis pour être exécutés sur les noeuds de processus actif. Plusieurs fois, le noeud Head et le noeud Bastion peuvent être la même machine. Pour cette démonstration, nous allons provisionner le noeud Bastion.
3.  Noeud GPU : instance de calcul spécialisée avec des processeurs GPU afin de rendre le résultat de la charge globale de calcul. Cette démonstration ne provisionne pas de machine GPU.
4.  Noeud Bastion : une instance de calcul avec adresse IP publique sert de point d'entrée pour la connexion aux noeuds de processus actif qui se trouvent généralement dans le réseau privé.
5.  Réseau RoCE : RDMA sur Ethernet convergé (RoCE) est un protocole réseau qui autorise l'accès direct à la mémoire distante (RDMA) sur un réseau Ethernet.

### Objectifs

1.  Provisionner un environnement HPC entièrement fonctionnel à l'aide de l'image du marketplace OCI
2.  Déploiement d'une application de simulation basée sur CFD nommée OpenFoam
3.  Exécuter la charge globale Openfoam
4.  Affichez la sortie à l'aide de l'application de visualisation Paraview.

### Prérequis

Avant de commencer à utiliser cet exercice, assurez-vous d'avoir accès aux éléments suivants :

1.  Autorisez le service Compute à gérer les espaces de noms de balise en votre nom à l'aide de la stratégie suivante :
    
    "Autoriser le service compute\_management à utiliser l'espace de noms de balise dans la location"
    
2.  La forme Bare Metal BM.HPC2.36 n'est autorisée que dans cette démo. Un minimum de 2 formes de calcul est nécessaire dans le même domaine de disponibilité.
    
3.  Image de calcul de machine virtuelle pour le serveur de bastion. Il ne doit pas nécessairement se trouver dans le même domaine de disponibilité que les noeuds HPC.
    
4.  Les informations d'identification de l'utilisateur doivent avoir un quota pour créer ou utiliser un VCN existant avec un sous-réseau privé et public.
    
5.  Accès à l'image de Marketplace "CFD Ready Cluster". La version de l'image Marketplace utilisée dans cette DEMO est (Version : 20200625).
    

## Tâche 1 : image Lancer Marketplace

1.  Avant le lancement de l'image de Marketplace, vérifiez le domaine de disponibilité où 2 noeuds HPC ou plus avec la forme BM.HPC2.36 sont disponibles. Accédez au menu -> Gouvernance -> Limites, quotas et utilisation ![marché](images/01-Service_Limits.png)

2.  Accédez à Menu -> Marketplace -> Applications ![marché](images/02-Marketplace.png)

3.  Dans la boîte de recherche de type "cfd" ![marché](images/03-Search_marketplace.png)

4.  Cliquez sur l'image, sélectionnez-la, puis cliquez sur le bouton "Lancer la pile". Vérifiez que vous êtes dans le compartiment approprié. ![marché](images/04-Launch_Stack.png)

5.  Une fois la pile lancée, remplissez les détails pour créer des ressources, y compris les noeuds de processus actif HPC, le serveur de bastion, les composants de mise en réseau, le serveur VCN. La pile demandera également des entrées pour déployer des packages tels qu'openMPI et openFoam.
    
6.  Indiquez le nom de la pile ci-dessous ![marché](images/05-Create_Stack01.png)
    

7.  Sélectionnez le domaine de disponibilité dans lequel vous disposez de noeuds HPC disponibles pour le provisionnement. Indiquez la clé publique SSH qui sera nécessaire pour la connexion au serveur Bastion. ![marché](images/06-Create_Stack02.png)
    
8.  Sélectionnez le domaine de disponibilité pour provisionner le serveur Bastion. Il peut être différent du domaine de disponibilité sur les noeuds HPC. Sélectionnez la forme hôte Bastion de votre choix. Cochez la case pour installer le serveur VNC sur le serveur Bastion et indiquez le mot de passe qui sera nécessaire pour établir la connexion VNC ultérieurement. ![marché](images/07-Create_Stack03.png)
    

9.  Pour les noeuds de processus actif, la seule option sélectionnée est BM.HPC2.36. Vous aurez besoin d'au moins 2 formes de calcul HPC pour terminer le provisionnement. Sélectionnez 2 noeuds de calcul ou plus dans l'écran ci-dessous. Restez toutes les options par défaut. ![marché](images/08-Create_Stack04.png)

10.  Sous Options réseau, créez un nouveau VCN en utilisant les valeurs par défaut transmises dans la pile. Cette démo crée un VCN créé via cette pile. Mais il existe également une option pour utiliser l'VCN existant. ![marché](images/09-Create_Stack05.png)

11.  Pour la section Système de fichiers, utilisez les options ci-dessous qui créent le montage NFS et installent le système de fichiers gluster sur celui-ci. ![marché](images/10-Create_Stack06.png)

12.  Cochez la case "INSTALL OPENFOAM" pour installer l'application OpenFoam dans cette pile HPC. ![marché](images/11-Create_Stack07.png)

13.  Sur la dernière page de la pile, vérifiez les détails saisis. Si tout est correct, cliquez sur le bouton "Créer" pour démarrer le provisionnement de l'infrastructure et de l'installation du logiciel. ![marché](images/12-Create_Stack08.png)

14.  Vérifier la progression de l'installation de la pile HPC ![marché](images/13-StackJob.png)

15.  Une fois le provisionnement terminé dans environ 20 minutes, le fichier journal doit contenir le message suivant. Les journaux se trouvent dans Menu -> Resource Manager -> Stacks (cliquez sur votre nom de pile) -> Jobs (Cliquez sur le lien du nom du travail pour afficher le journal complet). ![marché](images/14-StackJobDetails.png)

## Tâche 2 : valider la configuration

1.  Cliquez sur le lien Associer des ressources à gauche de l'écran pour afficher les ressources d'infrastructure provisionnées par cette pile. L'option Associer des ressources est disponible dans Menu -> Gestionnaire de ressources -> Piles (cliquez sur le nom de votre pile) -> Travaux (cliquez sur le lien du nom du travail) -> Associer des ressources ![marché](images/15-Stack_Resources.png)

2.  Sous Compute -> Instances, 3 instances de calcul seront provisionnées, 2 noeuds HPC dans le réseau privé et 1 serveur Bastion avec une adresse IP publique. ![marché](images/16-Compute_Details.png)

3.  Connectez-vous au serveur Bastion à l'aide de l'adresse IP publique en tant qu'utilisateur opc à l'aide de la clé privée SSH pour laquelle une clé publique a été téléchargée dans la pile ci-dessus. Utilisez cette méthode pour vous connecter à partir de Linux/Mac Desktop. Pour le bureau Windows, utilisez Putty et fournissez une clé privée ppk pour établir la connexion. ![marché](images/17-Bastion_ssh.png)

4.  Vérifiez le stockage NFS partagé monté sur 2 noeuds HPC et le serveur de bastion. Ce stockage NFS contient le logiciel OpenFoam qui sera utilisé pour exécuter la charge globale. ![marché](images/18-Bastion_storage.png)

5.  L'application OpenFoam est hébergée dans le dossier "/mnt/gluster-share/work". Recherchez le fichier d'hôte contenant l'adresse IP priavte des 2 noeuds HPC sur lesquels la charge de travail sera exécutée. ![marché](images/19-Bastion_openfoam.png)

6.  La sortie ci-dessous illustre un serveur VNC en cours d'exécution sur le port 5901. Ce port sera utilisé pour établir une connexion VNC au bastion. ![marché](images/20-Bastion_VNCdetails.png)

7.  Dans la liste de sécurité du serveur Bastion, vérifiez que le port 5901 est autorisé dans les règles entrantes. Si VNC est exécuté sur un autre port et que ce port n'est pas autorisé pour le trafic entrant dans la liste de sécurité, créez une règle de sécurité pour le même port. ![marché](images/21-SecList_OpenVNC_Port.png)

8.  Vérifier les noms des noeuds HPC ![marché](images/22-ListHPCNodes.png)

## Tâche 3 : exécuter la charge globale de simulation et afficher la sortie

1.  Sur le serveur Bastion, exécutez les commandes suivantes qui seront nécessaires pour afficher la sortie à l'aide du package Paraview.
    
        sudo yum install -y mesa-libGLU
        cd /mnt/gluster-share
        curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" https://www.paraview.org/paraview-downloads/download.php > file.tar.gz
        tar -xf file.tar.gz
        

2.  Connectez-vous à l'un des noeuds HPC à partir du serveur de bastion et exécutez la charge de travail
    
        ssh 172.16.1.2
        cd /mnt/gluster-share/work/
        ./Allrun 36
        
    
        ./Allrun 36
        Cleaning /mnt/gluster-share/work case
        Mesh Dimensions: (40 16 16)
        Cores:36: 6, 6, 1
        Running surfaceFeatures on /mnt/gluster-share/work
        Running blockMesh on /mnt/gluster-share/work
        Running decomposePar on /mnt/gluster-share/work
        Running snappyHexMesh
        Running patchsummary
        Running potentialFoam
        Running simpleFoam
        Running reconstructParMesh on /mnt/gluster-share/work
        Running reconstructPar on /mnt/gluster-share/work
        219.95
        [opc@inst-quqyz-accurate-swan work]$
        

3.  Une fois la charge globale terminée, configurez le client VNC sur votre ordinateur comme suit. Fournir une adresse IP publique du serveur Bastion et du port VNC ![marché](images/24-VNC_Connection.png)

4.  OPTIONNELLEMENT, Si vous n'êtes pas autorisé à ouvrir le port VNC 5901 ou si, pour des raisons de sécurité, vous souhaitez créer un tunnel SSH pour ce port, utilisez la commande suivante pour créer un tunnel SSH sur le port 5901 sans ouvrir le port dans la liste de sécurité

Créez un tunnel à partir de votre ordinateur portable/bureau à l'aide de la commande suivante à partir de la fenêtre de terminal. Ici, la communication pour le port 5901 sera effectuée sur le port SSH 22 et l'adresse IP 150.136.41.3 est l'adresse IP publique du serveur de bastion.

      ```
      ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3
      ```
    

5.  Ne fermez pas la fenêtre de terminal de tunnel SSH ci-dessus. Maintenant, lancez la session VNC et cette fois-ci, au lieu de l'adresse IP, utilisez "localhost" sur le port 5901, même si ce port n'est pas ouvert dans la liste de sécurité du sous-réseau. ![marché](images/28-ssh_Tunnel.png)

6.  Démarrer l'application Paraview à partir du serveur bastion
    
        cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        ./paraview
        
7.  Dans la fenêtre de l'application Paraview, File -> Open -> Path "/mnt/gluster-share/work" et sélectionnez le nom de fichier motorbike.foam. Il s'agit d'un fichier de zéro octet, ce qui devrait être parfait. ![marché](images/25-Paraview_Menu.png)
    

8.  A gauche de la fenêtre, sous l'onglet Propriétés, sélectionnez Régions de maillage pour sélectionner toutes les valeurs, puis désélectionnez les valeurs supérieures qui ne commencent pas par le préfixe motorBike\_. Assurez-vous que toutes les valeurs commençant par motorBike\_ sont sélectionnées. Cliquez sur le bouton Apply (Appliquer), certaines erreurs apparaissent dans la fenêtre d'erreur qui apparaît pour afficher le rendu de l'image dans la console VNC. ![marché](images/26-Mesh_Regions.png)

9.  Une image comme ci-dessous sera affichée à l'écran. Selon certains paramètres d'affichage, l'image à l'écran peut sembler un peu différente. ![marché](images/27-Image_Rendering.png)

Tout fait ! Cette opération termine la démonstration pour exécuter l'application OpenFoam sur un cluster HPC.

_Vous pouvez maintenant passer à l'exercice suivant._

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Dernière mise à jour le/la date** - Harrison Dvoor, octobre 2020

# <<<<<<<< RESPONSABLE

> > > > > > > amont/maître