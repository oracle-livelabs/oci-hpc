# Anwendung ausführen

## Einführung

In dieser Übung führen Sie die Anwendung aus.

Geschätzte Laborzeit: 5 Minuten

## Aufgabe: Anwendung ausführen

Wenn die bereitgestellten Terraform-Skripte zum Starten der Anwendung verwendet werden, wird NAMD mit CUDA im Ordner /mnt/block/NAMD/NAMD\_2.13\_CUDA installiert, und zwei Beispielmodelle sind im Ordner /mnt/block/work/NAMD\_models verfügbar.

1.  Führen Sie NAMD mit CUDA über den folgenden Befehl aus:
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    wobei: - +p - Anzahl der CPU-Cores - +setcpuaffinity - den verfügbaren Cores Threads/Prozesse in der Reihenfolge zuweisen, in der sie vom Betriebssystem nummeriert werden - +devices - Anzahl der GPU-Geräte - +idlepoll - die GPU nach Ergebnissen abfragen, anstatt im Leerlauf zu schlafen - +commap - Kommunikationszuordnung der CPU mit GPUs - output.txt - Ausgabedatei mit der Analyse
    
    Beispiel für BM.GPU2.2:
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    Beispiel für BM.GPU3.8:
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  Führen Sie ns\_per\_day.py für die Ausgabedatei aus, um Nanosekunden pro Tag zu berechnen, die über die protokollierten Timing-Anweisungen gemittelt werden. Damit werden die Performance und Effizienz der Anwendung mit der aktuellen CPU-/GPU-Konfiguration identifiziert.
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020