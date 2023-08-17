# Acceder a su nodo de GPU

## Introducción

En este laboratorio, accederá a su nodo de GPU.

Tiempo estimado de laboratorio: 5 minutos

## Tarea: Acceder a su nodo de GPU

1.  Una vez que el trabajo se haya completado correctamente en Resource Manager, puede encontrar las direcciones IP públicas para el nodo de GPU y la clave privada en el menú inferior izquierdo en **Salidas**.
    
2.  Copie la clave privada en la máquina local, cambie los permisos de la clave y conéctese a la instancia:
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  Una vez conectado al nodo de GPU, puede ejecutar Gromacs desde /mnt/block. Consulte el archivo README.md para obtener comandos específicos sobre cómo ejecutar Gromacs en la instancia de GPU.
    

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020