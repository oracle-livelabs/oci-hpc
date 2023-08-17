# Ajout du programme d'installation Gromacs à Object Storage

## Présentation

Dans cet exercice, vous allez ajouter le programme d'installation Gromacs à Object Storage.

Temps de laboratoire estimé : 5 minutes

## Tâche : ajout du programme d'installation Gromacs à Object Storage

_Notez que les scripts terraform [zip file](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip) fournis dans ce github contiennent déjà des URL de stockage d'objets pour Gromacs 2020.1, VMD 1.9.3 et un exemple de modèle d'évaluation des performances de l'[Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench). Si vous souhaitez modifier ces URL, modifiez le fichier variable.tf et remplacez les valeurs de gromacs\_url, model\_url et visualizer\_url par vos propres URL de demande pré-authentifiées._

1.  Sélectionnez le menu ![](./images/menu.png) en haut à gauche, puis Object Storage --> Object Storage
2.  Créez un bucket ou sélectionnez-en un existant. Pour en créer un, cliquez sur![](./images/create_bucket.png)
3.  Conservez les options par défaut : Standard as Storage tiers et Oracle-Managed keys. Cliquez sur![](./images/create_bucket.png)
4.  Cliquez sur le nom du bucket que vous venez de créer, puis sélectionnez![](./images/upload_object.png)
5.  Sélectionnez votre fichier tar d'installation Gromacs et cliquez sur![](./images/upload_object.png)
6.  Cliquez sur les 3 points à droite de l'objet que vous venez de télécharger et sélectionnez "Créer une demande pré-authentifiée".
7.  Dans le menu suivant, conservez les options par défaut et sélectionnez une date d'expiration pour l'URL de votre programme d'installation. Cliquez sur![](./images/pre-auth.png)
8.  Dans la fenêtre suivante, copiez l'URL de demande pré-autorisée et conservez-la. Vous ne pourrez plus le récupérer après avoir fermé cette fenêtre. Si vous lâchez ou qu'il expire, il est toujours possible de recréer une autre demande pré-authentifiée qui générera une autre URL

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020