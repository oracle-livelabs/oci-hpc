# Gromacs-Installationsprogramm zu Object Storage hinzufügen

## Einführung

In dieser Übung fügen Sie Object Storage das Gromacs-Installationsprogramm hinzu.

Geschätzte Laborzeit: 5 Minuten

## Aufgabe: Gromacs-Installationsprogramm zu Object Storage hinzufügen

_Beachten Sie, dass die [ZIP-Datei](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip) der Terraform-Skripte, die in diesem Github bereitgestellt werden, bereits Objektspeicher-URLs für Gromacs 2020.1, VMD 1.9.3 und ein Beispiel-Benchmarking-Modell des [Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench) enthalten. Wenn Sie diese URLs ändern möchten, ändern Sie die Datei variable.tf, und ersetzen Sie die Werte für gromacs\_url, model\_url und visualizer\_url durch Ihre eigenen vorab authentifizierten Anforderungs-URLs._

1.  Wählen Sie oben links das Menü ![](./images/menu.png) aus, und wählen Sie "Object Storage" --> "Object Storage" aus.
2.  Erstellen Sie einen neuen Bucket, oder wählen Sie einen vorhandenen Bucket aus. Um eine zu erstellen, klicken Sie auf![](./images/create_bucket.png)
3.  Behalten Sie die Standardoptionen bei: "Standard" als Speicherebenen und "Von Oracle verwaltete Schlüssel". Klicken Sie auf![](./images/create_bucket.png)
4.  Klicken Sie auf den neu erstellten Bucket-Namen, und wählen Sie die Option aus.![](./images/upload_object.png)
5.  Wählen Sie Ihre TAR-Datei für das Gromacs-Installationsprogramm aus, und klicken Sie auf![](./images/upload_object.png)
6.  Klicken Sie auf die 3 Punkte rechts neben dem gerade hochgeladenen Objekt, und wählen Sie "Vorab authentifizierte Anforderung erstellen" aus.
7.  Übernehmen Sie im folgenden Menü die Standardoptionen, und wählen Sie ein Ablaufdatum für die URL des Installationsprogramms. Klicken Sie auf![](./images/pre-auth.png)
8.  Kopieren Sie im nächsten Fenster die URL "PRE-AUTHENTICATED REQUEST", und behalten Sie sie bei. Nach dem Schließen dieses Fensters können Sie es nicht mehr abrufen. Wenn Sie ihn verlieren oder er abläuft, können Sie immer eine andere vorab authentifizierte Anforderung neu erstellen, die eine andere URL generiert

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020