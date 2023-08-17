# Préparer la configuration

## Présentation

Cet atelier vous explique comment télécharger le fichier ZIP de pile Oracle Resource Manager (ORM) nécessaire pour configurer la ressource nécessaire à l'exécution de cet atelier.

_Temps estimé de laboratoire :_ 10 minutes

### Objectifs

*   Télécharger la pile ORM
*   Configure an existing Virtual Cloud Network (VCN)

### Prérequis

Cet exercice suppose que vous avez :

*   Un compte Oracle Cloud

## Tâche 1 : téléchargement du fichier ZIP de pile Oracle Resource Manager (ORM)

1.  Cliquez sur le lien ci-dessous pour télécharger le fichier ZIP Resource Manager dont vous avez besoin pour créer votre environnement :
    
    _Remarque 1 :_ si vous fournissez un téléchargement de pile unique pour l'atelier, utilisez cette expression simple.
    
    *   [exemple-mkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _Remarque 2 :_ si vous fournissez le téléchargement de plusieurs piles pour le même atelier, utilisez une expression conditionnelle semblable à celle ci-dessous. N'oubliez pas que la condition ou le _type_ doit être associé à une entrée valide dans le fichier _manifest.json_. Reportez-vous aux rubriques _freetier-advanced_ et _freetier-basics_
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  Enregistrez dans votre dossier de téléchargement.

Nous vous recommandons vivement d'utiliser cette pile pour créer un VCN autonome/dédié avec vos instances. Passez à la _Tâche 3_ pour suivre nos recommandations. Si vous préférez utiliser un VCN existant, passez à la tâche suivante pour mettre à jour votre VCN existant avec les règles entrantes requises.

## Tâche 2 : ajout de règles de sécurité à un VCN existant

Cet atelier nécessite qu'un certain nombre de ports soient disponibles, une exigence pouvant être satisfaite en utilisant l'exécution de pile ORM par défaut qui crée un VCN dédié. Pour utiliser un VCN/sous-réseau existant, les règles suivantes doivent être ajoutées à la liste de sécurité.

| Type | Port source | CIDR source | Port de destination | Protocole | Description : |
| :-- | :-: | :-: | :-: | :-: | :-- |
| Entrée | Tout | 0.0.0.0/0 | 22 | TCP | SSH ; |
| Entrée | Tout | 0.0.0.0/0 | 80 | TCP | Bureau à distance à l'aide de noVNC |
| Sortie | Tout | S/O | 80 | TCP | Accès HTTP sortant |
| Sortie | Tout | S/O | (443) | TCP | Accès HTTPS sortant |
| { : title="Liste des règles de sécurité réseau requises"} |  |  |  |  |  |

1.  Accédez à _Networking >> réseaux cloud virtuels_
2.  Choisir un réseau
3.  Sous Ressources, sélectionnez Listes de sécurité
4.  Cliquez sur Default Security Lists (Listes de sécurité par défaut) sous le bouton Create Security List (Créer une liste de sécurité).
5.  Cliquez sur le bouton Add Ingress Rule.
6.  Entrez les informations suivantes :
    *   Type de source : CIDR
    *   CIDR source : 0.0.0.0/0
    *   Protocole IP : TCP
    *   Plage de ports source : Tous (conserver la valeur par défaut)
    *   Plage de ports de destination : _sélectionnez une valeur dans le tableau ci-dessus_
    *   Description : _Sélectionnez la description correspondante dans le tableau ci-dessus_
7.  Cliquez sur le bouton Add Ingress Rules (Ajouter des règles entrantes).
8.  Répétez les étapes \[5 à 7\] jusqu'à ce qu'une règle soit créée pour chaque port répertorié dans le tableau.

## Tâche 3 : Configurer le calcul

A l'aide des détails des deux tâches ci-dessus, passez à l'exercice _Configuration de l'environnement_ pour configurer l'environnement de votre atelier à l'aide d'Oracle Resource Manager (ORM) et de l'une des options suivantes :

*   Créer une pile : _Compute + Networking_
*   Créer une pile : _Calcul uniquement_ avec un VCN existant où les listes de sécurité ont été mises à jour conformément à la _tâche 2_ ci-dessus

Vous pouvez maintenant passer à l'exercice suivant.

## Accusés de réception

*   **Auteur** - René Fontcha, LiveLabs Responsable de plate-forme, NA Technology
*   **Contributeurs** - Meghana Banka
*   **Dernière mise à jour le/la date** - Rene Fontcha, LiveLabs Platform Lead, NA Technology, août 2022