---
title: Rappels de probabilités et statistiques

---

# Lois usuelles

:::{note} Loi uniforme
$X$ suit une **loi uniforme** sur $[a,b] \in \mathbb{R}$ si elle admet la densité de probabilité $f: \mathbb{R} \mapsto \mathbb{R}$

$$ 
\forall x \in \mathbb{R}, f(x) = \begin{array}{rcl}
\frac{1}{b-a} & si\,  x \in [a, b] \\
        0 & sinon
\end{array} $$
:::

:::{note} Loi exponentielle
$X$ suit une **loi exponentielle** $\mathcal{E}(\lambda)$ ($\lambda \gt 0$) si elle admet la densité de 
probabilité $f: \mathbb{R} \mapsto \mathbb{R}$ 

$$\forall x \in \mathbb{R}, f(x) = \begin{array}{rcl} 
\lambda e^{-\lambda x} & si \, x \geq 0 \\
0  & sinon
\end{array} $$

- Espérance : $\mathbb{E}[X] = 1/\lambda$
- Variance : $\mathbb{V}[X] = 1/\lambda^2$
:::


:::{note} Loi Normale
$X$ suit une **loi normale** $\mathcal{N}(m, \sigma^2), ((m, \sigma) \in \mathbb{R}\,  \times  \mathbb{R_+})$

$$ \forall x \in \mathbb{R}, f(x) = \frac{1}{\sqrt{2\pi} \sigma} exp(-\frac{(x - m)^2}{2\sigma^2})$$

- Espérance :$\mathbb{E}[X] = m$
- Variance : $\mathbb{V}[X] = \sigma^2$
:::

:::{note} Loi Gamma
$X$ suit une **loi gamma** $\gamma(p, \lambda), (p \gt 0, \lambda \gt 0)$ si sa densité est 

$$ \forall x \in \mathbb{R}, f(x) = \begin{array}{rcl} 
\frac{\lambda}{\Gamma(p)}(\lambda x)^{p-1}e^{-\lambda x} & si & x \geq 0 \\
0  & sinon
\end{array} $$

- Espérance : $\mathbb{E}[X] = p/\lambda$$, 
- Variance : $\mathbb{V}[X] = p/\lambda^2$
:::

## Exemple d'application : modélisation microphysique

En microphysique (modélisation des nuages et interactions entre hydrométéores), la distribution de concentration des gouttes
par rapport à leur diamètre est donnée par une loi exponentielle (Loi de Marshall-Palmer).

:::{note} Distribution de Marshall-Palmer
$$ N(D) dD = N_0 e^{-\lambda D} dD$$

où la densité de probabilité est $f(D) = e^{-\lambda D}$.
- D: diamètre des gouttes dans une parcelle d'air
- N: concentration de gouttes dans une parcelle d'air
:::


On peut alors lier les moments de la distribution avec les caractéristiques physiques des gouttes :

- **Concentration de goutelettes** $N_c$ et **moment d'ordre 0** : $N = \int_0^{\infty} f(D) dD$ 
- **Contenu en eau** $q_c$ et **moment d'ordre 3** : $L = \frac{\pi \rho_w}{6} \int_0^{\infty} D^3 f(D) dD$ _(lié au volume des gouttes)_
- **Réflectivité radar** $\Sigma_c$ et **moment d'ordre 6** : $R \propto \int_0^{\infty} D^6 f(D) dD$ _(lié à la diffusion de Rayleigh)_

En modélisation microphysique, "le jeu" est de choisir le nombre de moments à modéliser (**fermeture**),
la discrétisation sur l'espace des diamètres (**échantillonage**), pour représenter les interactions possibles
entre les gouttes (de pluie, de neige, etc.).

# Théorèmes fondamentaux

## Loi forte des Grands Nombres

**Théorème**

:::{note} Théorème
Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **indépendantes** et **identiquement distribuées (i.i.d.)** et définies sur le même espace de probabilité $(\Omega, \mathcal{F}, \mathbf{P})$.


En posant $\mu = \mathbb{E}[X_i]$ et $\sigma^2 = \mathbb{V}(X_i) < \infty$.

La variable aléatoire $S_n = \sum_{i=1}^n X_i$ vérifie
$$\lim_{n \rightarrow \infty} \frac{S_n}{n} = \mu,  p.s.$$
:::

**p.s.** : presque sûr = "à une infinité dénombrable de points près"

**Application** Méthode de Monte-Carlo : calcul d'une intégrale par des tirages de variables aléatoires.

Pour $U$ variable aléatoire uniforme sur $[0,1]$, $\lim\limits_{n \rightarrow \infty} \sum f(U_i) = \mathbb{E}[f(U)] = \int_0^1 f(x) dx$

## Théorème Centrale Limite

:::{note} Théorème
Soit $(X_n)_{n \in \mathbb{N}}$ une suite de variables aléatoires réelles **i.i.d.**

On note $\mu = \mathbb{E}[X]$ et $\sigma^2 = \mathbb{V}(X)$. On suppose $0 \lt \sigma^2 \lt \infty$.

En posant $S_n = \sum_{i=1}^n X_i$,

$$Y_n = \frac{S_n - n\mu}{\sigma \sqrt(n)} \underset{n \rightarrow \infty}{\rightarrow} Y \sim \mathcal{N}(0, 1)$$
:::

**Remarque** Il s'agit d'une convergence en loi : la suite des lois $Y_n$ tend vers $Y$ qui est aussi une loi de probabilité.

**Application** Le Thèorème Centrale Limite donne des indications sur les vitesses de convergence vers la loi assymptotique.

## Loi des Grands Nombres vs Théorème Centrale Limite

**Loi des Grand Nombres** : convergence des moyennes empiriques vers la moyenne de la loi des variables aléatoires. La LGN donne un résultat sur l'espérance.


**Théorème Centrale Limite** : convergence asymptotique de l'estimateur vers une loi normale limite.
Le TCL donne un résultat sur la loi limite et donc sa dispersion (et sa **variance**).

## Estimation bayésienne

L'estimation bayésienne est fondée sur le Théorème de Bayes

:::{note} Théorème de Bayes

Etant donné deux évènements où l'évènement B est de probabilité non-nulle $P(B) \neq 0$,

$$P(A|B) = \frac{P(B|A)P(A)}{P(B)}$$

:::

En estimation bayésienne, on cherche à construire un estimateur (bayésien), c'est à dire une distribution conditionnée aux données d'apprentissage :

$$P(\theta | data ) = \frac{P(\theta) \cdot P(data | \theta)}{P(data)}$$

où :
- $P(\theta)$ est la **distribution à priori** des paramètres du modèle, ou **prior**
- $P(data| \theta)$ est la **vraisemblance du modèle** par rapport aux données, ou **likelihood** en anglais. 
- $P(\theta | data)$ est la **distribution à posteriori** des paramètres du modèle, ou **posterior**
- $P(data)$ est la loi ou distribtion marginale des données.

:::{note} Negative Log-Likelihood (NLL)
La _**Negative Log Likelihood**_ est une fonction de coût (loss) couramment utilisée en _deep learning_.
:::

- Le problème principal : estimer **la loi marginale** $P(data)$

**En pratique** : on cherche à éviter le calcul de la loi marginale des données.

- Cas discret :

$$P(data) = \sum_{\theta} P(data | \theta) \times P(\theta)$$

- Cas continu :

$$P(data) = \int_{\theta} P(data | \theta) \times P(\theta) d\theta$$

Dans le cas continu, calculer $P(data)$ peut être impossible selon la dimension du problème.

**Exercice**

Une loi Normale ($\theta = (\mu, \sigma)$) et un échantillon dont les éléments sont des réalisations de cette loi.


