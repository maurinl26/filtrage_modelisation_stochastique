---
title: Introduction

---


# Objectifs du cours 

**Construire une boîte à outils pour modéliser l'incertitude et les phénomènes aléatoires**

**Applications en ingénierie et science des données** : 
  - Assimiler des données bruitées _(ex : capteurs de vitesse, température, etc.)_
  - Modéliser des phénomènes chaotiques _(ex : dynamique atmosphérique)_
  - Contrôler et optimiser des systèmes dont la dynamique est inconnue (boîte noire)

  
**Applications en Météo et sciences du climat** :
  - Estimer les variances d'ébauche, à l'assimilation de données d'observations
  - Construction des ensemblistes


## Démystifions l'aspect stochastique !

  - Dans ce cours, nous nous intéresserons à modéliser la moyenne et la (co)-variance des processus 

    - _en sachant que les méthodes peuvent être étendues aux moments d'ordres supérieurs_


  - Ici, Stochastique = Gaussien (dans la mesure du possible) 
    - _on modélisera les processus par rapport à la loi normale, en tirant partie du Théorème Central Limite_, 
    - _en gardant à l'esprit d'autres lois peuvent supporter des modèles (ex: processus de poisson)_


## Quelques exemples d'applications

**Notions abordées** :
- Marche aléatoire et Mouvement brownien
- Régression par processus gaussiens
- Processus de Markov : Transition d'états sans mémoire


**Modèles et techniques développés** :
- Optimisation Bayésienne : échantillonage sur un processus gaussien
- Echantillonage par méthodes Monte-Carlo : Markov Chain Monte-Carlo
- Filtre Kalman : cas particulier de processus gaussien


### Filtrage Kalman
Quelques exemples d'applications

**Utiliser au mieux la dynamique d'un système pour recaler ses observations** 

- Localisation / Recalage GPS
- Filtrage de données robotiques
- Assimilation de données pour la Météo 
  (Ensemble Kalman Filter - EnKF)


### Markov Chain Monte-Carlo (MCMC)

**Echantillonner et estimer les probabilités de transitions entre états d'un système**

- Recherche de pages web (Page Rank)
  - Marche aléatoire sur le graphe des pages webs

- Estimation des récurrences et probabilités d'évènements extrêmes dans un modèle de climat : 
  - Marche aléatoire sur des séries temporelles de pluies : comparaison de données historiques et projetées par le modèle de climat.

:::{note} Application en climatologie
Les estimateurs MCMC sont utilisé en climatologie, pour mesurer les changements de régime de temps d'une projection climatique par rapport à une série de données historiques : [](https://doi.org/10.5194/npg-23-375-2016)
:::

### Régression par Processus Gaussiens

**Modéliser les incertitudes lors de l'exploration d'un champ / d'un espace de paramètres**

- Résolution de problèmes inverses :
  - Contrôle de bras robotiques,
  - Cartographie des sols : recherche pétrolière, géophysique

- Optimisation
  - Tuning des hyperparamètres d'un réseau de neurone
  - Réglage de procédés industriels par plans d'expériences


**Ajustement d'un processus gaussien**

![ajustement d'un processus gaussien](../img/gaussian_processes.png)


# Applications en Météorologie

## Modélisation stochastique et prévision d'ensemble

## Filtrage stochastique et assimilation de données

