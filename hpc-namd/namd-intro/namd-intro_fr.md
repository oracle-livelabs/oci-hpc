# Introduction à NAMD

Ce Runbook explique comment déployer une machine GPU sur Oracle Cloud Infrastructure, installer NAMD et exécuter un modèle à l'aide du logiciel NAMD.

NAMD est un logiciel de dynamique moléculaire qui simule les mouvements des atomes dans des biomolécules dans un ensemble prédéfini de conditions. Il est utilisé pour identifier le comportement de ces biomolécules lorsqu'elles sont exposées à des changements de température, de pression et d'autres entrées qui imitent les conditions réelles rencontrées dans un organisme vivant. La NAMD peut être utilisée pour établir des modèles dans le pliage des protéines, la liaison protéine-ligand et le transport des membranes cellulaires, ce qui en fait une application très utile pour la recherche et la découverte de médicaments.

NAMD est basé sur Charm++ et Converse et peut s'exécuter sur des ordinateurs hautes performances pour exécuter un traitement parallèle. Il a été développé par l'Université de l'Illinois. Vous trouverez plus d'informations [ici](http://charm.cs.illinois.edu/research/moldyn).

![](./images/protein.gif)

## **Architecture**

L'architecture de ce guide d'exécution est simple, une machine unique s'exécutant dans un VCN OCI avec un sous-réseau public. Etant donné qu'une instance de GPU est utilisée, le stockage de blocs est attaché à l'instance et installé avec l'application NAMD. L'instance se trouve dans un sous-réseau public et une adresse IP publique lui est affectée, accessible via SSH.

![](./images/arch-draft.png)

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020