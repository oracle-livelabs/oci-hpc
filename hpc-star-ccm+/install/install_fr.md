# Installation de STAR-CCM+

## Présentation

Dans cet exercice, vous allez installer STAR-CCM+.

Temps de laboratoire estimé : 25 minutes

## Tâche 1 : ajout de bibliothèques spécifiques

_**Si vous avez utilisé le cluster CFD Ready à partir de Marketplace, cette étape n'est pas nécessaire**_

1.  Certaines bibliothèques doivent être ajoutées à l'image Oracle Linux sur tous les noeuds de calcul.
    
            sudo yum -y install libSM libX11 libXext libXt
        

## Tâche 2 : téléchargement des fichiers binaires

Vous pouvez télécharger le programme d'installation STAR-CCM+ à partir du site Web de Siemens PLM ou le pousser sur votre machine à l'aide de scp.

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

Vous pouvez également télécharger le programme d'installation dans Object Storage.

1.  Dans le menu principal de la console, sélectionnez Object Storage.
2.  Choisissez la région appropriée en haut à droite
3.  Sélectionner le bon compartiment sur le côté gauche
4.  Créer un bucket si vous n'en avez pas encore créé
5.  Dans le bucket, sélectionnez l'objet de téléchargement et indiquez le chemin du programme d'installation.
6.  Sélectionnez les 3 points à droite de l'objet d'installation et sélectionnez Créer une demande pré-authentifiée
7.  Si vous perdez l'URL, vous ne pouvez pas la récupérer, mais vous pouvez regénérer une nouvelle demande pré-authentifiée

Téléchargez le fichier de stockage d'objet du formulaire d'installation avec

        wget PAR_URL
    

Décompressez ou décompressez le programme d'installation en fonction de votre version.

        tar -xf installer.tgz
        unzip installer.tgz
    

## Tâche 3 : installation

1.  Lancez le programme d'installation sur un emplacement partagé. Par défaut, un cluster HPC dispose d'un partage NFS monté sur tous les noeuds de calcul.
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020