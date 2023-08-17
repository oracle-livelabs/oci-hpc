# Présentation

Mettre en œuvre une formation de classification d'images est le "Hello World" de la vision informatique. Bien qu'il existe de nombreux articles de blog sur le sujet, la plupart d'entre eux présentent la solution sans expliquer correctement l'API de bibliothèque, les principaux sous-jacents, ou les deux. Le guide [Formation d'un classificateur](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) d'PyTorch est un excellent point de départ, mais certains détails du code sont expliqués de manière trop rare pour un lecteur nouveau sur le terrain. Cet atelier s'appuie sur ce tutoriel comme une base avec des explications ligne par ligne du code pour faciliter un parcours d'apprentissage plus clair. Ce guide vous explique comment entraîner un modèle à l'aide de CPU, puis comment mettre à jour le code pour utiliser les ressources GPU. Les instances de GPU ne sont pas disponibles dans le niveau libre.

Durée estimée du laboratoire : 60 minutes

### À propos de Tensors

Pour le meilleur ou pour le pire, les représentations visuelles aident à contextualiser la terminologie, les structures de données et les algorithmes en informatique. Pour les tenseurs, [KDnuggets fournit une explication claire](https://www.kdnuggets.com/2018/05/wtf-tensor.html). Lorsque vous recherchez une définition en ligne, vous pouvez rencontrer une certaine confusion entre la définition technique d'un tenseur et l'utilisation colloque. Techniquement, un vecteur est un tenseur en 1 dimension et une matrice est un tenseur en 2 dimensions, mais le plus souvent le tenseur fait référence à un récipient en n dimensions qui est plus grand qu'un vecteur et une matrice.

![Représentation visuelle de dizaines de tailles différentes](images/tensor.png)

Ce terme est important à comprendre, car les dizaines sont au cœur de la création d'algorithmes d'intelligence artificielle, et ils sous-tendent les discussions sur l'optimisation des ressources de calcul. Un sujet de préoccupation commun lors de l'entraînement d'un modèle est l'utilisation des GPU, car NVIDIA [Turing Tensor Cores](https://www.nvidia.com/en-us/data-center/tensor-cores/) est capable d'une accélération de calcul significative, mais peut prendre un certain réglage. Heureusement, PyTorch contient un chargeur de données qui prend la responsabilité de transmettre des dizaines au GPU.

### Objectifs

Sujets traités dans cet atelier :

*   Tensors
*   DataLoaders
*   Réseaux neuronaux révolutionnaires (CNN)
*   Fonction de perte et optimiseur
*   Entraînement du modèle
*   Accélération GPU ;

### Prérequis

Cet exercice suppose que vous avez :

*   Un compte de niveau gratuit ou de cloud payant Oracle

## Accusés de réception

*   **Auteur** - Justin Blau, architecte senior de solutions, Big Compute
*   **Dernière mise à jour le/la date** - Justin Blau, Big Compute, octobre 2020