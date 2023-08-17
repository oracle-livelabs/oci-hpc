# LS-DYNA ausführen

## Einführung

In dieser Übung führen Sie LS-DYNA aus.

Geschätzte Laborzeit: 10 Minuten

## LS-DYNA ausführen

Das Laufen von LS-DYNA ist ziemlich einfach. Um den Host anzugeben, auf dem Sie ausführen müssen, müssen Sie eine Rechnerdatei erstellen. Sie können ihn wie folgt oder manuell generieren. Das Format ist hostname:corenumber für Platform und IntelMPI.

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

Einige Ausführungsparameter können durch eine Parameterdatei angegeben werden: pfile

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

Diese bestimmte PFILE weist LSDyna an, nicht auf viele Informationen auf den Datenträger zu schreiben. Verwendet Speicher, um lokale Dateien zu speichern und globale Dateien in /mnt/nfs-share/benchmark/one\_global\_dir zu speichern.

ANother Platz zum Speichern lokaler Dateien, wenn sie nicht in den Speicher passen, ist /mnt/localdisk/tmp, um das lokale NVMe auf dem Rechner zum Speichern dieser Dateien zu verwenden.

Um auf mehreren Knoten auszuführen, platzieren Sie das Modell auf dem Share-Laufwerk (Ex:/mnt/nfs-share/work/). Hier wird beispielsweise das Modell 3 Autos ausgeführt. Sie können ihn dem Objektspeicher hinzufügen, wie das Installationsprogramm, und es herunterladen oder auf den Rechner scannen.

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

Stellen Sie sicher, dass alle richtigen Variablen für MPI korrekt ausgeführt werden. Führen Sie es mit dem folgenden Befehl für Intel MPI aus (ändern Sie den Modelnamen und die Core-Nummer):

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

Für Plattform MPI:

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## Sonderfall

Bei einigen Modellen reichen Douple Precision-Programmdateien nicht aus, um die Zerlegung des Modells durchzuführen. Sie können entweder die Version mit doppelter Genauigkeit der ausführbaren Datei verwenden oder die Zersetzung mit doppelter Genauigkeit durchführen und dennoch die Ausführung mit einer einzigen Genauigkeit durchführen, um die Geschwindigkeit zu erhöhen.

Fügen Sie für die Zerlegung der Befehlszeile ncycles=2 hinzu, und fügen Sie diesen Teil der pfile hinzu:

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

Das Modell wird in einer Datei mit dem Namen Zerlegung zerlegt und im Verzeichnis des Modells gespeichert. Während der zweiten Ausführung wird LS-Dyna diese Datei sehen und nicht die Zersetzung wiederholen.

Für die Ausführung mit Single Precision können Sie dieselben Befehle in der PFILE verwenden.

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020