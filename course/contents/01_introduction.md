---
title: Introduction

---


# Objectifs du cours 

L'objectif du cours est de construire une boîte à outils pour **modéliser l'incertitude et les phénomènes aléatoires**. Le cours introduit des notions de modélisation stochastique (**mouvement brownien, **équations différentielles stochastiques) employées pour **modéliser et prévoir des phénomènes
chaotiques** (prévision du temps, modélisation d'un cours de bourse), et des notions de filtrage stochastiques pour assimiler des observation sur un modèle chaotique. Ces techniques (filtre de Kalman, filtres particulaires, modèles de diffusion) sont utilisées en ingénierie pour
estimer d'une part les erreurs d'observation et d'autre part l'incertitude sur la trajectoire du modèle.

Ces méthodes de modélisation sont clés pour représenter d'une part des phénomènes chaotiques, et d'autre part, rendre la modélisation robuste par rapport aux erreurs introduites (erreurs d'observation, de discrétisation du modèle, etc).

**Applications en ingénierie et science des données** : 
  - Assimiler des données bruitées _(ex : capteurs de vitesse, température, etc.)_.
  - Modéliser des phénomènes chaotiques _(ex : dynamique atmosphérique)_.
  - Contrôler et optimiser des systèmes dont la dynamique est inconnue _(boîte noire)_.

  
**Applications en Météo et sciences du climat** :
  - Estimer les variances d'ébauche (état initial de la prévision) et d'observation.
  - Construire les modèles de prévision d'ensemble (estimation de la dispersion des trajectoires).

## Démystifions l'aspect stochastique !

Dans la mesure où les systèmes déterministes (physiques) modélisent la trajectoire moyenne d'un phénomène, nous cherchons également à modéliser sa dispersion. Ici, nous nous attacherons à modéliser l'évolution de la **moyenne** et de la (co)-**variance** du phénomène.

:::{note} Modélisation des moments d'ordres supérieurs
Il existent des méthodes pour modéliser les moments d'ordre supérieur de la loi, notamment le _skew_, coefficient d'assymétrie, et le _kurtosis_, coefficient d'applatissement d'une distribution.
:::

Ici, nous présenterons des processus gaussiens : à chaque pas de temps, le processus est modélisé par la moyenne et la variance d'une distribution gaussienne. Nous nous appuyerons en particulier sur la **Loi des Grands 
Nombres** et le **Théorème Central Limite** pour étudier la vitesse de convergence de l'erreur des filtres d'assimilation.


Historiquement, la modélisation stochastique apparaît à la fin du $XIX^e$ siècle avec la description de 
la **marche aléatoire** et du **mouvement brownien** (ou processus de Wiener), principalement pour modéliser des processus physiques (ex : mouvement d'une particule dans un champ de forces). Elles sont 
formalisées dans le courant du $XX^e$ siècle par les **équations différentielles stochastiques**, **l'intégrale d'Ito**. Leur utilisation en ingénierie prend son essort dès la seconde partie du $XX^e$ siècle, avec l'emploi de simulations numériques pour des domaines tels que les géosciences, la météorologie, le nucléaire, la biologie, la recherche pétrolière et la finance. 


## Quelques exemples d'applications

Les exemples d'applications présentent l'utilisation d'un modèle stochastique (évolution temporelle 
représentée sous la forme d'une distribution de probabilité à chaque pas de temps) et son échantillonnage à partir de données
réelles (mesures physiques, graphe web, etc.).

### Filtre de Kalman

Le **filtre de Kalman** utilise au mieux les incertitudes connues sur un système (bruit d'état, et bruit de mesure) pour **estimer la trajectoire d'un système avec le minimum d'incertitude**. La méthode est introduite par Rudolf Kalman (1960) pour [filrer des systèmes linéaires](doi:10.1109/9780470544334.ch9) et mise en oeuvre pour la première fois dans l'ordinateur de navigation des programmes Apollo.

Ses applications notoires sont :
- Les systèmes de positionnement par satellite,
- La cartographie et localisation simultanées (SLAM),
- Les pilotes automatiques,
- Assimilation de données pour la Météo.


### Chaînes de Markov

Les chaînes de Markov représentent les probabilités de transition entre les différents états d'un systèmes. Sur une trajectoire modélisée par chaînes de Markov, l'état présent dépend uniquement de l'état précédent, ce qui en fait une modélisation simple et économique (en termes de stockage). Des estimateurs de Monte-Carlo (échantillonage de trajectoires) peuvent être construits pour connaître
la dispersion d'un système.

- Recherche de pages web (Page Rank) : classement des pages webs par leur occurences sur une marche aléatoire. Vous trouverez ci-joint [la publication](https://doi.org/10.1016/S0169-7552(98)00110-X) et [le brevet](https://patents.google.com/patent/US6285999B1/en) originels de Google.

- Estimation des probabilités d'occurence d'évènements extrêmes dans un modèle de climat. Ici, la marche aléatoire sur des séries temporelles de pluies permet de comparer une référence historique (ERA5) et les données projetées par un modèle de climat.

:::{note} Application en climatologie
Les estimateurs MCMC sont utilisé en climatologie, pour mesurer les changements de régime de temps d'une projection climatique par rapport à une série de données historiques : [](https://doi.org/10.5194/npg-23-375-2016)
:::

### Régression par Processus Gaussiens

Dans le cadre des régressions par processus gaussiens, chaque point de l'espace (continu) est représenté par une gaussienne. Le processus gaussiens modélise la corrélation entre 2 points proches, 
par une fonction de covariance : **à priori, 2 points proches sont plus fortement corrélés**.

Les régressions par processus gaussiens permettent, de proche en proche, d'explorer un système dont la dynamique est inconnue et les réalisations coûteuses ou difficiles à obtenir. 

Ses applications notoires sont la résolution de problèmes inverses coûteux :
- Contrôle de bras robotiques,
- Cartographie des sols : recherche pétrolière, géophysique
- Réglage des hyperparamètres d'un réseau de neurone
- Réglage de procédés industriels par plans d'expériences


**Ajustement d'un processus gaussien**

![processus_gaussien](../img/gaussian_processes.png)


# Applications en Météorologie

## Modélisation stochastique et prévision d'ensemble

## Filtrage stochastique et assimilation de données

