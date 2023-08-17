# OpenFOAM-Projekte auf HPC-Clustern und Standard-VMs auf OCI ausführen

## Einführung

Dieses Runbook unterstützt Sie bei der Bewertung der CFD-Software OpenFOAM in Oracle Cloud Infrastructure. OpenFOAM wird automatisch heruntergeladen und konfiguriert. OpenFOAM ist die kostenlose Open-Source-CFD-Software, die seit 2004 in erster Linie von OpenCFD Ltd veröffentlicht und entwickelt wurde. Es verfügt über eine große Nutzerbasis in den meisten Bereichen Technik und Wissenschaft, sowohl von kommerziellen als auch akademischen Organisationen. OpenFOAM verfügt über eine umfangreiche Palette von Funktionen, um alles von komplexen Flüssigkeitsströmen mit chemischen Reaktionen, Turbulenzen und Wärmeübertragung, Akustik, Festkörpermechanik und Elektromagnetik zu lösen.

### High Performance Computing (HPC)

HPC oder Supercomputing ist wie alltägliches Computing, nur leistungsfähiger. Es ist eine Möglichkeit, große Datenmengen mit sehr hohen Geschwindigkeiten zu verarbeiten, indem mehrere Computer und Speichergeräte als zusammenhängende Struktur verwendet werden. HPC ermöglicht es, Antworten auf einige der weltweit größten Probleme in Wissenschaft, Technik und Wirtschaft zu finden.

Ein Clusternetzwerk ist ein Pool aus High Performance Computing-(HPC-)Instanzen, die mit einem Netzwerk mit hoher Bandbreite und extrem geringer Latenz verbunden sind. Jeder Knoten im Cluster ist ein Bare-Metal-Rechner, der sich in physischer Nähe zu den anderen Knoten befindet. Ein Remote Direct Memory Access-(RDMA-)Netzwerk zwischen Knoten bietet Latenzzeiten im einstelligen Mikrosekundenbereich, vergleichbar mit On-Premise-HPC-Clustern.

High Performance Computing wird auf Oracle Cloud Infrastructure in OCI-Regionen angeboten.

High Performance Computing-Instanz in Oracle Marketplace Image und BM.HPC2.36 in OCI verfügbar.

High Performance Computing Rack in Oracle Marketplace Image umfasst HPC-Clusterknoten, Clusternetzwerk und NFS-Share.

Die Compute Nodes sind über ein Clusternetzwerk verbunden, das RDMA-basierten Speicherzugriff auf die Compute Nodes bietet.

Derzeit wird ein einzelner BM pro Compute Node unterstützt. Er ermöglicht den Root-Zugriff für Kunden und schützt gleichzeitig Hardware und Netzwerk. Compute Nodes werden mit BM.HPC2.36 virtualisiert.

_Geschätzte Zeit_: 2 Stunden

### Ziele

*   Führen Sie OpenFOAM-Projekte mit HPC-Instanzen aus, die über RDMA (Remote Direct Memory Access) miteinander verbunden sind
    
*   Führen Sie OpenFOAM-Projekte mit regulären standardmäßigen virtuellen Maschinen (VMs) auf Oracle Cloud Infrastructure (OCI) aus
    

### Voraussetzungen

*   Ein Oracle Cloud Infrastructure-Account mit Berechtigungen zum Erstellen eines Compartments und eines Stacks.
    
*   Die Vertrautheit mit der OCI-Konsole und den Oracle Cloud-Ressourcen (d.h. virtuelle Cloud-Netzwerke, Compute und Speicher) ist hilfreich.
    
*   Es wird davon ausgegangen, dass Sie die OCI Cloud Architect Associate-Prüfung abgeschlossen haben.
    

## Über diesen Workshop

*   OpenFOAM-Projekte mit HPC-Cluster ausführen
    
*   Führen Sie OpenFOAM-Projekte mit der Standard-VM auf OCI aus
    

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Zuletzt aktualisiert von/Datum** - Harrison Dvoor, Januar 2021