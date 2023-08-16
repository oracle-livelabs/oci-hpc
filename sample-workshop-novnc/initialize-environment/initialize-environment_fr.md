# Initialiser l'environnement

## Présentation

Dans cet atelier, nous allons passer en revue et démarrer tous les composants requis pour réussir.

_Temps de laboratoire estimé :_ 30 minutes.

### Objectifs

*   Initialisez l'environnement de l'atelier.

### Prérequis

Cet exercice suppose que vous avez :

*   Un compte Oracle Cloud
*   Vous avez terminé :
    *   Laboratoire : préparation de la configuration (_niveau gratuit_ et _locataires payants_ uniquement)
    *   Exercice : Configuration de l'environnement

> **Remarque :** _Lorsque vous effectuez un copier/coller à l'aide de la fonction_ **Copier** _utilisée dans l'ensemble du guide, vous devez appuyer sur la touche_ **Entrée** _après collage. Sinon, la dernière ligne reste dans le tampon jusqu'à ce que vous atteigniez_ **ENTER !**

## Tâche 1 : vérifiez que les processus requis sont en cours d'exécution.

1.  Maintenant, avec accès à votre session de bureau à distance, procédez comme indiqué ci-dessous pour valider votre environnement avant de commencer à exécuter les exercices suivants. Les processus suivants doivent être en fonctionnement :
    
    *   Processus d'écoute de base de données
        *   ÉCOUTEUR (1521)
        *   LISTCDB2 (1522)
    *   Instances de serveur de base de données
        *   CDB1
        *   CDB2
    
    Vous pouvez tester la connectivité de base de données en cliquant sur le signe _+_ en regard des bases de données, comme indiqué ci-dessous dans le panneau _Connexions Oracle SQL Developer_.
    
    ![](./images/19c_hol_landing.png " ")
    
2.  Cliquez sur l'icône _Terminal_ sur le bureau pour lancer une session, puis exécutez la commande suivante pour valider le fonctionnement des processus attendus.
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    Si tous les processus attendus sont affichés dans votre sortie comme indiqué ci-dessus, votre environnement est prêt pour la tâche suivante.
    
3.  Si vous voyez des sorties douteuses, une défaillance ou des composants arrêtés, redémarrez le service en conséquence.
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Tâche 2 : initialisation de la base de données pour les cas d'emploi colocatifs

1.  A partir de votre session de bureau distant en tant qu'utilisateur _oracle_, exécutez le bloc ci-dessous pour actualiser les artefacts des exercices
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

Vous pouvez maintenant [passer à l'exercice suivant](#next).

## Annexe 1 : Gérer les services de démarrage

1.  Service Database (toutes les bases de données et le processus d'écoute standard).
    
    *   Début
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   Arrêter
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   Statut
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   Redémarrer
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  Service Database (processus d'écoute non standard).
    
    *   Début
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   Arrêter
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   Statut
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   Redémarrer
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Accusés de réception

*   **Auteur** - Andy Rivenes, chef de produit principal, Oracle Database In-Memory
*   **Contributeurs** - Kay Malcolm, Didi Han, Rene Fontcha
*   **Dernière mise à jour le/la date** - Rene Fontcha, LiveLabs Platform Lead, NA Technology, octobre 2021