# Post-traitement

## Présentation

Dans cet atelier, vous allez utiliser le logiciel Visual Molecular Dynamics (VMD) pour analyser les modèles pour post-traitement.

Temps de laboratoire estimé : 5 minutes

## Tâche : post-traitement

1.  Pour le post-traitement, vous pouvez utiliser le logiciel Visual Molecular Dynamics (VMD) pour analyser les modèles. Exécutez les commandes suivantes pour configurer VMD :
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  Si vous utilisez vnc, lancez le serveur VNC et créez un mot de passe VNC comme suit :
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  Démarrez une connexion VNC à l'aide de localhost:5901 (assurez-vous que la mise en tunnel est configurée), puis exécutez les commandes suivantes pour démarrer VMD :
    
        <copy>
        vmd
        </copy>
        
4.  Ouvrez les fichiers de base de données pluggable apoa1 et stmv dans /mnt/block/work/NAMD\_models et commencez à jouer avec vos modèles.
    

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020