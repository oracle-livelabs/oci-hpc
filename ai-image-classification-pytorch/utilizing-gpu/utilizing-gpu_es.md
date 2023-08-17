# Uso de GPU

## Introducción

El último tema que se tratará está relacionado con la optimización computacional mediante GPU. Al final del tutorial, después de guardar el modelo y probar su precisión, el código actual está utilizando solo recursos de CPU. Al modificar ligeramente el código, podemos utilizar los recursos de GPU. Oracle Cloud Infrastructure proporciona una matriz de GPU NVIDIA con diferentes unidades en función de la carga de trabajo computacional necesaria. Para empezar, recomiendo ejecutar este código en VM.GPU2.1. Después de encontrar la ubicación de GPU adecuada, es probable que `cuda:0`, el modelo, los datos por lotes y las etiquetas se puedan enviar al dispositivo para su procesamiento mediante métodos `.to(device)`. PyTorch ajusta todo el código de transición de memoria en estos métodos para que no tenga que preocuparse de asignar correctamente memoria GPU.

Tiempo estimado de laboratorio: 5 minutos

### Objetivos

En este laboratorio, aprenderá sobre:

*   Formación en GPU

### Requisitos previos

En esta práctica se asume que tiene:

*   Finalizó las prácticas anteriores.
*   Tener una instancia con GPU activado

## Tarea 1: Activación de la aceleración de GPU

1.  Después de las importaciones, agregue esta línea de código:
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  Esta línea de código establecerá el dispositivo para la estructura de soplete en función de lo que esté disponible en el sistema. La sentencia if en línea permite que el código se ejecute incluso si una GPU no está disponible, ya que vuelve al valor por defecto `cpu`. Después de la línea 45 en el código completo proporcionado, después de instanciar `net`, mueva el modelo `net` a la GPU ejecutando esta línea de código.
    
        net.to(device)
        
3.  Por último, durante la formación necesitamos trasladar los datos por lotes a la GPU para su procesamiento. Sustituya la línea 55 en el código proporcionado por esta línea.
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]` contiene las imágenes por lotes y `data[1]` contiene las etiquetas coincidentes. Todos estos valores se envían a la GPU con `.to(device)`. ¡Eso es todo! PyTorch facilita la activación de la GPU con sintaxis declarativa.

Ejemplo de código completo

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
    

_**NOTA:**_ al ejecutar este ejemplo específico, puede que no vea una disminución significativa en el tiempo de formación. Este ejemplo está diseñado para que las cosas sean simples, por lo que el objetivo es simplemente asegurarse de que el código se ejecuta correctamente. El problema es que las imágenes y el modelo son pequeños. El aumento del número de capas y canales del modelo, así como el aumento de `batch_size` en el cargador de datos, empezarán a mostrar diferencias en el tiempo de ejecución al comparar CPU con GPU.

## Acuses de recibo

*   **Autor**: Justin Blau, arquitecto sénior de soluciones, Big Compute
*   **Fecha/fecha de última actualización**: Justin Blau, Big Compute, octubre de 2020