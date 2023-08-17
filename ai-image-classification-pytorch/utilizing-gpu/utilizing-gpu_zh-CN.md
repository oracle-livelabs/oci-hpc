# 利用 GPU

## 简介

最后一个主题涉及使用 GPU 的计算优化。在教程结束时，保存模型并测试其准确性后，当前代码仅使用 CPU 资源。通过稍微修改代码，我们可以使用 GPU 资源。Oracle Cloud Infrastructure 提供了一系列具有不同配置的 NVIDIA GPU，具体取决于所需的计算负载。为开始使用，我建议在 VM.GPU2.1 上运行此代码。在找到合适的 GPU 位置（可能为 `cuda:0`）后，可以将模型、批处理的数据和标签发送到设备以使用 `.to(device)` 方法进行处理。PyTorch 会包装这些方法中的所有内存转换代码，这样您就不必担心正确分配 GPU 内存了。

估计实验室时间：5 分钟

### 目标

在本练习中，您将了解：

*   GPU 培训

### 前提条件

本练习假定您具有：

*   已完成以前的实验
*   具有启用 GPU 的实例

## 任务 1：启用 GPU 加速

1.  导入后，添加以下代码行：
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  此代码行将根据系统上的可用内容设置火炬框架的设备。内嵌 if 语句允许执行代码，即使 GPU 不可用，因为该代码会将值默认回 `cpu`。在提供的完整代码的第 45 行之后，在实例化 `net` 之后，通过运行此代码行将 `net` 模型移动到 GPU。
    
        net.to(device)
        
3.  最后，在培训期间，我们需要将批处理数据移至 GPU 进行处理。将提供的代码中的第 55 行替换为此行。
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]` 包含批处理映像，`data[1]` 包含匹配的标签。所有这些值都通过 `.to(device)` 发送到 GPU。就这么简单！通过 PyTorch，可以使用声明式语法轻松启用 GPU。

完整代码示例

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
    

_**注意：**_运行此特定示例时，培训时间可能不会显著减少。此示例旨在简化操作，因此目的在于确保代码正确执行。问题是图像和模型很小。增加模型中的层和通道数量以及增加数据加载程序中的 `batch_size` 将开始显示 CPU 与 GPU 比较时的执行时间差异。

## 确认

*   **作者** - Big Compute 高级解决方案架构师 Justin Blau
*   **上次更新者/日期** - Justin Blau，大计算，2020 年 10 月