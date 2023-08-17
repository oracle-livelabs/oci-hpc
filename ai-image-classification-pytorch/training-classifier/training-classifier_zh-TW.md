# 訓練 Classifier

## 簡介

PyTorch 的 [Training a Classifier](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) 指南是開始的一個很棒的地方，但一些程式碼的細節對新領域有太過稀疏的讀者來說。這個實驗室以教學課程為基礎，並搭配程式碼的逐行說明，以提供更清晰的學習路徑。

預估實驗室時間：45 分鐘

### 目標

在此實驗室中，您將瞭解：

*   DataLoaders
*   革命性神經網路 (CNN)
*   遺失函數與最佳化處理程式
*   模型訓練

### 必要條件

此實驗室假設您具有：

*   Oracle Free Tier 或 Paid Cloud 帳戶
*   存取實驗室 1 和 2 中所涵蓋的 CPU 或 GPU 執行處理

## 作業 1：設定環境

設定 python 環境以執行 PyTorch 的方法有很多。您可以按照您偏好的方法或下列的一組指示進行操作。

1.  瀏覽至網站以向下捲動至「Linux 安裝程式」並複製頂端連結，即可取得[最新版 miniconda](https://docs.conda.io/en/latest/miniconda.html) 的連結。
    
2.  在執行處理的終端機中，使用 wget 下載該檔案。
    
         wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
        
3.  [執行安裝指令](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html)並完成安裝。
    
         bash Miniconda3-latest-Linux-x86_64.sh
        
4.  在安裝之後結束終端機並開啟新的終端機，以確保啟用 conda。選擇性：執行 `conda create --name pytorch python=3.6` 命令，然後執行 `conda activate pytorch` 啟用環境，為 PyTorch 建立特定環境。
    
5.  [透過 conda `conda install pytorch torchvision cudatoolkit=10.2 -c pytorch` 安裝 PyTorch](https://pytorch.org/)
    

## 作業 2：CIFAR10 和 DataLoader

完整程式碼範例位於此頁面底部。

大多數的人工智慧研究者的時間並不花費在執行訓練和測試模型，而是清理和準備資料。下面幾行程式碼已經包含了不可思議的工作量。CIFAR10 是影像和標籤的完整預先處理資料集。它被建立並公開作為標準化基準，供測試新模型或開始使用深度學習。將一組原始資料轉換成與 PyTorch 搭配使用的結構可能需要很長的時間，因此可降低 PyTorch 程式庫分送中包含資料集的障礙。

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
    

[transform.Compose](https://pytorch.org/docs/stable/torchvision/transforms.html) 可確保資料採用模型所需的張量格式，並已[標準化](https://discuss.pytorch.org/t/understanding-transform-normalize/21730)以確保所有值都符合定義的範圍。標準化資料通常可以改善模型訓練，因為這樣可協助讓資料集中的值更為一致。

這些程式碼行負責管理下載資料集、將影像分割成三個色版 (紅色、綠色和藍色)、將影像與正確標籤 (飛機、鳥類、狗、青蛙等等) 相符，並將所有影像和標籤轉換成 [torch.Tensors](https://pytorch.org/docs/stable/tensors.html) 。[torchvision.datasets.CIFAR10](http://pytorch.org/docs/stable/torchvision/datasets.html?highlight=cifar10#torchvision.datasets.CIFAR10) 方法的輸出就是準備使用的物件。下一個 [torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html?highlight=dataloader#torch.utils.data.DataLoader) 方法可管理最佳化運算資源所涉及的物流作業。使用 GPU 系統時，必須先將資料載入 CPU 記憶體，然後傳輸至 GPU 記憶體，才能加速平行處理。理想狀況下，有足夠的 CPU 工作者可以確保處理的資料被刪除，而未處理的資料會儘可能短暫地取代。

GPU 使用量可能會因資料集大小和模型的運算程序而異。開發有效率的資料載入演算法和進行人工智慧研究通常具有兩個不同的技能，因此 PyTorch 團隊已建立一般的最佳化方法來處理任何 torch.Tensor 的這項任務。若要改善 GPU 使用量，您可以調整資料載入器的超參數，例如 `batch_size` 和 `num_workers`。在此情況下，`batch_size` 為 4 而 `num_workers` 為 2，這表示兩個 CPU 處理程序一次會載入四個影像至 GPU。視您影像的大小和使用的系統而定，您可以調整這些值，以達到最佳的 GPU 使用率。這個特定範例並沒有特別挑戰 GPU 的執行，因為神經網路是淺的，而資料集很小。不過，當您開發具有更多通道和更大型資料集的模型時，效能提升將會變得更加重要。

此範例使用兩個資料集。CIFAR10 會分割成重複套用於開發模型的_訓練_資料集，以及用來測試模型效能的_測試_資料集。使用您的資料集時，請考慮將您的原始資料分割成 80% 以用於訓練，而將 20% 用於測試。您應該避免對用來訓練模型的資料進行測試，因為它會限制您偵測[過度適合](https://www.kdnuggets.com/2019/12/fighting-overfitting-deep-learning.html)的能力，這是當您的模型對訓練資料 (而非新資料) 運作良好時會發生的問題。

## 工作 3:Convolutional Neural Network

[定義革命性神經網路](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#define-a-convolutional-neural-network)，定義模型。資料集包含分割成三個顏色色版的影像：紅色、綠色及藍色。這些值會傳送至 LCD 顯示器，以像素顯示不同的顏色。將顏色分割成三個不同的通道，讓我們能夠透過機器學習功能流失顏色，並建立深度神經網路。我們會多次對每個通路執行迴旋。

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
    

![動畫顯示迴旋 2-D 陣列的濾鏡](images/cnn.gif)

在高層次，[nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html?highlight=conv2d#torch.nn.Conv2d) 函數會對輸入張量執行轉換，如先前動畫中所示。_篩選_ ( _篩選_和_核心_這兩個術語在各種文字中可互換使用)，以黃色方形表示，會在每個通道 (例如打字機) 上傳送，並輸出新的通道。教學課程指示我們將第一個旋積設定為 `nn.Conv2d(3, 6, 5)`。這對 `Conv2d` 的呼叫預期輸入張量會有 3 個通道，並執行迴旋多次以 6 個通道輸出張量。最終值 5 為篩選器 (核心) 大小，即 5x5 平方。如果傳遞了元組 (5,2)，則會使用矩形篩選器。請務必注意資料集中的所有映像檔都是 32x32 像素方塊。如果此演算法輸入具有不同大小影像的資料集，則表示輸出矩陣大小不相同，因此會發生程式實際執行錯誤。

教學課程的下一行定義向下範例資料的方法，以協助控制神經網路的大小，並防止過度使用 [nn.MaxPool2d](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html) 。如前，輸入參數先是篩選的大小，然後是字串。依預設，`nn.Conv2d` 函數中的字串為 1，但對於 `nn.MaxPool2d`，我們使用 2。因此，篩選器會移到右邊兩個方，而非移到右邊。在每次移動之後，`nn.MaxPool2d` 就會佔用篩選所能見到的最大值，並放入輸出通道。

![集區上限的視覺摘要](images/maxpool.png)

第二個卷積層，從第一個卷積取得 6 個輸出通道作為輸入和輸出 16 個通道，如下所述。重複迴旋和最大集線圖層可以幫助模型尋找圖像中的圖徵。不過，運算工作負載和記憶體需求都可快速擴展，並增加更多層不一定會產生效能更佳的模型。

為了達成預測，模型使用三個 [nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html?highlight=nn%20linear#torch.nn.Linear) 層，稱為完全連接層。此函數是直線迴歸的應用。`Conv2d` 和 `MaxPool2d` 函數的輸出張量均一化成一維陣列，該陣列的長度則是第一個輸入。取得正確的輸入編號會採用教學課程中未描述的一些數學。我們已經定義了卷積的運作方式，並且可以使用下列方程式計算輸出高度和寬度：

`Output Width = ((Image Width – Filter Width + 2 * Padding) / Stride) + 1`

`Output Height = ((Image Height – Filter Height + 2 * Padding) / Stride) + 1`

( [padding and stride](https://deepai.org/machine-learning-glossary-and-terms/padding) 的預設值分別是 0 和 1。)

卷積後，會套用最大集合步數，因此將輸出寬度與高度除以每個步驟後的最大集合寬度與高度，以取得最終輸出大小。因為我們使用方形維度，所以只需要執行一組計算：

`Convolution 1: ((32 – 5 + 2 * 0) / 1 ) + 1 = 28`

`Max Pool 1: 28 / 2 = 14`

`Convolution 2: ((14 - 5 + 2 * 0) / 1 ) + 1 = 10`

`Max Pool 2: 10 / 2 = 5`

最終輸出為 5x5 平方的 16 個通道，因此第一個完全連接層的輸入如下：

`16 * 5 * 5 = 400`

請注意，`16 * 5 * 5` 是 `fc1` 層的第一次輸入。第二個輸入是目標輸出陣列大小。最佳做法是透過使用三層說明的連續層，逐步縮減陣列的大小。建立分類的新模型時，最終完全連接圖層輸出的良好猜測，與您嘗試篩選影像的分類數相同。在我們的案例中，有 10 種類別：飛機、汽車、鳥類、貓、鹿、狗、青蛙、馬、船和卡車。

![從每個 CIFAR10 類別取樣影像](images/cifar10.png)

在模型的程式碼中，每個作業都在類別初始化中定義，而對範例執行作業的順序定義在 `forward` 方法中。 [F.relu](https://pytorch.org/docs/stable/nn.functional.html?highlight=relu#torch.nn.functional.relu) 是 [activation 函數](https://www.kdnuggets.com/2017/09/neural-network-foundations-explained-activation-function.html)。深入啟用功能超出這篇文章的範圍，但簡而言之有三個熱門選項：Sigmoid、TanH 及 ReLU。這三個 (含) 在不同的情況下很有用，但 ReLU 適用於此範例。Tensor.view 會變更張數的形狀，在此情況下，會將其轉換為前述的一維 400 長度陣列。

## 作業 4：遺失函數與最佳化處理程式

    import torch.optim as optim
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    

PyTorch 教學課程的下一節定義了使用的 [loss 函數 (準則)](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html) 和 [optimizer](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html?highlight=optimizer#optimizers) ，它們也是深度主題本身，但在此超出範圍。所選的選項是「一般用途」選項。[機器學習辭彙](https://ml-cheatsheet.readthedocs.io/)簡要說明這兩項作業：

」最佳化程式 ... 透過更新模型以回應損失函數的輸出，將損失函數和模型參數連結在一起。簡單來說，優化器可調整重量，將模型塑造為最準確的形式。損耗函數是地形的導引，在最佳化處理程式以正確或錯誤的方向移動時告訴最佳化處理程式。」

![顯示不同最佳化程式瀏覽漸層的動畫](images/loss.gif) 圖片來源：[CS231n](https://cs231n.github.io/neural-networks-3/)

訓練處理程序會產生一個漸層，可將其視為山脈。當您重複資料時，您想要到達山區的最低點以取得完整訓練模型，但問題是缺乏地圖。遺失函數與最佳化處理程式可協助您消除範圍，但如果您速度太快或使用錯誤的系統，可能會停滯在區域低點，而非實際的最小值。

## 工作 5：訓練模型

我們準備就緒，能夠開始訓練，讓我們持續不斷地迭代資料以到達訓練模型 (步驟 4，[訓練網路](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#train-the-network) )。

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
    

查看第一個提供的行，`epoch` 會對資料集中的每個影像進行完整重複。教學課程的重複資料只會重複一次，資料不足，無法找到最小值。您只要輸入不同的 `range` 輸入即可增加該數字。系統會追蹤運行損失，以確定每個紀元對模型的影響程度。訓練通常會在執行損失變小後停止，模型已有效地停止變更。

在第二個迴圈中，我們會列舉 `trainloader`。如前所述，載入器會將資料分成批次，然後處理每個批次。`Inputs` 是包含批次影像資料的張數，而 `labels` 是批次中每個影像的正確類別。`inputs` 會透過 `net` (以上定義的 CNN) 向前傳輸來取得預測，或通過 `outputs`。`Outputs` 會傳送到具有正確標籤的 `criterion` 以取得 `loss` 函數，以判斷模型已變更的數量。然後將 `loss` 傳遞至 `backward` 傳輸，以調整模型中的每個加權，將預測中錯誤所佔的金額降到最低。`optimizer.step` 函數會根據學習速率讓山脈範圍 (使用先前類比) 下一步，您可以將其視為步驟的長度。您想要使用夠大的學習率來沿著路徑移動，但大小不足以阻礙潛在的營收，以取得更好的結果。

成功的輸出應該要用這種格式列印輸出行，但是遺失的數字並不完全相同。

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
    

## 作業 6：儲存模型與測試準確度

    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    

這些程式碼行會在完成對檔案的訓練迴圈之後儲存模型。此方法也有助於在整個模型訓練中建立檢查點。以範例為例，我們將從上面儲存的檔案載入模型。

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
    

測試模型以確定測量的準確度很重要。這會指出模型是否為指定使用案例提供足夠的效能。我們僅在上述培訓循環中訓練兩個紀元的模型，因此該模型不會產生令人印象深刻的性能結果。如果您增加到損耗停止減少為止的紀元數，您會發現模型效能有所改善。此處理程序可定期在訓練期間執行，以進行詳細監控，雖然不應太常重複，或會延長訓練時間。

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
    

在這個範例中，我們只能深入探索整體的準確性。上述程式碼列印了每個類別的準確性，並可以提供一些關於模型可能被混淆之處的資訊。

完整程式碼範例

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
    

這涵蓋訓練深度學習電腦視覺模型的主要點。下一步是加速 GPU 的運算工作負載。

您可以進入下一個實驗室。

## 確認

*   **作者** - 大運算資深解決方案架構師 Justin Blau
*   **上次更新者 / 日期** - Justin Blau，Big Compute，2020 年 10 月