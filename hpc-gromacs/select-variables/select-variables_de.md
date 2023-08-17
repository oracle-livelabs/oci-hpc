# Variablen auswählen

## Einführung

In dieser Übung füllen Sie die Variablen für Ihren Stack aus.

Geschätzte Laborzeit: 10 Minuten

## Aufgabe: Variablen auswählen

1.  Klicken Sie auf ![](./images/next.png), und füllen Sie die Variablen aus.
    
    **GPU-Knoten**:
    
    *   SHAPE des GPU-COMPUTE-NODE: Ausprägung des COMPUTE Nodes zur Ausführung von Gromacs. Wählen Sie Bare-Metal-Ausprägungen BM.GPU2.2 oder BM.GPU3.8 aus, um die beste Performance zu erzielen.
    *   VERFÜGBARKEITSDOMAIN: AD der Instanz und des Block-Volumes. AD muss verfügbare GPUs haben.
    *   GPU-Knotenanzahl: Anzahl erforderliche GPU-Knoten.
    *   VNC-Typ für GPU: Visualisierungstyp für den Headnode: keine, VNC oder X11VNC.
    *   PASSWORT FÜR DIE VNC-SESSIONS: Kennwort zur Verwendung der VNC-Session auf dem Pre/Post-Knoten.
    
    **Blockoptionen**:
    
    *   BLOCK VOLUME-Größen (GB): Größe des freigegebenen Block-Volumes.
    
    **Gromate**:
    
    *   URL zum Herunterladen von Gromacs: URL der Gromacs 2020.1 kompilierten Binärdateien (ersetzen Sie die URL mit einer anderen Version oder lassen Sie das Feld leer, wenn Sie später herunterladen möchten).
    *   URL zum Herunterladen eines MODEL TARBALL: URL des Modells, das Sie ausführen möchten (ersetzen Sie die URL mit einem anderen Modell, oder lassen Sie das Feld leer, wenn Sie später herunterladen möchten).
    *   URL zum Herunterladen von VMD VISUALIZATION SOFTWARE: URL zum Herunterladen von VMD 1.9.3 zur Visualisierung von Gromacs-Modellen (ersetzen Sie die URL mit einer anderen Visualisierungssoftware, oder lassen Sie das Feld leer, wenn Sie später herunterladen möchten).
2.  Klicken Sie auf ![](./images/next.png). Prüfen Sie die Informationen, und klicken Sie auf ![](./images/create.png)
    

## Bestätigungen

*   **Autor** - Leistungsstarkes Compute-Team
*   **Mitwirkende** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Zuletzt aktualisiert von/Datum** - Samrat Khosla, Oktober 2020