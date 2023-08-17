# Einführung in den Workshop

## Was Sie in diesem Workshop lernen werden

In diesem Workshop lernen Sie, wie Sie die Deepfake-Anwendung [Faceswap](https://faceswap.dev/) auf einer virtuellen GPU-Maschine auf Oracle Cloud Infrastructure ausführen. Alle Schritte vom Provisioning und der Vorbereitung der Instanz zur Verwendung der GPU-Cores zum Ausführen des Python-Skripts (Faceswap GAN) zum Extrahieren der Flächen werden behandelt. Dabei werden die GAN und die Konvertierung der ausgelagerten Fläche in einem Video trainiert.

Geschätzte Workshopzeit: 120 Minuten (exkl. Trainingszeit des GAN)

## Was ist Deepfake

Deepfake ist ein zusammengesetztes Wort von "Deep Learning" und "Fake" und steht für KI-generierte Medien, bei denen eine Person in einem vorhandenen Bild oder Video durch eine andere Person ersetzt oder mit anderen externen Funktionen (wie Alter, Frisur, Hautfarbe, Stimme usw.) modifiziert wird. Die Manipulation von Videos und Bildern gibt es schon seit langem, aber die Nutzung von maschinellem Lernen und KI, um Medien mit hohem Täuschungspotenzial zu produzieren, ist neu. Es ist fast unmöglich, echte und gefälschte Medien mit bloßem Auge zu unterscheiden.

## Wie funktioniert Deepfake?

Deepfakes basieren auf künstlichen neuronalen Netzwerken, die Muster (z.B. Gesichter, Stimmen) in Daten (Fotos oder Videos von Menschen) erkennen. In neuronale Netzwerke werden große Datenmengen eingegeben, die trainiert werden, Muster zu erkennen und zu fälschen. Neuronale Netzwerke sind Algorithmen, die vom menschlichen Gehirn inspiriert werden. Ein Neuron empfängt und überträgt Signale an und von anderen Neuronen durch ihre Synapsen über Achsen und Dendriten, mit dem Ziel, z.B. einen Muskel zu kontrahieren. Wenn sich ein Signal von A nach B bewegt, kann es von Neuronenschichten weitergereicht werden. Ebenso empfängt ein neuronaler Netzwerkalgorithmus Eingabedaten, verarbeitet sie durch eine Schicht mathematischer Operationen (Matrixmultiplikationen), die mit unterschiedlichen Gewichtungen ausgestattet sind, die während des Trainings eingestellt werden, und erzeugt schließlich eine Ausgabe.

Um Matrixmultiplikationen mit 10 Milliarden Parametern zeitnah auszuführen, benötigt man eine ausreichende Rechenleistung. Es ist schneller, alle Vorgänge gleichzeitig anstatt nacheinander auszuführen. Eine CPU führt einen Prozess nach dem anderen mit einer kleinen Anzahl von Threads aus, während eine GPU die parallele Berechnung mit einer großen Anzahl von Threads gleichzeitig erleichtert. Die Möglichkeit, mehrere Berechnungen gleichzeitig zu verarbeiten, macht GPU zu einer besseren Wahl, um neuronale Netzwerkalgorithmen zu trainieren. Die neuronalen Netzwerke, die für Deepfakes verwendet werden, werden als GANs (Generative Adversial Networks) bezeichnet, bei denen zwei Modelle für maschinelles Lernen miteinander konkurrieren. Das erste Modell wird als Generator bezeichnet, der die tatsächlichen Daten liest und gefälschte Daten generiert. Das zweite Modell namens Diskriminator ist für die Erkennung der gefälschten Daten verantwortlich. Der Generator erzeugt Fake-Daten, bis der Diskriminator sie nicht mehr erkennen kann. Je mehr Trainingsdaten für den Generator verfügbar sind, desto einfacher wird es für ihn, eine glaubwürdige Deepfake zu erzeugen.

## Was ist Faceswap?

Die derzeit bekannteste und am weitesten verbreitete Plattform zur Generierung von Deepfakes ist [Faceswap](https://faceswap.dev/). Es handelt sich um eine Open Source-Plattform, die auf Tensorflow, Keras und Python basiert und unter Windows, macOS und Linux ausgeführt werden kann. Eine aktive Community hat sich gebildet, die Informationen über Github und andere Foren austauscht und Skripte und Lösungsvorschläge für Fragen teilt.

Sie können Ihre Deep-Fake-Workloads mit Oracle Free Tier ausführen, das aus dem Angebot "Immer kostenlos" und einer 30-tägigen kostenlosen Testversion besteht. VM.Standard.E2.1. im Always Free Tier. Micro kann verwendet werden, und im 30-Tage-Test können alle verfügbaren VM- und BM-Instanzen getestet werden. Wenn Sie das GPU-Angebot testen möchten, können Sie Ihr Oracle-Cloud-Account auf PAYG upgraden.

## **Bestätigungen**

*   **Erstellt von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021
*   **Zuletzt aktualisiert von/Datum** - Maria Patelkou, HPC Solution Architect, Oracle Proposal to Production-Programm, März 2021