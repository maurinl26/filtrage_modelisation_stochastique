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
layout: end

---

---
