# Acceso a una VNC

## Introducción

En este laboratorio, accederá a su VNC.

Tiempo estimado de laboratorio: 10 minutos

## Tarea: Acceder a VNC

1.  Conectaremos a través de un túnel SSH a la instancia. En la máquina, conéctese con ssh PORT a continuación se mostrará el número que resulta de 5900 + N. N es el número de visualización, si la salida para N era 1, PORT es 5901, si la salida era 9, PORT es 5909 public\_ip es la dirección IP pública del nodo principal, que está ejecutando el servidor VNC. Si utilizó las instrucciones anteriores, el puerto será 5901
    
        <copy>
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        </copy>
        
        
2.  Ahora puede conectarse mediante cualquier visor VNC utilizando localhost:N como servidor VNC y la contraseña que configuró durante la instalación de VNC. Puede elegir un cliente VNC que prefiera o utilizar esta guía para instalar en su máquina local:
    

*   [Windows: TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [MacOS/Windows: RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020