# Présentation

## Présentation

Le calcul hautes performances (HPC) modifie le développement de produits et la recherche, ce qui permet aux clients de résoudre plus rapidement les problèmes complexes. Cela signifie moins de prototypes, accélère les tests et réduit le temps de mise sur le marché. Oracle propose une infrastructure HPC à la demande, adaptée à toutes les charges de travail HPC, reposant sur les technologies logicielles, de stockage, de réseau et de calcul les plus avancées. Vous obtenez tout cela pour une fraction du coût de création vous-même et évitez les problèmes d'utilisation de la capacité.

Oracle fournit l'infrastructure cloud la plus élastique et la plus évolutive pour exécuter vos applications HPC. Avec une capacité pratiquement illimitée, les ingénieurs, les chercheurs et les propriétaires de systèmes HPC peuvent innover au-delà des limites de l'infrastructure HPC sur site. Oracle propose une suite intégrée de services qui fournit tout ce qui est nécessaire pour créer et gérer rapidement et facilement des clusters HPC dans le cloud afin d'exécuter les charges de travail les plus exigeantes en calcul sur divers secteurs verticaux. Ces charges de travail couvrent les applications HPC traditionnelles, telles que la génomique, la chimie de calcul, la modélisation des risques financiers, l'ingénierie assistée par ordinateur, l'imagerie sismique et la prévision météorologique, ainsi que les applications émergentes, telles que l'apprentissage automatique, le deep learning et la conduite autonome.

Avec Oracle Cloud Infrastructure, les entreprises peuvent exécuter des charges de travail HPC exigeantes en performances nécessitant des millions d'E/S par seconde, une latence de milliseconde et de nombreux Go/s de bande passante, selon un modèle payant par utilisation ou non mesuré, ce qui permet d'économiser 32 % sur un coût total de possession sur 3 ans.

Ces guides d'exercices pratiques expliquent étape par étape le processus de déploiement de NAMD sur Oracle Cloud à l'aide de 2 méthodes, Resource Manager, disponibles sur la console Oracle Cloud et la console Web.

Durée estimée de l'atelier : 30 minutes

### A propos du calcul hautes performances

Le calcul hautes performances, ou supercalcul, est comme l'informatique quotidienne, seulement plus puissant. Il s'agit d'un moyen de traiter d'énormes volumes de données à des vitesses très élevées en utilisant plusieurs ordinateurs et dispositifs de stockage comme un tissu cohérent. Le HPC permet d'explorer et de trouver des réponses à certains des plus grands problèmes mondiaux dans les domaines de la science, de l'ingénierie et des affaires.

Un réseau de cluster est un pool d'instances de calcul hautes performances connectées à un réseau à très faible latence et à bande passante élevée. Chaque noeud du cluster est une machine Bare Metal située à proximité physique des autres noeuds. Un réseau RDMA (Remote Direct Memory Access) entre les noeuds fournit une latence aussi faible que les microsecondes à un chiffre, comparable aux clusters HPC sur site.

Le calcul hautes performances est proposé sur Oracle Cloud Infrastructure, dans les régions OCI.

Instance de calcul hautes performances disponible dans l'image Oracle Marketplace et BM.HPC2.36 dans OCI.

Le rack de calcul hautes performances dans l'image Oracle Marketplace comprend les noeuds de cluster HPC, le réseau de cluster et le partage NFS.

Les noeuds de calcul sont connectés via un réseau de cluster qui fournit un accès de stockage RDMA aux noeuds de calcul.

Actuellement, un seul BM par noeud de calcul est pris en charge. Il permet aux clients d'accéder à la racine tout en protégeant le matériel et le réseau, les noeuds de calcul sont virtualisés à l'aide de BM.HPC2.36.

### Objectifs

Dans cet exercice, vous allez :

*   Connexion
*   Déploiement
*   Gestionnaire de ressources
*   Licence
*   Exécuter l'application
*   Après traitement

### Prérequis

*   Une certaine compréhension des termes du cloud et de la base de données est utile
*   Une bonne connaissance d'Oracle Cloud Infrastructure (OCI)
*   Connaissance de la mise en réseau utile

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020