# Configurar e acessar por meio do VNC

## Introdução

Neste laboratório, examinaremos as etapas necessárias para configurar e acessar sua instância do Ubuntu por meio de um cliente VNC (aqui: TigerVNC).

Tempo estimado do workshop: 15 minutos

### Objetivos

*   Conecte-se à sua instância de computação por meio do SSH
*   Configurar e configurar a VNC na sua instância do Ubuntu
*   Acesse sua instância de computação por meio de um cliente VNC (por exemplo, TigerVNC)

### O que você precisa?

*   Laboratório 1 e Laboratório 2 Completos
*   [Adicione uma regra de entrada à lista de segurança de VCN](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using) para permitir tráfego TCP para a porta de portas 5900+N em que N é o número de exibição (geralmente:0 para uma exibição física). ![](images/vncseclist.PNG " ")

## Tarefa 1: Estabelecer conexão com sua instância de computação por meio do SSH

1.  Abra um terminal (também conhecido como linha de comando ou console) de sua escolha. Neste exemplo, trabalhamos em uma máquina local do Windows e usamos o Windows PowerShell como terminal.
    
2.  Observe o endereço IP público da sua instância do Ubuntu que você encontrará em **Compute -> Instâncias** na lista de instâncias do seu compartimento.
    
3.  Em seu terminal, navegue até a pasta em que reside seu par de chaves SSH que você usou durante a criação da instância de computação do Ubuntu. No nosso caso, a pasta é chamada de _ssh_.
    

       <copy>cd .ssh</copy>
    

4.  Digite o comando a seguir para acessar sua instância de computação Ubuntu na qual você precisa substituir a _chave privada_ pela chave privada OpenSSH e o _ip público_ pelo endereço IP público da sua instância do Ubuntu.

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## Tarefa 2: Configurar e configurar o VNC na sua instância do Ubuntu

1.  Instale o servidor VNC executando os comandos abaixo em seu terminal:

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  Instale um ambiente de desktop. Instalaremos o ambiente de desktop Xfce (mas você pode optar por usar outros ambientes de Desktop) digitando o seguinte comando:

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  Execute o comando vncpasswd para definir a senha do servidor VNC.

       <copy>vncpasswd</copy>
    

Quando solicitado, informe sua senha e verifique-a digitando uma segunda vez.

4.  Inicie o Servidor VNC usando o comando:

       <copy>vncserver :1</copy>
    

Você receberá a saída a seguir. ![](images/vncserver.PNG " ")

5.  Elimine o Servidor VNC usando o comando:

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  O arquivo de configuração da VNC está localizado em ~/.vnc/xstartup. Edite-o com o seu editor de texto preferido. Vamos usar vim.

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  Adicione as seguintes linhas ao final do arquivo:

       <copy>exec /usr/bin/startxfce4 &</copy>
    

Caso você esteja trabalhando com vim: Pressione **A** para anexar o texto no final e copiar para colar o comando acima. Alterne para o modo de comando pressionando a tecla ESC. Digite **:wq** - para salvar e sair.

8.  Inicie o Servidor VNC executando o seguinte comando.

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  Feche a conexão SSH com sua instância executando o seguinte comando:

       <copy>exit</copy>
    

## Tarefa 3: Estabelecer conexão com o VNC Desktop

Você pode estabelecer conexão com a área de trabalho remota usando um cliente VNC (por exemplo, TigerVNC) e um túnel SSH.

1.  Crie um túnel SSH para o servidor VNC usando o seguinte comando (em que a chave privada é sua chave OpenSSH privada e public\_ip o endereço IP privado da sua instância de computação Ubuntu):

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  Instale o cliente vncviewer, que será TigerVNC no nosso caso.

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  Faça download e instale um cliente VNC (por exemplo, TigerVNC) na sua máquina local. Conecte-se a **127.0.0.1:5901** usando seu cliente VNC. ![](images/tigervnc.PNG " ")
    
4.  Quando for solicitada a senha, informe a senha criada durante a configuração do servidor VNC. ![](images/tigervncpwd.PNG " ")
    

Seu desktop Ubuntu remoto será aberto. ![](images/vncdesktop.PNG " ")

## **Confirmação**

*   **Criado por/Data** - Maria Patelkou, Arquiteto de Soluções HPC, programa Oracle Proposal to Production, março de 2021
*   **Data da Última Atualização** - Maria Patelkou, Arquiteto de Soluções HPC, programa Proposta para Produção da Oracle, março de 2021