# Présentation

Vous êtes-vous déjà trouvé dans une situation où vous avez un parapluie et pas de pluie ou tard la nuit dans un parking sans brosse à neige parce que vous avez reçu ce que vous considérez comme de la désinformation ? Êtes-vous fatigué de votre homme du temps local en prédisant le temps incorrectement et vous voulez faire quelque chose à ce sujet ? Eh bien, il s'agit d'un laboratoire dans lequel vous pouvez trouver de la valeur. Aujourd'hui, nous allons vous expliquer et vous guider dans la configuration et l'exécution de WRF sur l'infrastructure OCI. Je vais également fournir un script uniquement si vous voulez exécuter WRF sans apprendre à le configurer.

Durée estimée du laboratoire : 90 minutes

### A propos de WRF

WRF est l'abréviation de The Weather Research and Forecasting Model. Il s'agit d'un système de prédiction météorologique numérique mesoscal conçu à la fois pour la recherche atmosphérique et les applications de prévision opérationnelle. WRF existe aujourd'hui à un partenariat qui a débuté à la fin des années 90 entre le Centre national de recherche atmosphérique (NCAR), l'Administration nationale océanique et atmosphérique (représentée par les Centres nationaux de prédiction environnementale (NCEP) et le Laboratoire de recherche sur le système terrestre), l'Armée de l'air américaine, le Laboratoire de recherche navale, l'Université d'Oklahoma et l'Administration fédérale de l'aviation (FAA). WRF est open source et est disponible sur github. WRF permet aux chercheurs de simuler la météo en utilisant soit des données réelles qui ont été collectées via des observations et des analyses, soit des conditions atmosphériques idéalisées. WRF fournit une plate-forme de prévision opérationnelle, où de nombreux contributeurs ont contribué à faire des progrès en physique, numérique et assimilation des données. WRF est actuellement utilisé en service dans le NCEP et d'autres centres de prévision à l'international, ce qui est une assez grande affaire.

### Objectifs

Sujets traités dans cet atelier :

*   Téléchargement des dépendances et WRF
*   Compilation des bibliothèques pour WRF/WPS
*   Compiler WRF et WPS
*   Création d'un domaine WRF avec Geogrid
*   Télécharger les données GFS et exécuter Ungrib et Metgrid
*   Exécuter WRF avec des données réelles

### Prérequis

Cet exercice suppose que vous avez :

*   Un compte de niveau gratuit ou de cloud payant Oracle

## Accusés de réception

*   **Auteur** - Brian Bennett, ingénieur solutions, Big Compute
*   **Dernière mise à jour le/la date** - Brian Bennett, Big Compute, décembre 2020