# GPUの使用

## 概要

最後に取り上げるトピックは、GPUを使用した計算の最適化に関するものです。チュートリアルの最後に、モデルを保存してその精度をテストした後、現在のコードはCPUリソースのみを使用しています。コードをわずかに変更することで、GPUリソースを使用できます。Oracle Cloud Infrastructureは、必要な計算ワークロードに応じて、様々なシェイプでNVIDIA GPUのアレイを提供します。まず、VM.GPU2.1でこのコードを実行することをお薦めします。適切なGPUの場所(`cuda:0`)が見つかった後、`.to(device)`メソッドを使用して処理するために、モデル、バッチ・データおよびラベルをデバイスに送信できます。PyTorchは、これらのメソッドのすべてのメモリー遷移コードをラップするため、GPUメモリーの適切な割当てを心配する必要はありません。

予想ラボ時間: 5分

### 目的

この演習では、次について学習します:

*   GPUでのトレーニング

### 前提条件

この演習では、次のことを前提としています。

*   前の演習を完了している
*   GPU対応インスタンスがあること

## タスク1: GPUアクセラレーションの有効化

1.  インポート後に、次のコード行を追加します。
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  このコード行は、システムで使用可能なものに基づいて、聖火フレームワークのデバイスを設定します。インラインのif文では、GPUが使用できない場合でも、値を`cpu`に戻すため、コードを実行できます。指定された完全なコードの行45の後に、`net`がインスタンス化された後、次のコード行を実行して、`net`モデルをGPUに移動します。
    
        net.to(device)
        
3.  最後に、トレーニング中に、処理のためにバッチ・データをGPUに移動する必要があります。指定されたコードの行55をこの行に置き換えます。
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]`にはバッチ・イメージが含まれ、`data[1]`には一致するラベルが含まれます。これらの値はすべて、`.to(device)`でGPUに送信されます。これで終わりです。PyTorchを使用すると、宣言構文を使用してGPUを簡単に有効化できます。

完全なコード例

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
    

_**ノート:**_この特定の例を実行すると、研修時間が大幅に短縮されない場合があります。この例は簡潔に保つように設計されているため、コードが正しく実行されることを確認するだけです。問題は、イメージとモデルが小さいことです。モデル内のレイヤーおよびチャネルの数を増やすと同時に、データローダーで`batch_size`を増やすと、CPUとGPUを比較する際の実行時間の差異が表示されます。

## 確認

*   **著者** - Big Compute、シニア・ソリューション・アーキテクト、Justin Blau
*   **最終更新者/日付** - Justin Blau、Big Compute、2020年10月