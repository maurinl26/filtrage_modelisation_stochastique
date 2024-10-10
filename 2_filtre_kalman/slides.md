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

# Filtrage et Modélisation Stochastique


Loïc Maurin - _loic.maurin@meteo.fr_

---
layout: section

---

# 2 - Filtrage Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---
layout: section

---

## Au cours précédent
Processus stochastiques


<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

## Au cours précédent
Processus stochastiques

**Processus stochastique** : famille de variables aléatoires $(X_t)_{t \in \mathcal{T})$.

**Temps discret** : $t \in \mathbb{N}$, le processus se modélise avec des **probabilités de transition**.

**Temps continu** : $t \in \mathbb{R}$, le processus se modélise avec des **densités de probabilités** ou **taux de transitions** notés
$W(n,m)$  pour la transition entre de l'état $n$ vers l'état $m$.

**Equation Maîtresse** : équation différentielle qui représente l'évolution temporelle de la densité de probabilité. On a vu qu'elle 
s'établissait de manière analogue à un bilan physique.

**Mouvement brownien** ou **Processus de Wiener** _(d'où la notation $W(n,m)$)_, 

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

## Au cours précédent
Correction - Cinétique chimique

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>


---
layout: section

---

## Rappels de probabilités

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Lois usuelles
Rappels de probabilité

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

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Lois usuelles
Rappels de probabilité

**Loi Normale** : $X$ suit une **loi normale** $\mathcal{N}(m, \sigma^2), ((m, \sigma) \in \mathbb{R}\,  \times  \mathbb{R_+})$

$$ \forall x \in \mathbb{R}, f(x) = \frac{1}{\sqrt{2\pi} \sigma} exp(-\frac{(x - m)^2}{2\sigma^2})$$

On a alors $\mathbb{E}[X] = m, \mathbb{V}[X] = \sigma^2$

**Loi Gamma** : $X$ suit une **loi gamma** $\gamma(p, \lambda), (p \gt 0, \lambda \gt 0)$ si sa densité est 

$$ \forall x \in \mathbb{R}, f(x) = \begin{array}{rcl} 
\frac{\lambda}{\Gamma(p)}(\lambda x)^{p-1}e^{-\lambda x} & si & x \geq 0 \\
0  & sinon
\end{array} $$

On a alors $\mathbb{E}[X] = p/\lambda$, $\mathbb{V}[X] = p/\lambda^2$

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Lois usuelles
Exemple de distribution statistique

En microphysique (modélisation des nuages et interactions entre hydrométéores), la distribution des gouttes
par rapport à leur diamètre est donnée par une loi exponentielle (Loi de Marshall-Palmer).

**Loi de Marshall-Palmer**
$$ N(D) dD = N_0 e^{-\lambda D} dD, où \, f(D) = e^{-\lambda D}$$

où la densité de probabilité est $f(D) = e^{-\lambda D}$.

**Remarque** 

Une densité de loi Gamma (plus générique que la loi exponentielle) est choisie sur certains 
schémas microphysiques.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Lois usuelles 
Exemple de distribution statistique

**Loi de Marshall-Palmer**
$$ N(D) dD = N_0 e^{-\lambda D} dD$$

On peut alors lier les moments de la distribution avec les caractéristiques physiques des gouttes :

- **Concentration de goutelettes** $N_c$ et **moment d'ordre 0** : $N = \int_0^{\infty} f(D) dD$ 
- **Contenu en eau** $q_c$ et **moment d'ordre 3** : $L = \frac{\pi \rho_w}{6} \int_0^{\infty} D^3 f(D) dD$ _(lié au volume des gouttes)_
- **Réflectivité radar** $\Sigma_c$ et **moment d'ordre 6** : $R \propto \int_0^{\infty} D^6 f(D) dD$ _(lié à la loi de Rayleigh)_

En modélisation microphysique, "le jeu" est de choisir le nombre de moments à modéliser (**fermeture**),
la discrétisation sur l'espace des diamètres (**échantillonage**), pour représenter les interactions possibles
entre les gouttes (de pluie, neige, etc.).

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>


---

# Loi forte des Grands Nombres
Théorèmes fondamentaux


**Théorème** Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **indépendantes** et **identiquement distribuées (i.i.d.)** et
définies sur le même espace de probabilité $(\Omega, \mathcal{F}, \mathbf{P})$.

En posant $\mu = \mathbb{E}[X_i]$ et $\sigma^2 = \mathbb{V}(X_i) < \infty$.

La variable aléatoire $S_n = \sum_{i=1}^n X_i$ vérifier
$$\lim\limits_{n \rightarrow \infty} \frac{S_n}{n} = \mu,  p.s.$$ 

**p.s.** : presque sûr = "à une infinité dénombrable de points près"

**Application** Méthode de Monte-Carlo : calcul d'une intégrale par des tirages de variables aléatoires.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Théorème Centrale Limite
Théorèmes fondamentaux


**Théorème** Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **i.i.d.**

On note $\mu = \mathbb{E}[X]$ et $\sigma^2 = \mathbb{V}(X)$. On suppose $0 \lt \sigma^2 \lt \infty$.

En posant $S_n = \sum_{i=1}^n X_i$,

$$Y_n = \frac{S_n - n\mu}{\sigma \sqrt(n)} \rightarrow Y$$

**Remarque** Il s'agit d'une convergence en loi : la suite des lois $Y_n$ tend vers $Y$ qui est aussi une loi de probabilité.

**Application** Le Thèorème Centrale Limite donne des indications sur les vitesses de convergence vers la loi assymptotique.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Théorèmes fondamentaux
LGN vs TCL

**LGN vs TCL** 
- Loi des Grand Nombres : convergence des moyennes empiriques vers la moyenne de la loi des v.a.
- Théorème Centrale Limite : convergence asymptotique de la loi sur la moyenne empirique (donne des indications sur une loi limite).


**Modes de convergence**
- Convergence presque sûre (**p.s.**) : $P(\{\omega \in \Omega: \lim\limits_{n \rightarrow \infty\}} X_n(\omega) = X(\omega)) = 1$
- Convergence dans **$\mathrm{L}^P$** : $\lim\limits_{n \rightarrow \infty} \mathbb{E}[|X_n - X|^p] = 0$
- Convergence en probabilité **P**: $\lim\limits_{n \rightarrow \infty} P(|X_n - X| \gt \epsilon) = 0$

Convergence **$\mathrm{L}^p$** $\Rightarrow$ Convergence **P**, Convergence **p.s.** $\Rightarrow$ Convergence **P**  

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---
layout: section

---

## Filtre Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

#  Introduction
Filtre de Kalman

**Filtre de Kalman** (1960), Rudolf Kalman : utilisé pour la première fois pour l'estimation de trajectoire des programmes Apollo

- Comment concilier au mieux l'information disponible (capteur), et la dynamique du système pour contrôler un système dynamique

**Applications** Contrôle, Calage GPS, Filtrage de données.

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Système dynamique
Filtre de Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Estimateur linéaire récurrent
Filtre de Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Estimateur assymptotiquement sans biais
Filtre de Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Construction du Filtre - Prédicteur - Correcteur
Filtre de Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Implémentation ?
Filtre de Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Exemple - Estimation d'une constante aléatoire
Filtre de Kalman

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Filtre de Kalman Etendu
Application du Filtrage Kalman à des systèmes non-linéaires

<p class="absolute bottom-10 right-10 opacity-30 transform">
<SlideCurrentNo /> / <SlidesTotal />
</p>

---

# Filtre de Kalman Ensembliste
EnKF

---
layout: section
---

# Annexes

---
layout: end

---

---