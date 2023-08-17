# Atelier avec un seul ensemble d'ateliers

## Instructions : supprimez ce fichier lorsque vous avez terminé

1.  Ouvrir le modèle échantillon-atelier dans Atom ou Visual Studio Code
2.  Nous avons pré-créé 5 dossiers. Un atelier est créé à partir de plusieurs exercices.
3.  Supprimez les commentaires de ce type : _Répertoriez les objectifs de cet atelier_.
4.  Veillez à utiliser des tirets et un dossier minuscules pour les espaces (setup-adb NOT Setup\_ADB).
5.  Les noms de vos images doivent avoir des noms descriptifs. Pas seulement adb1, adb2, adb3. Pour l'accessibilité du handicap, nous avons besoin de descriptions d'image pour expliquer à quoi ressemble l'image. Rappelez-vous tous les tirets et minuscules.
6.  Téléchargez notre document QA Feedback sur WMS. Nous trouvons les ateliers plus rapides lorsque vous savez ce qui est nécessaire pour passer à la production avant et que vous utilisez le squelette.

PS : vous n'avez pas besoin de Readme.md. Les fichiers README existent uniquement aux niveaux supérieurs de la bibliothèque. Nous dirigeons tout le trafic vers LiveLabs, car nous ne pouvons pas suivre l'utilisation sur GitHub. Ne créez pas de liens directs vers GitHub, votre atelier peut être très populaire, mais nous ne pouvons pas le suivre, donc personne ne le saura.

## Chemin absolu pour la navigation dans le menu Oracle Cloud

**Laboratoire 1 : Provisionner une instance -> Etape 0 : Utilisation de ces images standardisées pour la navigation Oracle Cloud (en commun pour le provisionnement)** - Nous avons inclus une liste de captures d'écran communes pour la navigation dans le menu Oracle Cloud. Lisez cette section et utilisez les images de chemin absolu pertinentes, le cas échéant. Votre atelier pourra ainsi être testé à l'avenir en cas de mise à jour de l'interface utilisateur Oracle Cloud.

## Structure de dossiers

Dans cet exemple, l'objectif est de créer plusieurs ateliers "enfants"à partir d'un atelier"parent" plus long. Les enfants sont constitués de parties du parent.

*   atelier/échantillon
    *   laboratoires individuels
        *   prepare-setup/ -- contient le lien permettant de télécharger la pile ORM/Terraform pour l'atelier
        *   initialize-environment/ -- tâches pour initialiser l'environnement après provisionnement
        *   provision/ -- premier laboratoire de fiction
        *   dataaload/ -- deuxième laboratoire de fiction
        *   query/ -- troisième laboratoire de fiction
        *   Introduction/
            *   introduction.md -- description de tout l'atelier, notez qu'il s'agit d'une "lab" puisqu'il n'y en a qu'une
    *   ateliers/
        *   bureau/ -- Version de bureau _requise_ de l'atelier (tous les exercices)
            *   index.html
            *   manifest.json
        *   poste de travail avancé/ -- version de bureau de l'atelier (introduction, initialisation-environnement, chargement de données, requête)
            *   index.html
            *   manifest.json
        *   ordinateurs de bureau/ -- version ordinateur de l'atelier (introduction, initialisation-environnement, mise à disposition)
            *   index.html
            *   manifest.json
        *   location/ -- version de location _requise_ de l'atelier (tous les exercices)
            *   index.html
            *   manifest.json
        *   location-avancé/ -- version de location de l'atelier (introduction, initialisation-environnement, chargement de données, requête)
            *   index.html
            *   manifest.json
        *   location-basics/ -- version de location de l'atelier (introduction, initialisation-environnement, provisionnement)
            *   index.html
            *   manifest.json
        *   sandbox/ -- Version de modèle d'environnement restreint _requise_ de l'atelier (tous les exercices)
            *   index.html
            *   manifest.json
        *   sandbox-advanced/ -- version sandbox de l'atelier (introduction, initialisation-environnement, chargement de données, requête)
            *   index.html
            *   manifest.json
        *   sandbox-basics/ -- version sandbox de l'atelier (introduction, initialisation-environnement, mise à disposition)
            *   index.html
            *   manifest.json

### location, sandbox et bureau

*   "desktop" - Guide d'atelier affiché dans la session de bureau distant noVNC _required_
*   "location" - Atelier provisionné à l'aide de la location client _requis_
*   "sandbox"- Atelier provisionné à l'aide de"Reserve Workshop on LiveLabs Sandbox" (bouton vert) _requis_
*   "desktop-xxxxxxx" - Guide de sous-ensemble ou mini-atelier affiché dans la session de bureau distant noVNC
*   "tenancy-xxxxxxx" - Sous-ensemble ou mini-atelier provisionné à l'aide de la location client (bouton brun)
*   "sandbox-xxxxxxx"- Sous-ensemble ou mini-atelier provisionné à l'aide de"Reserve Workshop on LiveLabs Sandbox" (bouton vert)

### À propos de l'atelier

L'atelier comprend les 6 exercices individuels en une seule séquence.

La structure de dossiers inclut une "labre" d'introduction qui décrit l'atelier comme un ensemble complet de 6 exercices. Remarque : vous n'avez peut-être pas besoin d'avoir une introduction différente pour chacune des versions parent et enfant des ateliers, ceci est uniquement illustratif.

Consultez le dossier product-name-workshop/tenancy et consultez le fichier manifest.json pour voir la structure.

> **Remarque :** l'utilisation de "Lab n :" dans les titres est facultative.

Le "lab" prérequis est le premier atelier d'un dossier commun sur le référentiel oracle/learning-library. Comme cet exercice existe déjà, nous pouvons utiliser une URL RAW/absolue à la place :

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

Le fichier manifest.json doit connaître l'emplacement de chaque atelier par rapport à l'emplacement dans la hiérarchie. Dans cette structure, les exercices sont situés à deux niveaux, par exemple :

    "filename": "../../provision/provision.md"
    

### Par exemple :

Cet [atelier APEX](https://oracle-livelabs.github.io/apex/spreadsheet/) est un bon exemple d'atelier comportant un seul ensemble d'exercices : [https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).