# 使用 GPU

## 簡介

上一個要涵蓋的主題與使用 GPU 的運算最佳化有關。在教學課程結束時，儲存模型並測試其準確性之後，目前的程式碼只會使用 CPU 資源。我們可以藉由稍微修改程式碼，使用 GPU 資源。Oracle Cloud Infrastructure 會根據需要的運算工作負載，提供不同資源配置的 NVIDIA GPU 陣列。若要開始使用，我建議您在 VM.GPU2.1 上執行此程式碼。尋找適當的 GPU 位置 (可能為 `cuda:0`) 之後，即可將模型、批次資料和標籤傳送到裝置，以使用 `.to(device)` 方法進行處理。PyTorch 會包裝這些方法中的所有記憶體轉換程式碼，讓您不必擔心正確指派 GPU 記憶體。

預估實驗室時間：5 分鐘

### 目標

在此實驗室中，您將瞭解：

*   GPU 上的訓練

### 必要條件

此實驗室假設您具有：

*   已完成上一個實驗室
*   具有支援 GPU 的執行環境

## 作業 1：啟用 GPU 加速

1.  匯入之後，新增這一行程式碼：
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  這一行程式碼會根據系統上可以使用的項目來設定 torch 架構的裝置 。內嵌 if 敘述句可讓程式碼執行，即使 GPU 無法使用，因為它會將值預設回 `cpu`。在提供之完整程式碼中的第 45 行之後，在建立 `net` 之後，請執行此一行程式碼，將 `net` 模型移至 GPU。
    
        net.to(device)
        
3.  最後，在訓練期間，我們需要將批次資料移至 GPU 以進行處理。將提供之程式碼中的第 55 行取代為這一行。
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]` 包含批次影像，`data[1]` 包含相符的標籤。所有這些值都會以 `.to(device)` 傳送到 GPU。就這樣！PyTorch 可讓您輕鬆使用宣告式語法啟用 GPU。

完整程式碼範例

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
    

_**附註：**_當您執行此特定範例時，訓練時間可能會大幅減少。本範例的設計是要讓事情簡單，因此目標是確保程式碼能夠正確執行。問題是影像和模型很小。增加模型中的層和通道數目，以及增加資料載入器中的 `batch_size` 會在比較 CPU 與 GPU 時開始顯示執行時間的差異。

## 確認

*   **作者** - 大運算資深解決方案架構師 Justin Blau
*   **上次更新者 / 日期** - Justin Blau，Big Compute，2020 年 10 月