---
title: Apprentissage profond pour l'assimilation de données

---

# Modèles de diffusion pour l'assimilation de données

L'utilisation de Modèles de Diffusion pour les systèmes d'assimilation de donnée est un champ de recherche émergeant, avec des applications prometteuses. L'assimilation variationnelle et les filtres Kalman d'Ensemble constituent le fondement des méthodes d'assimilation (4DEnVar), développées dans les années 1990 et améliorées continuellement depuis pour un usage dans les systèmes opérationnels. Ces systèmes reposent sur la résolution numérique de l'équation de Fokker-Planck : l'équation de diffusion pour une équation différentielle stochastique de diffusion. 

Initialement, les modèles de diffusions s'inspirent de la [thermodynamique hors-équilibre](https://doi.org/10.48550/arXiv.1503.03585) pour débruiter des images et générer des disributions de pixels réalistes, à partir d'un bruit gaussien. Ici, le raisonnement est le même : à partir d'un bruit gaussien (le processus de diffusion), nous apprenons au réseau de neurone des états de l'atmosphère réalistes. Les références suivantes donnent un aperçu de ces méthodes :[](https://doi.org/10.48550/arXiv.2306.10574), [](https://doi.org/10.48550/arXiv.2401.05932),[](https://doi.org/10.48550/arXiv.2506.02249).

# Architecture du modèle de Diffusion

Nous nous intéressons particulièrement aux modèles de débruitage par diffusion, où [**Denoising Diffusion Probabilistic Models**](https://research.google/pubs/score-based-generative-modeling-through-stochastic-differential-equations/). Ces modèles sont une alternative aux Generative Adversarial Networks (GAN) pour la synthèse d'images ou d'enregistrements audio.

```mermaid
graph LR;
  e0(x_T) --> e1(x_{t});
  e1(x_{t}) --> e2(x_{t-1});
  e2(x_{t-1}) --> e4(x_{0});
```

## Denoising Probabilistic Diffusion Models

Les modèles de diffusion sont des modèles à variables latentes, de la forme $\int p_{\theta}(x_{0:T})dx_{1:T}$, où $p_{\theta}(x_{0:T})$ est le processus inverse d'une chaîne de Markov débutant à $p(x_{T}) = \mathcal{N}(x_T; 0, I)$. 

En détail, nous reconnaissons le problème d'inversion bayésienne sur une chaîne de Markov :

$$p_{\theta}(x_{0:T}) = p(x_T) \Pi_{t = 1}^T p_{\theta}(x_{t-1}|x_t), \\
p_{\theta}(x_{t-1}|x_t) = \mathcal{N}(x_{t-1}; \mu_{\theta}(x_t, t), \Sigma_{\theta}(x_t, t))
$$

Avec cette définition, le réseau de neurone entaîné approxime la distribution à posteriori $q(x_{0:T}|x_0)$, le **processus de diffusion** que nous voulons émuler. 

Le processus de diffusion (_forward process_), s'exprime alors comme une chaîne de Markov qui ajoute graduellement du bruit :

$$
q(x_{1:T|x_0}) = \Pi_{t=1}^T q_(x_t|x_{t-1}), \\
q(x_{t}|x_{t-1}) = \mathcal{N}(x_t; \sqrt{1 - \beta}x_{t-1}; \beta_t \bold{I})
$$

:::{note} Retour à l'assimilation de données
    La méthode a de l'intérêt en assimilation de données météorolgie, car à partir des trajectoires 
    perturbées, nous pouvons reconstruire l'observation initiale.
:::



## Forward Diffusion Process

Le "Forward Diffusion Process" fait intervenir une équation différentielle stochastique pour générer du bruit.

$$dx(t) = f(t)x(t) dt + g(t) dw(t)$$

## Reverse Diffusion Process with Sampling for Data Assimilation


$$dx(t) = [f(t)x(t) - g(t)^2 \nabla_{x(t)} log p(x(t))]dt + g(t)dw(t)$$


