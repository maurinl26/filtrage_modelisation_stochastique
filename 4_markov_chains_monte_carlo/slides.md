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

# Méthodes de Monte-Carlo
Construction de l'estimateur

**Estimateur de Monte-Carlo** Pour une suite de variables aléatoires indépendantes, identiquement distribuées, $\{q_1, ..., q_N\}$ (N arbitrairement grand), et pour une foonction $f$ à valeurs réelles et mesurables, les estimateur de Monte-Carlo sont défini par les **moyennes d'ensemble** de $f$ :

$$\hat{f}_N^{MC} = \frac{1}{N} \sum_{n=1}^N f(q_n)$$

La moyenne de l'ensemble converge alors vers l'espérance de $f$ :

$$\lim\limits_{N \rightarrow \infty} \hat{f}_{N}^{MC} = \mathbb{E}_{\pi}[f]$$

Formellement, la variable aléatoire définie par la moyenne d'ensemble $\hat{f}_N^{MC}$ tend vers une distribution de Dirac autour de l'espérance de la fonction : $\lim\limits_{N \rightarrow \infty} \hat{f}_{N}^{MC} = \delta_{\mathbb{E}_{\pi}[f]}$.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>


---

# Méthodes de Monte-Carlo
Erreur de l'estimateur

**Propriété** : L'estimateur défini est assymptotiquement sans biais.

$\rightarrow$ En pratique, on cherche à estimer l'exactitude de l'estimateur pour un petit échantillon _(afin d'utiliser ces méthodes sur des ressources de calcul limitées)_.

**Propriété** : L'estimateur de Monte-Carlo, pour une fonction réelle et de carré intégrable (pour laquelle $\mathbb{E}_{\pi}[f], \mathbb{E}_{\pi}[f]$ existent), satisfait le Théorème Central Limite. 
C'est-à-dire, la suite des estimateurs de Monte-Carlo standardisés converge vers une loi normale centrée réduite.

$$\lim\limits_{N \rightarrow \infty} \frac{\hat{f}_N^{MC} - \mathbb{E}_{\pi}[f]}{SE_{N}} \sim \mathcal{N}(0,1)$$

où $SE_N$ est l'erreur quadratique de l'estiamteur de Monte-Carlo : $SE_N = \sqrt{\frac{Var_{\pi}[f]}{N}}$

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Méthodes de Monte-Carlo
Résumé

Autrement dit, à la limite,

$$\hat{f}_N^{MC} = \mathcal{N}(\mathbb{E}_{\pi}[f], SE_N[f])$$

En pratique, la quantification de l'erreur par le TCL suppose de connaître la variance $Var_{\pi}[f]$. Si $f^2$ est de carré intégrable ($\mathbb{E}[f^4]$ existe), on peut approximer la variance par un autre estimateur de Monte-Carlo.

**Avantages** : $SE_N \propto \frac{1}{\sqrt{N}}$, l'erreur quadratique moyenne décroît avec l'inverse de la racine carrée de la taille de l'échantillon. En pratique, cela permet de dimensionnner au préalable le nombre d'échantillons nécessaires pour atteindre une erreur donnée. 

**Limitations** : la quantification de l'erreur est probabiliste. Il reste toujours une (mal)-chance que l'estimateur s'échoue dans la queue de la distribution (ex : $\hat{f}_N^{MC} > \mathbb{E}_{\pi}[f] + 3 SE_N[f   ]$).


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Loi forte des Grands Nombres
(Rappel, si nécessaire) Théorèmes fondamentaux


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

# Théorème Central Limite
(Rappel, si nécessaire) Théorèmes fondamentaux


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
Définition




<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Chaînes de Markov
Distribution stationnaire




<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Chaînes de Markov
Convergence



<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Chaînes de Markov
(Optionnel) Spectre de la matrice de transition et Convergence



<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---
layout: section

---

# Markov Chain Monte-Carlo

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Markov Chain Monte-Carlo
Construction de l'estimateur

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Markov Chain Monte-Carlo
Conditions de convergence

- Chaîne irréductible
- Chaîne apériodique (si nécessaire, 2eme slide)

$\rightarrow$ Attention pour la construction de la matrice de transitions

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

---

# Markov Chain Monte-Carlo
Vitesse de convergence et nombre d'itérations fini

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

# 

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

[Markov Chain Monte Carlo in Practice, M. Betancourt](https://betanalpha.github.io/assets/case_studies/markov_chain_monte_carlo.html#2_markov_chain_of_command)

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
