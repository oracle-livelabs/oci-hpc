# Configurer la visualisation

## Présentation

Les charges de travail HPC nécessitent souvent des outils de visualisation pour planifier, surveiller ou analyser le résultat des simulations. Dans ces scénarios, il est souvent souhaité de créer un noeud de visualisation GPU pour une résolution et un post-traitement optimaux. Une interface graphique n'est pas installée par défaut sur les instances OCI. Toutefois, vous pouvez facilement en configurer une à l'aide du protocole d'affichage à distance VNC ou X11. Les sous-sections ci-dessous expliquent comment créer un noeud de visualisation de GPU dans le sous-réseau public à l'aide des commandes TurboVNC et OpenGL.

Temps de laboratoire estimé : 25 minutes

## Tâche 1 : configuration d'un VNC dans votre bastion

Par défaut, le seul accès à la machine Oracle Linux s'effectue via SSH en mode console. Si vous voulez voir l'interface graphique, vous devez configurer une connexion VNC. Le script suivant fonctionne pour l'utilisateur opc par défaut. Le mot de passe de la session VNC est défini sur "HPC\_oci1", mais il peut être modifié dans l'ensemble de commandes suivant. Si vous n'êtes pas connecté au noeud de tête via SSH, faites-le car ces commandes doivent être exécutées sur le noeud de tête.

        sudo yum -y groupinstall "Server with GUI"
        sudo yum -y install tigervnc-server mesa-libGL
        sudo systemctl set-default graphical.target
        sudo cp /usr/lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
        sudo sed -i 's/<USER>/opc/g' /etc/systemd/system/vncserver@:1.service
        sudo sed -ie '/^ExecStart=/a PIDFile=/home/opc/.vnc/%H%i.pid' /etc/systemd/system/vncserver@:1.service
        sudo mkdir /home/opc/.vnc/
        sudo chown opc:opc /home/opc/.vnc
        echo "password" | vncpasswd -f > /home/opc/.vnc/passwd
        chown opc:opc /home/opc/.vnc/passwd
        chmod 600 /home/opc/.vnc/passwd
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
    

## Tâche 2 : ajout d'une instance de GPU

Les étapes ci-dessous sont effectuées à l'aide de OpenGL pour améliorer les cas d'utilisation des GPU sur OCI. Pour plus d'informations, reportez-vous au blog.

1.  Dans la console, accédez à Compute, puis à Instances.
    
2.  Créez une instance de calcul pour le noeud de visualisation :
    
    *   Sélectionnez le domaine de disponibilité souhaité.
    *   Sélectionner la forme de GPU souhaitée (machine virtuelle ou Bare Metal)
    *   Indiquez une image Oracle Linux compatible GPU. La dernière image Oracle Linux sera automatiquement activée par GPU.
    *   Sélectionner le VCN de réseau de cluster et le sous-réseau public
    *   Copier-coller votre clé SSH publique
    *   Cliquez sur Créer.
3.  Attendez que l'instance soit provisionnée, puis connectez-vous à l'instance via :
    
        ssh opc@<public ip> -i <private key> 
        
4.  Installez X Window System, un gestionnaire d'affichage (GNOME/GDM) et un environnement de bureau (MATE) :
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  Installez le serveur VNC et VirtualGL. VirtualGL est un toolkit open source qui permet à n'importe quelle console Linux ou Unix d'exécuter des applications OpenGL avec une accélération matérielle complète.
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  Configurez le serveur X pour activer le partage de GPU pour les sessions virtuelles. Exécutez les commandes suivantes :
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  Configurez le serveur X pour activer le partage de GPU pour les sessions virtuelles. Exécutez les commandes suivantes :
    
        sudo vglserver_config -config -s -f -t
        
8.  Pour éviter d'être verrouillé lors du lancement de l'économiseur d'écran, définissez le mot de passe de l'utilisateur local sur un élément que vous pourrez utiliser ultérieurement :
    
        sudo passwd opc
        
9.  Remplacez votre mot de passe VNC par un mot de passe que vous pouvez utiliser pour vous connecter :
    
        vncpasswd
        
10.  Redémarrez le serveur X :
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  Activer et démarrer GDM :
    
        systemctl enable gdm --now
        
12.  Lancez le serveur VNC :
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  Si vous souhaitez accéder directement au serveur VNC sans transfert SSH, assurez-vous que votre liste de sécurité autorise les connexions sur le port 5901/tcp.
    
    *   Dans la console, accédez à Mise en réseau, puis aux réseaux cloud virtuels.
    *   Sélectionnez Subnets, puis le sous-réseau public.
    *   Dans la liste de sécurité par défaut, ajoutez une règle entrante avec les détails suivants :
        *   Sans conservation de statut : Non
        *   Type de source : CIDR
        *   CIDR source : 0.0.0.0/0
        *   Protocole IP : TCP
        *   Plage de ports source : Tous
        *   Plage de ports de destination : 5901 Remarque : le port VNC standard est 5900 plus un numéro d'affichage (par exemple, 5901 pour :1, 5902 pour :2)
14.  Autorisez l'accès dans les paramètres du pare-feu local, comme suit :
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020