# Exécuter le projet OpenFoam dans une machine virtuelle standard sur OCI

## Présentation

Les tâches suivantes sont conçues pour vous aider à évaluer le logiciel CFD OpenFOAM dans Oracle Cloud Infrastructure. Effectuez les tâches pour télécharger et configurer automatiquement OpenFOAM. OpenFOAM est le logiciel CFD open source gratuit publié et développé principalement par OpenCFD Ltd depuis 2004. Il dispose d'une large base d'utilisateurs dans la plupart des domaines de l'ingénierie et de la science, des organisations commerciales et universitaires. OpenFOAM possède une vaste gamme de caractéristiques pour résoudre tout ce qui concerne les flux de fluide complexes impliquant des réactions chimiques, des turbulences et des transferts de chaleur, l'acoustique, la mécanique solide et l'électromagnétique.

Vous serez guidé tout au long de l'installation de OpenFoam dans une forme VM.Standard 2.1 de base, mais pour un déploiement réel, une forme plus grande telle que BM.HPC2.36 serait appropriée. Certaines formes hautes performances ne sont disponibles que dans des régions et des domaines de disponibilité spécifiques.

Nous allons créer deux noeuds, le noeud de tête du cluster sur le sous-réseau public et un noeud de calcul de processus actif dans le sous-réseau privé. Pour accéder aux noeuds de processus actif, nous allons d'abord créer le noeud headnode, puis générer une clé SSH sur le noeud headnode et utiliser cette clé publique lors de la création du noeud de processus actif.

Pour **signaler les problèmes**, cliquez [ici](https://github.com/oracle/learning-library/issues/new) pour accéder au formulaire de soumission des problèmes du référentiel oracle github.

Temps estimé : 60 minutes

### Objectifs

En tant que développeur, ingénieur données,

1.  Déployer manuellement vos instances de calcul hautes performances
    
2.  Création d'un réseau de cluster
    
3.  Création d'un système de fichiers réseau
    
4.  Configurer VNC
    
5.  Installer OpenFoam et Paraview
    

### Prérequis

*   Compte Oracle Cloud Infrastructure avec des privilèges permettant de créer une forme de machine virtuelle standard 2.1 ou BM.HPC2.36.

### Terminologie de l'infrastructure

*   Noeud de processus actif : instances de calcul HPC fournissant la puissance de traitement nécessaire pour exécuter la charge de travail des simulations de calcul ou d'autres charges de travail d'ingénierie. Dans cette forme de calcul de démonstration, les noeuds BM.HPC2.36 sont les noeuds de processus actif.
    
*   Noeud de tête : instance de calcul à partir de laquelle tous les travaux de calcul peuvent être programmés et soumis pour être exécutés sur les noeuds de processus actif. Plusieurs fois, le noeud Head et le noeud Bastion peuvent être la même machine. Pour cette démonstration, nous allons provisionner le noeud Bastion.
    
*   Noeud Bastion : une instance de calcul avec adresse IP publique sert de point d'entrée pour la connexion aux noeuds de processus actif qui se trouvent généralement dans le réseau privé.
    
*   Réseau RoCE : RDMA sur Ethernet convergé (RoCE) est un protocole réseau qui autorise l'accès direct à la mémoire distante (RDMA) sur un réseau Ethernet.
    

## Tâche 1 : provisionnement du réseau Oracle Virtual Cloud

1.  Avant de créer une instance, nous devons configurer un réseau cloud virtuel. Sélectionnez le menu en haut à gauche, puis **Fonctions de réseau** et **Réseaux cloud virtuels**
    
    ![](./images/vcn.png " ")
    
    ![](./images/create_vcn.png " ")
    
    ![](./images/vcn_content.png " ")
    
2.  Sur la page suivante, sélectionnez les éléments suivants pour votre VCN :
    
    *   Nom
        
    *   Compartiment
        
    *   Bloc CIDR (Exemple : 10.0.0/16)
        
3.  Accédez au bas de la page et cliquez sur Create VCN.
    
4.  Créer des sous-réseaux
    
    1.  Sous-réseau public
        
        *   Nom : hpc\_public
            
        *   Type de sous-réseau : régional
            
        *   Bloc CIDR : 10.0.0/24
            
        *   Table de routage : "Table de routage par défaut"
            
        *   Accès au sous-réseau : sous-réseau public
            
        *   Liste de sécurité : "Default Security List"
            
    2.  Sous-réseau privé
        
        *   Nom : hpc\_private
            
        *   Type de sous-réseau : régional
            
        *   Bloc CIDR : 10.0.3.0/24
            
        *   Table de routage : sélectionnez la table de routage créée à l'étape précédente
            
        *   Accès au sous-réseau : sous-réseau privé
            
        *   Liste de sécurité : sélectionnez la liste de sécurité créée à l'étape précédente
            
5.  Cliquez sur **create subnet**.
    
    ![](./images/create_subnet.png " ")
    
    ![](./images/create_subnet_content.png " ")
    
6.  Cliquez sur `hpc_vcn` que vous avez créé et, dans le menu **Ressources** situé à gauche de la page, sélectionnez **Passerelle Internet**, créez une passerelle Internet.
    
    ![](./images/create_IG.png " ")
    
    ![](./images/create_IG_content.png " ")
    
    > **Remarque :** la passerelle Internet sera créée et devra être associée à une table de routage. Dans ce cas, la table de routage par défaut étant utilisée pour le sous-réseau public, la passerelle Internet doit être associée à cette table de routage.
    
7.  Ajoutez des règles de routage à la table de routage. Dans le menu **Ressources** situé à gauche de la page, sélectionnez **Table de routage par défaut pour `hpc_vcn`**, puis cliquez sur **Ajouter des règles de routage**.
    
    *   Type de cible : passerelle Internet
        
    *   Bloc CIDR de destination : 0.0.0.0/0.
        
    *   Passerelle Internet cible dans le compartiment : passerelle Internet créée
        
    
    ![](./images/route_table.png " ")
    
    ![](./images/route_rules.png " ")
    

## Tâche 2 : création de noeuds de cluster

Nous allons créer deux noeuds pour cette tâche, **Headnode** pour le cluster sur le sous-réseau public et **un noeud de calcul de processus actif** dans le sous-réseau privé. Pour accéder aux noeuds de processus actif, nous allons d'abord créer le noeud headnode, puis générer une clé SSH sur le noeud headnode et utiliser cette clé publique lors de la création du noeud de processus actif.

> **Remarque :** pour cette tâche, nous utiliserons uniquement la forme VM.Standard2.1 de base, mais pour un déploiement réel, une forme plus grande telle que BM.HPC2.36 serait appropriée. Certaines formes hautes performances ne sont disponibles que dans des régions et des domaines de disponibilité spécifiques.

1.  Créer un noeud de tête
    
    *   Nom : hpc\_head
        
    *   Image ou système d'exploitation : dernière version d'Oracle Linux (par défaut)
        
    *   Domaine de disponibilité : domaine disponible pour les formes VM.Standard 2.1
        
    *   Forme d'instance :
        
        *   VM.Standard 2.1
            
        *   toute autre forme (BM.HPC2.36 préférée si disponible)
            
    *   Réseau cloud virtuel : VCN créé avant
        
    *   Sous-réseau : sous-réseau public créé avant
        
    *   Clé SSH : attachez votre fichier de clés publiques
        
    
    1.  Dans le menu supérieur gauche, sélectionnez Compute et créez l'instance.
        
        ![](./images/compute.png " ")
        
        ![](./images/compute_bm.png " ")
        
        ![](./images/compute_vm.png " ")
        
    2.  Connectez-vous via SSH à votre noeud de tête et générez une clé SSH spécifique au cluster pour communiquer.
        
            $ <copy>ssh -i <ssh_key> opc@<headnode_ip_address></copy>
            
            $ <copy>ssh-keygen</copy>
            
        
        > **Remarque :** ne modifiez pas l'emplacement du fichier de clés SSH (/home/opc/.ssh/id\_rsa) et appuyez sur Entrée lorsqu'une phrase de passe est demandée (deux fois).
        
        Exécutez et copiez la chaîne entière, qui sera utilisée pour créer le noeud de processus actif.
        
            $ <copy>cat ~/.ssh/id_rsa.pub</copy>
            
2.  Créer un noeud de processus actif
    
    1.  Dans le menu supérieur gauche, sélectionnez Compute et créez une instance avec les détails suivants :
        
        *   Nom : hpc\_worker1
            
        *   Image ou système d'exploitation : dernière version d'Oracle Linux (par défaut)
            
        *   Domaine de disponibilité : domaine disponible pour les formes VM.Standard 2.1
            
        *   Forme d'instance :
            
            *   VM.Standard 2.1
                
            *   toute autre forme (BM.HPC2.36 préférée si disponible)
                
        *   Réseau cloud virtuel : VCN créé avant
            
        *   Sous-réseau : sous-réseau privé créé avant
            
        *   Clé SSH : chaîne de clé publique copiée à partir d'une étape ci-dessus.
            
    2.  Connectez-vous via SSH au noeud de processus actif. Revenez à la console connectée au noeud de tête et prenez l'adresse IP privée et SSH dans le noeud de processus actif à partir du noeud de tête.
        
            $ <copy>ssh opc@10.x.x.x</copy>
            

## Tâche 3 : configuration de la passerelle NAT

> **Remarque :** pour le noeud de processus actif uniquement.

1.  Sélectionnez un noeud de processus actif et cliquez sur **Cartes d'interface réseau virtuelles attachées** dans le menu **Ressources** à gauche.
    
2.  Sélectionnez **Modifier la VNIC**.
    
3.  Désélectionnez **Ignorer la vérification de la source/de la destination** si elle est cochée et cliquez sur **Mettre à jour les cartes d'interface réseau virtuelles**.
    
    ![](./images/nat_gateway.png " ")
    

## Tâche 4 : montage d'un lecteur

> **Remarque :** uniquement si la forme de noeud est attachée à un élément NVMe (BM.HPC2.36 en possède un, et non à VM.Standard2.1), les machines HPC disposent d'un stockage NVMe local mais ne sont pas montées par défaut. Passez à l'étape 5 si vous utilisez VM.Standard2.1

1.  Connectez-vous en SSH à votre noeud de tête et exécutez les commandes ci-dessous.
    
        $ <copy>lsblk</copy>
        
    
    Le lecteur doit être répertorié avec le NAME à gauche (probablement nvme0n1, s'il est différent, modifiez-le dans les commandes suivantes).
    
    Le noeud de tête aura l'unité partagée avec l'installation et le modèle. Elle sera partagée entre les différents noeuds de processus actif. Chaque noeud de processus actif monte également le lecteur à exécuter localement sur un lecteur NVMe. Dans cet exemple, le répertoire de partage sera de 500 Go mais n'hésitez pas à le modifier.
    
2.  Partitionner le lecteur sur le noeud de processus actif (facultatif)
    
        $ <copy>sudo yum -y install gdisk</copy>
        
    
        $ <copy>sudo gdisk /dev/nvme0n1</copy>     $ > n    # Create new partition
        $ > 1    # Partition Number
        $ >      # Default start of the partition
        $ > +500G # Size of the shared partition
        $ > 8300  # Type = Linux File System
        $ > n     # Create new partition
        $ > 2     # Partition Number
        $ >       # Default start of the partition
        $ >       # Default end of the partition, to fill the whole drive
        
        $ > 8300  # Type = Linux File System
        
        $ > w      # Write to file
        $ > Y      # Accept Changes
        
3.  Formater le lecteur sur les noeuds de processus actif
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1</copy>
        
4.  Formater les partitions sur le noeud de tête
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p1</copy>
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p2</copy>
        
5.  Créer un répertoire et monter le lecteur
    
6.  Headnode(local et share) : sélectionnez /mnt/share en tant que répertoire de montage pour la partition 500G et /mnt/local en tant que plus grand.
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1p1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1p2 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        
7.  Headnode(share) :
    
        $ <copy>sudo mkdir /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        
8.  Noeuds de processus actif : sélectionnez /mnt/local comme répertoire de montage de l'ensemble du lecteur.
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        

## Tâche 5 : création d'un système de fichiers réseau

1.  Créer une cible de montage. Dans le menu supérieur gauche, cliquez sur **File Storage** et **Mount Target**.
    
    *   Nouveau nom de cible de montage : entrez un nom (exemple : openfoam\_fs)
        
    *   Réseau cloud virtuel : sélectionnez le VCN créé ci-dessus
        
    *   Sous-réseau : sélectionnez le VCN public
        
2.  Headnode se trouve dans un sous-réseau public. Nous allons maintenir le pare-feu et ajouter une exception via :  
    
        <copy>sudo firewall-cmd --permanent --zone=public --add-service=nfs</copy>
        <copy>sudo firewall-cmd --reload</copy>
        
3.  Cliquez sur la cible de montage créée, puis sur le chemin d'export, puis cliquez sur les commandes de montage et copiez les commandes qui doivent ressembler à ce qui suit :
    
        <copy>sudo yum install nfs-utils</copy>
        <copy>sudo mkdir -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        
4.  Sur les noeuds de processus actif, puisqu'ils se trouvent dans un sous-réseau privé avec une liste de sécurité limitant l'accès, nous pouvons désactiver complètement le pare-feu. Ensuite, nous pouvons installer les outils nfs-utils et monter le nfs comme nous l'avons fait précédemment.
    
        <copy>sudo systemctl stop firewalld</copy>
        <copy>sudo yum -y install nfs-utils</copy>
        <copy>sudo mkdir  -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        

## Tâche 6 : installation de OpenFOAM

1.  **Connexion de tous les noeuds de processus actif**  
    
    Chaque noeud de processus actif doit pouvoir communiquer avec tous les noeuds de processus actif. La communication SSH fonctionne mais la plupart des applications présentent des problèmes si tous les hôtes ne se trouvent pas dans le fichier hôte connu. Pour désactiver la vérification d'hôte connue pour les noeuds avec une adresse dans le VCN, vous pouvez la désactiver à l'aide des commandes suivantes. Vous devrez peut-être la modifier légèrement si vos sous-réseaux comportent des adresses différentes. Placez le bloc de code suivant dans un script shell et exécutez le script.
    
        <copy>for i in 0 1 2 3
        do
             echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
             echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done</copy>
        
2.  **Créer une liste d'usinelist**  
    
    OpenFOAM sur le noeud de tête ne sait pas automatiquement quels noeuds de calcul sont disponibles. Vous pouvez créer un fichier machine sur `/mnt/share/machinelist.txt with`, l'adresse IP privée de tous les noeuds, ainsi que le nombre de CPU disponibles. Le noeud de tête doit également être inclus. Le format des entrées est `private-ip-address cpu=number-of-cores`
    
        <copy>10.0.0.2 cpu=1
        10.0.3.2 cpu=1</copy>
        
3.  **Noeud de tête**
    
    Effectuez l'installation à partir de sources, modifiez le chemin d'accès aux bases de données tar dans les commandes suivantes. Cet exemple contient les sources OpenFOAM de base. OpenFOAM d'ESI a également été testé. Pour partager l'installation entre les différents noeuds de calcul, installez-la sur le système de fichiers réseau.
    
        <copy>$ sudo yum groupinstall -y 'Development Tools'
        $ sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        $ cd /mnt/share
        $ wget -O - http://dl.openfoam.org/source/7 | tar xvz
        $ wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        $ mv OpenFOAM-7-version-7 OpenFOAM-7
        $ mv ThirdParty-7-version-7 ThirdParty-7
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        $ echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc
        $ cd /mnt/share/OpenFOAM-7
        $ ./Allwmake -j</copy>
        
4.  **Noeud de salarié**  
    
        $ <copy>sudo yum -y install openmpi openmpi-devel
        $ cd /mnt/share
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        $ echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc</copy>
        

## Tâche 7 : exécuter la charge globale de simulation et afficher la sortie

1.  Sur Headnode, exécutez les commandes suivantes qui seront nécessaires pour afficher la sortie à l'aide du package Paraview.
    
        $ <copy>sudo yum install -y mesa-libGLU
        $ cd /mnt/share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php >  file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  Sur headnonde, exécutez ces commandes afin de configurer le serveur VNC :  
    
        $ <copy>sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver</copy>
        
3.  Téléchargez ce fichier ZIP avec les [scripts](../scripts/motorbike_RDMA.tgz) dans /mnt/share/work dans l'un des noeuds de processus actif. Décompressez le fichier à l'aide de `tar -xf motorbike_RDMA.tgz`.
    
4.  Avant d'exécuter la charge globale, nous devons modifier le fichier allrun afin qu'il corresponde à l'architecture que nous avons créée. Nous allons d'abord déplacer le dossier à partir du dossier du programme d'installation de OpenFOAM.
    
        $ <copy>model_drive=/mnt/share
        $ sudo mkdir $model_drive/work
        $ sudo chmod 777 $model_drive/work
        $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
        $ cd /mnt/share/work/motorBike/system</copy>
        
5.  Modifiez le système de fichiers/decomposeParDict et remplacez cette ligne par numberOfSubdomains 6, par numberOfSubdomains 12 ou le nombre de processus dont vous aurez besoin. Ensuite, dans le bloc hierarchicalCoeffs, remplacez n (3 2 1) par n (4 3 1). Si vous multipliez ces 3 valeurs, vous devez obtenir numberOfSubdomains.
    
    Pour une exécution avec une configuration de 1 noeud de processus actif VM.Standard2.1 :
    
        =========                 |
        \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
         \\    /   O peration     | Website:  https://openfoam.org
          \\  /    A nd           | Version:  7
           \\/     M anipulation  |
        \*---------------------------------------------------------------------------*/
        FoamFile
        {
             version     2.0;
             format      ascii;
             class       dictionary;
             object      decomposeParDict;
        }
        
        // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
        
        numberOfSubdomains 2;
        
        method          hierarchical;
        // method          ptscotch;
        
        simpleCoeffs
        {
             n               (4 1 1);
             delta           0.001;
        }
        
        hierarchicalCoeffs
        {
             n               (2 1 1);
             delta           0.001;
             order           xyz;
        }
        
        manualCoeffs
        {
             dataFile        "cellDecomposition";
        }
        
        // ************************************************************************* //
        
6.  Modifiez ensuite le fichier Allrun dans /mnt/share/work/motorBike pour obtenir l'aspect suivant :
    
        <copy>#!/bin/sh
        cd ${0%/*} || exit 1    # Run from this directory
        NP=$1
        install_drive=/mnt/share
        # Source tutorial run functions
        . $WM_PROJECT_DIR/bin/tools/RunFunctions
        
        # Copy motorbike surface from resources directory
        cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
        cp $install_drive/machinelist.txt hostfile
        
        runApplication surfaceFeatures
        
        runApplication blockMesh
        
        runApplication decomposePar -copyZero
        echo "Running snappyHexMesh"
        mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.snappyHexMesh
        ls -d processor* | xargs -I {} rm -rf ./{}/0
        ls -d processor* | xargs -I {} cp -r 0 ./{}/0
        echo "Running patchsummary"
        mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
        echo "Running potentialFoam"
        mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
        echo "Running simpleFoam"
        mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam
        
        runApplication reconstructParMesh -constant
        runApplication reconstructPar -latestTime
        
        foamToVTK
        touch motorbike.foam</copy>
        
7.  Assurez-vous que vous êtes dans le noeud de processus actif et exécutez la charge globale :
    
        $ <copy>ssh worker_node_IP
        $ cd /mnt/share/work/
        $ ./Allrun 2
        
        $ ./Allrun 2
        $ Cleaning /mnt/share/work case
        $ Mesh Dimensions: (40 16 16)
        $ Cores:36: 6, 6, 1
        $ Running surfaceFeatures on /mnt/share/work
        $ Running blockMesh on /mnt/share/work
        $ Running decomposePar on /mnt/share/work
        $ Running snappyHexMesh
        $ Running patchsummary
        $ Running potentialFoam
        $ Running simpleFoam
        $ Running reconstructParMesh on /mnt/share/work
        $ Running reconstructPar on /mnt/share/work
        219.95</copy>
        
8.  Une fois la charge globale terminée, configurez le client VNC sur votre ordinateur comme suit. Fournir une adresse IP publique du serveur Bastion et du port VNC
    
    ![](./images/24-VNC_Connection.png " ")
    
9.  OPTIONNELLEMENT, Si vous n'êtes pas autorisé à ouvrir le port VNC 5901 ou si, pour des raisons de sécurité, vous souhaitez créer un tunnel SSH pour ce port, utilisez la commande suivante pour créer un tunnel SSH sur le port 5901 sans ouvrir le port dans la liste de sécurité
    
10.  Créez un tunnel à partir de votre ordinateur portable/bureau à l'aide de la commande suivante à partir de la fenêtre de terminal. Ici, la communication pour le port 5901 sera effectuée sur le port SSH 22 et l'adresse IP 150.136.41.3 est l'adresse IP publique du serveur de bastion.
    
        $ <copy>ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
11.  Ne fermez pas la fenêtre de terminal de tunnel SSH ci-dessus. Maintenant, lancez la session VNC et cette fois-ci, au lieu de l'adresse IP, utilisez "localhost" sur le port 5901, même si ce port n'est pas ouvert dans la liste de sécurité du sous-réseau.
    
    ![](./images/28-ssh_Tunnel.png " ")
    
12.  Démarrer l'application Paraview à partir du serveur bastion
    
        $ <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview</copy>
        
13.  Dans la fenêtre de l'application Paraview, File -> Open -> Path "/mnt/gluster-share/work" et sélectionnez le nom de fichier motorbike.foam. Il s'agit d'un fichier de zéro octet, ce qui devrait être parfait.
    
    ![](./images/25-Paraview_Menu.png " ")
    
14.  A gauche de la fenêtre, sous l'onglet Propriétés, sélectionnez Régions de maillage pour sélectionner toutes les valeurs, puis désélectionnez les valeurs supérieures qui ne commencent pas par le préfixe motorBike\_. Assurez-vous que toutes les valeurs commençant par motorBike\_ sont sélectionnées. Cliquez sur le bouton Apply (Appliquer), certaines erreurs apparaissent dans la fenêtre d'erreur qui apparaît pour afficher le rendu de l'image dans la console VNC.
    
    ![](./images/26-Mesh_Regions.png " ")
    
15.  Une image comme ci-dessous sera affichée à l'écran. Selon certains paramètres d'affichage, l'image à l'écran peut sembler un peu différente.
    
    ![](./images/27-Image_Rendering.png " ")
    

Tout fait ! Cette opération termine la démonstration d'exécution de l'application OpenFoam sur une machine virtuelle standard sur OCI.

_Bravo ! Vous avez terminé l'atelier avec succès._

Voici des informations détaillées sur la gestion d'une instance de calcul hautes performances. Pour obtenir une référence complète sur les commandes, consultez la documentation OCI [ici](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Dernière mise à jour le/la date** - Harrison Dvoor, janvier 2021