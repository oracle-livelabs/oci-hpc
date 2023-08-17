# LS\_DYNA Introduction

LS-DYNA est un programme d'éléments finis à usage général capable de simuler des problèmes complexes du monde réel. Il est utilisé par les industries de l'automobile, de l'aérospatiale, de la construction, de l'armée, de la fabrication et de la bioingénierie. [Site Web de LS-DYNA](https://www.lstc.com/products/ls-dyna)

Ce Runbook vous guidera tout au long du processus de déploiement d'un cluster LS Dyna sur Oracle Cloud avec un réseau à faible latence entre les noeuds de calcul. L'exécution de LS Dyna sur Oracle Cloud est relativement simple. Suivez ce guide pour tous les conseils et astuces.

![](images/3cars.jpg)

## **Architecture**

L'architecture de ce guide est comme suit, nous avons une petite machine (bastion) dans laquelle vous allez vous connecter. Les noeuds de calcul se trouvent sur un réseau privé distinct lié à la mise en réseau RDMA RoCE v2. Le bastion sera accessible via SSH à partir de toute personne possédant la clé (ou VNC si vous décidez de l'activer). Les noeuds de calcul ne seront accessibles que via le bastion au sein du réseau. Cela est possible avec 1 réseau cloud virtuel avec 2 sous-réseaux, un public et un privé.

### Infrastructure de référence

Les réseaux de cluster sont pris en charge dans les régions suivantes. Dans chaque cas, nous vous recommandons d'utiliser l'architecture de référence de référence et de l'ajuster, le cas échéant, pour répondre à vos besoins spécifiques :

*   VCN
    
    *   Sous-réseau public, liste de sécurité, table de routage
    *   Sous-réseau privé, liste de sécurité, table de routage
    *   passerelle Internet
    *   Passerelle NAT
*   noeuds de calcul
    
*   Hôte de bastion dans un sous-réseau public
    
*   Noeuds de calcul HPC dans un sous-réseau privé
    

![](images/images.png)

L'infrastructure de référence ci-dessus fournit les spécifications suivantes :

*   Réseau
    *   RDMA 1 x 100 Gbits/s sur Ethernet convergé (ROCE) v2
    *   Latence aussi faible que 1,5 μs
*   Noeuds de calcul HPC (BM.HPC2.36)
    *   6,4 To de stockage SSD NVME local par noeud
    *   36 coeurs par noeud
    *   384 Go de mémoire par noeud

## Infrastructure facultative

### Stockage

En plus du stockage SSD NVME fourni avec la forme HPC, vous pouvez également associer des volumes de blocs à 32 ko d'IOPS par volume, soutenu par le contrat de niveau de service le plus élevé d'Oracle en matière de performances. Si vous utilisez nos solutions pour lancer l'infrastructure, un partage NFS est installé par défaut sur le stockage SSD NVME dans /mnt. Vous pouvez également installer votre propre système de fichiers parallèle sur le stockage SSD NVME ou le stockage de blocs, en fonction de vos besoins en performances.

### Noeud Visualizer

Vous pouvez créer un noeud de visualiseur, comme une machine Bare Metal ou de machine virtuelle de GPU, en fonction de vos besoins. Ce noeud de visualiseur peut être votre bastion ou peut être séparé. Selon les exigences de sécurité de la charge globale, le noeud de visualiseur peut être placé dans le sous-réseau privé ou public.

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020