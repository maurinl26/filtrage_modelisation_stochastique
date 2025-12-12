---
title: Méthodes variationnelles

---

Nous évoluons dans le cadre des méthodes d'assimilation de données pour la Météo.

![cycle d'assimilation](../img/assimilation.png)

# Méthodes variationnelles

## 4D-Var

Les méthodes variationnelles cherchent à minimiser un foncitonnelle globale sur une fenêtre temporelle donnée :

$$
J(x_0) = \frac{1}{2}(x_0 - x_0^b)^T B^{-1} (x_0 -x_0^b)
+ \frac{1}{2} \sum_{i=0}^N [H_i(x_i) - y_i]^T
$$

Avec la matrice d'erreurs d'observations :

$$
R^{-1}_i [H_i(x_i) - y_i] = J^b + J^0
$$

# Méthodes ensemblistes

## Filtre particulaire

## Filtre Kalman d'ensemble


# Description formelle


On note $x_{1:L} = (x_1, , x_L) \in \mathbb{R}^{LxD}$ une trajectoire d'états (ou $L$ et $D$ sont respectivement la longueur de la trajectoire et la dimension du vecteur d'état). On note
$p(x_{i+1}|x_i)$ la dynamique de transition de l'état $x_i$ vers l'état $x_{i+1}$.

L'observation $y \in \mathbb{R}^M$ de la trajectoir $x_{1:L}$ suit un processus d'observation
$p(y|x_{1:L})$, formulé comme $y = \mathcal{A}(x_{1:L}) + \eta$. $\mathcal{A}$ est une fonction
(opérateur) d'observation et $\eta \in \mathbb{R}^M$ une erreur d'observation (ou de mesure).

Par des techniques d'Assimilation de Données, nous cherchons à inférer les trajectoires plausibles $x_{1:L}$ étant donné l'observation $y$, c'est à dire, d'estimer la trajectoire 
à posteriori :

$$
    p(x_{1:L}|y) = \frac{p(y|x_{1:L})}{p(y)} p(x_1) \prod_{i = 1}^{L-1} p(x_{i+1}|x_{i})
$$


:::{note} Liens avec l'estimation bayésienne et les chaînes de Markov

Le problème ci-dessus est effectivement formulé comme un problème d'estimation bayésienne où :

- $p(x_{1:L}|y)$ représente la vraisemblance : _(likelihood) en anglais_,
- $p(y|x_{1:L})$ la distribution empirique des données d'observations,
- $p(y)$ la loi marginale des données,
- $p(x_1) \prod_{i = 1}^{L - 1} p(x_{i+1}|x_{i})$ le modèle,

La trajectoire est effectivement représentée par une chaîne de Markov, avec :
- une distribution initiale, appelée **background** en météo : $p(x_1)$
- une fonction de transition (la dynamique discrète) : $p(x_{i+1}|x_{i})$

:::


# Références

