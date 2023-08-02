# Formación de Clasificadores

## Introducción

La guía [Formación de un clasificador](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) de PyTorch es un buen lugar para empezar, pero algunos de los detalles del código se explican demasiado poco para un lector nuevo en el campo. Este laboratorio se basa en ese tutorial como base con explicaciones línea por línea del código para facilitar una vía de aprendizaje más clara.

Tiempo estimado de laboratorio: 45 minutos

### Objetivos

En este laboratorio, aprenderá sobre:

*   DataLoaders
*   Redes neuronales convolucionales (CNN)
*   Función de pérdida y optimizador
*   Entrenamiento del modelo

### Requisitos previos

En esta práctica se asume que tiene:

*   Una cuenta gratuita de Oracle o de pago en la nube
*   Acceso a una instancia de CPU o GPU como se trata en los laboratorios 1 y 2

## Tarea 1: Configurar el entorno

Hay una serie de métodos para configurar un entorno de python para ejecutar PyTorch. Puede seguir su método preferido o el siguiente conjunto de instrucciones.

1.  Obtenga un enlace para la [última versión de miniconda](https://docs.conda.io/en/latest/miniconda.html) navegando hasta el sitio desplazándose hasta "Instaladores de Linux" y copiando el enlace superior.
    
2.  En un terminal de la instancia, utilice wget para descargar el archivo.
    
         wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
        
3.  [Ejecute el comando de instalación](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html) y complete la configuración.
    
         bash Miniconda3-latest-Linux-x86_64.sh
        
4.  Salga del terminal y abra un nuevo terminal después de la instalación, lo que garantiza que conda esté activado. Opcional: cree un entorno específico para PyTorch ejecutando el comando `conda create --name pytorch python=3.6` y, a continuación, activando el entorno ejecutando `conda activate pytorch`
    
5.  [Instale PyTorch](https://pytorch.org/) mediante conda `conda install pytorch torchvision cudatoolkit=10.2 -c pytorch`
    

## Tarea 2: CIFAR10 y DataLoader

El ejemplo de código completo se encuentra en la parte inferior de esta página.

La mayor parte del tiempo de un investigador de IA no se dedica a ejecutar modelos de entrenamiento y pruebas, sino a limpiar y preparar datos. Una cantidad increíble de trabajo se ha condensado en las pocas líneas de código a continuación. CIFAR10 es un conjunto de datos completamente preprocesado de imágenes y etiquetas. Se creó y se hizo público para servir como referencia estandarizada para probar nuevos modelos o comenzar con el aprendizaje profundo. La transformación de un conjunto de datos raw en una estructura que funcione con PyTorch puede llevar mucho tiempo, por lo que se reduce la barrera que el conjunto de datos se ha incluido en la distribución de la biblioteca PyTorch.

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
    

[transform.Compose](https://pytorch.org/docs/stable/torchvision/transforms.html) garantiza que los datos estén en el formato de tensor necesario para el modelo y que se hayan [normalizado](https://discuss.pytorch.org/t/understanding-transform-normalize/21730) para garantizar que todos los valores se ajusten a un rango definido. La normalización de datos a menudo puede mejorar el entrenamiento del modelo, ya que ayuda a hacer que los valores del conjunto de datos sean más consistentes.

Estas líneas de código gestionan la descarga del conjunto de datos, la división de imágenes en tres canales de color (rojo, verde y azul), la coincidencia con la etiqueta correcta (avión, pájaro, perro, rana, etc.) y la conversión de todas las imágenes y etiquetas en [torch.Tensors](https://pytorch.org/docs/stable/tensors.html). La salida del método [torchvision.datasets.CIFAR10](http://pytorch.org/docs/stable/torchvision/datasets.html?highlight=cifar10#torchvision.datasets.CIFAR10) es un objeto que está listo para su uso. El siguiente método, [torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html?highlight=dataloader#torch.utils.data.DataLoader), gestiona las tareas logísticas implicadas en la optimización de los recursos informáticos. Con los sistemas GPU, los datos primero deben cargarse en la memoria de CPU y luego transferirse a la memoria de GPU para un procesamiento paralelo acelerado. Lo ideal es que haya suficientes trabajadores de CPU para garantizar que los datos procesados se eliminen y los datos sin procesar los sustituyan con el menor retraso posible.

La utilización de GPU puede variar, en función principalmente del tamaño del conjunto de datos y del proceso computacional del modelo. El desarrollo de un algoritmo de carga de datos eficiente y la realización de investigaciones de inteligencia artificial suelen ser dos habilidades diferentes, por lo que el equipo PyTorch ha creado un método general y optimizado para manejar esta tarea para cualquier torch.Tensor. Para mejorar el uso de GPU, puede ajustar los hiperparámetros del cargador de datos, como `batch_size` y `num_workers`. En este caso, `batch_size` es 4 y `num_workers` es 2, lo que significa que dos procesos de CPU cargan cuatro imágenes a la vez en la GPU. Según el tamaño de las imágenes y el sistema que utilice, puede ajustar estos valores para lograr un uso óptimo de GPU. Este ejemplo específico no es particularmente difícil de ejecutar para la GPU porque la red neuronal es poco profunda y el conjunto de datos es pequeño. Sin embargo, a medida que desarrolle sus propios modelos que tienen más canales y un conjunto de datos más grande, el aumento del rendimiento será más significativo.

En este ejemplo, se utilizan dos conjuntos de datos. CIFAR10 se divide en un conjunto de datos de _formación_ que se itera para desarrollar el modelo y un conjunto de datos de _prueba_ de datos no visualizados que se utiliza para probar el rendimiento del modelo. Al trabajar con su conjunto de datos, considere la posibilidad de dividir los datos no procesados en el 80 % utilizado para la formación y el 20 % utilizado para las pruebas. Debe evitar las pruebas con datos que se utilizan para entrenar el modelo porque limita su capacidad para detectar [sobreajuste](https://www.kdnuggets.com/2019/12/fighting-overfitting-deep-learning.html), un problema que ocurre cuando el modelo funciona bien con los datos de entrenamiento, pero no con los datos nuevos.

## Tarea 3: Red neuronal convolucional

[Defina una red neuronal convolucional](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#define-a-convolutional-neural-network), define el modelo. El conjunto de datos consta de imágenes que se dividen en tres canales de color: rojo, verde y azul. Esos valores se envían a pantallas LCD para mostrar diferentes colores en píxeles. La división de los colores en tres canales independientes nos permite convertirlos en funciones de aprendizaje automático y crear una red neuronal profunda. Lo hacemos ejecutando una convolución en cada canal varias veces.

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
    

![Animación que muestra una matriz 2D de conversión de filtro](images/cnn.gif)

En un nivel superior, la función [nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html?highlight=conv2d#torch.nn.Conv2d) ejecuta convoluciones sobre el tensor de entrada, como se muestra en la animación anterior. Un _filtro_ (los términos _filtro_ y _núcleo_ se utilizan indistintamente en varios textos), representado por el cuadrado amarillo, pasa por cada canal como una máquina de escribir y genera un nuevo canal. El tutorial nos indica que definamos la primera convolución en `nn.Conv2d(3, 6, 5)`. Esta llamada a `Conv2d` espera que el tensor de entrada tenga 3 canales y emite un tensor con 6 canales ejecutando la convolución varias veces. El valor final, 5, es el tamaño del filtro (núcleo), un cuadrado 5x5. Si se pasa una tupla (5,2), se utiliza un filtro rectangular. Es importante tener en cuenta que todas las imágenes del conjunto de datos son cuadrados de píxeles 32x32. Si se introduce un juego de datos con imágenes de tamaño diferente en este algoritmo, se produciría un error de tiempo de ejecución porque las matrices de salida no tienen el mismo tamaño.

La siguiente línea del tutorial define una manera de mostrar los datos para ayudar a controlar el tamaño de la red neuronal y evitar el sobreajuste, [nn.MaxPool2d](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html). Como antes, los parámetros de entrada son en primer lugar eltamaño del filtro y luego el caballete. Por defecto, el avance en la función `nn.Conv2d` es 1, pero para `nn.MaxPool2d` estamos utilizando 2. Por lo tanto, en lugar de cambiar a la derecha por un cuadrado, el filtro cambia a la derecha por dos cuadrados. Después de cada movimiento, `nn.MaxPool2d` toma el valor más grande que ve el filtro y lo coloca en el canal de salida.

![Resumen visual de agrupación máxima](images/maxpool.png)

A continuación se define una segunda capa convolucional,que toma los 6 canales de salida de la primera convolución comoentrada y salidas 16 canales. La repetición de capas convolucionales y máximas de agrupación puede ayudar al modelo a encontrar características dentro de la imagen. Sin embargo, tanto los requisitos de carga de trabajo computacional como de memoria se amplían rápidamente, y agregar más capas no siempre produce un modelo de mejor rendimiento.

Para llegar a una predicción, el modelo utiliza tres capas [nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html?highlight=nn%20linear#torch.nn.Linear), denominadas capas completamente conectadas. Esta función es una aplicación directa de regresión lineal. Los tensores de salida de las funciones `Conv2d` y `MaxPool2d` se aplananan en una matriz de 1 dimensión, y la longitud de esa matriz es la primera entrada. Obtener el número de entrada correcto requiere algunas matemáticas que no se describen en el tutorial. Ya hemos definido cómo funciona una convolución y podemos calcular la altura y el ancho de salida utilizando la siguiente ecuación:

`Output Width = ((Image Width – Filter Width + 2 * Padding) / Stride) + 1`

`Output Height = ((Image Height – Filter Height + 2 * Padding) / Stride) + 1`

(Los valores predeterminados para [padding and stride](https://deepai.org/machine-learning-glossary-and-terms/padding) son 0 y 1, respectivamente).

Después de una convolución, se aplica el paso de agrupación máxima, por lo que se divide el ancho y la altura de salida por el ancho y la altura máximos de agrupación después de cada paso para obtener el tamaño de salida final. Debido a que estamos utilizando dimensiones cuadradas, solo necesitamos realizar un juego de cálculos:

`Convolution 1: ((32 – 5 + 2 * 0) / 1 ) + 1 = 28`

`Max Pool 1: 28 / 2 = 14`

`Convolution 2: ((14 - 5 + 2 * 0) / 1 ) + 1 = 10`

`Max Pool 2: 10 / 2 = 5`

Nuestra salida final es de 16 canales de cuadrados 5x5, por lo que la primera entrada para la primera capa completamente conectada es la siguiente:

`16 * 5 * 5 = 400`

Observe que `16 * 5 * 5` es la primera entrada para la capa `fc1`. La segunda entrada es el tamaño de la matriz de salida de destino. La mejor práctica es reducir gradualmente el tamaño de la matriz a través de capas sucesivas, lo que explica el uso de tres capas. Al crear un nuevo modelo de clasificación, una buena estimación de la salida final de la capa totalmente conectada es la misma que el número de categorías en las que intenta filtrar las imágenes. En nuestro caso, tenemos 10 categorías: avión, automóvil, pájaro, gato, ciervo, perro, rana, caballo, barco y camión.

![Muestreo de imágenes de cada categoría de CIFAR10](images/cifar10.png)

En el código del modelo, cada operación se define en la inicialización de clase y el orden en el que se realizan las operaciones en los ejemplos se define en el método `forward`. [F.relu](https://pytorch.org/docs/stable/nn.functional.html?highlight=relu#torch.nn.functional.relu) es una [función de activación](https://www.kdnuggets.com/2017/09/neural-network-foundations-explained-activation-function.html). Avanzar en funciones de activación está más allá del alcance de este post, pero en resumen hay tres opciones populares: Sigmoid, TanH y ReLU. Estas tres (y más) son útiles en diferentes escenarios, pero ReLU funciona para este ejemplo. Tensor.view cambia la forma del tensor, aplanándola en este caso en una matriz de 400 longitudes de 1 dimensión, como se ha descrito anteriormente.

## Tarea 4: Función de pérdida y optimizador

    import torch.optim as optim
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    

La siguiente sección del tutorial PyTorch define la [función de pérdida (criterio)](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html) y el [optimizador](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html?highlight=optimizer#optimizers) utilizados, que también son temas profundos en sí mismos, pero fuera del ámbito aquí. Las seleccionadas son buenas opciones de "propósito general". El [glosario de aprendizaje automático](https://ml-cheatsheet.readthedocs.io/) describe sucintamente ambas operaciones:

"Los optimizadores ... unen la función de pérdida y los parámetros del modelo actualizando el modelo en respuesta a la salida de la función de pérdida. En términos más sencillos, los optimizadores conforman y moldean su modelo en su forma más precisa posible ajustando los pesos. La función de pérdida es la guía del terreno, indicando al optimizador cuando se mueve en la dirección correcta o incorrecta".

![Animación que muestra diferentes optimizadores que navegan por un gradiente](images/loss.gif) Crédito de imagen: [CS231n](https://cs231n.github.io/neural-networks-3/)

El proceso de entrenamiento genera un gradiente que se puede comparar con una cordillera. A medida que itera sobre los datos, desea llegar al punto más bajo de la cordillera para un modelo totalmente entrenado, pero el problema es que le falta un mapa. La función de pérdida y el optimizador le ayudan a descender el rango, pero si va demasiado rápido o utiliza el sistema incorrecto, puede quedar atascado en un punto bajo local en lugar del mínimo real.

## Tarea 5: Formación del modelo

Con todo preparado, estamos listos para comenzar la formación, en la que iteramos continuamente sobre los datos para llegar a nuestro modelo entrenado (paso 4, [Entrenar la red](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#train-the-network)).

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
    

Si observa la primera línea proporcionada, `epoch` es una iteración completa sobre cada imagen del conjunto de datos. El tutorial, tal y como está escrito, itera sobre los datos solo dos veces, lo que no es suficiente para encontrar el mínimo. Puede aumentar ese número simplemente escribiendo una entrada diferente para `range`. Se realiza un seguimiento de la pérdida de marcha para determinar cuánto influye cada época en el modelo. El entrenamiento se detiene normalmente después de que la pérdida en ejecución se vuelve tan pequeña que el modelo ha dejado de cambiar de manera efectiva.

En el segundo bucle, enumeramos `trainloader`. Como se ha descrito anteriormente, el cargador segmenta los datos en lotes y procesamos cada lote. `Inputs` son los tensores que contienen los datos de imagen por lotes y `labels` son la categoría correcta para cada imagen del lote. `inputs` se transfieren a través de la propagación directa a través de `net` (la CNN definida anteriormente) para obtener la predicción, o `outputs`. `Outputs` se transfieren a `criterion` con la etiqueta correcta para obtener la función `loss`, para determinar la cantidad que el modelo ha cambiado. A continuación, `loss` se pasa a la propagación `backward`, que ajusta cada peso del modelo para minimizar la cantidad que contribuye al error en la predicción. La función `optimizer.step` hace que el siguiente paso por la cordillera (utilizando la analogía anterior) se base en la tasa de aprendizaje, que se puede considerar como la longitud de nuestro paso. Desea utilizar una tasa de aprendizaje lo suficientemente grande como para avanzar por el camino, pero lo suficientemente pequeña como para no sobrepasar una posible avenida para un mejor resultado.

Una salida correcta debe imprimir líneas en este formato, aunque los números de pérdida no serán exactamente los mismos en cada ejecución.

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
    

## Tarea 6: Cómo guardar el modelo y comprobar la precisión

    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    

Estas líneas de código guardan el modelo una vez finalizado el bucle de formación en un archivo. Este método también es útil para crear puntos de control a lo largo del entrenamiento del modelo. En aras del ejemplo, cargaremos el modelo desde el archivo guardado anteriormente.

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
    

Es importante probar el modelo para determinar la precisión medida. Esto indicará si el modelo proporciona un rendimiento suficiente para un caso de uso determinado. Solo hemos entrenado el modelo para dos épocas en el bucle de entrenamiento anterior, por lo que el modelo no producirá resultados de rendimiento impresionantes. Si aumenta el número de épocas hasta que la pérdida deje de disminuir, verá que el rendimiento del modelo mejora. Este proceso se puede ejecutar periódicamente durante el entrenamiento para una supervisión detallada, aunque no se debe repetir con demasiada frecuencia o alargará el tiempo de entrenamiento.

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
    

Para este ejemplo, podemos profundizar un poco más que solo la precisión general. El código anterior imprime la precisión de cada categoría y puede proporcionar cierta información sobre dónde se puede confundir el modelo.

Ejemplo de código completo

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
    

Esto abarca los principales puntos de entrenamiento de modelos de visión informática de aprendizaje profundo. El siguiente paso es acelerar la carga de trabajo computacional con una GPU.

Puede continuar con la siguiente práctica.

## Acuses de recibo

*   **Autor**: Justin Blau, arquitecto sénior de soluciones, Big Compute
*   **Fecha/fecha de última actualización**: Justin Blau, Big Compute, octubre de 2020