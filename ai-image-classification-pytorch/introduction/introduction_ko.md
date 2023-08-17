# 소개

이미지 분류 교육을 구현하는 것은 컴퓨터 비전의 "Hello World"입니다. 주제에 대한 많은 블로그 게시물이 있지만 대부분 라이브러리 API, 기본 주체 또는 둘 다를 적절하게 설명하지 않고 솔루션을 제시합니다. PyTorch의 [분류자 교육](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) 가이드는 시작할 수 있는 좋은 장소이지만 코드의 일부 세부정보는 필드에 새로운 독자를 위해 너무 희박하게 설명되어 있습니다. 이 워크샵은 이 자습서를 기반으로 보다 명확한 학습 경로를 위해 코드에 대한 라인별 설명이 제공됩니다. 이 가이드는 CPU를 사용하여 모델을 교육한 다음 GPU 리소스를 활용하기 위해 코드를 업데이트하는 방법을 설명합니다. 무료 계층에서는 GPU 인스턴스를 사용할 수 없습니다.

예상 실습 시간: 60분

### 텐서 정보

더 좋거나 더 나쁜 시각 표현을 위해 컴퓨터 과학 용어, 데이터 구조 및 알고리즘을 컨텍스트화하는 데 도움이 됩니다. 텐서의 경우 [KDnuggets는 명확한 설명을 제공합니다](https://www.kdnuggets.com/2018/05/wtf-tensor.html). 온라인에서 정의를 검색할 때 텐서 및 콜로키얼 사용의 기술적 정의 사이에 약간의 혼란이 발생할 수 있습니다. 기술적으로 벡터는 1차원 텐서이고 행렬은 2차원 텐서이지만 대부분의 경우 텐서는 벡터와 행렬보다 큰 n차원 컨테이너를 나타냅니다.

![다양한 크기의 텐서의 시각적 표현](images/tensor.png)

텐서는 인공 지능 알고리즘을 구축하는 데 중점을 두고 있으며 컴퓨팅 리소스 최적화에 대한 논의를 뒷받침하기 때문에 이 용어를 이해하는 것이 중요합니다. NVIDIA [Turing Tensor Cores](https://www.nvidia.com/en-us/data-center/tensor-cores/)는 상당한 계산 가속화가 가능하지만 일부 튜닝을 수행할 수 있으므로 모델 교육 시 공통적인 관심 분야는 GPU 활용률입니다. 다행히 PyTorch에는 GPU로 테서를 전송하는 책임이 있는 데이터 로더가 포함되어 있습니다.

### 목표

이 실습에서 다루는 주제:

*   텐서
*   DataLoaders
*   CNN(Convolutional Neural Networks)
*   손실 기능 및 옵티마이저
*   모델 교육
*   GPU 가속;

### 필수 조건

이 실습에서는 다음 사항이 있다고 가정합니다.

*   Oracle Free Tier 또는 유료 클라우드 계정

## 확인

*   **작성자** - Big Compute 수석 솔루션 아키텍트인 Justin Blau
*   **최종 업데이트 수행자/날짜** - Justin Blau, Big Compute, 2020년 10월