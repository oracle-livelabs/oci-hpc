# Ejecución de la aplicación

## Introducción

En esta práctica, ejecutará la aplicación.

Tiempo estimado de laboratorio: 5 minutos

## Tarea: Ejecución de la aplicación

Si las secuencias de comandos de terraform proporcionadas se utilizan para iniciar la aplicación, NAMD con CUDA se instala en la carpeta /mnt/block/NAMD/NAMD\_2.13\_CUDA y hay dos modelos de ejemplo disponibles en la carpeta /mnt/block/work/NAMD\_models.

1.  Ejecute NAMD con CUDA mediante el siguiente comando:
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    donde: - +p - número de núcleos de CPU - +setcpuaffinity - asignar threads/procesos en una operación por turnos a núcleos disponibles en el orden en que están numerados por el sistema operativo - +dispositivos - número de dispositivos de GPU - +idlepoll - sondear la GPU para obtener resultados en lugar de dormir mientras está inactivo - +commap - asignación de comunicación de la CPU con GPU - output.txt - archivo de salida con el análisis
    
    Ejemplo para BM.GPU2.2:
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    Ejemplo para BM.GPU3.8:
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  Ejecute ns\_per\_day.py en el archivo de salida para calcular nanosegundos por día promediados en las sentencias de tiempo registradas. Se utiliza para identificar el rendimiento y la eficiencia de la aplicación mediante la configuración actual de CPU/GPU.
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020