# Classifier trainieren

## Einführung

Das Handbuch [Training a Classifier](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) von PyTorch ist ein großartiger Ausgangspunkt, aber einige der Details des Codes werden zu sparsam für ein neues Lesegerät im Feld erklärt. Diese Übung baut auf diesem Tutorial als Basis mit zeilenweisen Erklärungen des Codes auf, um einen klareren Lernpfad zu ermöglichen.

Geschätzte Laborzeit: 45 Minuten

### Ziele

In dieser Übung lernen Sie Folgendes kennen:

*   DataLoaders
*   Konvolutionale neuronale Netze (CNN)
*   Verlustfunktion und Optimizer
*   Modelltraining

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Free Tier- oder kostenpflichtiger Cloud-Account von Oracle
*   Zugriff auf eine CPU- oder GPU-Instanz gemäß den Übungen 1 und 2

## Aufgabe 1: Umgebung einrichten

Es gibt eine Reihe von Methoden zum Einrichten einer python-Umgebung zur Ausführung von PyTorch. Sie können Ihre bevorzugte Methode oder die folgenden Anweisungen befolgen.

1.  Rufen Sie einen Link für die [neueste Version von miniconda](https://docs.conda.io/en/latest/miniconda.html) auf, indem Sie zu der Site navigieren, indem Sie zu "Linux-Installationsprogramme" scrollen und den oberen Link kopieren.
    
2.  Verwenden Sie in einem Terminal für die Instanz wget, um die Datei herunterzuladen.
    
         wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
        
3.  [Führen Sie den Installationsbefehl aus](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html), und schließen Sie das Setup ab.
    
         bash Miniconda3-latest-Linux-x86_64.sh
        
4.  Beenden Sie das Terminal, und öffnen Sie nach der Installation ein neues Terminal, das sicherstellt, dass conda aktiviert ist. Optional: Erstellen Sie eine bestimmte Umgebung für PyTorch, indem Sie den Befehl `conda create --name pytorch python=3.6` ausführen und dann die Umgebung aktivieren, indem Sie `conda activate pytorch` ausführen.
    
5.  [Installieren Sie PyTorch](https://pytorch.org/) über conda `conda install pytorch torchvision cudatoolkit=10.2 -c pytorch`
    

## Aufgabe 2: CIFAR10 und DataLoader

Das vollständige Codebeispiel befindet sich unten auf dieser Seite.

Der Großteil der Zeit eines AI-Forschers wird nicht für die Ausführung von Schulungs- und Testmodellen, sondern für die Bereinigung und Vorbereitung von Daten verwendet. Eine unglaubliche Menge an Arbeit wurde in die wenigen Zeilen des Codes unten kondensiert. CIFAR10 ist ein vollständig vorverarbeitetes Dataset mit Bildern und Labels. Es wurde erstellt und veröffentlicht, um als standardisierte Benchmark für das Testen neuer Modelle oder den Einstieg in Deep Learning zu dienen. Die Transformation eines Sets von Rohdaten in eine Struktur, die mit PyTorch funktioniert, kann viel Zeit in Anspruch nehmen, um diese Barriere zu verringern, die das Dataset in die PyTorch-Bibliotheksverteilung aufgenommen wurde.

    import torch
    import torchvision
    import torchvision.transforms as transforms
    
    transform = transforms.Compose(
        [transforms.ToTensor(),
         transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    
    trainset = torchvision.datasets.CIFAR10(root='./data', train=True,
                                            download=True, transform=transform)
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4,
                                              shuffle=True, num_workers=2)
    
    testset = torchvision.datasets.CIFAR10(root='./data', train=False,
                                           download=True, transform=transform)
    testloader = torch.utils.data.DataLoader(testset, batch_size=4,
                                             shuffle=False, num_workers=2)
    
    classes = ('plane', 'car', 'bird', 'cat',
               'deer', 'dog', 'frog', 'horse', 'ship', 'truck')
    

[transform.Compose](https://pytorch.org/docs/stable/torchvision/transforms.html) stellt sicher, dass die Daten das für das Modell erforderliche Tensorformat aufweisen und [normalisiert](https://discuss.pytorch.org/t/understanding-transform-normalize/21730) wurden, um sicherzustellen, dass alle Werte in einen definierten Bereich passen. Durch die Normalisierung von Daten kann das Modelltraining häufig verbessert werden, da dadurch die Werte im Dataset konsistenter werden.

Mit diesen Codezeilen können Sie das Dataset herunterladen, Bilder in drei Farbkanäle (rot, grün und blau) aufteilen, sie mit dem richtigen Label (Flugzeug, Vogel, Hund, Frosch usw.) abgleichen und alle Bilder und Labels in [torch.Tensors](https://pytorch.org/docs/stable/tensors.html) konvertieren. Die Ausgabe der Methode [torchvision.datasets.CIFAR10](http://pytorch.org/docs/stable/torchvision/datasets.html?highlight=cifar10#torchvision.datasets.CIFAR10) ist ein Objekt, das verwendet werden kann. Die nächste Methode, [torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html?highlight=dataloader#torch.utils.data.DataLoader), verwaltet die logistischen Aufgaben bei der Optimierung von Compute-Ressourcen. Bei GPU-Systemen müssen Daten zunächst in den CPU-Speicher geladen und dann zur beschleunigten parallelen Verarbeitung in den GPU-Speicher übertragen werden. Im Idealfall gibt es genug CPU-Worker, um sicherzustellen, dass verarbeitete Daten abgezogen werden und unverarbeitete Daten diese so schnell wie möglich ersetzen.

Die GPU-Auslastung kann in Abhängigkeit von der Größe des Datasets und dem Berechnungsprozess für das Modell variieren. Die Entwicklung eines effizienten Datenladealgorithmus und die Durchführung von künstlicher Intelligenz sind in der Regel zwei unterschiedliche Fähigkeiten. Daher hat das PyTorch-Team eine allgemeine, optimierte Methode zur Bearbeitung dieser Aufgabe für torch.Tensor erstellt. Um die GPU-Auslastung zu verbessern, können Sie die Hyperparameter für den Data Loader wie `batch_size` und `num_workers` anpassen. In diesem Fall ist `batch_size` 4 und `num_workers` 2. Das bedeutet, dass zwei CPU-Prozesse vier Images gleichzeitig in die GPU laden. Je nach Größe der Images und des verwendeten Systems können Sie diese Werte anpassen, um eine optimale GPU-Auslastung zu erzielen. Dieses spezielle Beispiel stellt keine besondere Herausforderung für die GPU dar, da das neuronale Netzwerk flach und der Datensatz klein ist. Wenn Sie jedoch Ihre eigenen Modelle mit mehr Kanälen und einem größeren Datensatz entwickeln, ist der Performanceanstieg wichtiger.

In diesem Beispiel werden zwei Datasets verwendet. CIFAR10 ist in ein _Training_\-Dataset aufgeteilt, das zur Entwicklung des Modells iteriert wird, und ein _Test_\-Dataset mit nicht sichtbaren Daten, mit dem die Performance des Modells getestet wird. Wenn Sie mit Ihrem Dataset arbeiten, sollten Sie Ihre Rohdaten in 80 Prozent aufteilen, die für Schulungen verwendet werden, und 20 Prozent für Tests. Sie sollten das Testen mit Daten vermeiden, die zum Trainieren des Modells verwendet werden, da dadurch Ihre Fähigkeit zur Erkennung von [Überlagerungen](https://www.kdnuggets.com/2019/12/fighting-overfitting-deep-learning.html) eingeschränkt wird, ein Problem, das auftritt, wenn Ihr Modell gut mit Trainingsdaten, aber nicht mit neuen Daten funktioniert.

## Aufgabe 3: Konvolutionelles Neuronales Netzwerk

[Konvolutionales neuronales Netzwerk definieren](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#define-a-convolutional-neural-network), definiert das Modell. Das Dataset besteht aus Bildern, die in drei Farbkanäle unterteilt sind: rot, grün und blau. Diese Werte werden an LCD-Displays gesendet, um unterschiedliche Farben in Pixel anzuzeigen. Die Aufteilung der Farben in drei separate Kanäle ermöglicht es uns, sie durch Funktionen des maschinellen Lernens abwandern und ein tiefes neuronales Netzwerk zu schaffen. Wir tun dies, indem wir mehrere Male eine Faltung über jeden Kanal durchführen.

    import torch.nn as nn
    import torch.nn.functional as F
    
    
    class Net(nn.Module):
        def __init__(self):
            super(Net, self).__init__()
            self.conv1 = nn.Conv2d(3, 6, 5)
            self.pool = nn.MaxPool2d(2, 2)
            self.conv2 = nn.Conv2d(6, 16, 5)
            self.fc1 = nn.Linear(16 * 5 * 5, 120)
            self.fc2 = nn.Linear(120, 84)
            self.fc3 = nn.Linear(84, 10)
    
        def forward(self, x):
            x = self.pool(F.relu(self.conv1(x)))
            x = self.pool(F.relu(self.conv2(x)))
            x = x.view(-1, 16 * 5 * 5)
            x = F.relu(self.fc1(x))
            x = F.relu(self.fc2(x))
            x = self.fc3(x)
            return x
    
    
    net = Net()
    

![Animation mit Filter, die 2D-Array verschachteln](images/cnn.gif)

Im Allgemeinen führt die Funktion [nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html?highlight=conv2d#torch.nn.Conv2d) Konvertierungen über den Eingabetensensor aus, wie in der vorherigen Animation dargestellt. Ein _Filter_ (die Begriffe _Filter_ und _Kernel_ werden in verschiedenen Texten austauschbar verwendet), dargestellt durch das gelbe Quadrat, übergibt jeden Kanal wie eine Schreibmaschine und gibt einen neuen Kanal aus. Das Tutorial weist uns an, die erste Faltung auf `nn.Conv2d(3, 6, 5)` zu setzen. Dieser Aufruf von `Conv2d` erwartet, dass der Eingangstensor 3 Kanäle hat und einen Tensor mit 6 Kanälen ausgibt, indem die Faltung mehrmals ausgeführt wird. Der endgültige Wert, 5, ist die Filtergröße (Kernel), ein 5x5-Quadrat. Wenn ein Tupel (5,2) passiert ist, wird ein rechteckiger Filter verwendet. Es ist wichtig zu beachten, dass alle Bilder im Dataset 32x32-Pixelquares sind. Wenn ein Dataset mit unterschiedlich großen Bildern in diesen Algorithmus eingegeben wird, tritt ein Laufzeitfehler auf, weil die Ausgabematrizen nicht dieselbe Größe aufweisen.

Die nächste Zeile im Tutorial definiert eine Möglichkeit, die Daten mit einem Ausfall zu versehen, um die Größe des neuronalen Netzwerks zu kontrollieren und Überlagerungen zu vermeiden, [nn.MaxPool2d](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html). Wie zuvor sind die Eingabeparameter zuerst die Größe des Filters und dann die Strebe. Standardmäßig ist die Folie in der Funktion `nn.Conv2d` 1, aber für `nn.MaxPool2d` verwenden wir 2. Statt also um ein Quadrat nach rechts zu wechseln, verschiebt sich der Filter um zwei Quadrate nach rechts. Nach jeder Bewegung nimmt `nn.MaxPool2d` den größten Wert für den Filter an und legt ihn in den Ausgabekanal.

![Visuelle Übersicht über max. Pooling](images/maxpool.png)

Als nächstes wird eine zweite Faltungsschicht definiert, welche die 6 Ausgangskanäle von der ersten Faltung als Eingangs- und Ausgangskanäle 16 nimmt. Durch wiederholte Faltungs- und maximale Pooling-Layer kann das Modell Funktionen innerhalb des Images finden. Allerdings werden sowohl die Rechen-Workload- als auch die Speicheranforderungen schnell erweitert. Durch das Hinzufügen weiterer Schichten entsteht nicht immer ein leistungsfähigeres Modell.

Um eine Vorhersage zu erhalten, verwendet das Modell drei [nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html?highlight=nn%20linear#torch.nn.Linear)\-Layer, die als vollständig verbundene Layer bezeichnet werden. Diese Funktion ist eine einfache Anwendung der linearen Regression. Die Ausgabespannungen der Funktionen `Conv2d` und `MaxPool2d` werden in ein 1-dimensionales Array abgeflacht, und die Länge dieses Arrays ist die erste Eingabe. Wenn Sie die korrekte Eingabennummer abrufen, wird eine Mathematik benötigt, die nicht im Tutorial beschrieben ist. Wir haben bereits definiert, wie eine Faltung funktioniert, und wir können die Ausgabehöhe und -breite mit folgender Gleichung berechnen:

`Output Width = ((Image Width – Filter Width + 2 * Padding) / Stride) + 1`

`Output Height = ((Image Height – Filter Height + 2 * Padding) / Stride) + 1`

(Die Standardwerte für [Füllung und Folie](https://deepai.org/machine-learning-glossary-and-terms/padding) sind 0 bzw. 1.)

Nach einer Faltung wird der maximale Pooling-Schritt angewendet. Daher teilen wir die Ausgabebreite und -höhe nach jedem Schritt um die maximale Poolingbreite und -höhe auf, um die endgültige Ausgabegröße zu erhalten. Da wir quadratische Dimensionen verwenden, müssen wir nur eine Gruppe von Berechnungen durchführen:

`Convolution 1: ((32 – 5 + 2 * 0) / 1 ) + 1 = 28`

`Max Pool 1: 28 / 2 = 14`

`Convolution 2: ((14 - 5 + 2 * 0) / 1 ) + 1 = 10`

`Max Pool 2: 10 / 2 = 5`

Unser Endausgang ist 16 Kanäle mit 5x5 Quadraten, so dass der erste Eingang für die erste vollständig verbundene Schicht folgendermaßen lautet:

`16 * 5 * 5 = 400`

Beachten Sie, dass `16 * 5 * 5` die erste Eingabe für den Layer `fc1` ist. Die zweite Eingabe ist die Größe des Zielausgabearrays. Best Practice ist es, die Größe des Arrays schrittweise durch aufeinanderfolgende Schichten zu reduzieren, was die Verwendung von drei Schichten erklärt. Wenn Sie ein neues Modell zur Klassifizierung erstellen, ist eine gute Vermutung für die endgültige vollständig verbundene Layerausgabe die gleiche wie die Anzahl der Kategorien, in die Sie die Bilder filtern möchten. In unserem Fall haben wir 10 Kategorien: Flugzeug, Auto, Vogel, Katze, Hirsche, Hund, Frosch, Pferd, Schiff und LKW.

![Sampling von Bildern aus jeder Kategorie von CIFAR10](images/cifar10.png)

Im Code für das Modell wird jeder Vorgang in der Klasseninitialisierung definiert, und die Reihenfolge, in der die Vorgänge für Beispiele ausgeführt werden, wird in der Methode `forward` definiert. [F.relu](https://pytorch.org/docs/stable/nn.functional.html?highlight=relu#torch.nn.functional.relu) ist eine [Aktivierungsfunktion](https://www.kdnuggets.com/2017/09/neural-network-foundations-explained-activation-function.html). Das Trennen in Aktivierungsfunktionen geht über den Geltungsbereich dieses Beitrags hinaus. Kurz gesagt gibt es drei beliebte Optionen: Sigmoid, TanH und ReLU. Diese drei (und mehr) sind in verschiedenen Szenarios nützlich, aber ReLU funktioniert für dieses Beispiel. Tensor.view ändert die Form des Tensors, wobei es in diesem Fall in ein 1-dimensionales, 400-Längen-Array abgeflacht wird, wie zuvor beschrieben.

## Aufgabe 4: Verlustfunktion und Optimizer

    import torch.optim as optim
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    

Im nächsten Abschnitt des Tutorials PyTorch werden die verwendete [Löschfunktion (Kriterium)](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html) und [Optimizer](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html?highlight=optimizer#optimizers) definiert, die auch selbst tiefe Themen sind, hier jedoch nicht in den Geltungsbereich fallen. Die ausgewählten Optionen sind gute Optionen für "allgemeine Zwecke". Im [Glossar für maschinelles Lernen](https://ml-cheatsheet.readthedocs.io/) werden beide Vorgänge kurz beschrieben:

"Optimierer ... verbinden die Verlustfunktion und Modellparameter, indem sie das Modell als Antwort auf die Ausgabe der Verlustfunktion aktualisieren. Einfacher ausgedrückt formen Optimierer Ihr Modell in die genaueste Form, indem sie die Gewichte einstellen. Die Verlustfunktion ist die Führung zum Gelände und informiert den Optimizer, wenn er sich in die richtige oder falsche Richtung bewegt."

![Animation, die verschiedene Optimizer zeigt, die in einem Verlauf navigieren](images/loss.gif) Bildgutschrift: [CS231n](https://cs231n.github.io/neural-networks-3/)

Der Trainingsprozess erzeugt einen Gradienten, der mit einem Gebirgsbereich verglichen werden kann. Wenn Sie über Daten iterieren, möchten Sie den niedrigsten Punkt im Gebirgsbereich für ein vollständig trainiertes Modell erreichen, aber das Problem ist, dass Ihnen eine Karte fehlt. Mit der Verlustfunktion und dem Optimizer können Sie den Bereich absteigen. Wenn Sie jedoch zu schnell gehen oder das falsche System verwenden, können Sie an Stelle des tatsächlichen Minimums in einem lokalen Tiefpunkt hängen bleiben.

## Aufgabe 5: Modell trainieren

Wenn alles vorbereitet ist, können wir mit dem Training beginnen, in dem wir die Daten kontinuierlich iterieren, um zu unserem trainierten Modell zu gelangen (Schritt 4, [Netzwerk trainieren](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#train-the-network)).

    for epoch in range(2):  # loop over the dataset multiple times
    
        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # get the inputs; data is a list of [inputs, labels]
            inputs, labels = data
    
            # zero the parameter gradients
            optimizer.zero_grad()
    
            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()
    
            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:    # print every 2000 mini-batches
                print('[%d, %5d] loss: %.3f' %
                      (epoch + 1, i + 1, running_loss / 2000))
                running_loss = 0.0
    
    print('Finished Training')
    

Unter Berücksichtigung der ersten bereitgestellten Zeile ist `epoch` eine vollständige Iteration über jedes Image im Dataset. Das Tutorial wie geschrieben wiederholt die Daten nur zweimal, was nicht ausreicht, um das Minimum zu finden. Sie können diese Zahl erhöhen, indem Sie einfach eine andere Eingabe für `range` eingeben. Der laufende Verlust wird verfolgt, um zu bestimmen, wie viel jede Epoche das Modell beeinflusst. Üblicherweise stoppt das Training, nachdem der Laufverlust so klein wird, dass sich das Modell effektiv nicht mehr verändert hat.

In der zweiten Schleife werden `trainloader` aufgezählt. Wie bereits beschrieben, segmentiert der Loader die Daten in Batches und verarbeitet jeden Batch. `Inputs` sind die Tensoren, die die Batchimagedaten enthalten, und `labels` ist die korrekte Kategorie für jedes Bild im Batch. Die `inputs` werden über die Weiterleitungspropagierung über `net` (die oben definierte CNN) übergeben, um die Vorhersage abzurufen, oder über `outputs`. `Outputs` werden mit dem richtigen Label an `criterion` übergeben, um die Funktion `loss` abzurufen, um den Betrag zu bestimmen, den das Modell geändert hat. Die `loss` wird dann an die `backward`\-Propagierung übergeben, die jedes Gewicht im Modell anpasst, um den Betrag zu minimieren, der zum Fehler in der Vorhersage beiträgt. Die Funktion `optimizer.step` macht den nächsten Schritt in der Bergkette (mit der vorherigen Analogie) basierend auf der Lernrate, die Sie als die Länge unseres Schrittes betrachten können. Sie möchten eine Lernrate verwenden, die groß genug ist, um Sie entlang des Pfades zu bewegen, aber klein genug, um einen potenziellen Weg für ein besseres Ergebnis nicht zu überschreiten.

Eine erfolgreiche Ausgabe sollte Zeilen in diesem Format ausdrucken, obwohl die Verlustnummern nicht bei jeder Ausführung identisch sind.

    [1,  2000] loss: 2.105
    [1,  4000] loss: 1.775
    [1,  6000] loss: 1.639
    [1,  8000] loss: 1.577
    [1, 10000] loss: 1.493
    [1, 12000] loss: 1.462
    [2,  2000] loss: 1.388
    [2,  4000] loss: 1.367
    [2,  6000] loss: 1.314
    [2,  8000] loss: 1.313
    [2, 10000] loss: 1.276
    [2, 12000] loss: 1.267
    Finished Training
    

## Aufgabe 6: Modell speichern und Genauigkeit testen

    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    

Diese Codezeilen speichern das Modell, nachdem die Trainingsschleife in einer Datei abgeschlossen wurde. Diese Methode eignet sich auch zum Erstellen von Checkpoints während des Modelltrainings. Beispiel: Das Modell wird aus der oben gespeicherten Datei geladen.

    net = Net()
    net.load_state_dict(torch.load(PATH))
    
    correct = 0
    total = 0
    with torch.no_grad():
        for data in testloader:
            images, labels = data
            outputs = net(images)
            _, predicted = torch.max(outputs.data, 1)
            total += labels.size(0)
            correct += (predicted == labels).sum().item()
    
    print('Accuracy of the network on the 10000 test images: %d %%' % (
        100 * correct / total))
    

Es ist wichtig, das Modell zu testen, um die Messgenauigkeit zu bestimmen. Dies gibt an, ob das Modell eine ausreichende Performance für einen bestimmten Anwendungsfall bietet. Wir haben das Modell nur für zwei Epochen in der Trainingsschleife oben trainiert, so dass das Modell keine beeindruckenden Performance-Ergebnisse hervorbringt. Wenn Sie die Anzahl der Epochen erhöhen, bis der Verlust abnimmt, werden Sie feststellen, dass sich die Modellperformance verbessert. Dieser Prozess kann in regelmäßigen Abständen zur detaillierten Überwachung während des Trainings durchgeführt werden, sollte aber nicht zu oft wiederholt werden oder es verlängert die Trainingszeit.

    class_correct = list(0. for i in range(10))
    class_total = list(0. for i in range(10))
    with torch.no_grad():
        for data in testloader:
            images, labels = data
            outputs = net(images)
            _, predicted = torch.max(outputs, 1)
            c = (predicted == labels).squeeze()
            for i in range(4):
                label = labels[i]
                class_correct[label] += c[i].item()
                class_total[label] += 1
    
    
    for i in range(10):
        print('Accuracy of %5s : %2d %%' % (
            classes[i], 100 * class_correct[i] / class_total[i]))
    

In diesem Beispiel können wir etwas tiefer eintauchen als die Gesamtgenauigkeit. Der obige Code gibt die Genauigkeit für jede Kategorie aus und bietet einen Einblick, wo das Modell möglicherweise verwirrt wird.

Beispiel für vollständigen Code

    import torch
    import torchvision
    import torchvision.transforms as transforms
    import torch.nn as nn
    import torch.nn.functional as F
    import torch.optim as optim
    
    transform = transforms.Compose(
        [transforms.ToTensor(),
         transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    
    trainset = torchvision.datasets.CIFAR10(root='./data', train=True,
                                            download=True, transform=transform)
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4,
                                              shuffle=True, num_workers=2)
    
    testset = torchvision.datasets.CIFAR10(root='./data', train=False,
                                           download=True, transform=transform)
    testloader = torch.utils.data.DataLoader(testset, batch_size=4,
                                             shuffle=False, num_workers=2)
    
    classes = ('plane', 'car', 'bird', 'cat',
               'deer', 'dog', 'frog', 'horse', 'ship', 'truck')
    
    class Net(nn.Module):
        def __init__(self):
            super(Net, self).__init__()
            self.conv1 = nn.Conv2d(3, 6, 5)
            self.pool = nn.MaxPool2d(2, 2)
            self.conv2 = nn.Conv2d(6, 16, 5)
            self.fc1 = nn.Linear(16 * 5 * 5, 120)
            self.fc2 = nn.Linear(120, 84)
            self.fc3 = nn.Linear(84, 10)
    
        def forward(self, x):
            x = self.pool(F.relu(self.conv1(x)))
            x = self.pool(F.relu(self.conv2(x)))
            x = x.view(-1, 16 * 5 * 5)
            x = F.relu(self.fc1(x))
            x = F.relu(self.fc2(x))
            x = self.fc3(x)
            return x
    
    
    net = Net()
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    
    for epoch in range(2):  # loop over the dataset multiple times
    
        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # get the inputs; data is a list of [inputs, labels]
            inputs, labels = data
    
            # zero the parameter gradients
            optimizer.zero_grad()
    
            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()
    
            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:    # print every 2000 mini-batches
                print('[%d, %5d] loss: %.3f' %
                      (epoch + 1, i + 1, running_loss / 2000))
                running_loss = 0.0
    
    print('Finished Training')
    
    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    
    net = Net()
    net.load_state_dict(torch.load(PATH))
    
    correct = 0
    total = 0
    with torch.no_grad():
        for data in testloader:
            images, labels = data
            outputs = net(images)
            _, predicted = torch.max(outputs.data, 1)
            total += labels.size(0)
            correct += (predicted == labels).sum().item()
    
    print('Accuracy of the network on the 10000 test images: %d %%' % (
        100 * correct / total))
    
    class_correct = list(0. for i in range(10))
    class_total = list(0. for i in range(10))
    with torch.no_grad():
        for data in testloader:
            images, labels = data
            outputs = net(images)
            _, predicted = torch.max(outputs, 1)
            c = (predicted == labels).squeeze()
            for i in range(4):
                label = labels[i]
                class_correct[label] += c[i].item()
                class_total[label] += 1
    
    
    for i in range(10):
        print('Accuracy of %5s : %2d %%' % (
            classes[i], 100 * class_correct[i] / class_total[i]))
    

Dies deckt die Hauptpunkte des Trainings von Deep Learning-Computersichtmodellen ab. Im nächsten Schritt wird die Rechen-Workload mit einer GPU genauer beschrieben.

Sie können mit der nächsten Übung fortfahren.

## Bestätigungen

*   **Autor** - Justin Blau, Senior Solutions Architect, Big Compute
*   **Zuletzt aktualisiert von/Datum** - Justin Blau, Big Compute, Oktober 2020