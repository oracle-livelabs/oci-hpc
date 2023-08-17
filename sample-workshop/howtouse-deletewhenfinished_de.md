# Workshop mit einer einzigen Gruppe von Übungen

## Anweisungen - Löschen Sie diese Datei, wenn Sie fertig sind

1.  Öffnen Sie die Beispielworkshopvorlage in Atom oder Visual Studio Code
2.  Wir haben 5 Ordner vorab erstellt. Ein Workshop wird aus mehreren Übungen erstellt.
3.  Entfernen Sie die Kommentare wie die folgende: _Ziele für diese Übung auflisten_
4.  Stellen Sie sicher, dass Sie Ordner mit Kleinbuchstaben, Dateinamen und Bindestriche für Leerzeichen verwenden (setup-adb NOT Setup\_ADB)
5.  Ihre Bildnamen müssen beschreibende Namen haben. Nicht nur adb1, adb2, adb3. Zur Barrierefreiheit für Behinderte benötigen wir die Bildbeschreibungen, um das Aussehen des Bildes zu erläutern. Alle Kleinbuchstaben und Bindestriche speichern.
6.  Laden Sie unsere QS-Dokument von WMS herunter. Wir finden Workshops schneller in der Produktion, wenn Sie wissen, was erforderlich ist, um in die Produktion zu wechseln, und Sie verwenden das Skelett.

PS: Sie benötigen keine Readme.md. Readme ist nur auf den obersten Bibliotheksebenen vorhanden. Wir leiten den gesamten Traffic an LiveLabs weiter, da wir die Nutzung auf GitHub nicht verfolgen können. Erstellen Sie keine direkten Links zu GitHub, Ihr Workshop ist möglicherweise sehr beliebt, aber wir können es nicht verfolgen, so dass niemand weiß.

## Absoluter Pfad für Oracle Cloud-Menünavigation

**Lab 1: Provision an Instance -> Step 0: Use this Standardized Pictures for Oracle Cloud Navigation (Commonly for Provisioning)** - Wir haben eine Liste allgemeiner Screenshots zur Navigation im Oracle Cloud-Menü aufgenommen. Lesen Sie diesen Abschnitt, und verwenden Sie gegebenenfalls die relevanten absoluten Pfadbilder. Dadurch wird Ihr Workshop für den Fall eines Updates der Oracle Cloud-Benutzeroberfläche zukunftssicher.

## Ordnerstruktur

In diesem Beispiel sollen mehrere "Kinder"-Workshops aus einem längeren "übergeordneten" Workshop erstellt werden. Die Kinder bestehen aus Teilen des Elternteils.

Beispielworkshop/ -- Einzellabors

        provision/
        setup/
        dataload/
        query/
        introduction/
          introduction.md       -- description of the everything workshop, note that it is a "lab" since there is only one
    
    workshops/
       freetier/                -- freetier version of the workshop
        index.html
        manifest.json
       livelabs/                -- livelabs version of the workshop
        index.html
        manifest.json
    

### FreeTier vs. LiveLabs

*   "FreeTier" - umfasst kostenlose Versuche, bezahlte Accounts und für einige Workshops, Konten vom Typ "Immer kostenlos" (Braun-Taste)
*   "LiveLabs" - Hierbei handelt es sich um Workshops, in denen von Oracle bereitgestellte Mandanten verwendet werden (grüner Knopf)
*   "Desktop" - Dies ist ein neues Deployment, bei dem der Workshop in einer NoVNC-Umgebung gekapselt wird, die in einer Compute-Instanz ausgeführt wird

### Über den Workshop

Die Werkstatt umfasst alle 6 Einzellabore in einer einzigen Reihenfolge.

Die Ordnerstruktur enthält ein "Einführungslab", das den Workshop als eine vollständige Gruppe von 6 Übungen beschreibt. Hinweis: Möglicherweise müssen Sie nicht für jede übergeordnete und untergeordnete Version der Workshops eine andere Einführung haben. Dies ist nur illustrativ.

Schauen Sie sich den Ordner product-name-workshop/freetier an und sehen Sie sich die Datei manifest.json an, um die Struktur anzusehen.

> **Hinweis:** Die Verwendung von "Lab n:" in den Titeln ist optional.

Das "lab" ist die erste Übung in einem gemeinsamen Ordner im Repository "oracle/learning-library". Da diese Übung bereits vorhanden ist, können wir stattdessen eine RAW/absolute URL verwenden:

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

Die Datei manifest.json muss den Speicherort der einzelnen Übungen in Bezug auf den Speicherort in der Hierarchie kennen. In dieser Struktur befinden sich Übungen zwei Ebenen nach oben. Beispiel:

    "filename": "../../provision/provision.md"
    

### Beispiel:

Dieser [APEX-Workshop](https://oracle.github.io/learning-library/developer-library/apex/spreadsheet/workshops/freetier/) ist ein gutes Beispiel für einen Workshop mit einer einzelnen Gruppe von Übungen: [https://github.com/oracle-livelabs/APEX/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).