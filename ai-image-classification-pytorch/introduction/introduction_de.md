# Einführung

Die Implementierung eines Bildklassifizierungstrainings ist die "Hallo Welt" der Computer Vision. Obwohl es viele Blogposts zum Thema gibt, präsentieren die meisten von ihnen die Lösung, ohne die Bibliotheks-API, die zugrunde liegenden Principals oder beides angemessen zu erklären. Das Handbuch [Training a Classifier](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) von PyTorch ist ein großartiger Ausgangspunkt, aber einige der Details des Codes werden zu sparsam für ein neues Lesegerät im Feld erklärt. Dieser Workshop baut auf diesem Tutorial als Basis mit zeilenweisen Erklärungen des Codes auf, um einen klareren Lernpfad zu ermöglichen. In diesem Handbuch wird erläutert, wie Sie ein Modell mit CPUs trainieren. Anschließend wird erläutert, wie Sie Code aktualisieren, um GPU-Ressourcen zu nutzen. Beachten Sie, dass GPU-Instanzen nicht auf der Free Tier verfügbar sind.

Geschätzte Übungszeit: 60 Minuten

### Über Tensors

Visuelle Darstellungen helfen bei der Kontextualisierung von Informatikterminologie, Datenstrukturen und Algorithmen. Für die Spannungen bietet [KDnuggets eine klare Erklärung](https://www.kdnuggets.com/2018/05/wtf-tensor.html). Wenn Sie online nach einer Definition suchen, kann es zu Verwechslungen zwischen der technischen Definition einer Tensor- und umgangssprachlichen Verwendung kommen. Technisch ist ein Vektor ein 1-dimensionaler Tensor, und eine Matrix ist ein 2-dimensionaler Tensor, aber meist bezieht sich Tensor auf einen n-dimensionalen Behälter, der größer ist als ein Vektor und eine Matrix.

![Visuelle Darstellung verschiedener Tensoren](images/tensor.png)

Der Begriff ist wichtig zu verstehen, weil die Tensoren für den Aufbau von Algorithmen für künstliche Intelligenz von zentraler Bedeutung sind, und sie unterstützen Diskussionen über die Optimierung von Compute-Ressourcen. Ein häufiges Problem beim Training eines Modells ist die GPU-Auslastung, da NVIDIA [Turing Tensor Cores](https://www.nvidia.com/en-us/data-center/tensor-cores/) eine erhebliche Rechenbeschleunigung erzielen, aber etwas optimieren kann. Glücklicherweise enthält PyTorch einen Data Loader, der die Verantwortung für die Übertragung von Tensoren an die GPU übernimmt.

### Ziele

In dieser Übung behandelte Themen:

*   Tensoren
*   DataLoaders
*   Konvolutionale neuronale Netze (CNN)
*   Verlustfunktion und Optimizer
*   Modelltraining
*   GPU-Beschleunigung;

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Free Tier- oder kostenpflichtiger Cloud-Account von Oracle

## Bestätigungen

*   **Autor** - Justin Blau, Senior Solutions Architect, Big Compute
*   **Zuletzt aktualisiert von/Datum** - Justin Blau, Big Compute, Oktober 2020