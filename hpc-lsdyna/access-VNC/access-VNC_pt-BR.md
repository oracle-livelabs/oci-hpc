# Acessando um VNC

## Introdução

Neste laboratório, você acessará sua VNC.

Tempo Estimado de Laboratório: 10 minutos

## Tarefa: Acessar sua VNC

1.  Vamos nos conectar por meio de um túnel SSH à instância. Na sua máquina, a conexão usando ssh PORT abaixo será o número que resulta de 5900 + N. N é o número de exibição, se a saída para N foi 1, PORT é 5901, se a saída for 9, PORT é 5909 public\_ip é o endereço IP público do headnode, que está executando o servidor VNC. Se você usou as instruções anteriores, a porta será 5901
    
        <copy>
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        </copy>
        
        
2.  Agora você pode se conectar usando qualquer visualizador VNC usando localhost:N como servidor VNC e a senha definida durante a instalação do vnc. Você pode escolher um cliente VNC que prefira ou usar este guia para instalar na sua máquina local:
    

*   [Windows-TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [MacOS/Windows - RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020