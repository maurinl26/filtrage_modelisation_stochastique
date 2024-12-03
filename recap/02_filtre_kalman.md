---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Filtrage Kalman
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

Filtrage et Modélisation Stochastique
Loïc Maurin - _loic.maurin@meteo.fr_

# Filtre Kalman

**Filtre de Kalman** (1960), Rudolf Kalman (mathématicien hongrois) : utilisé pour la première fois pour l'estimation de trajectoire des programmes Apollo

$\rightarrow$ Comment concilier au mieux l'information disponible (capteurs), et les équations de la dynamique pour contrôler un système ?

**Applications** : Contrôle, Calage GPS, Filtrage et assimilation de données.


---
## Système dynamique

**Système dynamique discret**

$$x_{n+1} = A x_n + B u_n + \phi_n$$
$$y_{n+1} = C x_{n+1} + \psi_{n+1}$$

où, à chaque instant $n$:
- $x_n$ est l'état du système. _Exemple : Température d'un moteur d'avion_. 
- $u_n$ est la commande du système. _Exemple : Débit de carburant dans le moteur_.
- $y_n$ est la mesure de l'état du système. _Exemple : Mesure renvoyée par le thermomètre_.

**Remarque** : En météo, on appellerait $C$ un **opérateur d'observation**. C'est le lien entre **l'espace des mesures** (ex : la tension au bornes thermomètre), et **l'espace d'état** (ex : la température effective mesurée). 


---
## Bruits associés au système

**Système dynamique discret**

$$x_{n+1} = A x_n + B u_n + \phi_n$$
$$y_{n+1} = C x_{n+1} + \psi_{n+1}$$

**Bruits $\phi_n$, $\psi_n$**: on modélise un bruit d'état $\phi_n$ un bruit de mesure $\psi_n$.
1. **Bruit d'état** $\phi_n$: représente notre méconnaissance de la physique du système. _Exemple : Variations de la richesse du mélange_.
2. **Bruit de mesure** $\psi_n$: représente un bruit de mesure. _Exemple: bruit électronique de la sonde de température_.


**Hypothèse** : Les bruits $\phi$ et $\psi$ sont supposés blancs, gaussiens, centrés, stationnaires
et indépendants l'un de l'autre. Ces bruit sont chacun associé à une matrice de covariance $\Phi$, et $\Psi$. 

---
## Construction d'un estimateur

On cherche à construire un estimateur qui dépende de l'état estimé à l'instant précédent, de la mesure renoyée 
par le capteur et de la comande imposée.

Nous construisons alors un estimateur de la forme :

$$ \hat{x}_{n+1} = A_f \hat{x}_n + B_f u_n + K_{n+1} y_{n+1}$$

$\rightarrow$ Comment construire $A_f$, $B_f$, $K_{n+1}$ ?

_Note : On cherche à construire un estimateur de la forme générique $\hat{x}_{n+1} = f(\hat{x}_n, y_{n+1}, u_n)$_

_Nous nous concentrons sur des systèmes linéaires, et verrons plus tard comment l'étendre à des systèmes non-linéaires._


---
## Estimateur (assymptotiquement) sans biais

**Erreur d'estimation** :  On cherche à minimiser l'erreur d'estimation, définie ci-dessous,

$$e = \hat{x} - x$$

Pour un estimateur parfait, on aurait $\forall n \in \mathbb{N}, e_n = 0$. 

Sans accès aux vraies valeurs de 
$x$ et face à un processus stochastique, annuler l'erreur est généralement impossible.
Nous décrivons alors (non sans mal) l'erreur $e_{n}$ par récurrence:

$$e_{n+1} = \begin{array}{rcl}
(I - K_{n+1} C) A e_n
+ (A_f + K_{n+1} CA -A) \hat{x}_n + (B_f +K_{n+1}CB - B) u_{n} \\
+ (K_{n+1} C - I) \phi_n + K_{n+1} \psi_{n+1}
\end{array}$$

**Espérance de l'erreur** Sachant que les bruits blancs gaussiens sont d'espérance nulle : 


$\forall n \in \mathbb{N}, \, \mathbb{E}[\phi_n] = 0, \mathbb{E}[\psi_n] = 0$, on décrit l'évolution, l'espérance de l'erreur.

$$\mathbb{E}[e_{n+1}] = (I - K_{n+1}C) A\, \mathbb{E}[e_n] + (A_f + K_{n+1}CA - A)\, \hat{x}_n + (B_f + K_{n+1} CB - B) u_n$$

**Estimateur assymptotiquement sans biais** 

On cherche à construire un estimateur dont l'espérance de l'erreur tend vers 0.  


**Définition** 
$$\lim\limits_{n \rightarrow \infty} \mathbb{E}[e_n] = 0$$


**Espérance de l'erreur** Sachant que les bruits blancs gaussiens sont d'espérance nulle : 


$\forall n \in \mathbb{N}, \, \mathbb{E}[\phi_n] = 0, \mathbb{E}[\psi_n]$, on décrit l'évolution, l'espérance de l'erreur.

$$\mathbb{E}[e_{n+1}] = (I - K_{n+1}C) A\, \mathbb{E}[e_n] + (A_f + K_{n+1}CA - A)\, \hat{x}_n + (B_f + K_{n+1} CB - B) u_n$$

**Estimateur assymptotiquement sans biais**  $\rightarrow\, \lim\limits_{n \rightarrow \infty} \mathbb{E}[e_n] = 0$

**Conditions suffisantes**
1. $B_f + K_{n+1}\, CB - B = 0$ (_on annule le terme lié à la comande $u_n$_)
2. $A_f + K_{n+1}\, CA - A = 0$ (_on annule le terme dû à l'estimation $\hat{x}_n$_)
3. $(I - K_{n+1}C)$ est stable (_module inférieur à 1 pour que l'erreur puisse décroître )_ 



---
## Construction des Matrices du filtre
Filtre de Kalman

Les conditions de stabilité fixent les matrices $A_f$ et $B_f$.

$$A_f = (I - K_{n+1}C)A$$
$$B_f = (I - K_{n+1}C)B$$

Il reste à régler $K_{n+1}$ pour que $(I - K_{n+1}C)$ soit stable.

**Définition** : $K$ est appelé le **gain du filtre**.

**Forme de l'estimateur** : Nous établissons (et rencontrons souvent) la forme suivante du filtre.

$$\hat{x}_{n+1} = A \hat{x}_n + B u_n + K_{n+1} [y_{n+1} - C(A \hat{x}_n + B u_n)]$$

**Remarque** $K$ peut être vu comme un compromis à régler entre la fidélité au modèle numérique ($A \hat{x}_n + B u_n$) et la
fidélité aux valeurs de mesure ($y_{n+1}$). 


---
layout: image-right
image: ./img/kalman_loop.png
backgroundSize: 100%
figcaption: "Schéma du prédicteur-correcteur associé au Filtre de Kalman. Source : Welch & Bishop, Intro to the Kalman Filter"

---

## Filtre Prédicteur - Correcteur
Filtre de Kalman -  Implémentation

Le filtre de Kalman est un "prédicteur-correcteur", l'estimation $\hat{x}$ de $x$ se contruit en 2 temps :

1. **Prédiction** : Estimation à priori de l'état $\hat{x}^-$, 
comme si on n'avait que les équations du système
à disposition.

2. **Correction** : Construction de l'estimation à posteriori avec l'information apportée par les mesures.

_Légende : sur le schéma à droite,_

_$H \rightarrow C, Q \rightarrow \Phi, R \rightarrow \Psi$_


---
layout: image-right
image: ./img/kalman_loop.png
backgroundSize: 100%

Sans oublier la construction des matrices de covariances, liées au bruit.

1. **Prédiction** : Estimation de la matrice de covariance $P_k^-$,
par rapport au **bruit d'état** $Q$ (ou $\Phi$ comme noté précédemment).   

2. **Correction** : Construction de la matrice de covariance $P_k$ par rapport au **bruit de mesure** $R$ (ou $\Psi$ comme noté précédemment).

_Légende : sur le schéma à droite,_

_$H \rightarrow C, Q \rightarrow \Phi, R \rightarrow \Psi$_


---
# Filtre de Kalman - Implémentation
## La recette de cuisine !

1. On initialise $\hat{x}$ à $\hat{x}_0$ : on peut par exemple prendre la valeur $y_0$ renvoyée par le capteur.

2. On initialise $P$ à $P_0$ :  on peut prendre la valeur $\Psi$ de la covariance de bruit du capteur.

3. On fait évoluer $K$ selon :
$$K_{n+1} = (A P_n A^T + \Psi)C^T \times (C A P_n A^T C^T + C \Psi C^T + \Psi)^{-1}$$

4. On fait évoluer $\hat{x}$ selon :
$$\hat{x}_{n+1} = A \hat{x}_n + B u_n + K_{n+1} [y_{n+1} - C(A \hat{x}_n + B u_n)]$$

5. On fait évoluer $P$ selon :
$$P_{n+1} = (I - K_{n+1} C)(A P A^T + \Phi) $$


---
## Est-ce que ça marche vraiment en pratique ?
Considérations sur l'évolution de la variance 

On a établit que $\lim\limits_{n \rightarrow \infty} \mathbb{E}[e_n] = 0$,  ainsi que la formule pour $P_{n+1}$. 

$\rightarrow$ Est-ce qu'on est obligé d'attendre l'$\infty$ pour que le filtre commence à fonctionner ?

$\rightarrow$ Pourquoi on se balade avec une matrice de covariance $P_{n+1}$ (si ce n'est par pure beauté mathématique) ?

$\rightarrow$ En pratique, comment fixer $K_{n+1}$ le gain du filtre ?

**Dispersion** Nous allons travailler sur la dynamique de l'erreur $e_n$ et sa dispersion, pour 

$$P = \mathbb{E}[e_n \times e_n^T]$$

_Dans un cas simple à une dimension, $P = \mathbb{E}[(\hat{x}_n - x_n)^2] = \mathbb{V}[e_n]$, s'écrit bien comme la variance de l'erreur._

 
---
# Dynamique de l'erreur
## Choix du gain K

**Condition d'optimalité du gain du filtre** 

On cherche $K$ de telle sorte que la variance de l'erreur $P = \mathbb{E}[e \times e^T] \sim \mathbb{E}[e]$ soit minimale.

Un condition nécessaire est de chercher le gain $K$ tel que $P(k)$ soit extrêmal, autrement dit que 

$$\forall n \in \mathbb{N}, \frac{\partial P_{n+1}}{\partial K_{n+1}} = 0$$

**Dynamique de l'erreur** En maniuplant les équations du filtre, on obtient 

$$
e_{n+1} = (I - K_{n+1} C) A e_n + (K_{n+1} C - I) \phi_n + K_{n+1} \psi_{n+1}
$$
$$
\frac{\partial e_{n+1}}{\partial K_{n+1}} = - C A e_n + C \phi_n + \psi_n 
$$

On cherche le gain $K$ pour établir $\forall n \in \mathbb{N}, \frac{\partial P_{n+1}}{\partial K_{n+1}} = 0$

**Condition suffisante** 
$$\mathbb{E}[\frac{\partial e_{n+1}}{\partial K_{n+1}} e^T_{n+1}] = 0$$

**Formule de $K$ à partir de $P$**

$$P_{n+1} = (A P_nn A^T + \Psi) C^T \times (C A P_n A^T C^T + C \Phi C^T + \Psi)^{-1}$$

**Formule de $P$ à partir de $K$** (en prenant en compte $P_0 = \Psi$)

$$P_{n+1} = (I - K_{n+1} C)(A P_n A^T + \Phi)$$

**Remarque** On ne peut pas obtenir de condition d'optimalité, mais simplement d'une relation de récurrence entre $P$ et $K$. C'est elle qui nous permet d'implémenter $K$ en pratique.


---

# Synthèse
## Estimateur assymptotiquement sans biais construit de manière récurrente

**Estimateur sans biais** 

On cherche à obtenir un estimateur qui satisfait $\lim\limits_{n \rightarrow \infty} \mathbb{E}[e_n] = 0$ (assymptotiquement sans biais)

**Gain optimal** 

On cherche à régler $K$ pour que les variances d'erreurs soit les plus faibles à chaque pas de temps, c'est-à-dire $\frac{\partial P_{n+1}}{\partial K_{n+1}} = 0$

**Récurrence** 

Dans la mesure où nous ne connaissons pas les valeurs vraies $x_n$, nous tirons parti des relations de récurrence sur l'erreur $e_n$ pour progresser vers une erreur nulle.

C'est cela même qui fait la structure du Filtre Kalman. Et c'est bien pratique dans la mesure où le filtre ne dépend que des valeurs à l'état $n$ pour estimer l'état $n+1$ (le filtre est robuste et facile à mettre en oeuvre).


---
## Structure probabiliste

$\rightarrow$ Quel lien avec les probabilités ?


**Point de départ**

- Bruit d'état $\phi \sim \mathcal{N}(0, \Phi)$ (ex : $\Phi$ donné par la distibution de températures au point de mesure, i.e. la climatologie du lieu)  
- Bruit de mesure $\psi \sim \mathcal{N}(0, \Psi)$ (ex : $\Psi$, précision donnée par la fiche technique du capteur de température)

**Point d'arrivée**
- On modélise $P(x_n| z_n) \sim \mathcal{N}(\hat{x}_n, P_n)$, avec notre estimateur $\hat{x}_n$
- Avec les propriétés de l'estimateur, on progresse vers $\mathbb{E}[\hat{x}_n] = x_k$ en gardant une dispersion minimale $\mathbb{E}[(x_n - \hat{x}_n)(x_n - \hat{x_n})^T] = P_n$.


---
## Implémentation pratique d'un filtre de Kalman

**Hypothèses structurantes** Il reste en pratique à vérifier que les bruits de mesure et d'état sont effectivement des bruits blancs gaussiens, stationnaires.

1. Qu'on a "suffisament capté" la dynamique du système et son observation dans les matrices $A$, $B$ et $C$, pour que les bruits $\Phi$ et $\Psi$ soient effectivement centrés.

2. Que les bruits puissent être assimilés à des bruits blancs gaussiens. Cela implique des tests statistiques, et éventuellement un travail sur les équations pour
centrer et réduire le bruit (lien avec le TCL).

3. Qu'il y ait effectivement indépendance entre le bruit de mesure et le bruit d'état. _Exemple : le capteur de température qui influence son environnement en 
ralentissant le flux d'air dont il mesure la température._

**En pratique** 

Il n'y a pas forcément de réponse systématique à ces questions, simplement un travail de mise au point du filtre, sur un problème donné $\rightarrow$ **Coeur du travail de l'ingénieur**

 
---

# Exemple - Estimation d'une constante aléatoire
Filtre de Kalman

**Problème** 

Nous cherchons à estimer une tension constante. Avec, à disposition, un volt-mètre, dont les mesures sont
perturbées par un bruit blanc d'écart-type $\sigma = 0.1 V$.

**Système linéaire**

$$x_{n+1} = x_{n} + \phi_{n+1}$$
$$z_{n+1} = x_{n+1} + \psi_{n+1}$$


**Equations du Filtre** Dans ce cas simple, $A_f = B_f = C = I = 1$, nous étudions alors l'estimation 
$\hat{x}$, la (co)-variance de l'erreur $P_n$ et le gain $K_n$, qui sont les charactéristiques essentielles du filtre. 

1. Prédicteur :

$$\hat{x}^-_{n+1} = \hat{x}_{n}$$
$$P^-_{n+1} = P_n + \Phi \; (= P_n + \mathbb{V}[\phi_{n+1}]) $$

2. Correcteur :

$$K_{n+1} = P^-_{n+1} (P^-_{n+1} + \Psi)^{-1} = \frac{P_n + \Phi}{P_{n} + \Phi + \mathbb{V}[\psi_n]}$$
$$\hat{x}_{n+1} = \hat{x}^-_{n+1} + K_{n+1}(z_{n+1} - \hat{x}^-_{n+1}) $$
$$P_{n+1} = (1 - K_{n+1}) P_{n+1}^-$$


**Hypothèses et modélisation**

1. Ici, on suppose une variance sur le bruit d'état : $\Phi = 1 \times 10^{-5}$. C'est à dire qu'on se laisse la
possibilité d'avoir un petit bruit d'état autour de la constante à mesurer (perturbations électromagnétiques ou autre).

2. Choix des conditions initiales : on suppose que la tension à mesurer est une constante tirée d'une loi normale centrée.


On fixe alors $x_0 = 0$, et on choisit une valeur arbitraire, mais non-nulle ($P_0 \neq 0$) pour $P_0$. Ici, $P_0 = 1$.




---
layout: image
image: ./img/kalman_1.png
backgroundSize: 60%

---

## Vraie valeur, Mesures, et Estimation (50 relevés)




---
layout: image
image: ./img/kalman_2.png
backgroundSize: 50%


---

## Vitesse de convergence - (Co)-Variance de l'erreur $P_n$



---
layout: image
image: ./img/kalman_3.png
backgroundSize: 60%


---

## Influence des bruits $\Psi$ et $\Phi$, avec $\Psi = 1$



---
layout: image
image: ./img/kalman_4.png
backgroundSize: 60%


---

## Influence des bruits $\Psi$ et $\Phi$, avec $\Psi = 1 .  10^{-5}$



---


# Dynamique Non-Linéaire
## Extended Kalman Filter
Si la dynamique est non linéaire ?

Remplaçons :
$$x_{n+1} = A x_n + B u_n + \phi_n$$
$$y_{n+1} = C x_{n+1} + \psi_{n+1}$$


Par :
$$x_{n+1} = f(x_n, u_n, \phi_n), \, (modèle\; non-linéaire)$$
$$y_{n+1} = h(x_{n+1}, \psi_{n+1}), (observateur\; non-linéaire)$$

**Remarque** C'est le cas en Météo où le modèle repose sur les équations de Navier-Stokes (non-linéaires), et les observateurs reposent sur les lois non-linéaires (ex : réflectivité radar $R \propto D^6$)

**Solution** : Linéariser les équations (modèle et observateurs), autour du point de fonctionnement $\hat{x}_n$ estimé.

**Outil** : les matrices jacobiennes des opérateurs $y = h(x)$ et modèles $x_{n+1} = f(x_n)$

On note alors :

1. $J^A_{(i,j)} = \frac{\partial f_i}{\partial x_j}$, la jacobienne de $f$ par rapport à $x$
2. $W_{(i,j)} = \frac{\partial f_i}{\partial \phi_j}$, la jacobienne de $f$ par rapport au bruit d'état $\phi$
3. $J^H_{(i,j)} = \frac{\partial h_i}{\partial x_j}$, la jacobienne de $h$ par rapport à $x$
4. $V_{(i,j)} = \frac{\partial h_i}{\partial \psi_j}$, la jacobienne de $h$ par rapport au bruit de mesure $\psi$.


1. On utilise directement $f$, et $h$ directement pour le calcul de $\hat{x}^-_n$ à priori et l'évaluation de la mesure $h(y_n)$.
2. On utilise les matrices jacobiennes dans le calcul des covariances :

$P^-_{n+1} = A P_{n} A^T + \Phi$ devient $P^-_{n+1} = J^A P_n (J^{A})^T + W \Phi W^T$ 

$K_{n+1}$ devient $K_{n+1} = P^-_{n+1} (J^H)^T (J^H P^-_{n+1} (J^H)^T + V \Psi V^T)$

$P_{n+1}$ devient $P_{n+1} = (I - K_{n+1} J^H) P^-_{n+1}$

où $J^A$ et $J^H$ ont été évaluées au point $\hat{x}_n$

**En Météo** 

On ne s'étonnera pas de trouver les termes de **Tangent-Linéaire** (TL) et **Adjoint** (AD) en assimilation de données, il s'agit respectivement de la **jacobienne** $J^A$ et de sa **transposée** $(J^A)^T$
_(plus exactement du conjugué de sa transposée si on travaille sur un espace complexe)_.

**En pratique** 

Là encore, le **coeur du travail d'ingénieur** est d'obtenir les opérateurs adéquats (en respectant de manière empirique les hypothèses sur les distributions).

---

# A retenir 
## Qu'est-ce qu'un filtre de Kalman déjà ?


**Basique**
- La recette et le fonctionnement de prédicteur-correcteur pour le filtre Kalman,
- (Théorie), c'est un estimateur assymptotiquement sans biais, et avec une variance d'erreur à minimiser.

**En fonction de l'espace de stockage disponible**
- La démarche récursive de construction du filtre,  _utile pour comprendre le filtre "pas-à-pas"_.

**En pratique, avec l'algorithme à disposition**
- Comment régler le gain du filtre ?
- Comment respecter au mieux les hypothèses de bruit blanc gaussien ?
- Comment linéariser le modèle et les observateurs de mesure ?

_En gardant à l'esprit que le Filtre de Kalman est plutôt un filtre simple et robuste, et que le travail consiste à construire
les "bonnes" matrices de covariance, et régler le gain du filtre._

---
# Sources 
Liens utiles

([G. Welch, G. Bishop, University of North Carolina, An Introduction to the Kalman Filter](https://www.cs.unc.edu/~welch/media/pdf/kalman_intro.pdf))


([O. Herscovici-Schiller, ONERA, Introduction au filtrage de Kalman et à la commande optimale](https://www.onera.fr/sites/default/files/270/poly_Kalman_Herscovici.pdf))


([G. Chardon, CentraleSupélec, Filtrage de Kalman](https://gilleschardon.fr/fc/kalman/kalman.pdf))

---