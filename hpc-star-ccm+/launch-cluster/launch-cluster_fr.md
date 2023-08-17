# Lancer le réseau de cluster HPC

## Présentation

Il existe de nombreuses façons de lancer un réseau de cluster HPC. Ce guide des solutions abordera deux méthodes différentes :

*   Via Marketplace
*   Manuellement, selon votre système d'exploitation, vous souhaiterez utiliser une méthode spécifique. Si l'image du marché du réseau de cluster HPC ou nos scripts OCI HPC CN Terraform sont utilisés, cela s'applique à Oracle Linux 7 uniquement. Si vous souhaitez utiliser CentOS, Ubuntu ou un autre système d'exploitation, une configuration manuelle est requise.

Durée estimée du laboratoire : 30 minutes

## **OPTION 1** : Création d'un réseau de cluster via Marketplace

Marketplace contient des applications et des images qui peuvent être déployées avec notre infrastructure. Pour les clients qui souhaitent utiliser Oracle Linux, une image de réseau de cluster HPC est disponible et peut être lancée directement sur Marketplace. Nous vous suggérons de lancer le [cluster CFD Ready](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) qui contiendra les bibliothèques nécessaires pour la CFD.

1.  Dans Marketplace, sélectionnez Get App en haut à droite. ![](images/get-app.png " ")
    
2.  Sélectionnez la région OCI, puis cliquez sur Connexion. ![](images/sign-in.png " ")
    
3.  Après vous être connecté, vérifiez la version de l'image de cluster HPC, puis sélectionnez le compartiment dans lequel le cluster sera lancé. Acceptez les conditions générales, puis lancez la pile.
    
    ![](images/launch-stack.png " ")
    
4.  Remplissez les détails restants de la pile :
    
    *   Sélectionnez le domaine de disponibilité souhaité pour les formes de calcul et le bastion.
    *   Copier-coller votre clé SSH publique
    *   Saisir le nombre d'instances Compute pour le cluster
    *   Désélectionnez Installer OpenFOAM
    *   Si vous avez besoin de plus de 6 To d'espace disque partagé, consultez GlusterFS et sélectionnez le nombre de serveurs dont vous avez besoin. (6 To par serveur)
5.  Cliquez sur Créer.
    
6.  Accédez à Actions Terraform, puis cliquez sur Appliquer. Le provisionnement CN va être lancé.
    
    ![](images/apply.png " ")
    
7.  Attendez que le travail indique Succès, puis accédez aux sorties pour obtenir les adresses IP privées de noeud de calcul et de bastion.
    

## **OPTION 2** : Création d'un réseau de cluster via une configuration manuelle

Marketplace contient des applications et des images qui peuvent être déployées avec notre infrastructure. Pour les clients qui souhaitent utiliser Oracle Linux, vous pouvez créer manuellement un réseau de cluster comme suit :

1.  Sélectionnez la région OCI en haut à droite.
    
2.  Dans le menu principal, sélectionnez Networking et Virtual Cloud Networks.
    
3.  Cliquez sur Démarrer l'assistant VCN et sélectionnez VCN avec la connectivité Internet
    
4.  Choisissez et nommez le compartiment approprié, puis utilisez 172.16.0.0/16 comme CIDR VCN, 172.16.0.0/24 comme sous-réseau public et 172.16.1.0/24 comme sous-réseau privé. Cliquez sur **Suivant**, puis sur **Créer**.
    
5.  Dans le menu principal, sélectionnez Compute, Instances, puis Create Instance.
    
6.  Sélectionnez **Modifier l'image** et sélectionnez Oracle Linux 7 - HPC Cluster Networking Image, sous l'onglet Oracle Image.
    
7.  Sélectionnez le domaine de disponibilité dans lequel vous pouvez mettre en place une instance BM.HPC2.36
    
8.  Remplacez la forme par BM.HPC2.36 sous Bare Metal et spécialité.
    
9.  Sélectionnez l'VCN et le sous-réseau public que vous avez créés.
    
10.  Ajoutez une clé publique pour vous connecter à l'instance. Cette clé sera utilisée sur toutes les instances de calcul.
    
11.  Une fois l'ordinateur démarré, cliquez sur l'instance créée. Sous More Actions, sélectionnez Create Instance Configuration. Vous pouvez désormais mettre fin à l'instance sous Actions supplémentaires.
    
12.  Dans le menu principal, sélectionnez Compute, puis Cluster Networks.
    
13.  Cliquez sur Create Cluster Network et remplissez toutes les options. Utilisez le VCN, le sous-réseau privé et la configuration d'instance que vous venez de créer. Sélectionnez le domaine de disponibilité dans lequel vous pouvez lancer les instances BM.HPC2.36.
    
14.  Lancez le réseau de cluster.
    
15.  Pendant le chargement, créez une autre instance sous le menu principal, Compute et Instances.
    
16.  Placez-le dans le sous-réseau public qui vient d'être créé, en utilisant votre clé publique et votre forme doivent être VM.Standard2.1 ou similaires. Il s'agit du bastion que nous allons utiliser pour nous connecter au cluster.
    
17.  SCP la clé du cluster sur le bastion sur /home/opc/.ssh/cluster\_key et la copie également sur /home/opc/.ssh/id\_rsa
    
18.  Installez l'outil de provisionnement sur le bastion à l'aide de la commande suivante :
    
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        
19.  Accédez à Compute, puis aux pools d'instances dans la console et collectez toutes les adresses IP du pool de réseau du cluster. Vous pouvez également utiliser cette commande sur le bastion si vous n'avez rien d'autre à exécuter sur votre sous-réseau privé.
    
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done
        
20.  Installez l'outil de provisionnement à l'aide de la commande suivante :
    
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020