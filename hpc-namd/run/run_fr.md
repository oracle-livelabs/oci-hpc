# Exécuter l'application

## Présentation

Dans cet exercice, vous allez exécuter l'application.

Temps de laboratoire estimé : 5 minutes

## Tâche : exécution de l'application

Si les scripts terraform fournis sont utilisés pour lancer l'application, NAMD avec CUDA est installé dans le dossier /mnt/block/NAMD/NAMD\_2.13\_CUDA et deux exemples de modèle sont disponibles dans le dossier /mnt/block/work/NAMD\_models.

1.  Exécutez NAMD avec CUDA via la commande suivante :
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    où : - +p - nombre de coeurs de processeur - +setcpuaffinity - affecte des threads/processus de manière circulaire aux coeurs disponibles dans l'ordre dans lequel ils sont numérotés par le système d'exploitation - +devices - nombre de périphériques GPU - +idlepoll - interroge le GPU pour obtenir des résultats plutôt que de dormir pendant l'inactivité - +commap - mappage de communication de la CPU avec le fichier de sortie GPU - output.txt - avec l'analyse
    
    Exemple pour BM.GPU2.2 :
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    Exemple pour BM.GPU3.8 :
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  Exécutez ns\_per\_day.py sur le fichier de sortie pour calculer la moyenne des nanosecondes par jour sur les instructions Timing consignées. Elle permet d'identifier les performances et l'efficacité de l'application à l'aide de la configuration CPU/GPU en cours.
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020