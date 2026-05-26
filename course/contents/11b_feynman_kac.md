---
title: Feynman-Kac et advection-diffusion
---

:::{important} 📝 Révision Claude (2026-05-26)
Chapitre **créé** lors de l'extension du cours. À relire et valider :
- Niveau visé : compact mais autonome (élève absent doit pouvoir rattraper).
- Cible pédagogique : *comprendre pourquoi on peut résoudre une EDP d'advection-diffusion en lançant des trajectoires Monte-Carlo*, et inversement *pourquoi un modèle générateur peut s'écrire comme une EDP*.
- TODO : illustration ArchesWeatherGen à compléter (référence + figure).

Voir [`_revisions_claude.md`](_revisions_claude.md).
:::

# Motivation

On a vu deux mondes :
- celui des **trajectoires** $X_t$ régies par une EDS $dX_t = a\,dt + b\,dW_t$ — on les simule par Euler-Maruyama,
- celui des **densités** $p(x,t) = \mathbb{P}(X_t = x)$, régies par des EDP — on les résout par différences finies.

**La formule de Feynman-Kac est le pont** : elle dit que la solution d'une EDP linéaire d'advection-diffusion s'écrit comme l'**espérance d'une fonctionnelle le long des trajectoires d'une EDS bien choisie**.

Conséquences pratiques :
1. On peut **résoudre une EDP par Monte-Carlo** (utile en haute dimension, où les différences finies explosent).
2. On peut **interpréter un schéma stochastique** (e.g. un modèle génératif) comme une EDP — utile pour comprendre, analyser, calibrer.

# Énoncé (cas autonome)

Soit $u(x, t)$ la solution de l'**équation backward d'advection-diffusion** :

$$
\frac{\partial u}{\partial t} + a(x) \frac{\partial u}{\partial x} + \frac{1}{2} b^2(x) \frac{\partial^2 u}{\partial x^2} - r(x)\,u + f(x) = 0
$$

avec condition terminale $u(x, T) = g(x)$.

Alors, en notant $(X_s)_{s \geq t}$ la solution de l'EDS $dX_s = a(X_s)\,ds + b(X_s)\,dW_s$ partant de $X_t = x$ :

$$
\boxed{
u(x, t) = \mathbb{E}\left[\,g(X_T)\,e^{-\int_t^T r(X_s)\,ds} + \int_t^T f(X_s)\,e^{-\int_t^s r(X_u)\,du}\,ds \;\middle|\; X_t = x \right]
}
$$

**Lecture** :
- $g(X_T)$ : **payoff terminal** — la condition au bord en temps,
- $r(x)$ : **taux d'amortissement** (e.g. discount, dissipation),
- $f(x)$ : **terme source**,
- $a, b$ : drift et diffusion de l'EDS associée.

Le cas $r = 0$, $f = 0$ donne la version "pure" :

$$u(x, t) = \mathbb{E}[g(X_T) \mid X_t = x]$$

c'est-à-dire que **la solution est la moyenne du payoff terminal**, propagée à rebours dans le temps le long des trajectoires.

# Lien avec l'advection-diffusion

L'opérateur

$$\mathcal{L} = a(x)\,\partial_x + \tfrac{1}{2} b^2(x)\,\partial_{xx}$$

est le **générateur infinitésimal** du processus $X_t$. C'est précisément l'opérateur de l'équation d'**advection-diffusion** :

- $a(x)\,\partial_x u$ : transport de $u$ à la vitesse $a$,
- $\tfrac{1}{2} b^2 \partial_{xx} u$ : diffusion de coefficient $\tfrac{1}{2} b^2$.

En **forward** (densité $p$, voir [Fokker-Planck](10_diffusion_models.md)) on a $\partial_t p = \mathcal{L}^* p$ avec adjoint formel.
En **backward** (espérance $u$) on a $\partial_t u + \mathcal{L} u = 0$.

:::{note} Forward vs backward — où regarde-t-on ?
- **Fokker-Planck (forward)** : on connaît $p_0$, on calcule $p_t$ — la densité actuelle.
- **Feynman-Kac (backward)** : on connaît $g$ en $T$, on calcule $u(x, t)$ — la "valeur" en $(x, t)$ d'un événement futur. C'est exactement le pricing d'option et la programmation dynamique.
:::

# Algorithme — Résoudre une EDP par Monte-Carlo

Pour calculer $u(x, t)$ en un point donné :

```
Entrées : a, b, g, [r, f], x, t, T, Δt, N
1. Pour i = 1..N :
   a. X ← x ;  t' ← t ;  D ← 0 (poids log)
   b. Tant que t' < T :
        ΔW ~ N(0, Δt)
        X  ← X + a(X)·Δt + b(X)·ΔW          # Euler-Maruyama
        D  ← D - r(X)·Δt                     # accumulation discount
        t' ← t' + Δt
   c. payoff_i ← g(X) · exp(D)
2. Renvoyer (1/N) Σ payoff_i
```

Coût : $\mathcal{O}(N \cdot T/\Delta t)$. **Indépendant de la dimension d** de l'espace d'état — c'est l'argument décisif en grande dimension.

# Illustration — Modèles génératifs météo (ArchesWeatherGen)

<!-- TODO-CLAUDE 2026-05-26: à enrichir.
     Référence : ArchesWeatherGen (Couairon et al., 2024)
     Lien à faire : le modèle de diffusion conditionnelle peut être lu
     comme un opérateur d'advection-diffusion appris ; l'échantillonnage
     correspond à intégrer une EDS dont le drift est le score appris. -->

**ArchesWeatherGen** (Couairon et al., 2024) est un modèle génératif pour la prévision météo conditionnelle. Il échantillonne des trajectoires d'états atmosphériques cohérentes avec une condition (état initial, observations partielles).

Du point de vue Feynman-Kac :
- L'**EDP** sous-jacente est une équation d'advection-diffusion sur la densité conditionnelle de l'atmosphère,
- L'**EDS** d'échantillonnage propage des trajectoires dont le drift est appris (score),
- L'**espérance** $\mathbb{E}[g(X_T) \mid \text{condition}]$ donne des grandeurs dérivées (probabilité d'événement extrême, ensemble de prévisions).

C'est l'inversion exacte de la situation EDP→Monte-Carlo : ici on a appris un drift stochastique qui *est* l'EDP, et on échantillonne pour estimer des fonctionnelles.

# Lien avec l'assimilation de données

L'assimilation cherche $p(x_{1:L} | y)$ — densité conditionnelle de trajectoires sachant des observations. Cette densité satisfait une équation backward du type Feynman-Kac (avec $r$ relié à la vraisemblance des observations). Les filtres particulaires sont littéralement une mise en œuvre Monte-Carlo de cette formule.

Voir [`97_data_assimilation.md`](97_data_assimilation.md) pour le formalisme bayésien correspondant.

# Synthèse — Feynman-Kac en bref

**Idée** : la solution $u(x,t)$ d'une EDP backward d'advection-diffusion est l'espérance d'un payoff terminal $g(X_T)$ calculée sur des trajectoires EDS partant de $(x,t)$.

**Formule** : pour $\partial_t u + a \partial_x u + \tfrac{1}{2} b^2 \partial_{xx} u - r u + f = 0$, $u(x,T) = g(x)$ :

$$u(x,t) = \mathbb{E}\left[g(X_T) e^{-\int_t^T r\,ds} + \int_t^T f(X_s)\,e^{-\int_t^s r}\,ds\right]$$

**Pratique** : on résout par Monte-Carlo via Euler-Maruyama sur l'EDS associée. Coût indépendant de la dimension.

**Pourquoi c'est utile** :
- Pricing d'options (finance) — espérance risque-neutre d'un payoff,
- Résolution d'EDP en haute dimension (PDE en dim > 4 où les méthodes maillées explosent),
- Lecture des modèles génératifs (score-based) comme des opérateurs d'advection-diffusion appris.

**Pour aller plus loin** : [Fokker-Planck et modèles de diffusion](10_diffusion_models.md) — la version forward, qui régit les densités plutôt que les espérances.

# Références

Karatzas, I., & Shreve, S. — *Brownian Motion and Stochastic Calculus*, chapitre 5.

Pham, H. — *Continuous-time Stochastic Control and Optimization with Financial Applications* (chapitres Feynman-Kac et HJB).

Couairon, G., et al. — *ArchesWeatherGen: a generative weather forecasting model*, 2024 (à confirmer / mettre à jour la référence).
