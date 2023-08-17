# Présentation de l'atelier

## Ce que vous apprendrez dans cet atelier

Au cours de cet atelier, vous allez apprendre à exécuter l'application Deepfake [Faceswap](https://faceswap.dev/) sur une machine virtuelle de GPU sur Oracle Cloud Infrastructure. Nous aborderons toutes les étapes du provisionnement et de la préparation de l'instance afin d'utiliser les coeurs GPU pour exécuter le script Python (Faceswap GAN) pour l'extraction des faces, l'entraînement du GAN et la conversion du visage échangé dans une vidéo.

Durée estimée de l'atelier : 120 minutes (hors temps de formation du GAN)

## Ce qui est deepfake

Deepfake est un mot composé de "l'apprentissage profond"et de"fake" et représente un média généré par l'IA dans lequel une personne dans une image ou une vidéo existante est remplacée par quelqu'un d'autre ou modifiée avec d'autres caractéristiques externes (telles que l'âge, la coiffure, la couleur de la peau, la voix, etc.). L'acte de manipulation de vidéos et d'images a duré longtemps, mais tirer parti du machine learning et de l'IA pour produire des médias avec un fort potentiel de tromperie est nouveau. Il est presque impossible de distinguer les médias réels et faux avec l'œil nu.

## Comment le deepfake fonctionne-t-il ?

Les deepfakes sont basés sur des réseaux neuronaux artificiels qui reconnaissent des modèles (par exemple des visages, des voix) dans les données (photos ou vidéos de personnes). Une grande quantité de données est entrée dans des réseaux neuronaux qui sont formés pour reconnaître les modèles et les falsifier. Les réseaux neuronaux sont des algorithmes qui sont inspirés par le cerveau humain. Un neurone reçoit et transmet des signaux vers et depuis d'autres neurones à travers leurs synapses sur les axones et les dendrites, dans le but par exemple de contracter un muscle. Lorsqu'un signal se déplace d'A à B, il peut être transmis par des couches de neurones. De même, un algorithme de réseau neuronal reçoit des données d'entrée, les traite par l'intermédiaire d'une couche d'opérations mathématiques (matrice multiplications) équipée de différents poids, qui sont ajustés pendant l'entraînement, et génère finalement une sortie.

Pour exécuter des multiplications matricielles avec 10 milliards de paramètres en temps opportun, il faut une puissance de calcul adéquate. Il est plus rapide d'exécuter toutes les opérations simultanément les unes après les autres. Une CPU exécute un processus après un autre avec un petit nombre de threads, tandis qu'une GPU facilite le calcul parallèle avec un grand nombre de threads en même temps. La possibilité de traiter plusieurs calculs en même temps fait du GPU un meilleur choix pour entraîner des algorithmes de réseau neuronal. Les réseaux neuronaux utilisés pour les deepfakes sont appelés GAN (Generative Adversial Networks) où deux modèles de machine learning se font concurrence. Le premier modèle est appelé générateur qui lit les données réelles et génère des données fausses. Le deuxième modèle appelé le discriminateur est responsable de la détection des fausses données. Le générateur génère des données fausses jusqu'à ce que le discriminateur ne puisse plus les détecter. Plus le générateur dispose de données d'entraînement, plus il est facile pour lui de générer un creux crédible.

## Qu'est-ce que Faceswap ?

La plate-forme la plus connue et la plus répandue pour générer des deepfakes est [Faceswap](https://faceswap.dev/). Il s'agit d'une plate-forme open source basée sur Tensorflow, Keras et Python et peut être exécutée sur Windows, macOS et Linux. Une communauté active s'est formée, qui échange des informations sur Github et d'autres forums et partage des scripts et propose des solutions aux questions.

Vous pouvez exécuter vos propres charges de travail à l'aide d'Oracle Free Tier qui comprend l'offre Toujours gratuit et un essai gratuit de 30 jours. Dans le niveau Toujours gratuit, VM.Standard.E2.1. Micro peut être utilisé et pendant l'essai de 30 jours, toutes les instances Bare Metal et de machine virtuelle disponibles peuvent être testées. Si vous souhaitez tester l'offre GPU, vous pouvez mettre à niveau votre compte cloud Oracle vers PAYG.

## **Accusés de réception**

*   **Created By/Date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021
*   **Dernière mise à jour le/la date** - Maria Patelkou, architecte de solutions HPC, programme Proposition à la production d'Oracle, mars 2021