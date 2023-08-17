# HPC-Cluster aus Oracle Marketplace Image bereitstellen

## Einführung

In dieser Übung stellen Sie ein HPC-Cluster über den OCI Marketplace bereit. Der OCI Marketplace ist eine zentrale Anlaufstelle für eine Vielzahl von Branchenlösungen, die einfach in unserer Cloud-Konsole bereitgestellt werden können. Hier nutzen Sie diesen Marktplatz für das automatisierte HPC-Cluster-Deployment. Die Vorlage auf dem Markt bietet eine Basis für eine High Performance Computing-Lösung, einschließlich aller erforderlichen Komponenten zum Starten einer Workload mit minimalem Aufwand. Im Verlauf dieser Übungen sehen Sie, wie eine solche Baseline von Drittanbieteranwendungen integriert und genutzt werden kann, um Ihren Workflow zu beschleunigen.

Geschätzte Laborzeit: 60 Minuten

### Ziele

Als Entwickler oder Data Engineer,

*   Zu OCI Marketplace navigieren
*   HPC-Clusterimage aus OCI Marketplace bereitstellen
*   Stack auf OCI anzeigen und ausführen

### Voraussetzungen

*   Ein Oracle Cloud Infrastructure-Account mit Berechtigungen zum Erstellen einer Instanz-VM-Standardausprägung 2.1 oder BM.HPC2.36.

## Aufgabe 1: Zum OCI Marketplace navigieren

1.  Klicken Sie auf das Hamburger-Menü in der oberen linken Ecke. ![](./images/click_hamburger.png)
    
2.  Scrollen Sie nach unten, und klicken Sie auf "Marketplace". ![](./images/click_marketplace.png)
    
3.  Suchen Sie den Marktplatz für **HPC**, und wählen Sie "HPC-Cluster" aus. ![](./images/marketplace.png)
    
4.  Prüfen und akzeptieren Sie die **Oracle-Standardbedingungen und -beschränkungen**, und klicken Sie auf **Stack starten**. ![](./images/launch_stack.png)
    

## Aufgabe 2: HPC-Stack konfigurieren

Nach dem Starten des Stacks werden Sie zum Assistenten zum Erstellen von Stacks umgeleitet, in dem Sie die Stackinformationen eingeben.

1.  Geben Sie unter **Stackinformationen** einen Namen für den Stack an, und _(optional)_ geben Sie an, dass er in einem Compartment bereitgestellt werden soll, in dem Sie sich derzeit nicht befinden. ![](./images/stack_p1.png)
    
2.  Wählen Sie den gewünschten **AD** für die Compute-Ausprägungen und die Bastion aus.
    
3.  Kopieren - Fügen Sie den öffentlichen **ssh-Schlüssel** ein. ![](./images/stack_p2_1.png)
    
4.  Geben Sie die Anzahl der **Compute-Instanzen** für das Cluster ein ![](./images/stack_p2_2.png)
    
5.  Übernehmen Sie die Standardwerte unter **Network Options**. ![](./images/stack_p2_3.png)
    
6.  Klicken Sie auf **Weiter**, um die Konfiguration zu prüfen. ![](./images/stack_p3.png)
    
7.  Klicken Sie auf **Erstellen**.
    

**Hinweis:** Das Provisioning des Stacks dauert etwa 15 Minuten.

## Aufgabe 3: Ausgeführten Stack anzeigen

Nachdem Sie den Stack erstellt haben, werden Sie zu einer Seite umgeleitet, auf der Sie den ausgeführten Stackjob anzeigen können. Es sollte wie unten aussehen. Hier können Sie sehen:

1.  **Logs**
2.  **Variablen**, die an das Terraform-Skript übergeben oder vom Terraform-Skript generiert werden
3.  **Verknüpfte Ressourcen**, die vom Terraform-Skript bereitgestellt werden
4.  **Ausgaben**
5.  **Status anzeigen** des Jobs

![](./images/stack_detail_provisioning.png)

Um zu dieser Seite zu navigieren, auf der Sie alle Stacks in Ihrem aktuellen Compartment anzeigen können, klicken Sie auf das Hamburger-Menü (=) oben links und unter **Lösungen und Plattform** mit der Maus auf **Resource Manager** > **Stacks**.

![](./images/nav_resource_manager.png)

Alles fertig! Damit ist die Demo für das Provisioning eines HPC-Clusters aus Oracle Marketplace Image abgeschlossen.

_Sie können jetzt mit der nächsten Übung fortfahren_

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Zuletzt aktualisiert von/Datum** - Harrison Dvoor, Oktober 2020

# <<<<<<<<

> > > > > > > Upstream/Master