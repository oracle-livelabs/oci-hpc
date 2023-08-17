# Exécution de LS-DYNA

## Présentation

Dans cet exercice, vous allez exécuter LS-DYNA.

Temps de laboratoire estimé : 10 minutes

## Exécution de LS-DYNA

Exécuter LS-DYNA est assez simple. Pour spécifier l'hôte sur lequel vous devez exécuter, vous devez créer un fichier machine. Vous pouvez la générer comme suit, ou manuellement. Le format est hostname:corenumber pour Platform et IntelMPI.

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

Certains paramètres d'exécution peuvent être spécifiés par un fichier de paramètres : pfile

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

Ce fichier PFILE particulier indique à LSDyna de ne pas effectuer de vidage sur un grand nombre d'informations vers le disque. Utilise de la mémoire pour stocker les fichiers locaux et les fichiers globaux dans /mnt/nfs-share/benchmark/one\_global\_dir.

ANother placez l'emplacement de stockage des fichiers locaux s'il ne tient pas dans la mémoire /mnt/localdisk/tmp pour utiliser NVMe local sur l'ordinateur afin de stocker ces fichiers.

Pour s'exécuter sur plusieurs noeuds, placez le modèle sur le lecteur de partage (Ex:/mnt/nfs-share/work/). L'exemple fourni ici est d'exécuter le modèle des 3 voitures. Vous pouvez l'ajouter au stockage d'objets comme le programme d'installation et le télécharger ou le mettre à l'échelle vers l'ordinateur.

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

Assurez-vous d'avoir défini toutes les variables appropriées pour que mpi s'exécute correctement. Exécutez-l'avec la commande suivante pour Intel MPI (modifiez le nom de modèle et le numéro de coeur) :

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

Pour la plate-forme MPI :

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## Cas spécial

Pour certains modèles, les exécutables Douple Precision ne suffiront pas à décomposer le modèle. Vous pouvez soit utiliser la version à double précision de l'exécutable, soit faire la décomposition en double précision et toujours exécuter en une seule précision pour gagner en vitesse.

Pour la décomposition, ajoutez ncycles=2 à la ligne de commande et ajoutez cette partie au fichier pfile :

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

Le modèle sera décomposé dans un fichier appelé décomposition et stocké dans le répertoire du modèle. Lors de la deuxième exécution, LS-Dyna verra ce fichier et ne refait pas la décomposition.

Pour une exécution avec une précision unique, vous pouvez utiliser les mêmes commandes dans le fichier PFILE.

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020