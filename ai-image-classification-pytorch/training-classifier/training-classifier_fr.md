# Formation à un classificateur

## Présentation

Le guide [Formation d'un classificateur](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) d'PyTorch est un excellent point de départ, mais certains détails du code sont expliqués de manière trop rare pour un lecteur nouveau sur le terrain. Ce laboratoire s'appuie sur ce tutoriel comme une base avec des explications ligne par ligne du code pour faciliter un parcours d'apprentissage plus clair.

Temps de laboratoire estimé : 45 minutes

### Objectifs

Dans cet atelier, vous allez découvrir :

*   DataLoaders
*   Réseaux neuronaux révolutionnaires (CNN)
*   Fonction de perte et optimiseur
*   Entraînement du modèle

### Prérequis

Cet exercice suppose que vous avez :

*   Un compte de niveau gratuit ou de cloud payant Oracle
*   Accès à une instance de CPU ou de GPU tel que décrit dans les exercices 1 et 2

## Tâche 1 : configurer l'environnement

Plusieurs méthodes permettent de configurer un environnement python pour exécuter PyTorch. Vous pouvez suivre votre méthode préférée ou l'ensemble d'instructions suivant.

1.  Obtenez un lien pour la [dernière version de miniconda](https://docs.conda.io/en/latest/miniconda.html) en accédant au site en descendant vers "Linux Installers" et en copiant le lien supérieur.
    
2.  Dans un terminal pour l'instance, utilisez wget pour télécharger le fichier.
    
         wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
        
3.  [Exécutez la commande d'installation](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html) et terminez la configuration.
    
         bash Miniconda3-latest-Linux-x86_64.sh
        
4.  Quittez le terminal et ouvrez un nouveau terminal après l'installation, ce qui garantit que conda est activé. Facultatif : créez un environnement spécifique pour PyTorch en exécutant la commande `conda create --name pytorch python=3.6`, puis en activant l'environnement en exécutant `conda activate pytorch`.
    
5.  [Installez PyTorch](https://pytorch.org/) via conda `conda install pytorch torchvision cudatoolkit=10.2 -c pytorch`
    

## Tâche 2 : CIFAR10 et DataLoader

L'exemple de code complet se trouve en bas de cette page.

La plupart du temps, un chercheur en IA n'est pas passé à exécuter des modèles de formation et de test, mais à nettoyer et à préparer des données. Une quantité incroyable de travail a été condensée dans les quelques lignes de code ci-dessous. CIFAR10 est un ensemble de données entièrement prétraité d'images et d'étiquettes. Il a été créé et rendu public pour servir de référence normalisée pour tester de nouveaux modèles ou commencer avec le deep learning. La transformation d'un ensemble de données brutes en une structure qui fonctionne avec PyTorch peut prendre beaucoup de temps, afin de réduire cette barrière, le jeu de données a été inclus dans la distribution de la bibliothèque PyTorch.

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
    

[transform.Compose](https://pytorch.org/docs/stable/torchvision/transforms.html) garantit que les données sont au format de tenseur requis pour le modèle et ont été [normalisées](https://discuss.pytorch.org/t/understanding-transform-normalize/21730) pour garantir que toutes les valeurs correspondent à une plage définie. La normalisation des données peut souvent améliorer l'entraînement des modèles car elle permet de rendre les valeurs de l'ensemble de données plus cohérentes.

Ces lignes de code gèrent le téléchargement de l'ensemble de données, le fractionnement des images en trois canaux de couleur (rouge, vert et bleu), leur mise en correspondance avec l'étiquette correcte (avion, oiseau, chien, grenouille, etc.) et la conversion de toutes les images et étiquettes en [torch.Tensors](https://pytorch.org/docs/stable/tensors.html). La sortie de la méthode [torchvision.datasets.CIFAR10](http://pytorch.org/docs/stable/torchvision/datasets.html?highlight=cifar10#torchvision.datasets.CIFAR10) est un objet prêt à être utilisé. La méthode suivante, [torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html?highlight=dataloader#torch.utils.data.DataLoader), gère les tâches logistiques impliquées dans l'optimisation des ressources de calcul. Avec les systèmes GPU, les données doivent d'abord être chargées dans la mémoire CPU, puis transférées dans la mémoire GPU pour un traitement parallèle accéléré. Idéalement, il y a suffisamment de processeurs pour s'assurer que les données traitées sont retirées et que les données non traitées les remplacent le plus rapidement possible.

L'utilisation des GPU peut varier, en fonction principalement de la taille de l'ensemble de données et du processus de calcul du modèle. Le développement d'un algorithme de chargement de données efficace et la réalisation de recherches en intelligence artificielle sont généralement deux compétences différentes, de sorte que l'équipe PyTorch a créé une méthode générale et optimisée pour gérer cette tâche pour n'importe quel torch.Tensor. Pour améliorer l'utilisation des GPU, vous pouvez ajuster les hyperparamètres du chargeur de données, tels qu'`batch_size` et `num_workers`. Dans ce cas, `batch_size` est 4 et `num_workers` est 2, ce qui signifie que deux processus CPU chargent quatre images à la fois pour le GPU. En fonction de la taille des images et du système que vous utilisez, vous pouvez ajuster ces valeurs pour obtenir une utilisation optimale des GPU. Cet exemple spécifique n'est pas particulièrement difficile à exécuter pour le GPU car le réseau neuronal est peu profond et l'ensemble de données est petit. Cependant, au fur et à mesure que vous développez vos propres modèles qui ont plus de canaux et un ensemble de données plus grand, l'augmentation des performances sera plus importante.

Cet exemple utilise deux jeux de données. CIFAR10 est divisé en un jeu de données _training_ qui est itéré pour développer le modèle et un jeu de données _test_ de données non vues utilisé pour tester les performances du modèle. Lorsque vous utilisez votre jeu de données, pensez à fractionner vos données brutes en 80 % utilisés pour la formation et 20 % utilisés pour les tests. Vous devez éviter les tests avec les données utilisées pour entraîner le modèle car il limite votre capacité à détecter la [suradaptation](https://www.kdnuggets.com/2019/12/fighting-overfitting-deep-learning.html), un problème qui se produit lorsque votre modèle fonctionne bien contre les données d'entraînement mais pas les nouvelles données.

## Tâche 3 : Réseau neuronal convolutif

[Définir un réseau neuronal révolutionnaire](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#define-a-convolutional-neural-network), définit le modèle. L'ensemble de données se compose d'images divisées en trois canaux de couleur : rouge, vert et bleu. Ces valeurs sont envoyées aux écrans LCD pour afficher différentes couleurs en pixels. La division des couleurs en trois canaux distincts nous permet de les faire passer par des fonctions d'apprentissage automatique et de créer un réseau neuronal profond. Pour ce faire, nous organisons une convolution sur chaque canal plusieurs fois.

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
    

![Animation présentant un filtre convertissant un tableau en 2D](images/cnn.gif)

A un niveau élevé, la fonction [nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html?highlight=conv2d#torch.nn.Conv2d) exécute des convolutions sur le tenseur d'entrée comme indiqué dans l'animation précédente. Un _filtre_ (les termes _filter_ et _kernel_ sont utilisés de manière interchangeable dans différents textes), représentés par le carré jaune, passe sur chaque canal comme un graveur et génère un nouveau canal. Le tutoriel nous demande de définir la première convolution sur `nn.Conv2d(3, 6, 5)`. Cet appel à `Conv2d` s'attend à ce que le tenseur d'entrée ait 3 canaux, et délivre en sortie un tenseur avec 6 canaux en exécutant la convolution plusieurs fois. La valeur finale, 5, est la taille du filtre (noyau), un carré 5x5. Si un tuple (5, 2) est passé, un filtre rectangulaire est utilisé. Il est important de noter que toutes les images du jeu de données sont des carrés de pixels 32x32. Si un jeu de données avec des images de taille différente est entré dans cet algorithme, une erreur d'exécution se produit car les matrices de sortie ne sont pas de la même taille.

La ligne suivante du tutoriel définit un moyen de réduire l'échantillonnage des données afin de contrôler la taille du réseau neuronal et d'empêcher le surajustement, [nn.MaxPool2d](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html). Comme précédemment, les paramètres d'entrée sont d'abord la taille du filtre, puis la bande. Par défaut, le stride dans la fonction `nn.Conv2d` est 1, mais pour `nn.MaxPool2d`, nous utilisons 2. Ainsi, au lieu de se déplacer vers la droite d'un carré, le filtre se déplace vers la droite de deux carrés. Après chaque mouvement, `nn.MaxPool2d` prend la plus grande valeur vue par le filtre et la place dans le canal de sortie.

![Récapitulatif visuel du regroupement maximum](images/maxpool.png)

On définit ensuite une deuxième couche convolutionnelle, qui prend les 6 canaux de sortie de la première convolution en tant qu'entrées et sorties 16 canaux. La répétition des couches de regroupement convolutif et maximal peut aider le modèle à trouver des fonctions dans l'image. Cependant, les exigences en matière de mémoire et de charge de travail de calcul se développent rapidement et l'ajout de couches n'entraîne pas toujours un modèle plus performant.

Pour parvenir à une prédiction, le modèle utilise trois couches [nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html?highlight=nn%20linear#torch.nn.Linear), appelées couches entièrement connectées. Cette fonction est une application simple de la régression linéaire. Les tenseurs de sortie des fonctions `Conv2d` et `MaxPool2d` sont mis à plat dans un tableau en 1 dimension et la longueur de ce tableau est la première entrée. Obtenir le bon numéro d'entrée requiert des calculs qui ne sont pas décrits dans le tutoriel. Nous avons déjà défini le fonctionnement d'une convolution et nous pouvons calculer la hauteur et la largeur de sortie à l'aide de l'équation suivante :

`Output Width = ((Image Width – Filter Width + 2 * Padding) / Stride) + 1`

`Output Height = ((Image Height – Filter Height + 2 * Padding) / Stride) + 1`

(Les valeurs par défaut de [padding and stride](https://deepai.org/machine-learning-glossary-and-terms/padding) sont respectivement 0 et 1.)

Après une convolution, l'étape de regroupement maximum est appliquée. Nous divisons donc la largeur et la hauteur de sortie par la largeur et la hauteur de regroupement maximum après chaque étape pour obtenir la taille de sortie finale. Etant donné que nous utilisons des dimensions carrées, nous devons effectuer un seul ensemble de calculs :

`Convolution 1: ((32 – 5 + 2 * 0) / 1 ) + 1 = 28`

`Max Pool 1: 28 / 2 = 14`

`Convolution 2: ((14 - 5 + 2 * 0) / 1 ) + 1 = 10`

`Max Pool 2: 10 / 2 = 5`

Notre sortie finale est de 16 canaux de carrés 5x5, de sorte que la première entrée pour la première couche entièrement connectée est la suivante :

`16 * 5 * 5 = 400`

`16 * 5 * 5` est la première entrée de la couche `fc1`. La deuxième entrée correspond à la taille du tableau de sortie cible. La meilleure pratique consiste à réduire progressivement la taille du réseau par le biais de couches successives, ce qui explique l'utilisation de trois couches. Lorsque vous créez un nouveau modèle de classification, une bonne idée de la sortie finale de la couche entièrement connectée est identique au nombre de catégories dans lesquelles vous essayez de filtrer les images. Dans notre cas, nous avons 10 catégories : avion, automobile, oiseau, chat, cerf, chien, grenouille, cheval, navire et camion.

![Echantillonnage des images de chaque catégorie de CIFAR10](images/cifar10.png)

Dans le code du modèle, chaque opération est définie lors de l'initialisation de la classe, et l'ordre dans lequel les opérations sont effectuées sur les échantillons est défini dans la méthode `forward`. [F.relu](https://pytorch.org/docs/stable/nn.functional.html?highlight=relu#torch.nn.functional.relu) est une [fonction d'activation](https://www.kdnuggets.com/2017/09/neural-network-foundations-explained-activation-function.html). La réalisation de fonctions d'activation dépasse la portée de cet article, mais en bref il y a trois options populaires : Sigmoid, TanH et ReLU. Ces trois (et plus) sont utiles dans différents scénarios, mais ReLU fonctionne pour cet exemple. Tensor.view modifie la forme du tenseur, dans ce cas l'aplatir en un réseau 1 dimensionnel de 400 longueurs, comme décrit précédemment.

## Tâche 4 : Fonction de perte et optimiseur

    import torch.optim as optim
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    

La section suivante du tutoriel PyTorch définit la [fonction de perte (critère)](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html) et l'[optimiseur](https://ml-cheatsheet.readthedocs.io/en/latest/optimizers.html?highlight=optimizer#optimizers) utilisés, qui sont également des sujets approfondis eux-mêmes, mais hors de portée ici. Les options sélectionnées sont de bonnes options "à usage général". Le [glossaire Machine Learning](https://ml-cheatsheet.readthedocs.io/) décrit succinctement ces deux opérations :

"Les optimiseurs ... relient la fonction de perte et les paramètres du modèle en mettant à jour le modèle en réponse à la sortie de la fonction de perte. En termes plus simples, les optimiseurs façonnent et moulent votre modèle dans sa forme la plus précise possible en ajustant les poids. La fonction de perte est le guide du terrain, indiquant à l'optimiseur quand il se déplace dans la bonne direction ou dans la mauvaise direction."

![Animation présentant différents optimiseurs parcourant un dégradé](images/loss.gif) Crédit d'image : [CS231n](https://cs231n.github.io/neural-networks-3/)

Le processus d'entraînement génère un dégradé qui peut être comparé à une chaîne de montagnes. Lorsque vous itérez sur les données, vous voulez arriver au point le plus bas de la chaîne de montagnes pour un modèle entièrement entraîné, mais le problème est que vous ne disposez pas d'une carte. La fonction de perte et l'optimiseur vous aident à descendre de la plage, mais si vous allez trop vite ou utilisez le mauvais système, vous pourriez être coincé dans un point bas local au lieu du minimum réel.

## Tâche 5 : Entraînement du modèle

Avec tout ce qui est préparé, nous sommes prêts à commencer la formation, dans laquelle nous itérons continuellement les données pour arriver à notre modèle entraîné (étape 4, [Former le réseau](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#train-the-network)).

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
    

En examinant la première ligne fournie, `epoch` est une itération complète sur chaque image de l'ensemble de données. Le tutoriel tel qu'écrit itère seulement deux fois sur les données, ce qui n'est pas suffisant pour trouver le minimum. Vous pouvez augmenter ce nombre simplement en saisissant une entrée différente pour `range`. La perte d'exécution est suivie pour déterminer combien chaque période influence le modèle. L'entraînement s'arrête généralement lorsque la perte de fonctionnement devient si faible que le modèle a effectivement cessé de changer.

Dans la deuxième boucle, nous énumérons `trainloader`. Comme décrit précédemment, le chargeur segmente les données en lots et traite chaque lot. `Inputs` sont les tenseurs qui contiennent les données d'image de lot et `labels` sont la catégorie appropriée pour chaque image du lot. L'élément `inputs` est transmis via la propagation directe via `net` (le CNN défini ci-dessus) pour obtenir la prédiction, ou `outputs`. `Outputs` est transmis à l'élément `criterion` avec le libellé correct pour obtenir la fonction `loss`, afin de déterminer la quantité modifiée par le modèle. La valeur `loss` est ensuite transmise à la propagation `backward`, qui ajuste chaque poids du modèle afin de minimiser la quantité qui contribue à l'erreur dans la prédiction. La fonction `optimizer.step` fait le pas suivant vers le bas de la chaîne de montagnes (en utilisant l'analogie précédente) en fonction du taux d'apprentissage, que vous pouvez considérer comme la longueur de notre pas. Vous voulez utiliser un taux d'apprentissage assez grand pour vous déplacer le long du chemin mais assez petit pour ne pas dépasser une avenue potentielle pour un meilleur résultat.

Une sortie réussie doit imprimer des lignes dans ce format, bien que les numéros de perte ne soient pas exactement les mêmes à chaque exécution.

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
    

## Tâche 6 : Sauvegarder le modèle et vérifier l'exactitude des tests

    PATH = './cifar_net.pth'
    torch.save(net.state_dict(), PATH)
    

Ces lignes de code enregistrent le modèle après avoir terminé la boucle d'entraînement vers un fichier. Cette méthode est également utile pour créer des points de reprise tout au long de l'entraînement du modèle. Par exemple, nous chargerons le modèle à partir du fichier enregistré ci-dessus.

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
    

Il est important de tester le modèle afin de déterminer la précision mesurée. Indique si le modèle fournit des performances suffisantes pour un cas d'emploi donné. Nous avons seulement entraîné le modèle pour deux époques dans la boucle d'entraînement ci-dessus, de sorte que le modèle ne produira pas des résultats de performance impressionnants. Si vous augmentez le nombre de périodes jusqu'à ce que la perte cesse de diminuer, vous constaterez que les performances du modèle s'améliorent. Ce processus peut être exécuté périodiquement pendant la formation pour un suivi détaillé, bien qu'il ne soit pas répété trop souvent ou qu'il allonge le temps de formation.

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
    

Pour cet exemple, nous pouvons plonger un peu plus profondément que la précision globale. Le code ci-dessus affiche la précision de chaque catégorie et peut fournir des informations sur l'endroit où le modèle peut être confondu.

Exemple de code complet

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
    

Cela couvre les principaux points de formation des modèles de vision informatique de deep learning. L'étape suivante consiste à acceller la charge de travail de calcul avec un GPU.

Vous pouvez passer à l'exercice suivant.

## Accusés de réception

*   **Auteur** - Justin Blau, architecte senior de solutions, Big Compute
*   **Dernière mise à jour le/la date** - Justin Blau, Big Compute, octobre 2020