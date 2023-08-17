# Sélectionner des variables

## Présentation

Dans cet exercice, vous allez remplir les variables de votre pile.

Temps de laboratoire estimé : 10 minutes

## Tâche : Sélectionner des variables

1.  Cliquez sur ![](./images/next.png) et remplissez les variables.
    
    **Noeud GPU** :
    
    *   SHAPE OF THE GPU COMPUTE NODE : forme du noeud de calcul qui sera utilisé pour exécuter Gromacs. Sélectionnez les formes Bare Metal BM.GPU2.2 ou BM.GPU3.8 pour des performances optimales.
    *   DOMAINE DE DISPONIBILITÉ : AD de l'instance et de Block Volume. Le domaine de disponibilité doit disposer de GPU disponibles.
    *   Nombre de noeuds de GPU : nombre de noeuds de GPU requis.
    *   TYPE VNC POUR LE GPU : Type de visualisation pour le noeud de tête : aucun, VNC ou X11VNC.
    *   Mot de passe pour les SESSIONS VNC : mot de passe permettant d'utiliser la session VNC sur le noeud à exécuter avant/après.
    
    **Options de bloc** :
    
    *   BLOCK VOLUME SIZE (Go) : taille du volume de blocs partagé.
    
    **Gromacs** :
    
    *   URL TO DOWNLOAD Gromacs : URL des binaires compilés Gromacs 2020.1 (remplacez l'URL avec une version différente ou laissez vide si vous souhaitez télécharger plus tard).
    *   URL permettant de télécharger un modèle TARBALL : URL du modèle que vous souhaitez exécuter (remplacez l'URL par un modèle différent ou laissez le champ vide si vous souhaitez télécharger plus tard).
    *   URL POUR TÉLÉCHARGER LE LOGICIEL DE VISUALISATION VMD : URL permettant de télécharger VMD 1.9.3 pour visualiser les modèles Gromacs (remplacez l'URL par un autre logiciel de visualisation ou laissez vide si vous souhaitez télécharger plus tard).
2.  Cliquez sur ![](./images/next.png). Vérifiez les informations et cliquez sur ![](./images/create.png)
    

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020