# Provisionner un cluster HPC à partir d'une image Oracle Marketplace

## Présentation

Dans cet atelier, vous allez provisionner un cluster HPC à partir d'OCI Marketplace. OCI Marketplace est un guichet unique pour une grande variété de solutions sectorielles qui peuvent être facilement déployées dans notre console cloud. Vous utiliserez cette plate-forme pour le déploiement automatisé de clusters HPC. Le modèle de Marketplace fournit une base pour une solution de calcul hautes performances, comprenant tous les composants nécessaires pour démarrer une charge de travail avec un minimum d'effort. Au fur et à mesure que ces exercices progressent, vous verrez comment une telle référence peut être intégrée et utilisée par des applications tierces pour accélérer votre workflow.

Temps de laboratoire estimé : 60 minutes

### Objectifs

En tant que développeur ou ingénieur de données,

*   Accéder à OCI Marketplace
*   Déployer une image de cluster HPC à partir d'OCI Marketplace
*   Affichage et exécution d'une pile sur OCI

### Prérequis

*   Compte Oracle Cloud Infrastructure avec des privilèges permettant de créer une forme de machine virtuelle standard 2.1 ou BM.HPC2.36.

## Tâche 1 : accédez à OCI Marketplace

1.  Cliquez sur le menu Hamburger (==) en haut à gauche. ![](./images/click_hamburger.png)
    
2.  Faites défiler la page vers le bas et cliquez sur Marketplace. ![](./images/click_marketplace.png)
    
3.  Recherchez **HPC** dans Marketplace et sélectionnez Cluster HPC. ![](./images/marketplace.png)
    
4.  Vérifiez et acceptez les **Conditions standard Oracle et restrictions**, puis cliquez sur **Lancer la pile**. ![](./images/launch_stack.png)
    

## Tâche 2 : configuration de la pile HPC

Après le lancement de la pile, vous serez redirigé vers l'assistant de création de pile où vous remplirez les informations de pile.

1.  Dans **Informations sur la pile**, nommez la pile et _(facultatif)_ indiquez si elle doit être déployée dans un compartiment dans lequel vous ne vous trouvez pas actuellement. ![](./images/stack_p1.png)
    
2.  Sélectionnez l'**AD** souhaité pour les formes de calcul et le bastion.
    
3.  Copiez-collez votre **clé SSH** publique. ![](./images/stack_p2_1.png)
    
4.  Saisissez le nombre d'**instances Compute** pour le cluster. ![](./images/stack_p2_2.png)
    
5.  Sous **Options réseau**, conservez les valeurs par défaut. ![](./images/stack_p2_3.png)
    
6.  Cliquez sur **Suivant** pour vérifier la configuration. ![](./images/stack_p3.png)
    
7.  Cliquez sur **Créer**.
    

**Remarque :** le provisionnement de la pile prend environ 15 minutes.

## Tâche 3 : afficher la pile en cours d'exécution

Une fois la pile créée, elle vous redirige vers une page où vous pouvez visualiser le travail de pile en cours d'exécution. Il devrait ressembler à ce qui suit. Vous pouvez consulter les éléments suivants :

1.  **Journaux**
2.  **Variables** transmises au script Terraform ou générées par celui-ci
3.  **Ressources associées** provisionnées par le script Terraform
4.  **Sorties**
5.  **Visualiser l'état** du travail

![](./images/stack_detail_provisioning.png)

Pour accéder à cette page où vous pouvez visualiser toutes les piles de votre compartiment en cours, cliquez sur le menu Hamburger (==) en haut à gauche et sous **Solutions et plate-forme**, survolez **Resource Manager** > **Piles**.

![](./images/nav_resource_manager.png)

Tout fait ! Cette opération termine la démonstration de provisionnement d'un cluster HPC à partir d'une image Oracle Marketplace.

_Vous pouvez maintenant passer à l'exercice suivant._

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Dernière mise à jour le/la date** - Harrison Dvoor, octobre 2020

# <<<<<<<< RESPONSABLE

> > > > > > > amont/maître