# Installation de LS-DYNA

## Présentation

Dans cet exercice, vous allez installer LS-DYNA.

Temps de laboratoire estimé : 25 minutes

## Tâche 1 : téléchargement des fichiers binaires

Vous pouvez télécharger les fichiers binaires LS-DYNA à partir du [site Web LSTC](http://www.lstc.com/download/ls-dyna) ou le propager vers votre machine à l'aide de scp.

Prenez la version créée pour mpi et compilée pour RedHat Ent Srv 5.4. Selon nos résultats, IntelMPI fonctionne plus rapidement que Platform MPI sur OCI. (ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1).tar.gz)

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

Vous pouvez également télécharger le programme d'installation dans Object Storage.

1.  Dans le menu principal de la console, sélectionnez Object Storage.
2.  Choisissez la région appropriée en haut à droite
3.  Sélectionner le bon compartiment sur le côté gauche
4.  Créer un bucket si vous n'en avez pas encore créé
5.  Dans le bucket, sélectionnez l'objet de téléchargement et indiquez le chemin du programme d'installation.
6.  Sélectionnez les 3 points à droite de l'objet d'installation et sélectionnez Créer une demande pré-authentifiée
7.  Si vous perdez l'URL, vous ne pouvez pas la récupérer, mais vous pouvez regénérer une nouvelle demande pré-authentifiée

Téléchargez le fichier de stockage d'objet du formulaire d'installation avec

    <copy>
        wget PAR_URL
    </copy>
    

Décompressez ou décompressez le programme d'installation en fonction de votre version.

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## Tâche 2 : installation de LS-DYNA

1.  Décompressez les fichiers binaires dans un emplacement partagé. Par défaut, un cluster HPC dispose d'un partage NFS ou d'un partage Gluster monté sur tous les noeuds de calcul.
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## Tâche 3 : installation des bibliothèques MPI

**Intel MPI 2018**

Exécutez ces commandes sur chaque noeud.

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**MPI de plate-forme**

Installez ces bibliothèques :

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

Téléchargez le fichier tar à partir du site Web IBM et exécutez :

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

Suivez ensuite les instructions à l'écran. Si vous installez la plate-forme sur un lecteur de partage, elle sera accessible à tous les noeuds de calcul.

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020