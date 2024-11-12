---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Markov Chains Monte Carlo
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

# Filtrage et Modélisation Stochastique


Loïc Maurin - _loic.maurin@meteo.fr_

---
layout: section

---

# 4 - Markov Chain Monte Carlo

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---
layout: section

---

# Aux cours précédents
Processus stochastiques et Filtre de Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Au cours précédent
Processus stochastiques

**Processus stochastique** : famille de variables aléatoires $(X_t)_{t \in \mathcal{T}}$.

**Temps discret** : $t \in \mathbb{N}$, le processus se modélise avec des **probabilités de transition**.

**Temps continu** : $t \in \mathbb{R}$, le processus se modélise avec des **densités de probabilités** ou **taux de transitions** notés
$W(n,m)$  pour la transition entre de l'état $n$ vers l'état $m$.

**Equation Maîtresse** : équation différentielle qui représente l'évolution temporelle de la densité de probabilité. On a vu qu'elle 
s'établissait de manière analogue à un bilan physique. 

**Relations sur la moyenne et la variance** : à partir de l'équation maîtresse, on peut déduire l'évolution temporelle
de la moyenne et de la variance du processus. Pour rappel,

$$\frac{d\braket{n}}{dt} = \braket{W^+(n) - W^-(n)}, 
\frac{dV}{dt} = 2 \braket{(n - \braket{n})(W^+(n) - W^-(n))} + \braket{W^+(n) + W^-(n)}$$

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Au cours précédent
Filtre de Kalman

**Construction de l'Estimateur** : $P(x_n | z_n) \sim \mathcal{N}(\hat{x}_n, P_n)$, par récurrence.

Estimateur **assymptotiquement sans biais** sur lequel **la covariance de l'erreur est minimisée**, grâce à la relation entre 
le gain du filtre $K_n$ et la matrice de covariance $P_n$.  


**Hypothèses de modélisation** : on suppose un **bruit d'état** $\phi \sim \mathcal{N}(0, \Phi)$ sur la dynamique du système et un **bruit de mesure** $\psi \sim \mathcal{N}(0, \Psi)$ sur l'observation du système.

**Influence du gain du filtre K** : si $K \rightarrow 0$, l'estimateur ne repose que sur la dynamique, et si $K \rightarrow \infty$, l'estimateur repose uniquement sur les mesures.

**Linéarité** : les propriétés du Filtre de Kalman sont établies pour un système linéaire perturbé par des bruits blancs gaussiens. Dans le cas d'un système non-linéaire, on introduit le **Tangeant-Linéaire** et l'**Adjoint** du système pour construire un **Filtre de Kalman Etendu**. 

_Remarque le filtre Kalman étendu ne minimise pas formellement l'erreur d'estimation_.


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Au cours précédent
Vers l'estimation de Chaînes de Markov par méthodes de Monte-Carlo (MCMC)


**Filtre de Kalman** : la fermeture du système d'équations du filtre s'effectue grâce aux propriétés gaussiennes des bruits d'état et de mesure. On peut ainsi se limiter à construire **l'évolution de la moyenne et de la variance** de l'estimateur.

$\rightarrow$ Comment construire un estimateur quand on ne connaît plus la structure du bruit ?



<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>


---

# Introduction
Chaînes de Markov et  Méthodes de Monte-Carlo

$\rightarrow$ Comment construire un estimateur quand on ne connaît plus la structure du bruit ?

**Méthodes de Monte-Carlo** : estimation d'une densité de probabilité par tirages. 

_Remarque : Une fois que l'on a accès à une estimation de la densité de probabilité, nous pouvons déduire les moments de la distribution, notamment la moyenne et la variance._

**Chaînes de Markov** : processus stochastique dont la transition entre 2 états $x_{n-1} \rightarrow x_{n}$ ne dépend que de l'état précédent $x_{n-1}$.

_Remarque : Le filtre Kalman entre dans le cadre des chaînes de Markov_.


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Remarque introductive
Markov Chain Monte Carlo

Ce cours est indépendant de celui sur le Filtrage Kalman.

$\rightarrow$ Le Filtre de Kalman et les Chaînes de Markov sont 2 représentants de Processus Stochastiques

$\rightarrow$ Les 2 sont utilisés en **Assimilation de données** pour établir le filtre de Kalman d'ensemble (EnKF), avec :

1. **Une composante de filtrage** :


    _Comment recaler un modèle par rapport aux observations / mesures_ ?

2. **Une composante d'échantillonage** :

    _Comment estimer la dispersion du modèle_ ?


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Markov Chain Monte-Carlo
MCMC

$\rightarrow$ Pourquoi utiliser les méthodes de Monte-Carlo sur des chaînes de Markov ?

- Apprendre une distribution, un processus stochastique par échantillonage aléatoire. 
- Très utile sur des systèmes "en boîte noire" pour lesquels il existe des réalisations (ou des simulations).
- Efficace sur des problèmes de grandes dimensions _(dont la combinatoire est trop grande pour des méthodes classiques)_.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Markov Chain Monte-Carlo
Historique et applications

- Aiguilles de Buffon (1733) : Estimation de $\pi$ par le compte de Buffon.
- Projet Manhattan (1940) :  utilisation des premiers ordinateurs pour la simulation de processus physiques, lors de la construction de la bombe atomique.

$\rightarrow$ Les Méthodes de Monte-Carlo se développent avec l'augmentation des puissances de calcul, avec, en particulier, quelques applications remarquables : 


- PageRank : estimation du poids d'une page web par échantillonage sur ses liens sortants,

- AlphaGo : (Monte-Carlo Tree Search), estimation du meilleur coup par échantillonage des différentes trajectoires possibles.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>


---
layout: section

---

# Méthodes de Monte-Carlo

---

# Description de la méthode

Supposons que l'on veuille calculer une quantité $I$.

- On met $I$ sous la forme $I = \mathbb{E}[X]$ avec $X$ un variable aléatoire.
- On effectue un tirage de variables aléatoires $X_1, ..., X_N$ **indépendantes et identiquement distribuées**

$I$ peut être approché par 
$$I \sim \frac{1}{N} \sum_i X_i$$

Sous réserve d'application de la **Loi des Grands Nombres**, c'est-à-dire N suffisament grand.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Loi forte des Grands Nombres
Théorèmes fondamentaux


**Théorème** Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **indépendantes** et **identiquement distribuées (i.i.d.)** et
définies sur le même espace de probabilité $(\Omega, \mathcal{F}, \mathbf{P})$.

En posant $\mu = \mathbb{E}[X_i]$ et $\sigma^2 = \mathbb{V}(X_i) < \infty$.

La variable aléatoire $S_n = \sum_{i=1}^n X_i$ vérifie
$$\lim\limits_{n \rightarrow \infty} \frac{S_n}{n} = \mu,  p.s.$$ 

**p.s.** : presque sûr = "à une infinité dénombrable de points près"

**Application** Méthode de Monte-Carlo : calcul d'une intégrale par des tirages de variables aléatoires.

Pour $U$ variable aléatoire uniforme sur $[0,1]$, $\lim\limits_{n \rightarrow \infty} \sum f(U_i) = \mathbb{E}[f(U)] = \int_0^1 f(x) dx$

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Théorème Centrale Limite
Théorèmes fondamentaux


**Théorème** Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **i.i.d.**

On note $\mu = \mathbb{E}[X]$ et $\sigma^2 = \mathbb{V}(X)$. On suppose $0 \lt \sigma^2 \lt \infty$.

En posant $S_n = \sum_{i=1}^n X_i$,

$$Y_n = \frac{S_n - n\mu}{\sigma \sqrt(n)} \underset{n \rightarrow \infty}{\longrightarrow} Y \sim \mathcal{N}(0, 1)$$

**Remarque** Il s'agit d'une convergence en loi : la suite des lois $Y_n$ tend vers $Y$ qui est aussi une loi de probabilité.

**Application** Le Thèorème Centrale Limite donne des indications sur les vitesses de convergence vers la loi assymptotique.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---
layout: section

---

# Chaînes de Markov

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Chaînes de Markov

**Définition** : Une chaîne de Markov homogène à valeurs dans $S$ de matrice de transition $P$ et de loi initial $\lambda$
est une famille de variables aléatoires $(X_n, n \in \mathbb{N})$ telle que :

  1. $\mathbb{P}(X_0 = i) = \lambda_i$
  2. $\mathbb{P}(X_{n+1} = i_{n+1} | X_n = i_n, ..., X_0 = i_0) = \mathbb{P}(X_{n+1} = i_{n+1} | X_n = i_n) = p_{i_n, j_n}$

$(X_n)_{n \in \mathbb{N}}$ est une chaîne de Markov de paramètres $(\lambda, P)$


**Caractérisation** : $(X_n)_{0 \leq n \leq N}$ est Markov $\lambda, P$ ssi pour tous $i_0, ..., i_N$

$$\mathbb{P}(X_0 = 0, ..., X_N = i_N) = \lambda(i_0) p_{i_0, i_1} p_{i_1, i_2} ... p_{i_{N-1},i_N}$$

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Chaînes de Markov
Conditionnements successifs et calcul


**Propriété** : Si $(X_n)_{0 \leq n \leq N}$ est Markov $(\lambda, P)$, alors, conditionnellement à $X_m = i$, le processus $(X_{m+n}, n \geq 0)$ est Markov  $(\delta_i, P)$ et est indépendant de $(X_0, ..., X_m)$.


On note $\delta_i$ la masse de Dirac en i, et $\mathbb{P}_i$ la loi d'une chaîne $(\delta_i, P)$. On dit que la chaîne est issue de i car $X_0 = i$ p.s.


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Chaînes de Markov
Conditionnements successifs et calcul

**Calcul algébrique**

On note une mesure positive $\lambda$ comme un vecteur ligne. $\lambda P$ est la mesure $(\lambda P)(j) = \sum_i \lambda(i) p_{i,j}$. On note $p_{i,j}^{(n)}$ l'élément $i,j$ de la matrice $P^n$. 

**Propriété** : Soit $(X_n)_{n \geq 0}$ Markov $(\lambda, P)$. Alors, pour tout n, la loi de $X_n$ est $\lambda P^n$, i.e.
$$
\mathbb{P}(X_n = j) = (\lambda P^n)(j)
$$

En particulier, pour $\lambda = \delta_i$ et tout $m \geq 0$, on obtient
$$
\mathbb{P}_i(X_n = j)= \mathbb{P}(X_{m+n} = j | X_m = i) = p_{i,j}^n
$$

---

# Etats transients et récurrents
Propriétés

**Définition** 
- On dit que $i$ est transient si $\sum_n p_{ii}^n \lt + \infty$ 
- On dit que $i$ est récurrent si $\sum_n p_{ii}^n \rightarrow + \infty$

On peut alors mettre la matrice de transition sous une forme canonique, avec les états absorbants au début et les états récurrents ensuite :

$$
P=
\left(
\begin{array}{c|c}
Q & R \\
\hline
0 & I
\end{array}
\right)
$$

où $Q^n \rightarrow 0$.

**Propriété** Pour une chaîne de Markov avec un état absorbant, la matrice $I - Q$ est inversible d'inverse $N = \sum_n Q^n$. $n_{ij}$ est le nombre moyen de 
fois où la chaîne est dans l'état $j$ si elle part de l'état $i$.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>


---

# Mesures invariantes
Propriétés

On dit que la mesure positive $\lambda$ est invariante (ou encore stationnaire) is $\lambda P = \lambda$.

**Proposition** Si $(X_n)_{n \in \mathbb{N}}$ est Markov $(\lambda, P)$ et $\lambda$ est invariante, alors la loi de $X_n$ est
constante et vaut $\lambda$. En outre, pour tout m, $(X_{m+n}, n \geq 0)$ est Markov $(\lambda, P)$.

En particulier, les limites des lois de $X_n$ sont automatiquement des probabilités invariantes sur un espace fini.

**Propriété** On suppose $I$ fini et que pour un $i_0 \in I$, on ait $\forall j, p_{i_0, j}^{(n)} \rightarrow \pi_j$.

Alors $\pi$ est une probabilité invariante.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Théorème ergodique
Propriétés

**Théorème** Soit $X$ une chaîne irréductible positive récurrente. Alros, pour toute fonction $f$ positive 
ou bornée, presque sûrement : 

$$\frac{1}{n} \sum_{k=1}^n f(X_k) \rightarrow \pi(f)$$


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Convergence vers l'équilibre
Propriétés

**Théorème** Soit une chaîne de Markov irréductible positive récurrente de probabilité invariante
$\pi$ et apériodique. Alors pour toute loi initiale

$$\mathbb{P}(X_n = i) \rightarrow \pi(i)$$

en particulier, pour tout i, $p_{ij}^{(n)} \rightarrow \pi(j)$.


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>




---

# Markov Chains Monte-Carlo
Calcul des probabilités dans une chaîne de Markov

$\rightarrow$ Comment estimer la distibution sur les états d'une chaîne de Markov ?

Etant donnée une chaîne de Markov, on note $D(s,t)$ la distribution des états sur $X_t$ sachant que $X_0 = s$.

_Remarque : On peut représenter $D(s,t)$ comme un vecteur pour lequel $D_i = P(X_t = s_i | X_0 = s)$._ 

**Méthodes de calcul**

- Calcul direct
- Simulation (Markov Chain Monte Carlo)

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Markov Chain Monte-Carlo
Calcul des probabilités dans une chaîne de Markov

$\rightarrow$ Comment estimer la distibution sur les états d'une chaîne de Markov ?

**Calcul direct** 

En prenant un vecteur d'état initial $D(0,s) = [0 ... 0, 1, 0 ... 0]$, $\forall t \geq 1$,

$$D(t,s) = D(t-1, s) P = D(0,s) P^t$$

**Coût numérique**

Le calcul de $P^t$ est d'une complexité $O(log(t))$, en utilisant un algorithme d'exponentiation rapide. (Calcul et stockage de $P^2, P^4, P^8, P^{16}, ..., P^{2 \lfloor log(t) \rfloor}$).

_Ce calcul est rapide tant que le nombre d'états est petit._

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Markov Chain Monte-Carlo
Calcul des probabilités dans une chaîne de Markov

$\rightarrow$ Comment estimer la distibution sur les états d'une chaîne de Markov ?

**Calcul par simulations - Markov Chain Monte-Carlo** 

On estime $D(t, s)$ par méthodes de Monte-Carlo. En prenant $X_0 = s$, et $\forall i = 1, ..., t$ on tire au sort les transitions vers l'état $X_i$ compte tenu de l'état $X_{i-1}$ et des probabilités de transition définies par $P$.

En répétant l'opération $k$ fois, on obtient $k$ échantillons de la distribution $D(s,t)$.

**Coût numérique** 

Le coût de l'estimation est de $k \cdot t \cdot update_T$, où $update_T$ est le coût d'un
tirage au sort.

_Pour un grand nombre d'états, Markov Chain Monte Carlo a un coût raisonnable pour estimer $D(t,s)$._

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Application au jeu de Go
Marko Chain Monte Carlo

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Application à la recherche Web - PageRank
Markov Chain Monte Carlo

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---



---

# Markov Chains Monte-Carlo
Algorithme de Metropolis-Hastings


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# A retenir 
Markov Chain Monte Carlo


---

# La semaine prochaine
Spoiler alert !

**TP** : Markov Chain Monte-Carlo 

$\rightarrow$ Apportez vos PC, TP sur Google Colab

---

# Liens du cours
Ressources utiles sur les Monte Carlo Markov Chains

[Compound extremes in a changing climate –
a Markov chain approach](https://npg.copernicus.org/articles/23/375/2016/npg-23-375-2016.pdf)

[Algorithme de Métropolis-Hastings](https://perso.lpsm.paris/~vlemaire/4ma074/cours/metropolis.html)

[Processus Stochastiques, Notes de cours, P. Carmona](https://philcarmona.github.io/extra/cecn4.pdf)

[CS168: The Modern Algorithmic Toolbox
Lecture #14: Markov Chain Monte Carlo](https://web.stanford.edu/class/cs168/l/l14.pdf)



---
layout: end

---

---
