# 分類子のトレーニング

## 概要

PyTorchの[分類子のトレーニング](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)・ガイドは開始するのに最適な場所です。ただし、コードの詳細の一部については、フィールドに新しく追加された読者にはあまりにも疎外と説明されています。このラボは、そのチュートリアルをベースとして構築し、コードのラインバイラインの説明を使用して、より明確な学習パスを容易にしています。

推定ラボ時間: 45分

### 目的

この演習では、次について学習します:

*   DataLoaders
*   Convolutional Neural Networks(CNN)
*   損失関数とオプティマイザ
*   モデル・トレーニング

### 前提条件

この演習では、次のことを前提としています。

*   Oracle Free Tierまたは有料クラウド・アカウント
*   演習1と2で説明されているように、CPUまたはGPUインスタンスへのアクセス

## タスク1: 環境の設定

python環境を設定してPyTorchを実行する方法は数多くあります。望ましい方法または次の一連の指示に従うことができます。

1.  サイトをスクロールダウンして「Linux Installers」に移動し、一番上のリンクをコピーすることで、[最新バージョンのminiconda](https://docs.conda.io/en/latest/miniconda.html)へのリンクを取得します。
    
2.  インスタンスのターミナルで、wgetを使用してファイルをダウンロードします。
    
         wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
        
3.  [インストール・コマンドを実行して](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html)、設定を完了します。
    
         bash Miniconda3-latest-Linux-x86_64.sh
        
4.  端末を終了し、インストール後に新しい端末を開きます。これにより、condaが確実にアクティブになります。オプション: コマンド`conda create --name pytorch python=3.6`を実行し、`conda activate pytorch`を実行して環境を有効にすることで、PyTorchの特定の環境を作成します。
    
5.  conda `conda install pytorch torchvision cudatoolkit=10.2 -c pytorch`を介して[PyTorchをインストール](https://pytorch.org/)
    

## タスク2: CIFAR10およびDataLoader

完全なコード例は、このページの下部にあります。

AI研究者の時間のほとんどは、トレーニングおよびテスト・モデルの実行ではなく、データのクリーニングと準備に費やされています。次の数行のコードに膨大な量の作業が凝縮されています。CIFAR10は、完全に前処理されたイメージおよびラベルのデータセットです。新しいモデルをテストしたり、ディープ・ラーニングを開始するための標準化されたベンチマークとして機能するように作成および公開されました。rawデータのセットを PyTorchで動作する構造に変換すると、膨大な時間がかかるため、そのバリアーを下げるためにデータセットが PyTorchライブラリ配布に含まれています。

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
    

[transform.Compose](https://pytorch.org/docs/stable/torchvision/transforms.html)は、データがモデルに必要なテンソル形式であり、すべての値が定義された範囲内に収まるように[正規化](https://discuss.pytorch.org/t/understanding-transform-normalize/21730)されていることを確認します。多くの場合、データを正規化すると、データセットの値の一貫性が向上するため、モデル・トレーニングを改善できます。

これらのコード行は、データセットのダウンロードを管理し、イメージを3つのカラーチャネル(赤、緑、青)に分割して、正しいラベル(飛行機、鳥、犬、カエルなど)に一致させ、すべてのイメージとラベルを [torch.Tensors](https://pytorch.org/docs/stable/tensors.html)に変換します。[torchvision.datasets.CIFAR10](http://pytorch.org/docs/stable/torchvision/datasets.html?highlight=cifar10#torchvision.datasets.CIFAR10)メソッドの出力は、すぐに使用できるオブジェクトです。次のメソッド[torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html?highlight=dataloader#torch.utils.data.DataLoader)は、コンピュート・リソースの最適化に関連するロジスティクス・タスクを管理します。GPUシステムでは、パラレル処理を高速化するために、まずデータをCPUメモリーにロードしてからGPUメモリーに転送する必要があります。理想的には、処理されたデータが取り除かれ、処理されていないデータができるだけ少ない遅延で置き換えられるように、十分なCPUワーカーが存在します。

GPUの使用率は、主にデータセットのサイズとモデルの計算プロセスによって異なります。効率的なデータ・ロード・アルゴリズムの開発と人工知能調査の実施は、通常2つの異なるスキルであるため、PyTorchチームは、すべてのtorch.Tensorでこのタスクを処理するための一般的な最適化された方法を作成しました。GPU使用率を向上させるために、データ・ローダー(`batch_size`や`num_workers`など)のハイパーパラメータを調整できます。この場合、`batch_size`は4で、`num_workers`は2です。つまり、2つのCPUプロセスが一度に4つのイメージをGPUにロードします。イメージのサイズおよび使用しているシステムに応じて、これらの値を調整して最適なGPU使用率を実現できます。この具体的な例は、ニューラル・ネットワークが浅く、データセットが小さいため、GPUを実行することは特に困難ではありません。ただし、チャネルを増やし、データセットを大きくする独自のモデルを開発すると、パフォーマンスのアップリフトがより重要になります。

この例では、2つのデータセットを使用します。CIFAR10は、モデルの開発に反復される_トレーニング_・データセットと、モデルのパフォーマンスのテストに使用される非表示データの_テスト_・データセットに分割されます。データセットを使用する場合は、生データをトレーニングに使用した80%、テストに使用した20%に分割することを検討してください。モデルのトレーニングに使用されるデータでのテストは避ける必要があります。これは、モデルがトレーニング・データに対して適切に動作するが、新規データに対して適切に動作する場合に発生する問題である、[オーバーフィッティング](https://www.kdnuggets.com/2019/12/fighting-overfitting-deep-learning.html)を検出する能力を制限するためです。

## タスク3: 畳み込みニューラル・ネットワーク

[「畳み込みニューラル・ネットワークの定義」](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#define-a-convolutional-neural-network)で、モデルを定義します。データセットは、赤、緑、青の3つの色チャネルに分割されたイメージで構成されます。これらの値は、さまざまな色をピクセルで示すためにLCDディスプレイに送信されます。色を3つの異なるチャネルに分割すると、機械学習機能で色を変え、ディープ・ニューラル・ネットワークを作成できます。これは、各チャネルでコンボリューションを複数回実行することで実現します。

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
    

![2-D配列を含むフィルタを示すアニメーション](images/cnn.gif)

上位レベルでは、[nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html?highlight=conv2d#torch.nn.Conv2d)関数は、前述のアニメーションに示すように、入力テンソルに対して変換を実行します。_フィルタ_ (_フィルタ_と_カーネル_という用語は、さまざまなテキストで同じ意味で使用され、黄色の四角形で表され、タイプライターのように各チャネルを渡して新しいチャネルを出力します。このチュートリアルでは、最初の畳み込みを`nn.Conv2d(3, 6, 5)`に設定するように指示します。`Conv2d`へのこのコールは、入力テンソルに3つのチャネルがあることを想定し、コンボリューションを複数回実行することで、6つのチャネルを持つテンソルを出力します。最終値の5は、フィルタ(カーネル)・サイズ(5x5平方)です。タプル(5,2)が渡されると、長方形のフィルタが使用されます。データセット内のすべてのイメージが32x32ピクセル正方形であることに注意してください。異なるサイズのイメージを持つデータセットがこのアルゴリズムに入力されると、出力マトリクスが同じサイズでないために実行時エラーが発生します。

チュートリアルの次の行では、ニューラル・ネットワークのサイズを制御し、オーバーフィッティングを防ぐためにデータをダウンサンプルする方法[nn.MaxPool2d](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html)を定義します。前述のように、入力パラメータは、最初にフィルタのサイズ、次に刻み幅になります。デフォルトでは、`nn.Conv2d`関数のstrideは1ですが、`nn.MaxPool2d`では2を使用しています。そのため、フィルタは1つの正方形で右にシフトする代わりに、2つの正方形で右にシフトします。すべての移動後、`nn.MaxPool2d`はフィルタで表示される最大値を取り、出力チャネルに配置します。

![最大プーリングのビジュアル・サマリー](images/maxpool.png)

次に、最初の畳み込みから6つの出力チャンネルを入力および出力16チャンネルとして取得する2番目の畳み込みレイヤーを定義します。畳み込みレイヤと最大プーリングレイヤを繰り返すと、モデルがイメージ内のフィーチャを見つけるのに役立ちます。ただし、計算ワークロードとメモリー要件の両方が迅速に拡張され、レイヤーを追加しても、必ずしもより優れたパフォーマンス・モデルが生成されるわけではありません。

予測に到達するために、モデルでは、完全に接続されたレイヤーと呼ばれる3つの[nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html?highlight=nn%20linear#torch.nn.Linear)レイヤーが使用されます。この関数は線形回帰の簡単な適用である。`Conv2d`および`MaxPool2d`関数の出力テンサは1次元配列にフラット化され、その配列の長さが最初の入力になります。正しい入力番号を取得するには、チュートリアルで説明されていない数学が必要です。畳み込みの動作をすでに定義しており、次の式を使用して出力の高さと幅を計算できます。

`Output Width = ((Image Width – Filter Width + 2 * Padding) / Stride) + 1`

`Output Height = ((Image Height – Filter Height + 2 * Padding) / Stride) + 1`

([padding and stride](https://deepai.org/machine-learning-glossary-and-terms/padding)のデフォルト値はそれぞれ0と1です。)

畳み込み後、最大プーリング・ステップが適用されるため、出力幅と高さを各ステップの後の最大プーリング幅と高さで除算し、最終出力サイズを取得します。平方ディメンションを使用しているため、1つの計算セットのみを実行する必要があります。

`Convolution 1: ((32 – 5 + 2 * 0) / 1 ) + 1 = 28`

`Max Pool 1: 28 / 2 = 14`

`Convolution 2: ((14 - 5 + 2 * 0) / 1 ) + 1 = 10`

`Max Pool 2: 10 / 2 = 5`

最後の出力は5x5正方形の16チャネルであるため、完全に接続された最初のレイヤーの最初の入力は次のとおりです。

`16 * 5 * 5 = 400`

`16 * 5 * 5`が`fc1`レイヤーの最初の入力であることに注意してください。2番目の入力は、ターゲット出力配列サイズです。ベスト・プラクティスは、3つのレイヤーの使用について説明する連続するレイヤーを介して、配列のサイズを徐々に減らすことです。分類用の新しいモデルを構築する場合、完全に接続された最終的なレイヤー出力の推測は、イメージをフィルタしようとしているカテゴリの数と同じです。このケースでは、飛行機、自動車、鳥、猫、鹿、犬、カエル、馬、船、トラックという10のカテゴリーがあります。

![CIFAR10の各カテゴリからのイメージのサンプリング](images/cifar10.png)

モデルのコードでは、各操作はクラスの初期化で定義され、サンプルに対して操作が実行される順序は`forward`メソッドで定義されます。[F.relu](https://pytorch.org/docs/stable/nn.functional.html?highlight=relu#torch.nn.functional.relu)は[アクティブ化関数](https://www.kdnuggets.com/2017/09/neural-network-foundations-explained-activation-function.html)です。アクティブ化機能への依存はこの投稿の範囲外ですが、要するに、Sigmoid、TanHおよびReLUの3つの一般的なオプションがあります。これらの3つ(以上)は様々なシナリオで役立ちますが、この例ではReLUが機能します。Tensor.viewは、前に説明したように、テンソルの形状を変更します。この場合、テンソルの形状は、1次元400長配列にフラット化されます。

## タスク4: Loss関数とオプティマイザ

    import torch.optim as optim
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    

The next section in the PyTorch tutorial defines the [loss function (criterion)](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html) and [optimizer](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html?highlight=optimizer#optimizers) used, which are also deep topics themselves, but out of scope here. The ones selected are good “general purpose” options. The [Machine Learning Glossary](https://ml-cheatsheet.readthedocs.io/) succinctly describes both of these operations:

オプティマイザーは、損失関数の出力に応じてモデルを更新することで、損失関数とモデルパラメータを結び付けます。簡単に言えば、オプティマイザは、重みを調整して、モデルを最も正確な形に形成し、形成します。損失関数は地形へのガイドであり、オプティマイザが正しい方向または間違った方向に移動しているときに通知します。」

![グラデーションをナビゲートする異なるオプティマイザを表示するアニメーション](images/loss.gif) イメージ・クレジット: [CS231n](https://cs231n.github.io/neural-networks-3/)

研修プロセスでは、山岳地帯に接近できるグラデーションが生成されます。データを反復処理するときに、完全にトレーニングされたモデルに対してマウンテン範囲の最下位ポイントに到達するとします。ただし、マップがないという問題があります。損失関数とオプティマイザは範囲の降下に役立ちますが、速すぎたり間違ったシステムを使用したりすると、実際の最小値ではなく局所的な低ポイントでスタックする可能性があります。

## タスク5: モデルのトレーニング

すべての準備が整ったため、トレーニングを開始する準備が整い、トレーニングされたモデルに到達するためにデータを繰り返し処理します(ステップ4、[ネットワークのトレーニング](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#train-the-network))。

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
    

最初に指定された行を見ると、`epoch`はデータセット内のすべてのイメージに対する完全な反復です。記述されたチュートリアルでは、データが2回のみ反復されるため、最小値を見つけるには十分ではありません。`range`に別の入力を入力するだけで、その数を増やすことができます。実行損失が追跡され、各エポックがモデルに与える影響が決定されます。通常、トレーニングは、実行中の損失が非常に小さいためにモデルが効果的に変更を停止した後、停止します。

2番目のループでは、`trainloader`を列挙します。前述のとおり、ローダーはデータをバッチにセグメント化し、各バッチを処理します。`Inputs`はバッチ・イメージ・データを含む10進数で、`labels`はバッチ内の各イメージの正しいカテゴリです。`inputs`は、予測を取得するために`net` (前述のCNN)を介して前方伝播を介して渡されます。つまり、`outputs`です。`Outputs`は、`loss`関数を取得するための正しいラベルで`criterion`に渡され、モデルが変更された量が決定されます。`loss`は`backward`伝播に渡され、モデルの各重みを調整して、予測でエラーの原因となる量を最小限に抑えます。`optimizer.step`関数は、学習率に基づいて、次のステップを(前の例のように)山岳地帯から下に移動します。これは、ステップの長さと考えることができます。パスに沿って移動するのに十分な大きさの学習レートを使用しますが、より適切な結果を得るための潜在的な収益をオーバーステップしないほど十分に小さい学習レートを使用します。

出力が成功すると、この書式で行が出力されます。ただし、損失番号は実行ごとにまったく同じとはかぎりません。

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
    

## タスク6: モデルの保存と精度のテスト

    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    

これらのコード行は、トレーニング・ループの完了後にモデルをファイルに保存します。この方法は、モデル・トレーニング全体でチェックポイントを作成する場合にも役立ちます。たとえば、上記のファイルに保存したファイルからモデルをロードします。

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
    

測定精度を決定するためにモデルをテストすることが重要です。これは、モデルが特定のユース・ケースに対して十分なパフォーマンスを提供するかどうかを示します。前述のトレーニング・ループで2つのエポックについてモデルのトレーニングのみを行っているため、モデルでは優れたパフォーマンス結果が生成されません。損失が減るまでエポックの数を増やすと、モデルのパフォーマンスが向上することがわかります。このプロセスは、トレーニング中に定期的に実行して詳細なモニタリングを実行できますが、頻繁に繰り返す必要がないか、トレーニング時間が長くなります。

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
    

この例では、全体の精度よりも少し深く掘り下げることができます。前述のコードでは、各カテゴリの精度が出力され、モデルが混乱する可能性のある場所に関するいくつかのインサイトが提供されます。

完全なコード例

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
    

ここでは、ディープ・ラーニング・コンピュータ・ビジョン・モデルのトレーニングの主なポイントについて説明します。次のステップは、GPUを使用して計算ワークロードを評価することです。

次の演習に進むことができます。

## 確認

*   **著者** - Big Compute、シニア・ソリューション・アーキテクト、Justin Blau
*   **最終更新者/日付** - Justin Blau、Big Compute、2020年10月