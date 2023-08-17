# Treinando um Classificador

## Introdução

O guia [Treinamento de um Classificador](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) de PyTorch é um ótimo lugar para começar, mas alguns detalhes do código são explicados com pouca margem para um leitor novo no campo. Este laboratório baseia-se nesse tutorial como uma base com explicações linha a linha do código para facilitar um caminho de aprendizado mais claro.

Tempo Estimado de Laboratório: 45 minutos

### Objetivos

Neste laboratório, você vai aprender sobre:

*   DataLoaders
*   Redes Neurais Convolutivas (CNN)
*   Função de Perda e Otimizador
*   Treinamento de Modelo

### Pré-requisitos

Este laboratório pressupõe que você tenha:

*   Uma conta no Oracle Free Tier ou Paid Cloud
*   Acesso a uma instância de CPU ou GPU, conforme abordado nos laboratórios 1 e 2

## Tarefa 1: Configurar o ambiente

Há vários métodos para configurar um ambiente python para executar PyTorch. Você pode seguir seu método preferido ou o conjunto de instruções a seguir.

1.  Obtenha um link para a [versão mais recente do miniconda](https://docs.conda.io/en/latest/miniconda.html) navegando até o site rolando para baixo até "Linux Installers" e copiando o link superior.
    
2.  Em um terminal para a instância, use wget para fazer download do arquivo.
    
         wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
        
3.  [Execute o comando install](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html) e conclua a configuração.
    
         bash Miniconda3-latest-Linux-x86_64.sh
        
4.  Saia do terminal e abra um novo terminal após a instalação, o que garante que o conda seja ativado. Opcional: crie um ambiente específico para PyTorch executando o comando `conda create --name pytorch python=3.6` e, em seguida, ativando o ambiente executando `conda activate pytorch`
    
5.  [Instale PyTorch](https://pytorch.org/) por meio do conda `conda install pytorch torchvision cudatoolkit=10.2 -c pytorch`
    

## Tarefa 2: CIFAR10 e DataLoader

O exemplo completo do código está localizado na parte inferior desta página.

A maior parte do tempo de um pesquisador de IA não é gasto executando modelos de treinamento e teste, mas limpando e preparando dados. Uma quantidade incrível de trabalho foi condensada nas poucas linhas de código abaixo. CIFAR10 é um conjunto de dados totalmente pré-processado de imagens e labels. Ele foi criado e tornado público para servir como um benchmark padronizado para testar novos modelos ou começar a usar o aprendizado profundo. A transformação de um conjunto de dados brutos em uma estrutura que funciona com PyTorch pode levar muito tempo, de modo a diminuir essa barreira que o conjunto de dados foi incluído na distribuição da biblioteca PyTorch.

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
    

[transform.Compose](https://pytorch.org/docs/stable/torchvision/transforms.html) garante que os dados estejam no formato de tensor necessário para o modelo e tenham sido [normalizados](https://discuss.pytorch.org/t/understanding-transform-normalize/21730) para garantir que todos os valores se ajustem em um intervalo definido. Normalmente, a normalização de dados pode melhorar o treinamento do modelo, pois ajuda a tornar os valores no conjunto de dados mais consistentes.

Essas linhas de código gerenciam o download do conjunto de dados, a divisão de imagens em três canais de cores (vermelho, verde e azul), a correspondência deles com o rótulo correto (avião, pássaro, cão, sapo etc.) e a conversão de todas as imagens e labels em [torch.Tensors](https://pytorch.org/docs/stable/tensors.html). A saída do método [torchvision.datasets.CIFAR10](http://pytorch.org/docs/stable/torchvision/datasets.html?highlight=cifar10#torchvision.datasets.CIFAR10) é um objeto pronto para ser usado. O próximo método, [torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html?highlight=dataloader#torch.utils.data.DataLoader), gerencia as tarefas logísticas envolvidas na otimização dos recursos de computação. Com sistemas GPU, os dados devem primeiro ser carregados na memória da CPU e depois transferidos para a memória GPU para processamento paralelo acelerado. O ideal é que haja trabalhadores de CPU suficientes para garantir que os dados processados sejam retirados e que os dados não processados os substituam pelo menor atraso possível.

A utilização de GPU pode variar, dependendo principalmente do tamanho do conjunto de dados e do processo computacional do modelo. O desenvolvimento de um algoritmo eficiente de carregamento de dados e a realização de pesquisas de inteligência artificial geralmente são duas habilidades diferentes, portanto, a equipe PyTorch criou um método geral e otimizado para lidar com essa tarefa para qualquer torch.Tensor. Para melhorar a utilização da GPU, você pode ajustar os hiperparâmetros do carregador de dados, como `batch_size` e `num_workers`. Nesse caso, `batch_size` é 4 e `num_workers` é 2, o que significa que dois processos de CPU carregam quatro imagens por vez na GPU. Dependendo do tamanho das imagens e do sistema que você está usando, é possível ajustar esses valores para obter a utilização ideal da GPU. Este exemplo específico não é particularmente desafiador para a GPU ser executada porque a rede neural é superficial e o conjunto de dados é pequeno. No entanto, à medida que você desenvolve seus próprios modelos que têm mais canais e um conjunto de dados maior, o aumento no desempenho será mais significativo.

Este exemplo usa dois conjuntos de dados. CIFAR10 é dividido em um conjunto de dados de _treinamento_ que é iterado para desenvolver o modelo e um conjunto de dados de _teste_ de dados não vistos que é usado para testar o desempenho do modelo. Ao trabalhar com seu conjunto de dados, considere dividir seus dados brutos em 80% usados para treinamento e 20% usados para teste. Evite testar com dados usados para treinar o modelo porque ele limita sua capacidade de detectar [sobreposição](https://www.kdnuggets.com/2019/12/fighting-overfitting-deep-learning.html), um problema que ocorre quando seu modelo funciona bem contra dados de treinamento, mas não dados novos.

## Tarefa 3: Rede Neural Convolutiva

[Definir uma Rede Neural Convolutiva](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#define-a-convolutional-neural-network), define o modelo. O conjunto de dados consiste em imagens que são divididas em três canais de cores: vermelho, verde e azul. Esses valores são enviados para monitores LCD para mostrar cores diferentes em pixels. Dividir as cores em três canais separados nos permite cancelá-las por meio de funções de aprendizado de máquina e criar uma rede neural profunda. Fazemos isso executando uma convolução em cada canal várias vezes.

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
    

![Animação mostrando filtro que envolve o array 2-D](images/cnn.gif)

Em um nível alto, a função [nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html?highlight=conv2d#torch.nn.Conv2d) executa convoluções no tensor de entrada, conforme mostrado na animação anterior. Um _filtro_ (os termos _filtro_ e _kernel_ são usados de forma intercambiável em vários textos), representado pelo quadrado amarelo, passa por cada canal como um gravador de tipo e gera um novo canal. O tutorial nos instrui a definir a primeira convolução como `nn.Conv2d(3, 6, 5)`. Essa chamada para `Conv2d` espera que o tensor de entrada tenha 3 canais e gera um tensor com 6 canais executando a convolução várias vezes. O valor final, 5, é o tamanho do filtro (kernel), um quadrado 5x5. Se uma tupla (5,2) for transmitida, um filtro retangular será usado. É importante observar que todas as imagens do conjunto de dados são quadrados de pixel 32x32. Se um conjunto de dados com imagens de tamanho diferente for inserido nesse algoritmo, ocorrerá um erro de tempo de execução porque as matrizes de saída não são do mesmo tamanho.

A próxima linha do tutorial define uma maneira de fazer downsample dos dados para ajudar a controlar o tamanho da rede neural e evitar overfitting, [nn.MaxPool2d](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html). Como antes, os parâmetros de entrada são primeiro o tamanho do filtro e, em seguida, o stride. Por padrão, o stride na função `nn.Conv2d` é 1, mas para `nn.MaxPool2d` estamos usando 2. Assim, em vez de mudar para a direita por um quadrado, o filtro muda para a direita por dois quadrados. Após cada movimento, `nn.MaxPool2d` obtém o maior valor visto pelo filtro e o coloca no canal de saída.

![Resumo visual do pool máximo](images/maxpool.png)

Uma segunda camada convolutiva, que utiliza os 6 canais de saída da primeira convolução como entrada e gera 16 canais, é definida em seguida. A repetição de camadas de agrupamento convolutivo e máximo pode ajudar o modelo a encontrar recursos dentro da imagem. No entanto, os requisitos de memória e carga de trabalho computacional se expandem rapidamente e a adição de mais camadas nem sempre produz um modelo de melhor desempenho.

Para chegar a uma previsão, o modelo usa três camadas [nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html?highlight=nn%20linear#torch.nn.Linear), chamadas camadas totalmente conectadas. Esta função é uma aplicação direta de regressão linear. Os tensores de saída das funções `Conv2d` e `MaxPool2d` são nivelados em um array 1-dimensional, e o tamanho desse array é a primeira entrada. A obtenção do número de entrada correto pega alguns cálculos que não são descritos no tutorial. Já definimos como uma convolução funciona e podemos calcular a altura e a largura da saída usando a seguinte equação:

`Output Width = ((Image Width – Filter Width + 2 * Padding) / Stride) + 1`

`Output Height = ((Image Height – Filter Height + 2 * Padding) / Stride) + 1`

(Os valores padrão para [padding and stride](https://deepai.org/machine-learning-glossary-and-terms/padding) são 0 e 1, respectivamente.)

Após uma convolução, a etapa máxima de pool é aplicada, portanto, dividimos a largura e a altura de saída pela largura e altura máximas de pool após cada etapa para obter o tamanho final da saída. Como estamos usando dimensões quadradas, precisamos executar apenas um conjunto de cálculos:

`Convolution 1: ((32 – 5 + 2 * 0) / 1 ) + 1 = 28`

`Max Pool 1: 28 / 2 = 14`

`Convolution 2: ((14 - 5 + 2 * 0) / 1 ) + 1 = 10`

`Max Pool 2: 10 / 2 = 5`

Nossa saída final é de 16 canais de quadrados 5x5, então a primeira entrada para a primeira camada totalmente conectada é a seguinte:

`16 * 5 * 5 = 400`

Observe que `16 * 5 * 5` é a primeira entrada da camada `fc1`. A segunda entrada é o tamanho do array de saída de destino. A melhor prática é reduzir gradualmente o tamanho da matriz através de camadas sucessivas, o que explica o uso de três camadas. Quando você está criando um novo modelo para classificação, uma boa suposição para a saída da camada totalmente conectada final é a mesma que o número de categorias em que você está tentando filtrar as imagens. No nosso caso, temos 10 categorias: avião, automóvel, pássaro, gato, veado, cachorro, sapo, cavalo, navio e caminhão.

![Amostragem de imagens de cada categoria de CIFAR10](images/cifar10.png)

No código do modelo, cada operação é definida na inicialização da classe, e a ordem na qual as operações são executadas em amostras é definida no método `forward`. [F.relu](https://pytorch.org/docs/stable/nn.functional.html?highlight=relu#torch.nn.functional.relu) é uma [função de ativação](https://www.kdnuggets.com/2017/09/neural-network-foundations-explained-activation-function.html). Entrar em funções de ativação está além do escopo deste post, mas em resumo há três opções populares: Sigmoid, TanH e ReLU. Esses três (e mais) são úteis em cenários diferentes, mas ReLU funciona para este exemplo. Tensor.view altera a forma do tensor, neste caso nivelando-o em uma matriz de 1 dimensão, de 400 tamanhos, conforme descrito anteriormente.

## Tarefa 4: Função de Perda e Otimizador

    import torch.optim as optim
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    

A próxima seção no tutorial PyTorch define a [função de perda (critério)](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html) e o [otimizador](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html?highlight=optimizer#optimizers) usados, que também são tópicos detalhados, mas fora do escopo aqui. As selecionadas são boas opções de "propósito geral". O [Glossário de Aprendizado de Máquina](https://ml-cheatsheet.readthedocs.io/) descreve sucintamente ambas as operações:

"Otimizadores ... unem a função de perda e os parâmetros do modelo atualizando o modelo em resposta à saída da função de perda. Em termos mais simples, os otimizadores moldam e moldam seu modelo em sua forma mais precisa possível, ajustando os pesos. A função de perda é o guia do terreno, dizendo ao otimizador quando ele está se movendo na direção certa ou errada."

![Animação mostrando diferentes otimizadores navegando em um gradiente](images/loss.gif) Crédito da imagem: [CS231n](https://cs231n.github.io/neural-networks-3/)

O processo de treinamento gera um gradiente que pode ser comparado a uma cadeia montanhosa. À medida que itera sobre os dados, você quer chegar no ponto mais baixo da faixa da montanha para um modelo totalmente treinado, mas o problema é que você não tem um mapa. A função de perda e o otimizador ajudam você a descer o intervalo, mas se você for muito rápido ou usar o sistema errado, você pode ficar preso em um ponto baixo local em vez do mínimo real.

## Tarefa 5: Treinando o Modelo

Com tudo preparado, estamos prontos para começar o treinamento, no qual iteramos continuamente sobre os dados para chegar ao nosso modelo treinado (etapa 4, [Treinar a Rede](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#train-the-network)).

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
    

Observando a primeira linha fornecida, um `epoch` é uma iteração completa em todas as imagens do conjunto de dados. O tutorial como escrito itera sobre os dados apenas duas vezes, o que não é suficiente para encontrar o mínimo. Você pode aumentar esse número simplesmente digitando outra entrada para `range`. A perda em execução é rastreada para determinar o quanto cada época influencia o modelo. O treinamento normalmente para depois que a perda em execução se torna tão pequena que o modelo efetivamente parou de mudar.

No segundo loop, enumeramos o `trainloader`. Conforme descrito anteriormente, o carregador segmenta os dados em lotes e processamos cada lote. `Inputs` são os tensores que contêm os dados da imagem do lote e `labels` são a categoria correta para cada imagem no lote. O `inputs` é passado pela propagação futura via `net` (a CNN definida acima) para obter a previsão, ou `outputs`. `Outputs` são passados para o `criterion` com o label correto para obter a função `loss`, para determinar a quantidade que o modelo mudou. Em seguida, o `loss` é passado para a propagação `backward`, que ajusta cada peso no modelo para minimizar o valor que contribui para o erro na previsão. A função `optimizer.step` faz o próximo passo para baixo na cadeia montanhosa (usando a analogia anterior) com base na taxa de aprendizagem, que você pode pensar como a duração da nossa etapa. Você quer usar uma taxa de aprendizado grande o suficiente para movê-lo ao longo do caminho, mas pequena o suficiente para não ultrapassar uma avenida potencial para obter um resultado melhor.

Uma saída bem-sucedida deve imprimir linhas nesse formato, embora os números de perda não sejam exatamente os mesmos a cada execução.

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
    

## Tarefa 6: Salvando o Modelo e Testando a Precisão

    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    

Essas linhas de código salvam o modelo depois que ele conclui o loop de treinamento em um arquivo. Esse método também é útil para criar pontos de verificação em todo o treinamento do modelo. Por exemplo, carregaremos o modelo do arquivo salvo acima.

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
    

É importante testar o modelo para determinar a precisão medida. Isso indicará se o modelo fornece desempenho suficiente para um determinado caso de uso. Nós só treinamos o modelo para duas épocas no loop de treinamento acima, de modo que o modelo não produzirá resultados de desempenho impressionantes. Se você aumentar o número de épocas até que a perda pare de diminuir, verá que o desempenho do modelo melhora. Esse processo pode ser executado durante o treinamento periodicamente para monitoramento detalhado, embora não deva ser repetido com muita frequência ou prolongará o tempo de treinamento.

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
    

Para este exemplo, podemos nos aprofundar um pouco mais do que apenas a precisão geral. O código acima imprime a precisão de cada categoria e pode fornecer algumas informações sobre onde o modelo pode estar sendo confuso.

Exemplo de código inteiro

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
    

Isso abrange os principais pontos de treinamento de modelos de visão computacional de aprendizado profundo. A próxima etapa é agilizar a carga de trabalho computacional com uma GPU.

Você pode ir para o próximo laboratório.

## Confirmação

*   **Autor** - Justin Blau, Arquiteto de Soluções Sênior, Big Compute
*   **Data da Última Atualização** - Justin Blau, Big Compute, outubro de 2020