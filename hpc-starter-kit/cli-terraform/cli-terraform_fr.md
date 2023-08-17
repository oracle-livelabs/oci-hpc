# Utilisation des commandes de l'interface de ligne de commande OCI et de Terraform pour le calcul hautes performances

## Présentation

`ocihpc` est un outil de simplification des déploiements d'applications HPC dans Oracle Cloud Infrastructure (OCI). L'interface de ligne de commande (CLI) HPC vous permet d'utiliser une seule commande pour déployer des clusters HPC de toute taille avec nos instances de calcul HPC Bare Metal dédiées et notre réseau RDMA Mellanox. Vous n'avez pas besoin de comprendre Terraform ou Oracle Cloud Infrastructure Resource Manager pour utiliser l'interface de ligne de commande HPC. Il s'appuie sur la CLI Oracle Cloud Infrastructure de base et inclut des scripts Terraform prédéfinis.

L'utilisation de l'interface de ligne de commande HPC présente les avantages suivants :

1.  Lancez rapidement un cluster de calcul complet avec RDMA sur Ethernet convergé (RoCE) v2 via une seule commande.
2.  Facile à utiliser Aucune connaissance d'Terraform ou d'Oracle Cloud Infrastructure Resource Manager requise pour lancer un réseau de cluster de base.
3.  Prend en charge le déploiement de l'interface de transmission de messages (MPI) comprend un ensemble complet de packages logiciels pour exécuter un traitement parallèle avec RDMA, y compris Mellanox OFED avec Open MPI, Intel MPI et Platform MPI.
4.  Vous pouvez exécuter vos propres scripts terraform ou les ajouter aux scripts existants fournis avec l'outil pour installer vos propres applications.

Temps de laboratoire estimé : 60 minutes

### Objectifs

En tant que développeur ou ingénieur de données,

1.  Déployer un réseau de cluster HPC à l'aide de l'interface de ligne de commande Oracle
2.  Voir, exécuter et gérer la pile via Cli

### Prérequis

1.  Compte Oracle Cloud Infrastructure avec des informations d'identification utilisateur suffisantes pour exécuter une pile Terraform.
2.  clés SSH générées

### Stratégies

Le compte utilisateur OCI que vous utilisez dans `ocihpc` doit comporter les stratégies nécessaires configurées pour OCI Resource Manager. Veuillez vérifier [ce lien](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm) pour obtenir des informations sur les stratégies requises.

## Tâche 1 : installation d'ocihpc sur macOS/Linux

1.  Téléchargez la dernière version à l'aide de la commande suivante et extrayez-la :
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  Décompressez le fichier que vous avez téléchargé à l'étape 1.
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  Rendez le fichier binaire ocihpc exécutable.
    
        $ chmod +x ./ocihpc 
        
4.  Déplacez le binaire ocihpc vers votre PATH.
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  Testez son fonctionnement.
    
        $ ocihpc version 
        

## Tâche 2 : installation d'ocihpc sous Windows

1.  Téléchargez la dernière version à partir de [ce lien](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip) et extrayez-la.
    
2.  Ajoutez le fichier binaire ocihpc à votre PATH.
    
3.  Testez son fonctionnement.
    
        $ ocihpc.exe version 
        

## Tâche 3 : création d'une paire de clés SSH sur la ligne de commande

Veuillez vous référer à [ce lien](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm) si vous utilisez des fenêtres.

1.  Ouvrez un shell ou un terminal pour entrer les commandes.
    
2.  Accédez à votre dossier .oci
    
        $ cd Users/enjli/.ssh
        
3.  Si vous n'avez pas déjà créé le dossier, créez un répertoire .ssh où stocker les informations d'identification :
    
        $ mkdir Users/enjli/.ssh
        
4.  Si aucune paire de clés id\_rsa n'apparaît dans le dossier, entrez la commande suivante et indiquez un nom et une phrase de passe lorsque vous y êtes invité. Les clés seront créées avec les valeurs par défaut : clés RSA de 2048 bits.
    
        $ ssh-keygen
        

## Tâche 4 : génération d'une clé de signature d'API

Vos demandes d'API seront signées avec votre clé privée et Oracle utilisera la clé publique pour vérifier l'authenticité de la demande. Veuillez vous référer à [ce lien](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other) si vous utilisez des fenêtres.

1.  Si vous ne l'avez pas déjà fait, créez un répertoire .oci où stocker les informations d'identification :
    
        $ mkdir Users/enjli/.oci
        
2.  Générez la clé privée à l'aide des commandes suivantes.
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  Vérifiez que vous seul pouvez lire le fichier de clés privées :
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  Générez la clé publique :
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## Tâche 5 : ajout d'une clé publique à Oracle Cloud Infrastructure

Maintenant que vous disposez d'une combinaison de clés publique/privée, vous devez l'ajouter à la console OCI sous le paramètre utilisateur :

1.  Accédez à votre dossier .oci contenant `oci_api_key_public.pem` et copiez la clé publique.
    
2.  Connectez-vous à votre console OCI, cliquez sur Menu et sélectionnez Identité et utilisateurs. Sélectionnez un utilisateur et accédez à la page Utilisateur - Détails.
    
3.  Cliquez sur Ajouter une clé publique dans la section des clés d'API. ![marché](images/ResourcesMenu.png) ![marché](images/APIKeys.png)
    
4.  Coller la clé publique que vous avez copiée à partir de la CLI dans Ajouter une clé publique ![marché](images/AddPublicKey.png)
    

## Tâche 6 : Configurer

Cette étape décrit la configuration requise pour la CLI et inclut des configurations facultatives qui permettent d'étendre les fonctionnalités de la CLI.

1.  Avant d'utiliser l'interface de ligne de commande, vous devez créer un fichier de configuration dans votre dossier .oci contenant les informations d'identification requises pour utiliser votre compte Oracle Cloud Infrastructure. Vous pouvez créer ce fichier à l'aide d'une boîte de dialogue de configuration ou manuellement, à l'aide d'un éditeur de texte.
    
2.  Exécutez _ocihpc configure_ pour vérifier si vous disposez d'une configuration valide pour accéder à OCI. L'outil vous guidera tout au long de la création d'une configuration.
    

Vous serez averti de l'emplacement d'écriture de votre fichier de configuration :

_Fichier de configuration enregistré dans : /Users/enjli/.oci/config_

Pour créer votre fichier de configuration, vous aurez besoin de : 1. Votre OCID utilisateur (dans la section de profil en haut à droite de l'écran sous > user settings > user information tab), 2. OCID de location (Administration > Détails de location > onglet Informations sur la location), 3. Région à partir de laquelle vous travaillez (par exemple, us-phoenix-1, us-ashburn-1, etc.), 4. Empreinte de l'utilisateur (dans l'onglet Informations utilisateur), 5. Chemin de votre clé de signature privée d'API.

Exemple de fichier `config` : ![marché](images/oci_config.png)

## Tâche 7 : Liste

Vous pouvez obtenir la liste des piles disponibles en exécutant `ocihpc list`.

Exemple :

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## Tâche 8 : Initialiser

1.  Créez un dossier que vous utiliserez comme source de déploiement.

IMPORTANT : utilisez un dossier différent par pile. N'initialisez pas plusieurs piles dans le même dossier. Sinon, l'outil remplacera le précédent.

2.  Accédez à ce dossier et exécutez `ocihpc init <stack name>`. `ocihpc` téléchargera les fichiers nécessaires dans ce dossier.
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**IMPORTANT :** modifiez le contenu du fichier /Users/enjli/ocihpc-test/config.json avant d'exécuter la commande de déploiement ocihpc

## Tâche 9 : Déployer

1.  Avant le déploiement, vous devez modifier les valeurs dans le fichier `config.json`. Les variables dépendent de la pile que vous déployez. Exemple de fichier `config.json` pour Cluster Network :
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  Pour modifier votre fichier `config.json`, accédez au répertoire que vous venez de créer (ocihpc-test dans ce cas) et ouvrez le fichier "config.json" à l'aide de texteditor ou de bloc-notes. ![marché](images/config_json.png)
    

Notez qu'il ne s'agit pas du même fichier de configuration que celui configuré à l'étape 1.

Pour ce fichier de configuration, nous aurons besoin des éléments suivants :

1.  Informations sur le domaine de disponibilité contenant les ressources HPC de notre location (Administration > Détails de location > Faites défiler la page jusqu'à la section Limites de service > Compute > et faites défiler la page vers le bas pour trouver "BM.HPC2.36") : dans la capture d'écran ci-dessous, nous voyons que 6 machines BM.HPC2.36 au total doivent être utilisées dans AD-2, dont 0 sont en cours d'utilisation. ![marché](images/hpc_resource.png)
    
2.  Le Bastion AD peut être n'importe quel domaine de disponibilité que vous avez choisi tant que la forme de bastion des ressources (forme VM.standard2.1) doit déjà être remplie - VM.Standard2.1
    
3.  Nombre de noeuds - dans le cadre de cet exercice, nous allons utiliser 2 pour utiliser toutes les ressources HPC
    
4.  Notre clé SSH publique
    

**Notes**

1.  Après avoir modifié les valeurs dans `config.json`, vous pouvez déployer la pile avec `ocihpc deploy <arguments>`. Cette commande permet de créer une pile sur Oracle Cloud Resource Manager et de déployer la pile à l'aide de celle-ci.
    
2.  Pour les piles prises en charge, vous pouvez définir le nombre de noeuds à déployer en l'ajoutant à la commande `ocihpc deploy`. Si la pile ne le prend pas en charge ou si vous ne fournissez pas de valeur, l'outil se déploiera avec les numéros par défaut.
    
3.  Par exemple, la commande suivante déploiera un réseau de cluster avec 5 noeuds :
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  L'outil génère un nom de déploiement composé de `<stack name>-<current directory>-<random-number>`.
    
    Exemple :
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## Tâche 10 : Connexion

Une fois le déploiement terminé, vous verrez l'adresse IP bastion/headnode à laquelle vous pouvez vous connecter :

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

Vous pouvez également obtenir les détails de connexion en exécutant la commande `ocihpc get ip`.

## Tâche 11 : Gérer

En outre, vous pouvez utiliser les commandes cli pour gérer et conserver facilement le suivi de vos ressources :

1.  Pour générer la liste de toutes les piles déployées dans un compartiment spécifique, procédez comme suit : `oci resource-manager job list -c [OCID OF COMPARTMENT]`
    
2.  Pour générer la liste des travaux d'une pile ou d'un compartiment, triés par heure de création : `oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  Pour consulter un travail et ses détails, procédez comme suit : `oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  Pour visualiser les entrées de journal du travail indiqué au format JSON, procédez comme suit : `oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`
    
5.  Pour déplacer une pile et ses travaux associés vers un autre compartiment, procédez comme suit : `oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## Tâche 12 : Supprimer

Une fois le déploiement terminé, vous pouvez le supprimer en passant au dossier de pile et en exécutant `ocihpc delete --stack <stack name>`.

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

Tout fait ! Vous avez déployé votre première instance de calcul hautes performances à l'aide de l'outil d'interface de ligne de commande OCI.

_Vous pouvez maintenant passer à l'exercice suivant._

Informations détaillées sur la gestion d'une instance de calcul hautes performances. Pour obtenir une référence complète sur les commandes, consultez la documentation OCI [ici](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Dernière mise à jour le/la date** - Harrison Dvoor, octobre 2020

# <<<<<<<< RESPONSABLE

> > > > > > > amont/maître