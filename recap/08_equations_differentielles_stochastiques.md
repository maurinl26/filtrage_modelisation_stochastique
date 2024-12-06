# Equations différentielles stochastiques


## Définition

Un **processus stochastique** est une collection de variables aléatoires $\{X_t\}_{t \geq 0}$ définies sur un espace probabilisé commun où $t$ représente le temps. Le processus stochastique décrit l'évolution d'un système soumis à l'aléatoire.

Une **équation différentielle stochastique** est la généralisation de la notion d'équation différentielle en prenant en compte un terme de bruit blanc.

## De l'équation différentielle ordinaire (EDO) à l'équation différentielle stochastique (EDS)

On considère une équation différentielle ordinaire $\frac{dX_t}{dt} = \mu(X(t))$ à laquelle nous aimerions rajouter du bruit. Nous considérons alors un **bruit blanc**
$\xi(t)$ dépendant du temps et de l'espace.

**Motivations** : on souhaiterait définir l'équation d'évolution 

$$\frac{dX_t}{dt} = \mu(X(t)) + \sigma(X(t))\xi(t)$$

_Remarque : on aurait envie que le processus évolue par petits sauts gaussiens, de moyenne $\mu(t)$ et de variance $\sigma(t)$._

$\rightarrow$ Encore faut-il qu'une différentiation par rapport à un bruit blanc $\xi(t)dt$ ait un sens ! 

On définit alors un mouvement browien $W_t$, où $W_t$ est un taux de transition, ou une densité de probabilité de transition. Le mouvement 
browien définit alors l'équation différentielle suivante :

$$dX(t) = \mu(X(t))dt + \sigma((X(t))) dW_t$$

Et on cherche à donner un sens à l'intégration suivante :

$$X(t) = X(0) + \int_0^t \mu(X(s)) ds + \int_0^t \sigma(X(s)) dW(s)$$

## Processus de premier ordre

Considérons une équation différentielle stochastique (EDS) du premier ordre :

$$dX_t = a(X_t, t) dt + b(X_t, t) dW_t$$

où :
- $X_t$ est le processus stochastique inconnu,
- $a(X_t, t)$ est le terme de dérive
- $b(X_t, t)$ est le terme de diffusion
- $W_t$ est le mouvement brownien standard

## Formule d'Itô

La **formule d'Itô** est une formule fondamentale du calcul stochastique. Elle permet de calculer la différentielle $df$ d'une fonction 
$f(X_t, t)$ où $X_t$ suit une équation différentielle stochastique.

_Remarque : la formule d'Itô nous sert de base pour passer d'une étude déterministe du système représenté par ses états $\{X_t\}_{t \geq 0}$ à une étude stochastique 
de ce même système (par exemple : ajout d'une force aléatoire ou d'un bruit de mesure)._

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

_Remarque : Il existe une autre interprétation des EDS, par [l'intégrale de Stratanovitch](https://en.wikipedia.org/wiki/Stratonovich_integral), qui permettrait l'équivalent d'une intégration trapéoïdale, là où Itô propose une
intégration cohérente numériquement avec Euler explicite._

## Intégration numérique - Méthode d'Euler-Maruyama

La **méthode d'Euler-Maruyama** permet d'approximer numériquement les solutions des EDS. En discrétisant selon un pas de temps $\Delta t$,  on obtient :

$$X_{n+1} = X_{n} + a(X_n, t_n) \Delta t + b(X_n, t_n) \Delta W_n$$

où $\Delta W_n = W_{t_{n+1}} - W_{t_n} \sim \mathcal{N}(0, \Delta t)$.

## (Optionnel) Convergence de la Méthode Euler-Maruyama

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

_Remarque : de la manière où nous avons défini le mouvement brownien, la variance du processus croît de $\Delta t$ à chaque pas de temps._

**Calcul de l'erreur** En posant :

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


## Applications

Les équations différentielles stochastiques ont de nombreuses applications en ingénierie, météorologie, biologie, finance.

**Mouvement brownien géométrique** : C'est un processus stochastique à valeurs positives. Il est utilisé en mathématiques financières pour modéliser un cours de bourse.
La dynamique de ce processus est la base du modèle de Black-Scholes.

$$dX_t = \mu X_t dt + \sigma X_t dW_t$$

_Remarque : l'équation du premier ordre $dX_t = \mu dt + \sigma dW_t$ avait déjà été utilisée par Louis Bachelier (1900) pour établir un modèle de cours de bourse._


**Equation de Langevin** : Elle modélise le mouvement d'une particule dans un "bain thermique", soumise à une force $\xi(t)$ fluctuante (qui pourrait être un bruit blanc). Dans ce cas, la différentielle de la vitesse associée au principe fondamentale de la dynamique est :

$$dV(t) = - \lambda V(t) dt + dW_t $$

## Références

Gratton, S., Sequential Filtering, (2024-2025)

Baehr, C., Modélisations Probabilistes d'Equations de la Mécanique des Fluides, Etat de l'art en modélisation stochastique (2004)

