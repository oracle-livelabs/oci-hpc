# Configurar Visualização

## Introdução

As cargas de trabalho de HPC geralmente requerem ferramentas de visualização para programar, monitorar ou analisar o resultado das simulações. Nesses cenários, geralmente é desejável criar um nó de visualização de GPU para resolução ideal e pós-processamento. Uma GUI não é instalada por padrão nas instâncias do OCI; no entanto, é possível configurar uma facilmente usando o protocolo de exibição remoto VNC ou X11. As subseções abaixo mostrarão como criar um nó de visualização de GPU na sub-rede pública usando TurboVNC e OpenGL.

Tempo Estimado de Laboratório: 25 minutos

## Tarefa 1: Configurando uma VNC no seu Bastion

Por padrão, o único acesso à máquina do Oracle Linux é por meio do SSH em um modo de console. Se você quiser ver a interface gráfica, precisará configurar uma conexão VNC. O script a seguir funcionará para a opc do usuário padrão. A senha da sessão vnc é definida como "HPC\_oci1", mas pode ser editada no próximo conjunto de comandos. Se você não estiver conectado atualmente ao headnode via SSH, faça isso porque esses comandos precisam ser executados no headnode.

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
    

## Tarefa 2: Adicionar uma Instância de GPU

As etapas abaixo são tomadas Usando OpenGL para Aprimorar casos de Uso de GPU no OCI - consulte o blog para obter mais detalhes.

1.  Na Console, navegue até Compute e depois Instance.
    
2.  Crie uma Instância do Serviço Compute para o Nó de Visualização:
    
    *   Selecione o AD desejado
    *   Selecione a forma de GPU desejada (VM ou BM)
    *   Especifique uma imagem do Oracle Linux compatível com GPU. A imagem mais recente do Oracle Linux será ativada automaticamente para GPU.
    *   Selecione a VCN e a Sub-rede Pública da Rede de Clusters
    *   Copie-se sua chave ssh pública
    *   Clique em Criar.
3.  Aguarde o provisionamento da instância e faça log-in na instância por meio de:
    
        ssh opc@<public ip> -i <private key> 
        
4.  Instalar o X Window System, um gerenciador de exibição (GNOME/GDM) e um ambiente de área de trabalho (MATE):
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  Instale o servidor VNC e VirtualGL. Observe que VirtualGL é um kit de ferramentas de código aberto que permite que qualquer console do Linux ou Unix execute aplicativos OpenGL com aceleração completa de hardware.
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  Configure o servidor X para ativar o compartilhamento de GPU para sessões virtuais. Execute os seguintes comandos:
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  Configure o servidor X para ativar o compartilhamento de GPU para sessões virtuais. Execute os seguintes comandos:
    
        sudo vglserver_config -config -s -f -t
        
8.  Para evitar que seja bloqueado quando o protetor de tela for iniciado, defina a senha do usuário local como algo que você possa usar posteriormente:
    
        sudo passwd opc
        
9.  Altere sua senha da VNC para algo que você possa usar para fazer logon:
    
        vncpasswd
        
10.  Reiniciar o X Server:
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  Ativar e Iniciar GDM:
    
        systemctl enable gdm --now
        
12.  Inicie o servidor VNC:
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  Se você quiser acessar o servidor VNC diretamente sem encaminhamento SSH, certifique-se de que sua lista de segurança permita conexões na porta 5901/tcp.
    
    *   Na Console, navegue até Rede e, em seguida, Redes Virtuais na Nuvem.
    *   Selecione Sub-redes e, em seguida, a sub-rede pública.
    *   Na lista de segurança padrão, adicione uma Regra de Entrada com os seguintes detalhes:
        *   Stateless: Não
        *   Tipo de Origem: CIDR
        *   CIDR de Origem: 0.0.0.0/0
        *   Protocolo IP: TCP
        *   Faixa de Portas de Origem: Todos
        *   Faixa de Portas de Destino: 5901 Observação: A porta VNC padrão é 5900 mais um número de exibição (por exemplo, 5901 para :1, 5902 para :2)
14.  Permita o acesso em definições de firewall local, da seguinte forma:
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020