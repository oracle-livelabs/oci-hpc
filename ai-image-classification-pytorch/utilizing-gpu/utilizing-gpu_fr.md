# Utilisation d'un GPU

## Présentation

La dernière rubrique à aborder concerne l'optimisation du calcul à l'aide de GPU. A la fin du tutoriel, après avoir enregistré le modèle et testé sa précision, le code en cours utilise uniquement des ressources d'UC. En modifiant légèrement le code, nous pouvons utiliser des ressources GPU. Oracle Cloud Infrastructure propose un ensemble de GPU NVIDIA avec différentes formes, en fonction de la charge de travail de calcul requise. Pour commencer, je vous recommande d'exécuter ce code sur un VM.GPU2.1. Après avoir trouvé l'emplacement de GPU approprié, probablement `cuda:0`, le modèle, les données en batch et les étiquettes peuvent être envoyés au périphérique pour traitement à l'aide des méthodes `.to(device)`. PyTorch encapsule tous les codes de transition de mémoire de ces méthodes afin que vous n'ayez pas à vous soucier de l'affectation correcte de la mémoire GPU.

Temps de laboratoire estimé : 5 minutes

### Objectifs

Dans cet atelier, vous allez découvrir :

*   Formation sur un GPU

### Prérequis

Cet exercice suppose que vous avez :

*   Exercices précédents terminés
*   disposent d'une instance compatible GPU

## Tâche 1 : activation de l'accélération GPU

1.  Après les importations, ajoutez cette ligne de code :
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  Cette ligne de code définit le dispositif de la structure de torche en fonction de ce qui est disponible sur le système. L'instruction if intégrée permet au code de s'exécuter même si aucun GPU n'est disponible car elle renvoie par défaut la valeur `cpu`. Après la ligne 45 du code complet fourni, après l'instanciation de `net`, déplacez le modèle `net` vers le GPU en exécutant cette ligne de code.
    
        net.to(device)
        
3.  Enfin, pendant l'entraînement, nous devons transférer les données en batch vers le GPU pour traitement. Remplacez la ligne 55 du code fourni par cette ligne.
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]` contient les images en batch et `data[1]` contient les libellés correspondants. Toutes ces valeurs sont envoyées au GPU avec `.to(device)`. Et voilà ! PyTorch facilite l'activation du GPU avec une syntaxe déclarative.

Exemple de code complet

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
    

_**REMARQUE :**_ Lors de l'exécution de cet exemple spécifique, il se peut que le temps de formation ne soit pas considérablement réduit. Cet exemple est conçu pour simplifier les choses. L'objectif est donc de vous assurer que le code s'exécute correctement. Le problème est que les images et le modèle sont petits. L'augmentation du nombre de couches et de canaux dans le modèle ainsi que l'augmentation de `batch_size` dans l'adaptateur de données commenceront à montrer des différences dans le temps d'exécution lors de la comparaison des CPU avec les GPU.

## Accusés de réception

*   **Auteur** - Justin Blau, architecte senior de solutions, Big Compute
*   **Dernière mise à jour le/la date** - Justin Blau, Big Compute, octobre 2020