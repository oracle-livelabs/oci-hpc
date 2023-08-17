# Utilizando uma GPU

## Introdução

O último tópico a ser abordado relaciona-se à otimização computacional usando GPUs. No fim do tutorial, depois de salvar o modelo e testar sua precisão, o código atual está usando apenas recursos de CPU. Ao modificar ligeiramente o código, podemos usar os recursos de GPU. O Oracle Cloud Infrastructure fornece uma matriz de GPUs NVIDIA com diferentes configurações, dependendo da carga de trabalho computacional necessária. Para começar, recomendo a execução desse código em uma VM.GPU2.1. Depois de encontrar o local apropriado da GPU, provavelmente `cuda:0`, o modelo, os dados em batch e os labels podem ser enviados para o dispositivo para processamento usando métodos `.to(device)`. PyTorch envolve todo o código de transição de memória nesses métodos para que você não precise se preocupar com a atribuição adequada da memória de GPU.

Tempo Estimado de Laboratório: 5 minutos

### Objetivos

Neste laboratório, você vai aprender sobre:

*   Treinamento em GPU

### Pré-requisitos

Este laboratório pressupõe que você tenha:

*   Concluiu os laboratórios anteriores
*   Ter uma instância ativada por GPU

## Tarefa 1: Ativando a Aceleração de GPU

1.  Após as importações, adicione esta linha de código:
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  Esta linha de código definirá o dispositivo para a estrutura da tocha com base no que está disponível no sistema. A instrução if em linha permite que o código seja executado mesmo que uma GPU não esteja disponível porque padroniza o valor de volta para `cpu`. Após a linha 45 no código completo fornecido, após a instanciação de `net`, mova o modelo `net` para a GPU executando essa linha de código.
    
        net.to(device)
        
3.  Por fim, durante o treinamento, precisamos transferir os dados em batch para a GPU para processamento. Substitua a linha 55 no código fornecido por esta linha.
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]` contém as imagens em batch e `data[1]` contém os labels correspondentes. Todos esses valores são enviados para a GPU com `.to(device)`. É isso aí! PyTorch facilita a ativação da GPU com sintaxe declarativa.

Exemplo de código inteiro

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
    

_**OBSERVAÇÃO:**_ ao executar esse exemplo específico, talvez você não veja uma redução significativa no tempo de treinamento. Esse exemplo foi projetado para manter as coisas simples. Portanto, a meta é simplesmente garantir que o código seja executado corretamente. O problema é que as imagens e o modelo são pequenos. O aumento do número de camadas e canais no modelo e o aumento do `batch_size` no dataloader começarão a mostrar diferenças no tempo de execução ao comparar CPUs com GPUs.

## Confirmação

*   **Autor** - Justin Blau, Arquiteto de Soluções Sênior, Big Compute
*   **Data da Última Atualização** - Justin Blau, Big Compute, outubro de 2020