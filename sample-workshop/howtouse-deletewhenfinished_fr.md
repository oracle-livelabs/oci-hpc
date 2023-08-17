# Atelier avec un seul ensemble d'ateliers

## Instructions : supprimez ce fichier lorsque vous avez terminé

1.  Ouvrir le modèle échantillon-atelier dans Atom ou Visual Studio Code
2.  Nous avons pré-créé 5 dossiers. Un atelier est créé à partir de plusieurs exercices.
3.  Supprimez les commentaires de ce type : _Répertoriez les objectifs de cet atelier_.
4.  Veillez à utiliser des tirets et un dossier minuscules pour les espaces (setup-adb NOT Setup\_ADB).
5.  Les noms de vos images doivent avoir des noms descriptifs. Pas seulement adb1, adb2, adb3. Pour l'accessibilité du handicap, nous avons besoin de descriptions d'image pour expliquer à quoi ressemble l'image. Rappelez-vous tous les tirets et minuscules.
6.  Téléchargez notre document d'assurance qualité depuis WMS. Nous trouvons les ateliers plus rapides lorsque vous savez ce qui est nécessaire pour passer à la production avant et que vous utilisez le squelette.

PS : vous n'avez pas besoin de Readme.md. Les fichiers README existent uniquement aux niveaux supérieurs de la bibliothèque. Nous dirigeons tout le trafic vers LiveLabs, car nous ne pouvons pas suivre l'utilisation sur GitHub. Ne créez pas de liens directs vers GitHub, votre atelier peut être très populaire, mais nous ne pouvons pas le suivre, donc personne ne le saura.

## Chemin absolu pour la navigation dans le menu Oracle Cloud

**Laboratoire 1 : Provisionner une instance -> Etape 0 : Utilisation de ces images standardisées pour la navigation Oracle Cloud (en commun pour le provisionnement)** - Nous avons inclus une liste de captures d'écran communes pour la navigation dans le menu Oracle Cloud. Lisez cette section et utilisez les images de chemin absolu pertinentes, le cas échéant. Votre atelier pourra ainsi être testé à l'avenir en cas de mise à jour de l'interface utilisateur Oracle Cloud.

## Structure de dossiers

Dans cet exemple, l'objectif est de créer plusieurs ateliers "enfants"à partir d'un atelier"parent" plus long. Les enfants sont constitués de parties du parent.

échantillon-atelier/ -- laboratoires individuels

        provision/
        setup/
        dataload/
        query/
        introduction/
          introduction.md       -- description of the everything workshop, note that it is a "lab" since there is only one
    
    workshops/
       freetier/                -- freetier version of the workshop
        index.html
        manifest.json
       livelabs/                -- livelabs version of the workshop
        index.html
        manifest.json
    

### FreeTier ou LiveLabs

*   "FreeTier" - comprend les essais gratuits, les comptes payants et, pour certains ateliers, les comptes Toujours gratuit (bouton brun)
*   "LiveLabs" : il s'agit d'ateliers qui utilisent les locations fournies par Oracle (bouton vert).
*   "Bureau" : il s'agit d'un nouveau déploiement dans lequel l'atelier est encapsulé dans un environnement NoVNC exécuté dans une instance de calcul

### À propos de l'atelier

L'atelier comprend les 6 exercices individuels en une seule séquence.

La structure de dossiers inclut une "labre" d'introduction qui décrit l'atelier comme un ensemble complet de 6 exercices. Remarque : vous n'avez peut-être pas besoin d'avoir une introduction différente pour chacune des versions parent et enfant des ateliers, ceci est uniquement illustratif.

Examinez le dossier product-name-workshop/freetier et consultez le fichier manifest.json pour voir la structure.

> **Remarque :** l'utilisation de "Lab n :" dans les titres est facultative.

Le "lab" prérequis est le premier atelier d'un dossier commun sur le référentiel oracle/learning-library. Comme cet exercice existe déjà, nous pouvons utiliser une URL RAW/absolue à la place :

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

Le fichier manifest.json doit connaître l'emplacement de chaque atelier par rapport à l'emplacement dans la hiérarchie. Dans cette structure, les exercices sont situés à deux niveaux, par exemple :

    "filename": "../../provision/provision.md"
    

### Par exemple :

Cet [atelier APEX](https://oracle.github.io/learning-library/developer-library/apex/spreadsheet/workshops/freetier/) est un bon exemple d'atelier comportant un seul ensemble d'exercices : [https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).