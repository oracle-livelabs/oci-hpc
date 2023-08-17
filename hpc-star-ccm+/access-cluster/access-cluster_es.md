# Acceso a su cluster

## Introducción

En esta práctica, accederá al cluster.

Tiempo estimado de laboratorio: 10 minutos

## Tarea: Acceso a su cluster

1.  La dirección IP pública del bastión se puede encontrar en el menú inferior izquierdo en Salidas. Si navega a sus instancias en el menú principal, también encontrará su instancia de bastión, así como la IP pública.
    
2.  La clave privada para acceder a las máquinas también se puede encontrar allí. Copie el texto en un archivo de su máquina, digamos/home/user/key:
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020