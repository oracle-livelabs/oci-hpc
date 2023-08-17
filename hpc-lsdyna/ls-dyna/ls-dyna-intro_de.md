# LS\_DYNA Einführung

LS-DYNA ist ein allgemeines endliches Elementprogramm, das komplexe reale Probleme simulieren kann. Es wird von der Automobil-, Luft- und Raumfahrt-, Bau-, Militär-, Fertigungs- und Biotechnologieindustrie verwendet. [LS-DYNA-Website](https://www.lstc.com/products/ls-dyna)

Dieses Runbook führt Sie durch den Prozess des Deployments eines LS Dyna-Clusters in Oracle Cloud mit Verbindungen mit geringer Latenz zwischen den Compute Nodes. Die Ausführung von LS Dyna in Oracle Cloud ist ziemlich einfach. Folgen Sie diesem Leitfaden für alle Tipps und Tricks.

![](images/3cars.jpg)

## **Architektur**

Die Architektur für dieses Runbook ist wie folgt: Wir haben eine kleine Maschine (Bastion), mit der Sie sich verbinden. Die Compute Nodes befinden sich in einem separaten privaten Netzwerk, das mit dem RDMA-Netzwerk RoCE v2 verknüpft ist. Die Bastion wird über SSH von allen Personen mit dem Schlüssel (oder VNC) verfügbar sein. Auf Compute Nodes kann nur über die Bastion im Netzwerk zugegriffen werden. Dies wird mit 1 virtuelles Cloud-Netzwerk mit 2 Subnetzen, einem öffentlichen und einem privaten Netzwerk ermöglicht.

### Basisinfrastruktur

Clusternetzwerke werden in den folgenden Regionen unterstützt. In jedem Fall wird empfohlen, die Referenzarchitektur für die Baseline zu verwenden und sie nach Bedarf anzupassen, um Ihre spezifischen Anforderungen zu erfüllen:

*   VCN
    
    *   Öffentliches Subnetz, Sicherheitsliste, Routentabelle
    *   Privates Subnetz, Sicherheitsliste, Routentabelle
    *   Internetgateway
    *   NAT-Gateway
*   Compute Knoten
    
*   Bastionhost in einem öffentlichen Subnetz
    
*   HPC-Compute-Knoten in privatem Subnetz
    

![](images/images.png)

Die oben genannte Baseline-Infrastruktur enthält die folgenden Spezifikationen:

*   Netzwerk
    *   1 x 100-Gbit/s-RDMA über konvergiertes Ethernet (ROCE) v2
    *   Latenzzeit von 1,5 μs
*   HPC-Compute-Knoten (BM.HPC2.36)
    *   6,4 TB lokaler NVME-SSD-Speicher pro Knoten
    *   36 Cores pro Knoten
    *   384 GB Speicher pro Knoten

## Optionale Infrastruktur

### Speicher

Zusätzlich zum NVME-SSD-Speicher, der mit der HPC-Ausprägungen ausgestattet ist, können Sie Block-Volumes auch mit 32 K IOPS pro Volume anschließen, basierend auf dem höchsten SLA für Performance von Oracle. Wenn Sie unsere Lösungen zum Starten der Infrastruktur verwenden, wird standardmäßig ein NFS-Share im NVME-SSD-Speicher in /mnt installiert. Je nach Performanceanforderungen können Sie auch ein eigenes paralleles Dateisystem auf dem NVME-SSD-Speicher oder Blockspeicher installieren.

### Visualizer-Knoten

Sie können je nach Ihren Anforderungen einen Visualizer-Knoten wie eine GPU-VM oder einen BM-Rechner erstellen. Dieser Visualisierungsknoten kann Ihr Bastionhost sein, oder er kann separat sein. Je nach den Sicherheitsanforderungen für die Workload kann der Visualizer-Knoten im privaten oder öffentlichen Subnetz platziert werden.

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020