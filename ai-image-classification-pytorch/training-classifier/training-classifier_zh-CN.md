# 培训分类器

## 简介

来自 PyTorch 的[培训分类器](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)指南是一个很好的起点，但对于新到该字段的读者而言，代码的一些细节解释得太少。本练习以该教程为基础，并逐行解释代码，以促进更清晰的学习路径。

估计实验室时间：45 分钟

### 目标

在本练习中，您将了解：

*   DataLoaders
*   卷积神经网络 (CNN)
*   损失功能和优化程序
*   模型培训

### 前提条件

本练习假定您具有：

*   Oracle 免费层级或付费云账户
*   访问练习 1 和 2 中介绍的 CPU 或 GPU 实例

## 任务 1：设置环境

有多种方法可以将 python 环境设置为运行 PyTorch。您可以遵循首选方法或以下说明集。

1.  通过导航到站点向下滚动到 "Linux Installers" 并复制顶部链接，获取 [miniconda 的最新版本](https://docs.conda.io/en/latest/miniconda.html)链接。
    
2.  在实例的终端中，使用 wget 下载文件。
    
         wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
        
3.  [运行安装命令](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html)并完成设置。
    
         bash Miniconda3-latest-Linux-x86_64.sh
        
4.  退出终端并在安装后打开一个新终端，这样可确保激活 conda。可选：通过运行命令 `conda create --name pytorch python=3.6` 为 PyTorch 创建特定环境，然后通过运行 `conda activate pytorch` 来启用环境
    
5.  通过 conda `conda install pytorch torchvision cudatoolkit=10.2 -c pytorch` [安装 PyTorch](https://pytorch.org/)
    

## 任务 2：CIFAR10 和 DataLoader

完整的代码示例位于此页面的底部。

大部分的人工智能研究员的时间不是用来运行培训和测试模型，而是用来清理和准备数据。令人难以置信的工作量已被压缩成以下几行代码。CIFAR10 是映像和标签的完全预处理数据集。它是创建和公开的，用作测试新模型的标准化基准或开始深度学习。将一组原始数据转换为使用 PyTorch 的结构可能需要大量时间，以便降低该数据集已包括在 PyTorch 库分发中的屏障。

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
    

[transform.Compose](https://pytorch.org/docs/stable/torchvision/transforms.html) 确保数据采用模型所需的传感器格式，并且已[规范化](https://discuss.pytorch.org/t/understanding-transform-normalize/21730)，以确保所有值都符合定义的范围。规范化数据通常可以改进模型训练，因为它有助于使数据集中的值更加一致。

这些代码行管理以下操作：下载数据集，将图像拆分成三个颜色通道（红色、绿色和蓝色），将其匹配到正确的标签（飞机、鸟、狗、青蛙等），并将所有图像和标签转换为 [torch.Tensors](https://pytorch.org/docs/stable/tensors.html) 。[torchvision.datasets.CIFAR10](http://pytorch.org/docs/stable/torchvision/datasets.html?highlight=cifar10#torchvision.datasets.CIFAR10) 方法的输出是已准备好使用的对象。下一种方法 [torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html?highlight=dataloader#torch.utils.data.DataLoader) 管理优化计算资源所涉及的后勤任务。对于 GPU 系统，必须首先将数据加载到 CPU 内存中，然后传输到 GPU 内存以加速并行处理。理想情况下，有足够的 CPU 工作进程来确保已处理的数据被移除且未处理的数据以尽可能短的延迟替换数据。

GPU 占用率可能会有所不同，这主要取决于数据集的大小和模型的计算过程。开发高效的数据加载算法和进行人工智能研究通常具有两种不同的技能，因此 PyTorch 团队创建了一个针对任何 torch.Tensor 处理此任务的一般优化方法。要提高 GPU 使用率，您可以调整数据加载器的超参数，例如 `batch_size` 和 `num_workers`。在这种情况下，`batch_size` 为 4，`num_workers` 为 2，这意味着两个 CPU 进程一次将四个映像装入 GPU。根据您的图像和您使用的系统的大小，您可以调整这些值以实现最佳 GPU 利用率。这个特定的例子对 GPU 来说并不是特别具有挑战性的，因为神经网络是浅的，数据集很小。但是，随着您开发具有更多渠道和更大数据集的模型，性能提升会更有意义。

此示例使用两个数据集。CIFAR10 分为_训练_数据集，该数据集经过迭代来开发模型和用于测试模型性能的_测试_数据集。使用数据集时，请考虑将原始数据拆分成 80% 用于培训，20% 用于测试。您应该避免使用用于训练模型的数据进行测试，因为这会限制您检测[过度拟合](https://www.kdnuggets.com/2019/12/fighting-overfitting-deep-learning.html)的能力，这是模型处理训练数据时发生的一个问题，而不是新数据时发生的问题。

## 任务 3：卷积神经网络

[定义卷积神经网络](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#define-a-convolutional-neural-network)，定义模型。数据集由分成三个颜色通道的图像组成：红色、绿色和蓝色。这些值将发送到 LCD 显示器，以像素显示不同的颜色。将颜色分成三个不同的渠道，使我们能够通过机器学习功能改变颜色并创建一个深度神经网络。我们通过多次在每个渠道上运行一个卷积来实现这一目标。

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
    

![动画中显示了过滤器卷积的二维数组](images/cnn.gif)

在高级别上，[nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html?highlight=conv2d#torch.nn.Conv2d) 函数对输入传感器运行卷积，如上面的动画中所示。 _filter_ （术语 _filter_ 和 _kernel_ 可交换地以各种文本使用），用黄色方形表示，通过每个通道（如打字机）传递，并输出新通道。教程指示我们将第一个卷积设置为 `nn.Conv2d(3, 6, 5)`。对 `Conv2d` 的此调用要求输入传感器具有 3 个通道，并通过多次运行卷积来输出具有 6 个通道的传感器。最终值 5 是过滤器（内核）大小，5x5 正方形。如果传递了元组 (5,2)，则使用矩形过滤器。请注意，数据集中的所有图像都是 32x32 像素方块。如果将具有不同大小图像的数据集输入到此算法中，则会发生运行时错误，因为输出矩阵的大小不同。

教程中的下一行定义了一种对数据进行降样的方法，以帮助控制神经网络的大小并防止过度拟合 [nn.MaxPool2d](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html) 。与之前一样，输入参数首先是过滤器的大小，然后是步长。默认情况下，`nn.Conv2d` 函数中的步长为 1，但是对于 `nn.MaxPool2d`，我们使用的是 2。因此，过滤器不能通过一个方形向右移动，而是由两个方形向右移动。每次移动后，`nn.MaxPool2d` 将获取过滤器看到的最大值并将其置于输出通道中。

![最大池的视觉摘要](images/maxpool.png)

第二个卷积层，从第一个卷积中提取 6 个输出通道作为输入，输出 16 个通道，随后定义。重复卷积层和最大池层可以帮助模型查找图像中的功能。然而，计算负载和内存需求快速扩展，增加更多层并不总是产生性能更好的模型。

为了实现预测，模型使用三个称为完全连接层的 [nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html?highlight=nn%20linear#torch.nn.Linear) 层。此函数是直线回归的直接应用。`Conv2d` 和 `MaxPool2d` 函数的输出传感器平展为一维数组，该数组的长度为第一个输入。获取正确的输入数字将采用教程中未介绍的一些数学计算。我们已经定义了卷积的工作方式，并且可以使用以下方程式计算输出高度和宽度：

`Output Width = ((Image Width – Filter Width + 2 * Padding) / Stride) + 1`

`Output Height = ((Image Height – Filter Height + 2 * Padding) / Stride) + 1`

（ [padding and stride](https://deepai.org/machine-learning-glossary-and-terms/padding) 的默认值分别为 0 和 1。）

在卷积之后，将应用最大池步骤，因此，在每个步骤之后，将输出宽度和高度除以最大池宽度和高度，以获得最终输出大小。因为我们使用正方形尺寸，所以只需要执行一组计算：

`Convolution 1: ((32 – 5 + 2 * 0) / 1 ) + 1 = 28`

`Max Pool 1: 28 / 2 = 14`

`Convolution 2: ((14 - 5 + 2 * 0) / 1 ) + 1 = 10`

`Max Pool 2: 10 / 2 = 5`

我们的最终输出是 5x5 平方的 16 个通道，因此第一个完全连接层的第一个输入如下所示：

`16 * 5 * 5 = 400`

请注意，`16 * 5 * 5` 是 `fc1` 层的第一个输入。第二个输入是目标输出数组大小。最佳做法是通过连续的层逐步减小数组的大小，这说明了如何使用三层。在构建用于分类的新模型时，对最终完全连接的层输出的猜测与试图将图像筛选到的类别数量相同。在这种情况下，我们有 10 个类别：飞机、汽车、鸟、猫、鹿、狗、青蛙、马、船只和卡车。

![CIFAR10 每个类别的图像采样](images/cifar10.png)

在模型的代码中，每个操作在类初始化中定义，对样本执行操作的顺序在 `forward` 方法中定义。 [F.relu](https://pytorch.org/docs/stable/nn.functional.html?highlight=relu#torch.nn.functional.relu) 是[激活函数](https://www.kdnuggets.com/2017/09/neural-network-foundations-explained-activation-function.html)。参与激活功能超出了本帖的范围，但总之有三个受欢迎的选项：Sigmoid，TanH 和 ReLU。这三个（及更多）在不同的情况下非常有用，但 ReLU 可用于此示例。Tensor.view 更改了理应器的形状，在本例中，将其调整为一维的 400 长度数组，如前所述。

## 任务 4：损失功能和优化程序

    import torch.optim as optim
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    

PyTorch 教程中的下一部分定义了使用的[丢失函数（标准）](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html)和[优化器](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html?highlight=optimizer#optimizers)，它们本身也是深层主题，但在此处超出范围。选择的选项是很好的“通用”选项。[机器学习词汇表](https://ml-cheatsheet.readthedocs.io/)简洁地描述了以下两种操作：

“优化器 ...... 通过更新模型来结合损失函数和模型参数，以响应损失函数的输出。简而言之，优化器通过调整权重，将您的模型配置成最准确的形式。损耗功能是地形的指南，在优化程序朝正确或错误的方向移动时告诉它。

![动画中显示了导航渐变的不同优化程序](images/loss.gif) 映像储值：[CS231n](https://cs231n.github.io/neural-networks-3/)

培训处理将生成一个可以比作山区的梯度。随着数据迭代，您想要达到全训练模型的山区最低点，但问题是您缺乏地图。损耗函数和优化程序可帮助您后退该范围，但如果您走得太快或使用错误的系统，则可以卡在本地低点而不是实际最小值。

## 任务 5：培训模型

准备好了一切之后，我们就可以开始培训了。在此培训中，我们将持续不断地迭代数据以达到训练有素的模型（第 4 步，[培训网络](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#train-the-network)）。

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
    

查看提供的第一行，`epoch` 是对数据集中的每个映像的完整迭代。编写的教程只对数据进行两次迭代，不足以找出最小值。只需为 `range` 键入其他输入即可增加该数字。跟踪运行损失以确定每个纪元对模型的影响程度。培训通常在运行损失如此之小后停止，以致模型有效地停止了更改。

在第二个循环中，我们列举 `trainloader`。如前所述，加载程序将数据分成批，我们处理每个批。`Inputs` 是包含批图像数据的传感器，`labels` 是批中每个图像的正确类别。通过 `net`（上面定义的 CNN）传递 `inputs` 来获取预测，或通过 `outputs` 进行转发传播。`Outputs` 将传递给具有正确标签的 `criterion`，以获取 `loss` 函数，以确定模型已更改的金额。然后，将 `loss` 传递到 `backward` 传播中，这将调整模型中的每个权重，以最大程度地减少它在预测中导致错误的量。`optimizer.step` 函数根据学习率在山区范围内下一步（使用以前的类比），您可以认为这是我们步骤的长度。您希望使用足够大的学习率来沿着路径移动，但足够小，不能超过可能的途径以获得更好的结果。

成功输出应按此格式输出行，但损失编号在每次运行时都不完全相同。

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
    

## 任务 6：保存模型和测试准确性

    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    

这些代码行会在模型完成文件训练循环后保存模型。此方法对于在整个模型培训中创建检查点也很有用。举例来说，我们将从上面保存的文件加载模型。

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
    

测试模型以确定测量的准确性非常重要。这将指示模型是否为给定用例提供了足够的性能。在上面的训练环节中，我们只训练过两个纪元的模型，因此该模型不会产生令人印象深刻的性能结果。如果增加纪元数，直到损失停止减少，您将发现模型性能会提高。这个过程可以在训练期间定期运行，以进行详细的监测，虽然不应该经常重复，或者会延长训练时间。

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
    

在本例中，我们可以比整体准确性更深入一点。上述代码可输出每个类别的准确性，并可提供有关模型可能混淆的位置的一些洞察。

完整代码示例

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
    

这涵盖了训练深度学习计算机视觉模型的要点。下一步是使用 GPU 加速计算负载。

您可以进入下一个实验室。

## 确认

*   **作者** - Big Compute 高级解决方案架构师 Justin Blau
*   **上次更新者/日期** - Justin Blau，大计算，2020 年 10 月