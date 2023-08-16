# Inicializar entorno

## Introducción

En este laboratorio, revisaremos e iniciaremos todos los componentes necesarios para ejecutar correctamente este taller.

_Tiempo de laboratorio estimado:_ 30 minutos.

### Objetivos

*   Inicialice el entorno del taller.

### Requisitos previos

En esta práctica se asume que tiene:

*   Una cuenta de Oracle Cloud
*   Ha finalizado:
    *   Laboratorio: prepare la configuración (solo para _nivel gratuito_ y _inquilinos pagados_)
    *   Laboratorio: configuración del entorno

> **Nota:** _Al copiar/pegar utilizando la función conveniente_ **Copiar** _que se utiliza en toda la guía, debe pulsar la tecla_ **INTRO** _después de pegar. De lo contrario, la última línea permanecerá en el buffer hasta que pulse_ **INTRO.**

## Tarea 1: Validar que los procesos necesarios estén activos y en ejecución.

1.  Ahora con acceso a la sesión de escritorio remota, continúe como se indica a continuación para validar el entorno antes de empezar a ejecutar las prácticas posteriores. Los siguientes procesos deben estar activos y en ejecución:
    
    *   Listeners de Base de Datos
        *   LISTENER (1521)
        *   LISTCDB2 (1522)
    *   Instancias del Servidor de Base de Datos
        *   CDB1
        *   CDB2
    
    Puede probar la conectividad de la base de datos haciendo clic en el signo _+_ junto a la base de datos, como se muestra a continuación en el panel _Conexiones de Oracle de SQL Developer_.
    
    ![](./images/19c_hol_landing.png " ")
    
2.  Haga clic en el icono _Terminal_ del escritorio para iniciar una sesión y, a continuación, ejecute lo siguiente para validar que los procesos esperados estén activos.
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    Si todos los procesos esperados se muestran en la salida como se ha visto anteriormente, el entorno estará listo para la siguiente tarea.
    
3.  Si ve salidas cuestionables, fallos o componentes inactivos, reinicie el servicio según corresponda
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Tarea 2: Inicialización de la base de datos para casos de uso de varios inquilinos

1.  Desde la sesión de escritorio remota como usuario _oracle_, ejecute el siguiente bloque para refrescar los artefactos de prácticas
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

Ahora puede [pasar a la siguiente práctica](#next).

## Apéndice 1: Gestión de Servicios de Inicio

1.  Servicio de base de datos (todas las bases de datos y el listener estándar).
    
    *   Inicio
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   Detener
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   Estado
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   Reiniciar
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  Servicio de base de datos ( listeners no estándar).
    
    *   Inicio
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   Detener
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   Estado
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   Reiniciar
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Acuses de recibo

*   **Autor**: Andy Rivenes, director principal sénior de productos, Oracle Database In-Memory
*   **Colaboradores**: Kay Malcolm, Didi Han, Rene Fontcha
*   **Fecha/fecha de última actualización**: Rene Fontcha, responsable de plataforma LiveLabs, tecnología NA, octubre de 2021