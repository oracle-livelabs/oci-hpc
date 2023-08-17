# Verwenden Sie OCI-CLI-Befehle und Terraform für die Arbeit mit HPC

## Einführung

`ocihpc` ist ein Tool zur Vereinfachung der Deployments von HPC-Anwendungen in Oracle Cloud Infrastructure (OCI). Mit der HPC-Befehlszeilenschnittstelle (HPC Command Line Interface, CLI) können Sie HPC-Cluster beliebiger Größe mit unseren dedizierten Bare-Metal-HPC-Compute-Instanzen und Mellanox-RDMA-Netzwerken über einen einzigen Befehl bereitstellen. Sie müssen Terraform oder Oracle Cloud Infrastructure Resource Manager nicht verstehen, um die HPC-CLI zu verwenden. Er basiert auf der Oracle Cloud Infrastructure-Kern-CLI und umfasst vordefinierte Terraform-Skripte.

Die Verwendung von HPC CLI bietet unter anderem folgende Vorteile:

1.  Schnelles Starten eines vollständigen Compute-Clusters mit RDMA über konvergiertes Ethernet (RoCE) v2 über einen einzigen Befehl.
2.  Benutzerfreundlich Keine Kenntnisse zu Terraform oder Oracle Cloud Infrastructure Resource Manager erforderlich, um ein grundlegendes Clusternetzwerk zu starten.
3.  Unterstützt das Message Passing Interface-(MPI-)Deployment umfasst ein vollständiges Set von Softwarepackages für die parallele Verarbeitung mit RDMA, einschließlich Mellanox OFED mit Open MPI, Intel MPI und Platform MPI.
4.  Anpassbar Sie können Ihre eigenen Terrassenform-Skripte ausführen oder die vorhandenen Skripte hinzufügen, die mit dem Tool zur Installation Ihrer eigenen Anwendungen bereitgestellt werden.

Geschätzte Laborzeit: 60 Minuten

### Ziele

Als Entwickler oder Data Engineer,

1.  HPC-Clusternetzwerk mit Oracle-Befehlszeilenschnittstelle bereitstellen
2.  Stack über Cli anzeigen, ausführen und verwalten

### Voraussetzungen

1.  Ein Oracle Cloud Infrastructure-Account mit Benutzerzugangsdaten zur Ausführung eines Terraform-Stacks.
2.  Generierte SSH-Schlüssel

### Policys

Für den OCI-Benutzeraccount, den Sie in `ocihpc` verwenden, müssen die erforderlichen Policys für OCI Resource Manager konfiguriert sein. Informationen zu erforderlichen Policys finden Sie unter [diesem Link](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm).

## Aufgabe 1: ocihpc auf macOS/Linux installieren

1.  Laden Sie das neueste Release mit dem folgenden Befehl herunter, und extrahieren Sie es:
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  Dekomprimieren Sie die Datei, die Sie in Schritt 1 heruntergeladen haben.
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  Machen Sie die ausführbare ocihpc-Binärdatei.
    
        $ chmod +x ./ocihpc 
        
4.  Verschieben Sie die Binärdatei von ocihpc in den Pfad.
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  Testen Sie, ob es funktioniert.
    
        $ ocihpc version 
        

## Aufgabe 2: ocihpc unter Windows installieren

1.  Laden Sie das neueste Release von [diesem Link](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip) herunter, und extrahieren Sie es.
    
2.  Fügen Sie die ocihpc-Binärdatei zu Ihrem PATH hinzu.
    
3.  Testen Sie, ob es funktioniert.
    
        $ ocihpc.exe version 
        

## Aufgabe 3: SSH-Tastatur in der Befehlszeile erstellen

Bitte lesen Sie [diesen Link](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm), wenn Sie Fenster verwenden.

1.  Öffnen Sie eine Shell oder ein Terminal zum Eingeben der Befehle.
    
2.  Navigieren Sie zum Ordner .oci.
    
        $ cd Users/enjli/.ssh
        
3.  Wenn Sie den Ordner noch nicht erstellt haben, erstellen Sie ein .ssh-Verzeichnis, um die Zugangsdaten zu speichern:
    
        $ mkdir Users/enjli/.ssh
        
4.  Wenn im Ordner keine id\_rsa-Schlüsselpaare angezeigt werden, geben Sie den folgenden Befehl ein, und geben Sie bei Aufforderung einen Namen und eine Passphrase ein. Die Schlüssel werden mit den Standardwerten erstellt: RSA-Schlüssel mit 2048 Bit.
    
        $ ssh-keygen
        

## Aufgabe 4: API-Signaturschlüssel generieren

Ihre API-Anforderungen werden mit Ihrem Private Key signiert, und Oracle verwendet den öffentlichen Schlüssel zur Prüfung der Authentizität der Anforderung. Bitte lesen Sie [diesen Link](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other), wenn Sie Fenster verwenden.

1.  Erstellen Sie ein OCI-Verzeichnis, sofern Sie es noch nicht getan haben, um die Zugangsdaten zu speichern:
    
        $ mkdir Users/enjli/.oci
        
2.  Generieren Sie den Private Key mit den folgenden Befehlen.
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  Stellen Sie sicher, dass nur Sie die Private-Key-Datei lesen können:
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  Generieren Sie den Public Key:
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## Aufgabe 5: Public Key zu Oracle Cloud Infrastructure hinzufügen

Nachdem Sie eine Private/Public-Key-Kombination haben, müssen Sie sie der OCI-Konsole unter der Benutzereinstellung hinzufügen:

1.  Navigieren Sie zu Ihrem .oci-Ordner, der `oci_api_key_public.pem` enthält, kopieren Sie den Public Key.
    
2.  Melden Sie sich bei Ihrer OCI-Konsole an, und klicken Sie auf "Menü", und wählen Sie "Identität und Benutzer" aus. Wählen Sie einen Benutzer aus, und navigieren Sie zur Seite "Benutzerdetails".
    
3.  Klicken Sie auf "Public Key hinzufügen" im Abschnitt "API-Schlüssel". ![](./images/ResourcesMenu.png) ![](./images/APIKeys.png)
    
4.  Public Key einfügen, den Sie aus CLI in "Public Key hinzufügen" kopiert haben ![](./images/AddPublicKey.png)
    

## Aufgabe 6: Konfigurieren

Dieser Schritt beschreibt die erforderliche Konfiguration für die CLI und umfasst optionale Konfigurationen, mit denen Sie die CLI-Funktionalität erweitern können.

1.  Bevor Sie die CLI verwenden, müssen Sie eine Konfigurationsdatei im Ordner .oci erstellen, die die erforderlichen Zugangsdaten für die Arbeit mit Ihrem Oracle Cloud Infrastructure-Account enthält. Sie können diese Datei mit einem Setupdialogfeld oder manuell mit einem Texteditor erstellen.
    
2.  Führen Sie _ocihpc configure_ aus, um zu prüfen, ob Sie über eine gültige Konfiguration für den Zugriff auf OCI verfügen. Das Tool führt Sie durch das Erstellen einer Konfiguration.
    

Sie werden benachrichtigt, wenn die Konfigurationsdatei in Folgendes geschrieben wird:

_Konfigurationsdatei gespeichert in: /Users/enjli/.oci/config_

Um die Konfigurationsdatei zu erstellen, benötigen Sie: 1. Ihre Benutzer-OCID (im Profilabschnitt oben rechts auf dem Bildschirm unter > Benutzereinstellungen > Registerkarte für Benutzerinformationen), 2. Mandanten-OCID (Administration > Mandantendetails > Registerkarte "Mandanteninformationen") 3. Die Region, aus der Sie arbeiten (z.B. us-phoenix-1, us-ashburn-1 usw.), 4. Fingerprint des Benutzers (in der Registerkarte für Benutzerinformationen gefunden), 5. Pfad zum privaten API-Signaturschlüssel.

Eine `config`\-Beispieldatei sieht folgendermaßen aus: ![](./images/oci_config.png)

## Aufgabe 7: Liste

Sie können die Liste der verfügbaren Stacks aufrufen, indem Sie `ocihpc list` ausführen.

Beispiel:

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## Aufgabe 8: Initialisieren

1.  Erstellen Sie einen Ordner, den Sie als Deployment-Quelle verwenden.

WICHTIG: Verwenden Sie einen anderen Ordner pro Stack. Initialisieren Sie nicht mehr als einen Stack in demselben Ordner. Andernfalls überschreibt das Tool das vorherige.

2.  Wechseln Sie in diesen Ordner, und führen Sie `ocihpc init <stack name>` aus. `ocihpc` lädt die erforderlichen Dateien in diesen Ordner herunter.
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**WICHTIG**: Bearbeiten Sie den Inhalt der Datei /Users/enjli/ocihpc-test/config.json, bevor Sie den ocihpc-Deployment-Befehl ausführen.

## Aufgabe 9: Deployment

1.  Vor dem Deployment müssen Sie die Werte in der Datei `config.json` ändern. Die Variablen hängen vom bereitgestellten Stack ab. Ein Beispiel für `config.json` für ein Clusternetzwerk sieht folgendermaßen aus:
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  Um die Datei `config.json` zu ändern, navigieren Sie zu Ihrem neu erstellten Verzeichnis (in diesem Fall "ocihpc-test"), und öffnen Sie die Datei "config.json" mit Texteditor oder Notepad. ![](./images/config_json.png)
    

Beachten Sie, dass es sich nicht um dieselbe Konfigurationsdatei handelt, die Sie in Schritt 1 konfiguriert haben.

Für diese Konfigurationsdatei benötigen Sie:

1.  Die Availability-Domaininformationen, die HPC-Ressourcen in unserem Mandanten enthalten (Administration > Mandantendetails > Scrollen Sie nach unten zum Abschnitt "Servicelimits" > "Compute" >, und scrollen Sie nach unten, um "BM.HPC2.36" zu finden). Im folgenden Screenshot sehen Sie, dass insgesamt 6 BM.HPC2.36-Rechner in AD-2 verwendet werden können, von denen 0 derzeit verwendet werden. ![](./images/hpc_resource.png)
    
2.  Die Bastion-AD kann jede ausgewählte AD sein, solange Ressourcen (VM.standard2.1-Ausprägung) vorhanden sind. Bastionausprägungen müssen bereits ausgefüllt werden - VM.Standard2.1
    
3.  Knotenanzahl - im Rahmen dieser Übung gehen wir mit 2 fort, um alle HPC-Ressourcen zu nutzen
    
4.  Unser öffentlicher SSH-Schlüssel
    

**Notizen**

1.  Nachdem Sie die Werte in `config.json` geändert haben, können Sie den Stack mit `ocihpc deploy <arguments>` bereitstellen. Mit diesem Befehl wird ein Stack in Oracle Cloud Resource Manager erstellt und der Stack damit bereitgestellt.
    
2.  Bei unterstützten Stacks können Sie die Anzahl der Knoten festlegen, die Sie bereitstellen möchten, indem Sie sie dem Befehl `ocihpc deploy` hinzufügen. Wenn der Stack ihn nicht unterstützt oder Sie keinen Wert angeben, wird das Tool mit den Standardnummern bereitgestellt.
    
3.  Beispiel: Mit dem folgenden Befehl wird ein Clusternetzwerk mit 5 Knoten bereitgestellt:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  Das Tool generiert einen Deployment-Namen, der aus `<stack name>-<current directory>-<random-number>` besteht.
    
    Beispiel:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## Aufgabe 10: Verbinden

Nach Abschluss des Deployments wird die Bastion-/Headnode-IP angezeigt, mit der Sie eine Verbindung herstellen können:

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

Sie können die Verbindungsdetails auch abrufen, indem Sie den Befehl `ocihpc get ip` ausführen.

## Aufgabe 11: Verwalten

Darüber hinaus können Sie CLI-Befehle verwenden, um Ihre Ressourcen einfach zu verwalten und nachzuverfolgen:

1.  So generieren Sie eine Liste aller Stacks, die in einem bestimmten Compartment bereitgestellt sind: `oci resource-manager job list -c [OCID OF COMPARTMENT]`
    
2.  So generieren Sie eine Liste der Jobs in einem Stack oder Compartment, sortiert nach Erstellungszeit: `oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  So prüfen Sie einen Job zusammen mit den Jobdetails: `oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  So zeigen Sie Logeinträge für den angegebenen Job im JSON-Format an: `oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`
    
5.  So verschieben Sie einen Stack und die zugehörigen Jobs in ein anderes Compartment: `oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## Aufgabe 12: Löschen

Wenn Sie mit dem Deployment fertig sind, können Sie es löschen, indem Sie in den Stackordner wechseln und `ocihpc delete --stack <stack name>` ausführen.

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

Alles fertig! Sie haben Ihre erste High Performance Compute-Instanz erfolgreich mit dem OCI CLI-Tool bereitgestellt.

_Sie können jetzt mit der nächsten Übung fortfahren_

Dies sind detaillierte Informationen zur Verwaltung von High Performance Compute-Instanzen. Eine vollständige Befehlsreferenz finden Sie in der OCI-Dokumentation [hier](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc).

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca
*   **Zuletzt aktualisiert von/Datum** - Harrison Dvoor, Oktober 2020

# <<<<<<<<

> > > > > > > Upstream/Master