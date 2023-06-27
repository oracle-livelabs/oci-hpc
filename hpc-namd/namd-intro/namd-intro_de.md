# NAMD - Einführung

Dieses Runbook enthält die Schritte zum Deployment eines GPU-Rechners auf Oracle Cloud Infrastructure, zur Installation von NAMD und zum Ausführen eines Modells mit NAMD-Software.

NAMD ist eine molekulare Dynamik-Software, die die Bewegungen von Atomen in Biomolekülen unter einem vordefinierten Satz von Bedingungen simuliert. Es wird verwendet, um das Verhalten dieser Biomoleküle zu identifizieren, wenn es Veränderungen in Temperatur, Druck und anderen Eingängen ausgesetzt ist, die den tatsächlichen Bedingungen eines lebenden Organismus nachahmen. NAMD kann verwendet werden, um Muster in der Proteinfaltung, Protein-Ligand-Bindung und Zellmembran-Transport zu etablieren, was es zu einer sehr nützlichen Anwendung für die Arzneimittelforschung und -entdeckung macht.

NAMD basiert auf Charm++ und Converse und kann auf leistungsstarken Computern ausgeführt werden, um die parallele Verarbeitung auszuführen. Er wurde von der University of Illinois entwickelt. Weitere Informationen finden Sie [hier](http://charm.cs.illinois.edu/research/moldyn).

![](./images/protein.gif)

## **Architektur**

Die Architektur für dieses Runbook ist einfach, ein einzelner Rechner, der in einem OCI-VCN mit einem öffentlichen Subnetz ausgeführt wird. Da eine GPU-Instanz verwendet wird, wird der Blockspeicher an die Instanz angehängt und mit der NAMD-Anwendung installiert. Die Instanz befindet sich in einem öffentlichen Subnetz und weist eine öffentliche IP zu, auf die über SSH zugegriffen werden kann.

![](./images/arch-draft.png)

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020