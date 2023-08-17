# GPU verwenden

## Einführung

Das letzte Thema betrifft die Berechnungsoptimierung mit GPUs. Nach dem Speichern des Modells und dem Testen der Genauigkeit verwendet der aktuelle Code nur CPU-Ressourcen. GPU-Ressourcen können leicht geändert werden. Oracle Cloud Infrastructure bietet ein Array von NVIDIA-GPUs mit unterschiedlichen Ausprägungen, je nach erforderlichem Rechen-Workload. Zunächst wird empfohlen, diesen Code auf einer VM.GPU2.1 auszuführen. Nachdem Sie den entsprechenden GPU-Speicherort gefunden haben, wahrscheinlich `cuda:0`, können das Modell, die im Batch enthaltenen Daten und Labels zur Verarbeitung mit `.to(device)`\-Methoden an das Gerät gesendet werden. PyTorch wrappt den gesamten Speicherübergangscode in diesen Methoden, sodass Sie sich keine Sorgen um die ordnungsgemäße Zuweisung des GPU-Speichers machen müssen.

Geschätzte Laborzeit: 5 Minuten

### Ziele

In dieser Übung lernen Sie Folgendes kennen:

*   Schulungen auf einer GPU

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Vorherige Übungen abgeschlossen
*   Sie verfügen über eine GPU-fähige Instanz

## Aufgabe 1: GPU-Beschleunigung aktivieren

1.  Fügen Sie nach den Importen die folgende Codezeile hinzu:
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  Diese Codezeile legt das Gerät für das Fackel-Framework basierend auf dem verfügbaren System fest. Mit der Inline-If-Anweisung kann der Code ausgeführt werden, auch wenn keine GPU verfügbar ist, da der Wert wieder auf `cpu` gesetzt wird. Verschieben Sie nach der Instanziierung von `net` im angegebenen vollständigen Code das `net`\-Modell in die GPU, indem Sie diese Codezeile ausführen.
    
        net.to(device)
        
3.  Abschließend müssen die Batch-Daten zur Verarbeitung an die GPU übertragen werden. Ersetzen Sie die Zeile 55 im angegebenen Code durch diese Zeile.
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]` enthält die im Batch enthaltenen Images, und `data[1]` enthält die übereinstimmenden Labels. Alle diese Werte werden mit `.to(device)` an die GPU übertragen. Das ist es! Mit PyTorch können Sie GPU mit deklarativer Syntax ganz einfach aktivieren.

Beispiel für vollständigen Code

    import torch
    import torchvision
    import torchvision.transforms as transforms
    import torch.nn as nn
    import torch.nn.functional as F
    import torch.optim as optim
    
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    
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
    net.to(device)
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    
    for epoch in range(2):  # loop over the dataset multiple times
    
        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # get the inputs; data is a list of [inputs, labels]
            inputs, labels = data[0].to(device), data[1].to(device)
    
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
    

_**HINWEIS:**_ Wenn Sie dieses Beispiel ausführen, wird möglicherweise keine signifikante Reduzierung der Schulungszeit angezeigt. Dieses Beispiel soll die Dinge einfach halten. Daher soll nur sichergestellt werden, dass der Code korrekt ausgeführt wird. Das Problem ist, dass die Bilder und das Modell klein sind. Wenn Sie die Anzahl der Schichten und Kanäle im Modell erhöhen und `batch_size` im Data Loader erhöhen, werden beim Vergleich von CPUs mit GPUs Unterschiede in der Ausführungszeit angezeigt.

## Bestätigungen

*   **Autor** - Justin Blau, Senior Solutions Architect, Big Compute
*   **Zuletzt aktualisiert von/Datum** - Justin Blau, Big Compute, Oktober 2020