# GPU-Instanz bereitstellen

## Einführung

In dieser Übung führen wir die erforderlichen Schritte durch, um eine GPU-Instanz auf OCI bereitzustellen, auf der Ubuntu ausgeführt wird.

**Hinweis:** Ein PAYG-Account ist erforderlich, um GPU-Instanzen auf OCI bereitzustellen. Wenn Sie die 30-tägige kostenlose Oracle-Testversion verwenden, können Sie alle verfügbaren VM- und BMs-Instanzen verwenden.

Geschätzte Workshopzeit: 15 Minuten

### Ziele

*   Bei Oracle Cloud anmelden
*   GPU-Instanz auf OCI bereitstellen

### Was benötigen Sie?

*   An user (with a username and password) that is part of a user group on OCI. Check the page [Managing users](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm) to read how **To create a user** and how **To add a user to a group** using the console.
*   Cloud-Accountname - Der Name Ihres Mandanten (vom Administrator oder in Ihrer Oracle Cloud-Begrüßungs-E-Mail angegeben)
*   Eine Benutzergruppe. Auf der Seite [Gruppen verwalten](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managinggroups.htm#three) finden Sie Informationen zum **So erstellen Sie eine Gruppe** mit der Konsole.
*   Zugewiesene Policys für die Gruppe, mit der Benutzer Compute-Instanzen starten können. Prüfen Sie auf der Seite [Allgemeine Policys](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm#top), welche Policys Sie benötigen, **damit Benutzer Compute-Instanzen mit der Konsole starten können**.
*   Ein Compartment für den Benutzer. Auf der Seite [Compartments verwalten](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingcompartments.htm#uscons) finden Sie Informationen zum **So erstellen Sie ein Compartment** mit der Konsole.
*   Prüfen Sie die Servicelimits für die maximal zulässige Anzahl von GPU-Instanzen pro Availability-Domain in der Region, in der Sie die GPU-Instanz bereitstellen möchten. Um das Servicelimit zu erhöhen, gehen Sie wie auf der Seite [Servicelimits](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/servicelimits.htm#top) beschrieben vor.

## Aufgabe 1: Bei Oracle Cloud anmelden

Wenn Sie sich bei Oracle Cloud abgemeldet haben, führen Sie die folgenden Schritte aus, um sich wieder anzumelden.

1.  Gehen Sie zu [cloud.oracle.com](https://cloud.oracle.com), geben Sie Ihren Cloud-Accountnamen ein, und klicken Sie auf **Weiter**. Dies ist der Name, den Sie beim Erstellen Ihres Accounts im vorherigen Abschnitt ausgewählt haben. Es ist NICHT Ihre E-Mail-Adresse. Wenn Sie den Namen vergessen haben, lesen Sie die Bestätigungs-E-Mail.
    
    ![](images/cloud-oracle.png " ")
    
2.  Blenden Sie den Pfeil nach _"Direkte Anmeldung bei Oracle Cloud Infrastructure"_ ein, um die Anmeldeeingabefelder anzuzeigen.
    
    ![](images/cloud-login-tenant.png " ")
    
3.  Geben Sie Ihre Zugangsdaten für den Cloud-Account ein, und klicken Sie auf **Anmelden**. Ihr Benutzername ist Ihre E-Mail-Adresse. Das Passwort ist das, was Sie beim Anmelden für einen Account ausgewählt haben.
    
    ![](images/oci-signin.png " ")
    
4.  Sie sind jetzt bei Oracle Cloud angemeldet.
    
    ![](images/oci-console-home-page.png " ")
    

## Aufgabe 2: GPU-Instanz starten

1.  Klicken Sie nach der Anmeldung im Abschnitt **Schnellaktionen** auf die Schaltfläche **VM-Instanz erstellen**.
    
    ![](images/click-create-vm-instance.png " ")
    

Sie können auch oben links auf das Servicemenü und dann auf **Compute** -> **Instanzen** klicken. Eine neue Seite wird mit der blauen Schaltfläche **Instanz erstellen** geöffnet, auf die Sie klicken.

2.  Geben Sie den Namen für Ihre Compute-Instanz ein.
    
    ![](images/fill-in-name.PNG " ")
    
3.  Wählen Sie Ihr Compartment in der Dropdown-Liste aus.
    
    ![](images/select-compartment.PNG " ")
    
4.  Bevor Sie die Availability-Domain auswählen, prüfen Sie unter **Governance** -> **Limits, Quota und Nutzung**, in der die Availability-Domain der ausgewählten Region GPU-Instanzen verfügbar sind. Wählen Sie die Availability-Domain aus, in der die Compute-Instanz bereitgestellt wird.
    
    ![](images/select-AD.PNG " ")
    
5.  Klicken Sie auf die Schaltfläche **Bild ändern**.
    
    ![](images/change-image.PNG " ")
    
    Wählen Sie das Image aus, das Sie für Ihre Compute-Instanz verwenden möchten, und klicken Sie auf die Schaltfläche **Image auswählen**. Wir wählen Ubuntu 18.04 aus, das von Faceswap empfohlen wird, aber auch andere Linux-Distributionen und Windows-Betriebssysteme können verwendet werden.
    
    ![](images/select-image.PNG " ")
    
6.  Klicken Sie auf die Schaltfläche **Einheit ändern**.
    
    ![](images/change-shape.PNG " ")
    
    Wählen Sie **Virtuelle Maschine** und **Spezialität und Legacy** aus. Dort finden Sie die GPU-Instanz und die VM.Standard.E2.1 vom Typ "**Immer kostenlos**". Micro-Ausprägung (wenn Sie nicht Eigentümer eines PAYG-Accounts sind, um GPU-Instanzen auszuführen).
    
    ![](images/select-VM-SL.PNG " ")
    
    Wählen Sie die GPU-Instanz aus, die Sie bereitstellen möchten, lesen und aktivieren Sie das Kontrollkästchen _Ich habe die folgenden Dokumente geprüft und akzeptiert: Nutzungsbedingungen für Oracle und NVIDIA_, und klicken Sie auf die Schaltfläche **Ausprägung auswählen**. In unserem Fall ist es die VM.GPU2.1, die auf der P100 (NVIDIA Pascal-GPU-Architektur) mit 1 GPU basiert. Sie können auch Servicelimits für virtuelle und Bare-Metal-Maschinen basierend auf V100 (GPU-Architektur von NVIDIA Volta) und A100 (GPU-Architektur von NVIDIA Ampere) anfordern.
    
    ![](images/select-GPU.PNG " ")
    
7.  Wenn Sie bereits ein VCN in Ihrem Compartment erstellt haben, wählen Sie das VCN und das Subnetz aus, in dem Sie die Compute-Instanz platzieren möchten. Andernfalls können Sie auf **Neues virtuelles Cloud-Netzwerk erstellen** klicken, einen Namen und ein Compartment für das VCN sowie einen Namen für das öffentliche Subnetz auswählen. Sie können den Standard-CIDR-Block beibehalten oder anpassen. Sie können Ihrer Instanz eine öffentliche IP-Adresse zuweisen. Wenn Sie eine öffentliche IP-Adresse zuweisen, kann diese Instanz über das Internet aufgerufen werden. Wenn Sie nicht sicher sind, ob Sie eine öffentliche IP-Adresse benötigen, können Sie diese später immer zuweisen.
    
    ![](images/create-vcn.PNG " ")
    
8.  Wenn Sie das SSH-Public- und -Private-Key-Paar verwenden möchten, das Sie in _Lab 1 - Create SSH keys_ erstellt haben, können Sie entweder auf **Choose public keys files** klicken und die SSH-Public-Key-Datei(en) auswählen oder auf **Public Keys einfügen** klicken und den bzw. die SSH-Public Key in das Feld einfügen. Sie können auch einen öffentlichen und privaten SSH-Schlüssel auf OCI generieren und auf Ihrem lokalen Rechner speichern. Wenn Sie keine SSH-Schlüssel angeben, können Sie keine SSH-Verbindung zur Instanz herstellen.
    
    ![](images/add-SSH.PNG " ")
    
9.  Im letzten Schritt geben Sie die Boot-Volume-Größe an. Dabei legen Sie fest, ob die Verschlüsselung während der Übertragung verwendet werden soll und wie die Verschlüsselungsschlüssel verwaltet werden sollen. Sie können die standardmäßige Boot-Volume-Größe mit 46,6 GB für Linux-Syxtems beibehalten oder diese auf maximal 32 TB erhöhen. Sie können alle Kontrollkästchen deaktiviert lassen und auf **Erstellen** klicken (um die Erstellung der Compute-Instanz abzuschließen).
    

![](images/boot-volume.PNG " ")

Nach einigen Minuten ändert Ihre GPU-Compute-Instanz ihren Status in **RUNNING**.

![](images/instance-running.PNG " ")

## **Bestätigungen**

*   **Erstellt von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021
*   **Zuletzt aktualisiert von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021