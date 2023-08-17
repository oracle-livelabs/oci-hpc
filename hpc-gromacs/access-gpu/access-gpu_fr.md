# Accéder à votre noeud GPU

## Présentation

Dans cet exercice, vous allez accéder à votre noeud GPU.

Temps de laboratoire estimé : 5 minutes

## Tâche : accédez à votre noeud GPU

1.  Une fois le travail terminé dans Resource Manager, vous pouvez trouver les adresses IP publiques du noeud GPU et la clé privée dans le menu inférieur gauche sous **Sorties**.
    
2.  Copiez la clé privée sur votre ordinateur local, modifiez les droits d'accès de la clé et connectez-vous à l'instance :
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  Une fois connecté à votre noeud GPU, vous pouvez exécuter Gromacs à partir de /mnt/block. Reportez-vous au fichier README.md pour connaître les commandes spécifiques permettant d'exécuter Gromacs sur votre instance de GPU.
    

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020