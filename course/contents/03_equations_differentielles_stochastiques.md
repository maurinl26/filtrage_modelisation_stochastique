---
title: Equations différentielles stochastiques 

---

# Définition

Un **processus stochastique** est une collection de variables aléatoires $\{X_t\}_{t \geq 0}$ définies sur un espace probabilisé commun où $t$ représente le temps. Le processus stochastique décrit l'évolution d'un système soumis à l'aléatoire.

Une **équation différentielle stochastique** est la généralisation de la notion d'équation différentielle en prenant en compte un terme de bruit blanc. 

# De l'équation différentielle ordinaire (EDO) à l'équation différentielle stochastique (EDS)

On considère une équation différentielle ordinaire $\frac{dX_t}{dt} = \mu(X(t))$ à laquelle nous aimerions rajouter du bruit. Nous considérons alors un **bruit blanc**
$\xi(t)$ dépendant du temps et de l'espace.


:::{note} Motivations
On aurait envie que le processus évolue par petits sauts gaussiens, de moyenne $\mu(t)$ et de variance $\sigma(t)$.

$$\frac{dX_t}{dt} = \mu(X(t)) + \sigma(X(t))\xi(t)$$

$\rightarrow$ Encore faut-il qu'une différentiation par rapport à un bruit blanc $\xi(t)dt$ ait un sens ! 
:::

On définit alors un mouvement browien $W_t$, où $W_t$ est un taux de transition, ou une densité de probabilité de transition. Le mouvement 
browien définit alors l'équation différentielle suivante :

$$dX(t) = \mu(X(t))dt + \sigma((X(t))) dW_t$$

Et on cherche à donner un sens à l'intégration suivante :

$$X(t) = X(0) + \int_0^t \mu(X(s)) ds + \int_0^t \sigma(X(s)) dW(s)$$

:::{note} Cours de bourse
Le cours de bourse en est l'exemple parfait. A partir d'un actif qui se compose exponentiellement dans le temps,
on souhaite modéliser des fluctuations de marché.

- Sans fluctuations de marché, la valeur de l'actif est modélisée par une Equation Différentielle Ordinaire,
- Avec fluctuations de marché, la valeur de l'actif est modélisée par une Equation Différentielle Stochastique.
:::

# Equation différentielle stochastique de premier ordre

Considérons une équation différentielle stochastique (EDS) du premier ordre :

$$dX_t = a(X_t, t) dt + b(X_t, t) dW_t$$

où :
- $X_t$ est le processus stochastique inconnu,
- $a(X_t, t)$ est le terme de dérive
- $b(X_t, t)$ est le terme de diffusion
- $W_t$ est le mouvement brownien standard

# Intégration analytique - Formule d'Itô

La **formule d'Itô** est une formule fondamentale du calcul stochastique. Elle permet de calculer la différentielle $df$ d'une fonction 
$f(X_t, t)$ où $X_t$ suit une équation différentielle stochastique.


:::{note} Différentielle stochastique
La formule d'Itô nous sert de base pour passer d'une étude déterministe du système représenté par ses états $\{X_t\}_{t \geq 0}$ à une étude stochastique de ce même système (par exemple : ajout d'une force aléatoire ou d'un bruit de mesure).
:::

**Enoncé** : Soit $\{X_t\}_{t \geq 0}$ un processus stochastique qui satisfait :

$$dX_t = a(X_t, t) dt + b(X_t, t) dW_t$$

Alors, pour une fonction $f(X_t, t) \in \mathcal{C}^2$ deux fois continuement différentiable, sa différentielle s'exprime :

$$df = (\frac{\partial f}{\partial t} + a(X_t, t) \frac{\partial f}{\partial x} + \frac{1}{2} b^2(X_t, t) \frac{\partial^2 f}{\partial x^2}) dt + b(X_t, t) \frac{\partial f}{\partial x} dW_t$$

**Exemple** : 

Soit $X_t = W_t$ (le mouvement brownien standard) et $f(X_t) = X_t^2$. Calculons $df$ :

$$\frac{\partial f}{\partial x} = 2 X_t, \frac{\partial^2 f}{\partial x^2} = 2, \frac{\partial f}{\partial t} = 0$$

Comme $dX_t = dW_t$, avec $a = 0, b = 0$, la fomule d'Itô donne :

$$df = (0 + 0 \cdot 2X_t + \frac{1}{2} \cdot 1^2 \cdot 2) dt + 1 \cdot 2 X_t dW_t = dt + 2 X_t dW_t$$

Ce résultat montre que :
$$d(W_t^2) = dt + 2 W_t dW_t$$


:::{note}
Il existe une autre interprétation des EDS, par [l'intégrale de Stratanovitch](https://en.wikipedia.org/wiki/Stratonovich_integral), qui permettrait l'équivalent d'une intégration trapéoïdale.


L'intégration d'Ito représente l'aléa au début du pas de temps tandis que l'intégration de Stratanovitch représente l'aléa comme la limite d'une suite d'équations différentielles ordinaires.
:::

# Une autre interprétation - L'intégrale de Stratanovitch

# Intégration numérique - Méthode d'Euler-Maruyama

La **méthode d'Euler-Maruyama** permet d'approximer numériquement les solutions des EDS. En discrétisant selon un pas de temps $\Delta t$,  on obtient :

$$X_{n+1} = X_{n} + a(X_n, t_n) \Delta t + b(X_n, t_n) \Delta W_n$$

où $\Delta W_n = W_{t_{n+1}} - W_{t_n} \sim \mathcal{N}(0, \Delta t)$.


:::{note} Résolution numérique d'équations différentielles ordinaires

Numériquement, quand on cherche à résoudre $\frac{dX}{dt} = \mathcal{M}(X)$ en se donnant un pas de temps d'intégration $\Delta t$, il existe 2 méthodes 
d'ordre 1 en temps.

**Euler Explicite** _(Euler forward)_

$$X_{n+1} = X_{n} + \Delta t\; \mathcal{M}(X_n) + \mathcal{O}(\Delta t)$$


**Euler Implicite** _(Euler backward)_

$$X_{n+1} = X_{n} + \Delta t\; \mathcal{M}(X_{n+1}) + \mathcal{O}(\Delta t)$$
:::

:::{note} Euler-Maruyama ou "Euler Explicite Stochastique"
En quelque sorte, on pourrait dire que la méthode d'Euler-Maruyama est une méthode d'Euler explicite stochastique.
:::

## Convergence de la Méthode Euler-Maruyama

En considérant l'EDS linéaire simple :
$$dX_t = \mu dt + \sigma dW_t, X_0 = 0$$

**Résolution analytique** En intégrant les 2 membres de $t = 0$ à $t = t_n$ :

$$X_{t_n} - X_0 = \int_0^{t_n} \mu dt +  \int_0^{t_n} \sigma dW_t$$

Avec $X_0 = 0$, et $W_0 = 0$, donc :
$$X_{t_n} = \mu t_n + \sigma W_{t_n}$$

**Résolution numérique** Par la méthode d'Euler-Maruyama :

En discrétisant selon un pas de temps $\Delta t$,

$$X_{n+1} = X_n + \mu \Delta t + \sigma \Delta W_n$$

où $\Delta W_n = W_{t_{n+1}} - W_{t_n} \sim \mathcal{N}(0, \Delta t)$.

:::{note} Croissance de la variance
De la même manière que nous avons défini le mouvement brownien, la variance et l'écart type du processus croîssent respectivement de $\Delta t$ et $\sqrt{\Delta t}$ à chaque pas de temps.

Il est alors possible d'écrire l'intégration discrète par rapport à une variable aléatoire gaussienne centrée réduite $X \sim \mathcal{N}(0,1)$ :

$$X_{n+1} = X_n + \mu \Delta t + \sigma \sqrt{\Delta t} X$$

:::

**Calcul de l'erreur**

En posant :

$$e_n = X_{t_n} - X_n$$

Et en utilisant d'une part l'expression analytique, d'autre part l'expression numérique, on obtient :

$$
\begin{array}{rcl}
    X_{t_n} &=&\mu t_n + \sigma W_{t_n} \\
    X_n &=& X_0 + \sum^{n-1}_{k=0} (\mu \Delta t + \sigma \Delta W_k) = \mu n \Delta t +  \sigma \sum^{n-1}_{k=0} \Delta W_k
\end{array}
$$

et 
$$
e_n = \mu t_n - \mu n \Delta t + \sigma (W_{t_n} - \sum^{n-1}_{k=0} \Delta W_k)
$$



**Simplification de l'erreur**  

Comme $t_n = n \Delta t$, le terme déterministe s'annule :

$$\mu t_n - \mu n \Delta t = 0$$

Il reste :

$$e_n = \sigma (W_{t_n} - \sum^{n-1}_{k=0} \Delta W_k)$$

Donc :

$$
\sum^{n-1}_{k=0} \Delta W_k = W_{t_n} - \Delta W_n
$$

L'erreur s'écrit :

$$
e_n = \sigma (W_{t_n} - (W_{t_n} - \Delta W_n)) = \sigma \Delta W_n
$$

**Erreur Quadratique moyenne** Comme $\Delta W_n \sim \mathcal{N}(0, \Delta t)$, on a :

$$
\mathbb{E}[e_n^2] = \sigma^2 \mathbb[(\Delta W_n)^2] = \sigma^2 \Delta t
$$


**Conclusion** L'erreur quadratique moyenne étant proportionnelle à $\Delta t$, cela montre que l'erreur tend vers zéro quand $\Delta t \rightarrow 0$. La méthode 
d'Euler-Maruyama converge vers la solution exacte de cette EDS.


# Applications

Les équations différentielles stochastiques ont de nombreuses applications en ingénierie, météorologie, biologie, finance.

**Mouvement brownien géométrique** : C'est un processus stochastique à valeurs positives. Il est utilisé en mathématiques financières pour modéliser un cours de bourse.
La dynamique de ce processus est la base du modèle de Black-Scholes.

$$dX_t = \mu X_t dt + \sigma X_t dW_t$$

:::{note} Modélisation d'un cours de bourse
L'équation du premier ordre $dX_t = \mu dt + \sigma dW_t$ avait déjà été utilisée par Louis Bachelier (1900) pour établir un modèle de cours de bourse.
:::

**Equation de Langevin** : Elle modélise le mouvement d'une particule dans un "bain thermique", soumise à une force $\xi(t)$ fluctuante (qui pourrait être un bruit blanc). Dans ce cas, la différentielle de la vitesse associée au principe fondamentale de la dynamique est :

$$dV(t) = - \lambda V(t) dt + dW_t $$

# Exercices

## Le Mouvement Brownien Geometric 

Le mouvement brownien géométrique est utilisé en finance pour modéliser la dynamique de prix d'un actif.

$$dS = \mu S dt + \sigma S dW$$

Où :
- $S$ est la valeur de l'action,
- $\mu$ est le coefficient de dérive,
- $\sigma$ est le coefficient de volatilité,
- $dW$ est un processus de Wiener.

Les caractéristiques intéressantes du mouvement brownien géométrique, dans ce cadre, sont :
- une croissance exponentielle,
- des fluctuations aléatoires,
- l'absence de bornes sur la valeur de l'actif.

Nous allons explorer plusieurs méthodes numériques pour simuler le mouvement brownien géométrique.

1. Donner la solution analytique du mouvement brownien géométrique. Elle servira de référence pour le calcul des erreurs numériques.

1. Implémenter la méthode d'Euler-Maruyama.

2. La méthode de Milstein ajoute un terme de correction pour les dérivées d'ordre 2. Cette correction améliore la précision mais rend le schéma plus instable pour de grands pas de temps ou de fortes variabilités stochastiques.

En ordres de grandeurs, la précision de la méthode d'Euler-Maruyama est $\mathcal{O}(\sqrt{t})$ tandis que celle du schéma de Milstein est $\mathcal{O}(t)$.

La formule est la suivante :

$$\delta S_t = (\mu + \frac{1}{2}\sigma^2)\delta t + \sigma \sqrt{\delta t}\phi + \frac{1}{2} \sigma^2\phi^2 \delta t$$

Implémenter la méthode de Milstein.

3. Nous allons comparer les différentes approches.
Implémenter une fonction pour calculer l'erreur quadratique moyenne.

4. Simulation d'un cours de bourse :

On donne :
- $S_0 = 100$ : la valeur initiale de l'action,
- $\mu = 0.05$ : le coefficient de dérive,
- $\sigma = 0.05$ : le coefficient de volatilité,

On modélisera le cours de bourse sur un an ($T=1$) avec un rafraîchissement journalier ($n=252$).
 
Afficher les résultats de simulation pour les 2 méthodes.

Evaluation des options d'achat et de vente :
En finance, une option d'achat (call) ou de ventre (put) donne le droit d'acheter ou de vendre une action à une date donnée, à un prix fixé à l'avance. C'est un contrat entre le propriétaire de l'option et son vendeur.

En pratique, une option d'achat permet de parier à la hausse d'un titre financier tandis qu'une option de vente permet de parier sur la baisse, ou de se couvrir contre le risque de baisse.

On appelle **prix d'exercice**, ou **strike** le prix, déterminé à l'avance, auquel le propriétaire peut respectivement acheter ou vendre l'action. Et **prix d'achat de l'option**, ou **prime** le montant initial 
versé pour acquérir l'option.

Le profit à échéance est alors :
- Pour une option d'achat :
$$Profit = Max(0, Prix - Strike) - Prime$$
- Pour une option de vente :
$$Profit = Max(0, Strike - Prix_{marché}) - Prime$$

Le pricing sous risque neutre est donné par :
$$ V(S,t) = e^{-\sigma (T-t)}\mathbb{E}^{\mathbb{Q}}[Payoff(S_T)] $$

Où :
- $V$ est la valeur de l'option,
- $\mathbb{E}^{\mathbb{Q}}$ est l'espérance sous risque neutre,
- $Payoff(S_T)$

Nous allons voir plusieurs modèles de pricing d'options :
- Pricing sous risque neutre,
- Asian pricing,
- Loopback pricing.

6. Nous reprenons l'exemple précédent, avec :
- $\sigma = 20 %$ pour le coefficient de volatilté,
- $E = 100$ le prix de l'option (strike)

Simuler plusieurs trajectoires de la valeur de l'action : on prendra $n = 100 000$ trajectoires.

7. Asian option, Average strike option :

- Achat : $V = max(0, S-A)$,
- Vente : $V = max(0, A-S)$

Où A est la moyenne arithmétique de la valeur de l'actif sur la période.

Calculer le prix d'achat et le prix de vente.

8. Lookback option, floating strike option :

- Achat : $V = max(0, S - M_{min})$
- Vente : $V = max(0, M_{max} - S)$

Où $M_{max}$ et $M_{min}$ sont respectivement les valeurs maximum et moyenne de l'actif.

Calculer le prix d'achat et le prix de vente.

:::{note} Assurance paramétrique
De manière analogue, ce type de produit est développé en assurance paramétrique. Il vise à couvrir les pertes en fixant le remboursement sur un paramètre météo donné.

Par exemple, le programme [**Index-Based Livestock Insurance**](https://www.sparc-knowledge.org/innovations/index-based-livestock-insurance) surveille les niveaux de végétations au Kenya et rembourse
les exploitants agricoles à un certain seuil de végétation basse.  
:::


# Références

Gratton, S., Sequential Filtering, (2024-2025)

Baehr, C., Modélisations Probabilistes d'Equations de la Mécanique des Fluides, Etat de l'art en modélisation stochastique (2004)

[Smith L., Itô and Stratanovitch, A guide for the perplexed](https://www.robots.ox.ac.uk/~lsgs/posts/2018-09-30-ito-strat.html)

[In-Depth Guide to Simulating Paths with the Euler-Maruyama Scheme for Exotic Options Pricing](https://quantfin.net/posts/option-introduction/)

