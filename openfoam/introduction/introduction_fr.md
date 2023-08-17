# Exécution de projets OpenFOAM sur des clusters HPC et des machines virtuelles standard sur OCI

## Présentation

Ce guide d'exécution est conçu pour vous aider à évaluer le logiciel CFD OpenFOAM dans Oracle Cloud Infrastructure. Il télécharge et configure automatiquement OpenFOAM. OpenFOAM est le logiciel CFD open source gratuit publié et développé principalement par OpenCFD Ltd depuis 2004. Il dispose d'une large base d'utilisateurs dans la plupart des domaines de l'ingénierie et de la science, des organisations commerciales et universitaires. OpenFOAM possède une vaste gamme de caractéristiques pour résoudre tout ce qui concerne les flux de fluide complexes impliquant des réactions chimiques, des turbulences et des transferts de chaleur, l'acoustique, la mécanique solide et l'électromagnétique.

### A propos du calcul hautes performances

Le calcul hautes performances, ou supercalcul, est comme l'informatique quotidienne, seulement plus puissant. Il s'agit d'un moyen de traiter d'énormes volumes de données à des vitesses très élevées en utilisant plusieurs ordinateurs et dispositifs de stockage comme un tissu cohérent. Le HPC permet d'explorer et de trouver des réponses à certains des plus grands problèmes mondiaux dans les domaines de la science, de l'ingénierie et des affaires.

Un réseau de cluster est un pool d'instances de calcul hautes performances connectées à un réseau à très faible latence et à bande passante élevée. Chaque noeud du cluster est une machine Bare Metal située à proximité physique des autres noeuds. Un réseau RDMA (Remote Direct Memory Access) entre les noeuds fournit une latence aussi faible que les microsecondes à un chiffre, comparable aux clusters HPC sur site.

Le calcul hautes performances est proposé sur Oracle Cloud Infrastructure, dans les régions OCI.

Instance de calcul hautes performances disponible dans l'image Oracle Marketplace et BM.HPC2.36 dans OCI.

Le rack de calcul hautes performances dans l'image Oracle Marketplace comprend les noeuds de cluster HPC, le réseau de cluster et le partage NFS.

Les noeuds de calcul sont connectés via un réseau de cluster qui fournit un accès de stockage RDMA aux noeuds de calcul.

Actuellement, un seul BM par noeud de calcul est pris en charge. Il permet aux clients d'accéder à la racine tout en protégeant le matériel et le réseau, les noeuds de calcul sont virtualisés à l'aide de BM.HPC2.36.

_Temps estimé_ : 2 heures

### Objectifs

*   Exécuter des projets OpenFOAM à l'aide d'instances HPC interconnectées via Remote Direct Memory Access (RDMA)
    
*   Exécuter des projets OpenFOAM à l'aide de machines virtuelles standard sur Oracle Cloud Infrastructure (OCI)
    

### Prérequis

*   Compte Oracle Cloud Infrastructure avec des privilèges permettant de créer un compartiment et une pile.
    
*   Une bonne connaissance de la console OCI et des ressources Oracle Cloud (réseaux cloud virtuels, calcul et stockage) est utile.
    
*   Supposons que vous ayez terminé l'examen associé OCI Cloud Architect.
    

## A propos de cet atelier

*   Exécuter des projets OpenFOAM à l'aide d'un cluster HPC
    
*   Exécuter des projets OpenFOAM à l'aide d'une machine virtuelle standard sur OCI
    

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Dernière mise à jour le/la date** - Harrison Dvoor, janvier 2021