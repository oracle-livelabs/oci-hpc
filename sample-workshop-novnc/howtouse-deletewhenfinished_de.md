# Workshop mit einer einzigen Gruppe von Übungen

## Anweisungen - Löschen Sie diese Datei, wenn Sie fertig sind

1.  Öffnen Sie die Beispielworkshopvorlage in Atom oder Visual Studio Code
2.  Wir haben 5 Ordner vorab erstellt. Ein Workshop wird aus mehreren Übungen erstellt.
3.  Entfernen Sie die Kommentare wie die folgende: _Ziele für diese Übung auflisten_
4.  Stellen Sie sicher, dass Sie Ordner mit Kleinbuchstaben, Dateinamen und Bindestriche für Leerzeichen verwenden (setup-adb NOT Setup\_ADB)
5.  Ihre Bildnamen müssen beschreibende Namen haben. Nicht nur adb1, adb2, adb3. Zur Barrierefreiheit für Behinderte benötigen wir die Bildbeschreibungen, um das Aussehen des Bildes zu erläutern. Alle Kleinbuchstaben und Bindestriche speichern.
6.  Laden Sie unsere QA Feedback-Dokumentation auf WMS herunter. Wir finden Workshops schneller in der Produktion, wenn Sie wissen, was erforderlich ist, um in die Produktion zu wechseln, und Sie verwenden das Skelett.

PS: Sie benötigen keine Readme.md. Readme ist nur auf den obersten Bibliotheksebenen vorhanden. Wir leiten den gesamten Traffic an LiveLabs weiter, da wir die Nutzung auf GitHub nicht verfolgen können. Erstellen Sie keine direkten Links zu GitHub, Ihr Workshop ist möglicherweise sehr beliebt, aber wir können es nicht verfolgen, so dass niemand weiß.

## Absoluter Pfad für Oracle Cloud-Menünavigation

**Lab 1: Provision an Instance -> Step 0: Use this Standardized Pictures for Oracle Cloud Navigation (Commonly for Provisioning)** - Wir haben eine Liste allgemeiner Screenshots zur Navigation im Oracle Cloud-Menü aufgenommen. Lesen Sie diesen Abschnitt, und verwenden Sie gegebenenfalls die relevanten absoluten Pfadbilder. Dadurch wird Ihr Workshop für den Fall eines Updates der Oracle Cloud-Benutzeroberfläche zukunftssicher.

## Ordnerstruktur

In diesem Beispiel sollen mehrere "Kinder"-Workshops aus einem längeren "übergeordneten" Workshop erstellt werden. Die Kinder bestehen aus Teilen des Elternteils.

*   Beispielworkshop/
    *   einzelne Labore
        *   prepare-setup/ -- enthält den Link zum Herunterladen des ORM/Terraform-Stacks für den Workshop
        *   initialize-environment/ -- Aufgaben zur Initialisierung der Umgebung nach dem Provisioning
        *   Bestimmung/ -- erstes fiktives Labor
        *   Dataload/ - zweites fiktives Labor
        *   Abfrage/ -- drittes fiktives Labor
        *   Einführung/
            *   introduction.md -- Beschreibung des gesamten Workshops; beachten Sie, dass es sich um ein "Labor" handelt, da es nur eine gibt
    *   Workshops/
        *   Desktop/ -- _erforderlich_ Desktopversion des Workshops (alle Übungen)
            *   index.html
            *   manifest.json
        *   Desktop-Erweiterte/ -- Desktop-Version des Workshops (Einführung, Initialisierungsumgebung, Dataload, Abfrage)
            *   index.html
            *   manifest.json
        *   Desktop-Grundlagen/ -- Desktopversion des Workshops (Einführung, Initialisierungsumgebung, Bereitstellung)
            *   index.html
            *   manifest.json
        *   Mandant/ -- _erforderlich_ Mandantenversion des Workshops (alle Übungen)
            *   index.html
            *   manifest.json
        *   Vom Mandanten hinzugefügt/ -- Mandantenversion des Workshops (Einführung, Initialisierungsumgebung, Dataload, Abfrage)
            *   index.html
            *   manifest.json
        *   Mandantengrundlagen/ -- Mandantenversion des Workshops (Einführung, Initialisierungsumgebung, Bereitstellung)
            *   index.html
            *   manifest.json
        *   sandbox/ -- _erforderliche_ Sandbox-Version des Workshops (alle Übungen)
            *   index.html
            *   manifest.json
        *   sandbox-advanced/ -- sandbox-Version des Workshops (Einführung, Initialisierungsumgebung, Dataload, Abfrage)
            *   index.html
            *   manifest.json
        *   Sandbox-Grundlagen/ -- Sandbox-Version des Workshops (Einführung, Initialisierungsumgebung, Bereitstellung)
            *   index.html
            *   manifest.json

### Mandant vs. Sandbox vs. Desktop

*   "desktop" - Workshop-Leitfaden, der in der noVNC-Remote-Desktopsitzung angezeigt wird _erforderlich_
*   "tenancy" - Workshop, der mit Kundenmandanten bereitgestellt wird _erforderlich_
*   "sandbox" - Workshop wird mit "Reserve Workshop on LiveLabs Sandbox" (grüner Knopf) bereitgestellt _erforderlich_
*   "desktop-xxxxxxx" - Untergruppe oder Mini-Workshop-Leitfaden, der in der Remote-Desktopsitzung noVNC angezeigt wird
*   "tenancy-xxxxxxx" - Untergruppe oder Mini-Workshop, der mithilfe des Kundenmandanten bereitgestellt wird (braune Schaltfläche)
*   "sandbox-xxxxxxx" - Untergruppe oder Mini-Workshop bereitgestellt mit "Reserve Workshop on LiveLabs Sandbox" (grüner Knopf)

### Über den Workshop

Die Werkstatt umfasst alle 6 Einzellabore in einer einzigen Reihenfolge.

Die Ordnerstruktur enthält ein "Einführungslab", das den Workshop als eine vollständige Gruppe von 6 Übungen beschreibt. Hinweis: Möglicherweise müssen Sie nicht für jede übergeordnete und untergeordnete Version der Workshops eine andere Einführung haben. Dies ist nur illustrativ.

Sehen Sie sich den Ordner product-name-workshop/tenancy an, und sehen Sie sich die Datei manifest.json an, um die Struktur anzuzeigen.

> **Hinweis:** Die Verwendung von "Lab n:" in den Titeln ist optional.

Das "lab" ist die erste Übung in einem gemeinsamen Ordner im Repository "oracle/learning-library". Da diese Übung bereits vorhanden ist, können wir stattdessen eine RAW/absolute URL verwenden:

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

Die Datei manifest.json muss den Speicherort der einzelnen Übungen in Bezug auf den Speicherort in der Hierarchie kennen. In dieser Struktur befinden sich Übungen zwei Ebenen nach oben. Beispiel:

    "filename": "../../provision/provision.md"
    

### Beispiel:

Dieser [APEX-Workshop](https://oracle-livelabs.github.io/apex/spreadsheet/) ist ein gutes Beispiel für einen Workshop mit einer einzelnen Gruppe von Übungen: [https://github.com/oracle-livelabs/APEX/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).