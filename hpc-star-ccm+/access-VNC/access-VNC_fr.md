# Accéder à VNC

## Présentation

Dans cet exercice, vous allez accéder à votre instance VNC.

Temps de laboratoire estimé : 10 minutes

## Tâche : accédez à VNC

1.  Nous allons nous connecter via un tunnel SSH à l'instance. Sur votre ordinateur, la connexion à l'aide de ssh PORT ci-dessous sera le nombre qui résulte de 5900 + N. N est le nombre d'affichage, si la sortie pour N était 1, PORT est 5901, si la sortie était 9, PORT est 5909 public\_ip est l'adresse IP publique du noeud de tête, qui exécute le serveur VNC. Si vous avez utilisé les instructions précédentes, le port sera 5901
    
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        
2.  Vous pouvez désormais vous connecter à l'aide de n'importe quel VNC Viewer en utilisant localhost:N en tant que serveur VNC et le mot de passe que vous avez défini lors de l'installation du VNC. Vous pouvez choisir un client VNC que vous préférez ou utiliser ce guide pour l'installation sur votre ordinateur local :
    

*   [Windows-TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [MacOS/Windows - RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## Accusés de réception

*   **Auteur** - Equipe de calcul hautes performances
*   **Contributeurs** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Dernière mise à jour le/la** - Samrat Khosla, octobre 2020