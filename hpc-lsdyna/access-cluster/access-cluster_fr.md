# Accéder à votre cluster

## Présentation

Dans cet exercice, vous allez accéder au cluster.

Temps de laboratoire estimé : 10 minutes

## Tâche : accédez à votre cluster

1.  L'adresse IP publique du bastion est disponible dans le menu inférieur gauche sous Sorties. Si vous accédez à vos instances dans le menu principal, vous trouverez également votre instance de bastion ainsi que l'adresse IP publique.
    
2.  La clé privée permettant d'accéder aux machines est également disponible. Copiez le texte d'un fichier sur votre ordinateur, par exemple/home/user/key :
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020