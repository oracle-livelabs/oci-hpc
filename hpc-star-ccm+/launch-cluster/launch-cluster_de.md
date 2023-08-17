# HPC-Clusternetzwerk starten

## Einführung

Es gibt viele Möglichkeiten, ein HPC-Clusternetzwerk zu starten. In diesem Lösungshandbuch werden zwei verschiedene Methoden behandelt:

*   Via Marketplace
*   Abhängig von Ihrem Betriebssystem möchten Sie eine bestimmte Methode verwenden. Wenn das Marketplace-Image des HPC-Clusternetzwerks oder unsere OCI HPC CN Terraform-Skripte verwendet werden, ist dies nur für Oracle Linux 7 relevant. Wenn Sie CentOS, Ubuntu oder ein anderes BS verwenden möchten, ist eine manuelle Konfiguration erforderlich.

Geschätzte Laborzeit: 30 Minuten

## **OPTION 1**: Erstellung eines Clusternetzwerks über Marketplace

Marketplace enthält Anwendungen und Images, die mit unserer Infrastruktur bereitgestellt werden können. Für Kunden, die Oracle Linux verwenden möchten, ist ein HPC-Clusternetzwerkimage verfügbar, das direkt auf dem Markt gestartet werden kann. Wir empfehlen, das [CFD Ready Cluster](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) zu starten, das Librarys enthält, die für CFD erforderlich sind.

1.  Wählen Sie im Marketplace oben rechts "App abrufen". ![](images/get-app.png " ")
    
2.  Wählen Sie die OCI-Region aus, und klicken Sie auf "Anmelden". ![](images/sign-in.png " ")
    
3.  Prüfen Sie nach der Anmeldung die Version des HPC-Clusterimages, und wählen Sie dann das Compartment aus, in dem das Cluster gestartet wird. Akzeptieren Sie die Vertragsbedingungen, und starten Sie dann den Stack.
    
    ![](images/launch-stack.png " ")
    
4.  Geben Sie die restlichen Details des Stacks ein:
    
    *   Wählen Sie die gewünschte AD für die Compute-Ausprägungen und die Bastion aus.
    *   Kopieren - Fügen Sie Ihren öffentlichen SSH-Schlüssel ein
    *   Geben Sie die Anzahl der Compute-Instanzen für das Cluster ein
    *   Deaktivieren Sie "Install OpenFOAM".
    *   Wenn Sie mehr als 6 TB Shared Disk-Speicherplatz benötigen, aktivieren Sie GlusterFS, und wählen Sie aus, wie viele Server Sie benötigen. (6 TB pro Server)
5.  Klicken Sie auf "Erstellen".
    
6.  Navigieren Sie zu "Terraform-Aktionen", und klicken Sie auf "Anwenden". Dadurch wird das CN-Provisioning gestartet.
    
    ![](images/apply.png " ")
    
7.  Warten Sie, bis der Job "Erfolgreich" angezeigt wird, und navigieren Sie dann zu "Ausgaben", um die privaten IPs der Bastion und Compute Node abzurufen.
    

## **OPTION 2**: Erstellen eines Clusternetzwerks über manuelle Konfiguration

Marketplace enthält Anwendungen und Images, die mit unserer Infrastruktur bereitgestellt werden können. Für Kunden, die Oracle Linux verwenden möchten, können Sie wie folgt manuell ein Clusternetzwerk erstellen:

1.  Wählen Sie oben rechts die OCI-Region aus.
    
2.  Wählen Sie im Hauptmenü "Networking" (Netzwerk und virtuelle Cloud-Netzwerke) aus.
    
3.  Klicken Sie auf den Assistenten "VCN starten", und wählen Sie "VCN mit Internetverbindung" aus
    
4.  Wählen Sie das richtige Compartment aus, und nennen Sie es. Verwenden Sie 172.16.0.0/16 als VCN-CIDR, 172.16.0.0/24 für das öffentliche Subnetz und 172.16.1.0/24 für das private Subnetz. Klicken Sie auf **Weiter** und dann auf **Erstellen**.
    
5.  Wählen Sie im Hauptmenü "Compute", "Instances" und dann "Create Instance" aus.
    
6.  Wählen Sie **Change Image** aus, und wählen Sie Oracle Linux 7 - HPC Cluster Networking Image auf der Registerkarte Oracle Image aus.
    
7.  Wählen Sie die Availability-Domain aus, in der Sie eine BM.HPC2.36-Instanz starten können
    
8.  Ändern Sie die Form in BM.HPC2.36 unter Bare Metal und Specialty.
    
9.  Wählen Sie das von Ihnen erstellte VCN und das öffentliche Subnetz aus.
    
10.  Fügen Sie einen Public Key hinzu, um eine Verbindung zur Instanz herzustellen. Dieser Schlüssel wird auf allen Compute-Instanzen verwendet.
    
11.  Wenn der Rechner hochgefahren ist, klicken Sie auf die erstellte Instanz. Wählen Sie unter "More Actions" die Option "Create Instance Configuration" aus. Sie können die Instanz jetzt unter "Weitere Aktionen" beenden.
    
12.  Wählen Sie im Hauptmenü "Compute" und dann "Clusternetzwerke"
    
13.  Klicken Sie auf "Clusternetzwerk erstellen", und geben Sie alle Optionen ein. Verwenden Sie die soeben erstellte VCN-, private Subnetz- und Instanzkonfiguration. Wählen Sie die AD aus, in der Sie BM.HPC2.36-Instanzen starten können.
    
14.  Starten Sie das Clusternetzwerk.
    
15.  Erstellen Sie beim Laden eine weitere Instanz unter "Hauptmenü", "Compute" und "Instanzen".
    
16.  Platzieren Sie sie in dem soeben erstellten öffentlichen Subnetz. Verwenden Sie dazu den Public Key und die Ausprägung VM.Standard2.1 oder Ähnliches. Hierbei handelt es sich um die Bastion, mit der eine Verbindung zum Cluster hergestellt wird.
    
17.  SCP des Schlüssels zum Cluster auf der Bastion unter /home/opc/.ssh/cluster\_key, und kopieren Sie ihn auch in /home/opc/.ssh/id\_rsa
    
18.  Installieren Sie das Provisioning Tool auf der Bastion mit dem folgenden Befehl:
    
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        
19.  Navigieren Sie in der Konsole zu Compute, und erfassen Sie alle IP-Adressen für den Clusternetzwerkpool. Sie können diesen Befehl auch auf der Bastion verwenden, wenn in Ihrem privaten Subnetz nichts anderes ausgeführt wird.
    
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done
        
20.  Installieren Sie das Provisioning Tool mit dem folgenden Befehl:
    
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020