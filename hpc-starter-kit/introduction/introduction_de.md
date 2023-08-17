# Einführung

## Einführung

High Performance Computing verändert die Produktentwicklung und -forschung, sodass Kunden komplexe Probleme schneller lösen können. Dies bedeutet weniger Prototypen, beschleunigt Tests und verkürzt die Markteinführungszeit. Oracle bietet eine On-Demand-HPC-Infrastruktur an, die für alle HPC-Workloads geeignet ist und auf den modernsten Compute-, Speicher-, Netzwerk- und Softwaretechnologien basiert. Sie erhalten all das zu einem Bruchteil der Kosten, die Sie selbst erstellen, und vermeiden Probleme mit der Kapazitätsauslastung.

Oracle bietet eine der elastischsten und skalierbarsten Cloud-Infrastrukturen zur Ausführung Ihrer HPC-Anwendungen. Mit nahezu unbegrenzter Kapazität können Ingenieure, Forscher und HPC-Systemeigentümer über die Grenzen der On-Premise-HPC-Infrastruktur hinaus Innovationen schaffen. Oracle bietet eine integrierte Suite von Services, die alle erforderlichen Services für das schnelle und einfache Erstellen und Verwalten von HPC-Clustern in der Cloud bereitstellt, um die rechenintensivsten Workloads in verschiedenen Branchenlösungen auszuführen. Diese Workloads umfassen traditionelle HPC-Anwendungen wie Genomik, Rechenchemie, Modellierung finanzieller Risiken, computergestütztes Engineering, seismische Bildgebung und Wettervorhersage sowie neue Anwendungen wie maschinelles Lernen, Deep Learning und autonomes Fahren.

Mit Oracle Cloud Infrastructure können Unternehmen performanceintensive HPC-Workloads ausführen, die Millionen IOPs, Latenz im Millisekundenbereich und viele GB/s Bandbreite erfordern. Das sorgt für nutzungsbasierte oder nicht nutzungsabhängige Bezahlung und spart 32% bei den Gesamtbetriebskosten von 3 Jahren.

Diese praktischen Übungen enthalten Schritt-für-Schritt-Anleitungen für das Einrichten und Verwenden der High Performance Computing-Plattform in Oracle Cloud Infrastructure.

Geschätzte Workshopzeit: 4 Stunden

### High Performance Computing (HPC)

HPC oder Supercomputing ist wie alltägliches Computing, nur leistungsfähiger. Es ist eine Möglichkeit, große Datenmengen mit sehr hohen Geschwindigkeiten zu verarbeiten, indem mehrere Computer und Speichergeräte als zusammenhängende Struktur verwendet werden. HPC ermöglicht es, Antworten auf einige der weltweit größten Probleme in Wissenschaft, Technik und Wirtschaft zu finden.

Ein Clusternetzwerk ist ein Pool aus High Performance Computing-(HPC-)Instanzen, die mit einem Netzwerk mit hoher Bandbreite und extrem geringer Latenz verbunden sind. Jeder Knoten im Cluster ist ein Bare-Metal-Rechner, der sich in physischer Nähe zu den anderen Knoten befindet. Ein Remote Direct Memory Access-(RDMA-)Netzwerk zwischen Knoten bietet Latenzzeiten im einstelligen Mikrosekundenbereich, vergleichbar mit On-Premise-HPC-Clustern.

High Performance Computing wird auf Oracle Cloud Infrastructure in OCI-Regionen angeboten.

High Performance Computing-Instanz in Oracle Marketplace Image und BM.HPC2.36 in OCI verfügbar.

High Performance Computing Rack in Oracle Marketplace Image umfasst HPC-Clusterknoten, Clusternetzwerk und NFS-Share.

Die Compute Nodes sind über ein Clusternetzwerk verbunden, das RDMA-basierten Speicherzugriff auf die Compute Nodes bietet.

Derzeit wird ein einzelner BM pro Compute Node unterstützt. Er ermöglicht den Root-Zugriff für Kunden und schützt gleichzeitig Hardware und Netzwerk. Compute Nodes werden mit BM.HPC2.36 virtualisiert.

### Ziele

In dieser Übung führen Sie folgende Schritte aus:

*   Bereiten Sie Ihr privates Netzwerk in Oracle Cloud Infrastructure vor
*   High Performance Compute-Instanz in einem privaten OCI-Netzwerk bereitstellen
*   Entwicklungssystem für die Verwendung mit Ihrer High Performance Compute-Instanz konfigurieren
*   Mit OCI-CLI-Befehlen mit Ihrer High Performance Compute-Instanz arbeiten
*   Automatisierung mit Terraform
*   Führen Sie OpenFoam-Projekte mit Ihrer High Performance Compute-Instanz aus

### Voraussetzungen

*   Ein Verständnis von Cloud- und Datenbankbegriffen ist hilfreich
*   Vertrautheit mit Oracle Cloud Infrastructure (OCI) ist hilfreich
*   Vertrautheit mit Networking ist hilfreich

## Über diesen Workshop

*   Übung 1: High Performance Compute-Instanz in Oracle Cloud Infrastructure einrichten
    
*   Übung 2: High Performance Compute-Instanz mit OCI CLI-Tools verwalten
    
*   Übung 3: Unterstützung bei der Bewertung der CFD-Software OpenFOAM in Oracle Cloud Infrastructure.
    

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Zuletzt aktualisiert von/Datum** - Harrison Dvoor, Oktober 2020