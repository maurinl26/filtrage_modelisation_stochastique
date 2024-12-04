---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Introduction
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# take snapshot for each slide in the overview
overviewSnapshots: true
---
# Introduction


Loïc Maurin - _loic.maurin@meteo.fr_

---
# Objectif du cours 

**Objectif : construire une boîte à outils pour modéliser l'incertitude et les phénomènes aléatoires**

**Applications en ingénierie et science des données** : 
  - Assimiler des données bruitées (ex : capteurs de vitesse, température, etc.)
  - Modéliser des phénomènes chaotiques (ex : dynamique atmosphérique)
  - Contrôler et optimiser des systèmes dont la dynamique est inconnue (boîte noire)

  
**Applications en Météo et sciences du climat** :
  - Estimer les variances d'ébauche, à l'assimilation de données d'observations,
  - Construction des ensemblistes


---
# Plan du cours 

- Introduction
- Filtre de Kalman
  - TP : Application au filtrage d'un oscillateur harmonique
- Chaînes et processus de Markov
  - TP : Markov Chains Monte-Carlo avec Page-Rank
- (Un peu de) théorie sur les processus stochastiques et Ensemble Kalman Filter
  - TP : Assimilation de mesures de température et de salinité _in situ_ pour un modèle d'océan 
- Régression par Processus Gaussiens et Optimisation Bayésienne
  - TP : modèle de courant de surface par rapport aux données de bouées 
  - TP : optimisation des hyper-paramètres d'un réseau de neurone
- Devoir 


---
# Démystifions l'aspect stochastique !

  - Dans ce cours, nous nous intéresserons à modéliser la moyenne et la (co)-variance des processus 

    - _en sachant que les méthodes peuvent être étendues aux moments d'ordres supérieurs_


  - Ici, Stochastique = Gaussien (dans la mesure du possible) 
    - _on modélisera les processus par rapport à la loi normale, en tirant partie du Théorème Centrale Limite_, 
    - _en gardant à l'esprit d'autres lois peuvent supporter des modèles (ex: processus de poisson)_

---
# Processus stochastiques - Applications


**Notions abordées** :
- Marche aléatoire et Mouvement brownien
- Régression par processus gaussiens
- Processus de Markov : Transition d'états sans mémoire


**Modèles et techniques développés** :
- Optimisation Bayésienne : échantillonage sur un processus gaussien
- Echantillonage par méthodes Monte-Carlo : Markov Chain Monte-Carlo
- Filtre Kalman : cas particulier de processus gaussien



---
## Filtrage Kalman
Quelques exemples d'applications

**Utiliser au mieux la dynamique d'un système pour recaler ses observations** 

- Localisation / Recalage GPS
- Filtrage de données robotiques
- Assimilation de données pour la Météo 
  (Ensemble Kalman Filter - EnKF)



---
## Markov Chain Monte-Carlo (MCMC)

**Echantillonner et estimer les probabilités de transitions entre états d'un système**

- Recherche de pages web (Page Rank)
  - Marche aléatoire sur le graphe des pages webs

- Estimation des récurrences et probabilités d'évènements extrêmes dans un modèle de climat : 
  - Marche aléatoire sur des séries temporelles de pluies : comparaison de données historiques et projetées par le modèle de climat.


([Sedlmeier, K., Mieruch, S., Schädler, G., and Kottmeier, C.: Compound extremes in a changing climate – a Markov chain approach, Nonlin. Processes Geophys., 23, 375–390, https://doi.org/10.5194/npg-23-375-2016, 2016.](https://doi.org/10.5194/npg-23-375-2016))


---
## Régression par Processus Gaussiens

**Modéliser les incertitudes lors de l'exploration d'un champ / d'un espace de paramètres**

- Résolution de problèmes inverses :
  - Contrôle de bras robotiques,
  - Cartographie des sols : recherche pétrolière, géophysique

- Optimisation
  - Tuning des hyperparamètres d'un réseau de neurone
  - Réglage de procédés industriels par plans d'expérience



---
layout: image
image: ./img/gaussian_processes.png
backgroundSize:  50%

**Ajustement d'un processus gaussien**

---
# Rappels de Probabilités et Statistiques
Pour être à l'aise sur les notions du cours

- Loi Normale
- Loi jointe, Loi marginale
- Théorème Centrale Limite et Loi des Grand Nombres
- Théorème de Bayes
- Estimateur du Maximum Vraisemblance

## Lois usuelles


**Loi uniforme** : $X$ suit une **loi uniforme** sur $[a,b] \in \mathbb{R}$ si elle admet la densité de probabilité $f: \mathbb{R} \mapsto \mathbb{R}$

$$ 
\forall x \in \mathbb{R}, f(x) = \begin{array}{rcl}
\frac{1}{b-a} & si\,  x \in [a, b] \\
        0 & sinon
\end{array} $$

**Loi exponentielle** : $X$ suit une **loi exponentielle** $\mathcal{E}(\lambda)$ ($\lambda \gt 0$) si elle admet la densité de 
probabilité $f: \mathbb{R} \mapsto \mathbb{R}$ 

$$\forall x \in \mathbb{R}, f(x) = \begin{array}{rcl} 
\lambda e^{-\lambda x} & si \, x \geq 0 \\
0  & sinon
\end{array} $$

On a alors $\mathbb{E}[X] = 1/\lambda, \mathbb{V}[X] = 1/\lambda^2$


**Loi Normale** : $X$ suit une **loi normale** $\mathcal{N}(m, \sigma^2), ((m, \sigma) \in \mathbb{R}\,  \times  \mathbb{R_+})$

$$ \forall x \in \mathbb{R}, f(x) = \frac{1}{\sqrt{2\pi} \sigma} exp(-\frac{(x - m)^2}{2\sigma^2})$$

On a alors $\mathbb{E}[X] = m, \mathbb{V}[X] = \sigma^2$

**Loi Gamma** : $X$ suit une **loi gamma** $\gamma(p, \lambda), (p \gt 0, \lambda \gt 0)$ si sa densité est 

$$ \forall x \in \mathbb{R}, f(x) = \begin{array}{rcl} 
\frac{\lambda}{\Gamma(p)}(\lambda x)^{p-1}e^{-\lambda x} & si & x \geq 0 \\
0  & sinon
\end{array} $$

On a alors $\mathbb{E}[X] = p/\lambda$, $\mathbb{V}[X] = p/\lambda^2$


---
## Exemple d'application : la loi gamma en microphysique

En microphysique (modélisation des nuages et interactions entre hydrométéores), la distribution des gouttes
par rapport à leur diamètre est donnée par une loi exponentielle (Loi de Marshall-Palmer).

**Loi de Marshall-Palmer**
$$ N(D) dD = N_0 e^{-\lambda D} dD, où \, f(D) = e^{-\lambda D}$$

où la densité de probabilité est $f(D) = e^{-\lambda D}$.


On peut alors lier les moments de la distribution avec les caractéristiques physiques des gouttes :

- **Concentration de goutelettes** $N_c$ et **moment d'ordre 0** : $N = \int_0^{\infty} f(D) dD$ 
- **Contenu en eau** $q_c$ et **moment d'ordre 3** : $L = \frac{\pi \rho_w}{6} \int_0^{\infty} D^3 f(D) dD$ _(lié au volume des gouttes)_
- **Réflectivité radar** $\Sigma_c$ et **moment d'ordre 6** : $R \propto \int_0^{\infty} D^6 f(D) dD$ _(lié à la diffusion de Rayleigh)_

En modélisation microphysique, "le jeu" est de choisir le nombre de moments à modéliser (**fermeture**),
la discrétisation sur l'espace des diamètres (**échantillonage**), pour représenter les interactions possibles
entre les gouttes (de pluie, de neige, etc.).

_Remarque : Une densité de loi Gamma (plus générique que la loi exponentielle) est choisie sur certains 
schémas microphysiques._

---
## Loi forte des Grands Nombres

**Théorème** Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **indépendantes** et **identiquement distribuées (i.i.d.)** et
définies sur le même espace de probabilité $(\Omega, \mathcal{F}, \mathbf{P})$.

En posant $\mu = \mathbb{E}[X_i]$ et $\sigma^2 = \mathbb{V}(X_i) < \infty$.

La variable aléatoire $S_n = \sum_{i=1}^n X_i$ vérifie
$$\lim\limits_{n \rightarrow \infty} \frac{S_n}{n} = \mu,  p.s.$$ 

**p.s.** : presque sûr = "à une infinité dénombrable de points près"

**Application** Méthode de Monte-Carlo : calcul d'une intégrale par des tirages de variables aléatoires.

Pour $U$ variable aléatoire uniforme sur $[0,1]$, $\lim\limits_{n \rightarrow \infty} \sum f(U_i) = \mathbb{E}[f(U)] = \int_0^1 f(x) dx$

---
## Théorème Centrale Limite


**Théorème** Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **i.i.d.**

On note $\mu = \mathbb{E}[X]$ et $\sigma^2 = \mathbb{V}(X)$. On suppose $0 \lt \sigma^2 \lt \infty$.

En posant $S_n = \sum_{i=1}^n X_i$,

$$Y_n = \frac{S_n - n\mu}{\sigma \sqrt(n)} \underset{n \rightarrow \infty}{\longrightarrow} Y \sim \mathcal{N}(0, 1)$$

**Remarque** Il s'agit d'une convergence en loi : la suite des lois $Y_n$ tend vers $Y$ qui est aussi une loi de probabilité.

**Application** Le Thèorème Centrale Limite donne des indications sur les vitesses de convergence vers la loi assymptotique.

---
## LGN vs TCL

**Loi des Grand Nombres** : convergence des moyennes empiriques vers la moyenne de la loi des v.a.


**Théorème Centrale Limite** : convergence asymptotique de la loi sur la moyenne empirique (donne des indications sur une loi limite).

---
## Modes de convergences

- Convergence presque sûre (**p.s.**) : $$P(\{\omega \in \Omega: \lim\limits_{n \rightarrow \infty\}} X_n(\omega) = X(\omega)) = 1$$
- Convergence dans **$\mathrm{L}^P$** : $$\lim\limits_{n \rightarrow \infty} \mathbb{E}[|X_n - X|^p] = 0$$
- Convergence en probabilité **P**: $$\lim\limits_{n \rightarrow \infty} P(|X_n - X| \gt \epsilon) = 0$$

Remarque :
- Convergence **$\mathrm{L}^p$** $\Rightarrow$ Convergence **P**, 

- Convergence **p.s.** $\Rightarrow$ Convergence **P**.


---
## Estimation bayésienne

En estimation bayésienne, on cherche à construire un estimateur (bayésien), c'est à dire une distribution conditionnée aux données d'apprentissage :

$$P(\theta | data ) = \frac{P(\theta) \cdot P(data | \theta)}{P(data)}$$

où :
- $P(\theta)$ est la **distribution à priori** des paramètres du modèle, ou **prior**
- $P(data| \theta)$ est la **vraisemblance du modèle** par rapport aux données, ou **likelihood** en anglais. 
- $P(\theta | data)$ est la **distribution à posteriori** des paramètres du modèle, ou **posterior**
- $P(data)$ est la loi ou distribtion marginale des données.

_Remarque : on utilise souvent le logarithme de ces grandeurs. Ainsi, on ne s'étonnera pas de minimiser la **Negative Log Likelihood** ou (NLL) en Deep Learning, 
ce qui revient exactement à **maximiser la vraisemblance** du modèle._

$\rightarrow$ Le problème principal : estimer **la loi marginale** $P(data)$

**En pratique** : on cherche à éviter le calcul de la loi marginale des données.

- Cas discret :

$$P(data) = \sum_{\theta} P(data | \theta) \times P(\theta)$$

- Cas continu :

$$P(data) = \int_{\theta} P(data | \theta) \times P(\theta) d\theta$$

Dans le cas continu, calculer $P(data)$ peut devenir intractable.

---
