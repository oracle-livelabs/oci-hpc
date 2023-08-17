# Ejecución de LS-DYNA

## Introducción

En este laboratorio, ejecutará LS-DYNA.

Tiempo estimado de laboratorio: 10 minutos

## Ejecución de LS-DYNA

Correr LS-DYNA es bastante sencillo. Para especificar el host en el que debe ejecutarse, debe crear un archivo de máquina. Puede generarlo como sigue o manualmente. El formato es el nombre de host:corenumber para Platform y IntelMPI.

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

Algunos parámetros de ejecución se pueden especificar mediante un archivo de parámetros: pfile

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

Este pfile concreto indica a LSDyna que no se vuelque con mucha información en el disco. Utiliza memoria para almacenar archivos locales y almacenar archivos globales en /mnt/nfs-share/benchmark/one\_global\_dir.

ANother coloque para almacenar archivos locales si no cabe en la memoria /mnt/localdisk/tmp para utilizar el NVMe local en la máquina para almacenar esos archivos.

Para ejecutarse en varios nodos, coloque el modelo en la unidad de recurso compartido (Ex:/mnt/nfs-share/work/). Ejemplo proporcionado aquí es ejecutar el modelo de 3 coches. . Puede agregarlo al almacenamiento de objetos como el instalador y descargarlo o enviarlo a la máquina.

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

Asegúrese de que ha definido todas las variables correctas para que mpi se ejecute correctamente. Ejecute este comando con el siguiente para Intel MPI (cambie el nombre de modelo y el número de núcleo):

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

Para MPI de plataforma:

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## Caso especial

Para algunos modelos, los ejecutables de Douple Precision no serán suficientes para hacer la descomposición del modelo. Puede utilizar la versión de precisión doble del ejecutable o puede realizar la descomposición con precisión doble y seguir ejecutándose con precisión única para ganar velocidad.

Para la descomposición, agregue ncycles=2 a la línea de comandos y agregue esta parte al pfile:

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

El modelo se descompondrá en un archivo denominado descomposición y se almacenará en el directorio del modelo. Durante la segunda ejecución, LS-Dyna verá este archivo y no rehacer la descomposición.

Para la ejecución con precisión única, puede utilizar los mismos comandos en el pfile.

## Acuses de recibo

*   **Autor**: equipo de recursos informáticos de alto rendimiento
*   **Colaboradores**: Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Fecha/fecha de última actualización**: Samrat Khosla, octubre de 2020