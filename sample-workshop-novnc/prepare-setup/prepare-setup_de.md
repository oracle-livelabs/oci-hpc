# Setup vorbereiten

## Einführung

In dieser Übung erfahren Sie, wie Sie die ZIP-Datei des Oracle Resource Manager-(ORM-)Stacks herunterladen, die für die Einrichtung der für die Ausführung dieses Workshops erforderlichen Ressource erforderlich ist.

_Geschätzte Übungszeit:_ 10 Minuten

### Ziele

*   ORM-Stack herunterladen
*   Vorhandenes virtuelles Cloud-Netzwerk (VCN) konfigurieren

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Einen Oracle Cloud-Account

## Aufgabe 1: ZIP-Datei für den Oracle Resource Manager-(ORM-)Stack herunterladen

1.  Klicken Sie auf den folgenden Link, um die Resource Manager-ZIP-Datei herunterzuladen, die Sie für die Erstellung Ihrer Umgebung benötigen:
    
    _Hinweis 1:_ Wenn Sie einen einzelnen Stackdownload für den Workshop angeben, verwenden Sie diesen einfachen Ausdruck.
    
    *   [Beispiel-mkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _Hinweis 2:_ Wenn Sie mehrere Stacks für denselben Workshop herunterladen, verwenden Sie einen bedingten Ausdruck wie unten. Beachten Sie, dass die Bedingung oder der _Typ_ mit einem gültigen Eintrag in der Datei _manifest.json_ gekoppelt sein müssen. Weitere Informationen finden Sie unter _freetier-advanced_ und _freetier-basics_
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  Speichern Sie diese im Ordner.

Wir empfehlen dringend, mit diesem Stack ein eigenständiges/dediziertes VCN mit Ihren Instanzen zu erstellen. Gehen Sie zu _Aufgabe 3_, um unseren Empfehlungen zu folgen. Wenn Sie ein vorhandenes VCN verwenden möchten, fahren Sie mit der nächsten Aufgabe fort, um das vorhandene VCN mit den erforderlichen Ingress-Regeln zu aktualisieren.

## Aufgabe 2: Sicherheitsregeln zu einem vorhandenen VCN hinzufügen

Für diesen Workshop müssen eine bestimmte Anzahl von Ports verfügbar sein, eine Anforderung, die mit der standardmäßigen ORM-Stackausführung erfüllt werden kann, die ein dediziertes VCN erstellt. Um ein vorhandenes VCN/Subnetz zu verwenden, müssen die folgenden Regeln zur Sicherheitsliste hinzugefügt werden.

| Typ | Quellport | Quell-CIDR | Ziel-Port | Protokoll | Beschreibung |
| :-- | :-: | :-: | :-: | :-: | :-- |
| Ingress | Alle | 0.0.0.0/0 | 22 | TCP | SSH |
| Ingress | Alle | 0.0.0.0/0 | 80 | TCP | Remotedesktop mit noVNC |
| Egress | Alle | N/V | 80 | TCP | Ausgehender HTTP-Zugriff |
| Egress | Alle | N/V | (443) | TCP | Ausgehender HTTPS-Zugriff |
| {: title="Liste der erforderlichen Netzwerksicherheitsregeln"} |  |  |  |  |  |

1.  Gehen Sie zu _Networking >> Virtual Cloud Networks_
2.  Wählen Sie Ihr Netzwerk aus
3.  Wählen Sie unter "Resources" die Option "Security Lists".
4.  Klicken Sie unter der Schaltfläche "Sicherheitsliste erstellen" auf "Standardsicherheitslisten".
5.  Klicken Sie auf die Schaltfläche "Add Ingress Rule".
6.  Geben Sie Folgendes ein:
    *   Quelltyp: CIDR
    *   Quell-CIDR: 0.0.0.0/0
    *   IP-Protokoll: TCP
    *   Quellportbereich: Alle (Standard beibehalten)
    *   Zielportbereich: _Aus obiger Tabelle auswählen_
    *   Beschreibung: _Wählen Sie die entsprechende Beschreibung aus der obigen Tabelle aus._
7.  Klicken Sie auf die Schaltfläche "Add Ingress Rules".
8.  Wiederholen Sie die Schritte \[5-7\], bis eine Regel für jeden in der Tabelle aufgeführten Port erstellt wird

## Aufgabe 3: Compute einrichten

Fahren Sie mit den Details aus den beiden oben genannten Aufgaben mit der Übung _Umgebung einrichten_ fort, um die Workshop-Umgebung mit Oracle Resource Manager (ORM) und einer der folgenden Optionen einzurichten:

*   Stack erstellen: _Compute + Networking_
*   Stack erstellen: _Nur Compute_ mit einem vorhandenen VCN, in dem Sicherheitslisten gemäß _Aufgabe 2_ oben aktualisiert wurden

Sie können jetzt mit der nächsten Übung fortfahren.

## Bestätigungen

*   **Autor** - Rene Fontcha, LiveLabs Platform Lead, NA-Technologie
*   **Mitwirkende** - Meghana Banka
*   **Zuletzt aktualisiert von/Datum** - Rene Fontcha, LiveLabs Platform Lead, NA Technology, August 2022