# Postprocesamiento

## Introducción

En este laboratorio, utilizará el software de Visual Molecular Dynamics (VMD) para analizar los modelos para el postprocesamiento.

Tiempo estimado de laboratorio: 5 minutos

## Tarea: Postprocesamiento

1.  Para el procesamiento posterior, puede utilizar el software de Visual Molecular Dynamics (VMD) para analizar los modelos. Ejecute los siguientes comandos para configurar VMD:
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  Si utiliza vnc, inicie vncserver y cree una contraseña de vnc de la siguiente manera:
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  Inicie una conexión vnc con localhost:5901 (asegúrese de configurar el túnel) y ejecute los siguientes comandos para iniciar VMD:
    
        <copy>
        vmd
        </copy>
        
4.  Abra los archivos pdb apoa1 y stmv en /mnt/block/work/NAMD\_models y empiece a reproducir con los modelos.
    

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020