# Introdução

Implementar um treinamento de classificação de imagens é o "Hello World" da visão computacional. Embora haja muitas postagens de blog sobre o tópico, a maioria delas apresenta a solução sem explicar adequadamente a API da biblioteca, os principais subjacentes ou ambos. O guia [Treinamento de um Classificador](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) de PyTorch é um ótimo lugar para começar, mas alguns detalhes do código são explicados com pouca margem para um leitor novo no campo. Este workshop baseia-se nesse tutorial como uma base com explicações linha a linha do código para facilitar um caminho de aprendizagem mais claro. Este guia mostrará como treinar um modelo usando CPUs e depois explicará como atualizar o código para utilizar os recursos de GPU. Observe que as instâncias de GPU não estão disponíveis na camada livre.

Tempo estimado do laboratório: 60 minutos

### Sobre Tensores

Para melhor ou pior, as representações visuais ajudam a contextualizar a terminologia, as estruturas de dados e os algoritmos da ciência da computação. Para tensores, [KDnuggets fornece uma explicação clara](https://www.kdnuggets.com/2018/05/wtf-tensor.html). Ao pesquisar uma definição on-line, você pode encontrar alguma confusão entre a definição técnica de um uso de tensor e coloquial. Tecnicamente, um vetor é um tensor 1-dimensional e uma matriz é um tensor 2-dimensional, mas na maioria das vezes o tensor refere-se a um recipiente n-dimensional que é maior do que um vetor e uma matriz.

![Representação visual de diferentes tensores](images/tensor.png)

O termo é importante para ser compreendido porque os tensores são fundamentais para a criação de algoritmos de inteligência artificial, e eles sustentam discussões sobre a otimização de recursos de computação. Uma área comum preocupante ao treinar um modelo é a utilização da GPU, porque a [Turing Tensor Cores](https://www.nvidia.com/en-us/data-center/tensor-cores/) da NVIDIA é capaz de acelerar computacionalmente, mas pode levar algum ajuste. Felizmente, PyTorch contém um carregador de dados que assume a responsabilidade de transmitir tensores para a GPU.

### Objetivos

Tópicos abordados neste laboratório:

*   Tensores
*   DataLoaders
*   Redes Neurais Convolutivas (CNN)
*   Função de Perda e Otimizador
*   Treinamento de Modelo
*   Aceleração da GPU;

### Pré-requisitos

Este laboratório pressupõe que você tenha:

*   Uma conta no Oracle Free Tier ou Paid Cloud

## Confirmação

*   **Autor** - Justin Blau, Arquiteto de Soluções Sênior, Big Compute
*   **Data da Última Atualização** - Justin Blau, Big Compute, outubro de 2020