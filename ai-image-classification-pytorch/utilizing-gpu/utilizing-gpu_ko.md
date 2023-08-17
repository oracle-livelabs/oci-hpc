# GPU 활용

## 소개

마지막으로 다루어야 할 주제는 GPU를 사용한 계산 최적화와 관련이 있습니다. 튜토리얼이 끝나면 모델을 저장하고 정확도를 테스트한 후 현재 코드는 CPU 리소스만 사용합니다. 코드를 약간 수정하면 GPU 리소스를 사용할 수 있습니다. Oracle Cloud Infrastructure는 필요한 계산 워크로드에 따라 다양한 구성의 NVIDIA GPU 배열을 제공합니다. 시작하려면 VM.GPU2.1에서 이 코드를 실행하는 것이 좋습니다. 적절한 GPU 위치(예: `cuda:0`)를 찾은 후 모델, 일괄 처리된 데이터 및 레이블을 장치로 전송하여 `.to(device)` 메소드를 사용하여 처리할 수 있습니다. PyTorch는 이러한 방법으로 모든 메모리 전환 코드를 래핑하므로 GPU 메모리를 제대로 할당할 필요가 없습니다.

예상 실습 시간: 5분

### 목표

이 실습에서는 다음 내용을 다룹니다.

*   GPU에 대한 교육

### 필수 조건

이 실습에서는 다음 사항이 있다고 가정합니다.

*   이전 실습 완료
*   GPU 사용 인스턴스가 있습니다.

## 작업 1: GPU 가속 사용

1.  임포트 후 다음 코드 행을 추가합니다.
    
        device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        
2.  이 코드 행은 시스템에서 사용 가능한 토치 프레임워크에 대한 장치를 설정합니다. 인라인 if 문은 GPU를 사용할 수 없는 경우에도 값을 다시 `cpu`로 설정하므로 코드가 실행되도록 허용합니다. 제공된 전체 코드의 행 45 뒤에, `net`가 인스턴스화된 후에, 이 코드 행을 실행하여 `net` 모델을 GPU로 이동합니다.
    
        net.to(device)
        
3.  마지막으로 교육 과정에서 처리하기 위해 일괄 처리된 데이터를 GPU로 이동해야 합니다. 제공된 코드의 행 55를 이 행으로 바꿉니다.
    
        inputs, labels = data[0].to(device), data[1].to(device)
        

`data[0]`에는 일괄 처리된 이미지가 포함되고, `data[1]`에는 일치하는 레이블이 포함됩니다. 이러한 모든 값은 `.to(device)`를 사용하여 GPU로 전송됩니다. 그게 다야! PyTorch를 사용하면 선언적 구문으로 GPU를 쉽게 활성화할 수 있습니다.

전체 코드 예제

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
    

_**참고:**_ 이 특정 예제를 실행하는 경우 교육 시간이 크게 단축되지 않을 수 있습니다. 이 예제는 간단한 작업을 수행하도록 설계되었으므로 그 목적은 코드가 제대로 실행되는지 확인하는 것입니다. 문제는 이미지와 모델이 작다는 것입니다. 모델에서 계층 및 채널 수를 늘리고 데이터 로더에서 `batch_size`를 늘리면 CPU와 GPU를 비교할 때 실행 시간에 차이가 표시됩니다.

## 확인

*   **작성자** - Big Compute 수석 솔루션 아키텍트인 Justin Blau
*   **최종 업데이트 수행자/날짜** - Justin Blau, Big Compute, 2020년 10월