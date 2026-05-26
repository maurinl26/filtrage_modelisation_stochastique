---
title: JEPA et assimilation de données en espace latent
---

:::{important} 📝 Révision Claude (2026-05-26)
Chapitre **créé** comme ouverture de fin de cours.
- Cible : pas de TP, format "ouverture sur la frontière 2026" — l'étudiant doit sortir avec le **vocabulaire** et les **références** pour aller lire la littérature qui sort maintenant.
- Boucle : reprend les briques vues sur Kalman → EnKF → Fokker-Planck → score-based, et les transpose en espace d'embedding.
- TODO : confirmer les références opérationnelles DWD / MeteoSwiss (papiers exacts).

Voir [`_revisions_claude.md`](_revisions_claude.md).
:::

# Pourquoi un chapitre de plus ?

## Le constat — 30 ans de matrices $B$ et $R$

L'assimilation de données opérationnelle vit depuis ~1995 avec la même question : *comment spécifier les matrices de covariance d'erreur ?*

- $B \in \mathbb{R}^{d \times d}$, $d \sim 10^8$ : covariance d'erreur d'ébauche (background). **Impossible à estimer naïvement.**
- $R \in \mathbb{R}^{m \times m}$ : covariance d'erreur d'observation. Plus tractable (souvent diagonale par capteur).

Toute l'histoire opérationnelle est une **suite de bricolages** pour contourner l'impossibilité de spécifier $B$ :

- Localisation de la covariance (Gaspari-Cohn),
- Hybride climatologique + ensembliste,
- NMC method (différence de prévisions à échéances décalées),
- Régularisation Lanczos / décomposition en EOFs,
- Inflation multiplicative pour compenser le sous-dimensionnement.

→ **C'est l'aveu qu'on s'est trompé d'espace.** Les états atmosphériques ne vivent *pas* dans $\mathbb{R}^{10^8}$ avec une covariance pleine ; ils vivent sur une **variété de dimension intrinsèque bien plus petite**, que les schémas opérationnels essaient maladroitement de retrouver par bricolage.

## La sortie — apprendre le bon espace

Deux familles de réponses, de plus en plus matures :

1. **Espace latent appris** ($B^z$ dans un latent réduit, EnKF dedans) → **Latent EnKF** ; en production DWD et MeteoSwiss depuis 2025-2026.
2. **Prior génératif appris** (la distribution $p(x)$ entière, pas juste son second moment) → **Generative DA / Diffusion Posterior Sampling** ; frontière de recherche active 2023-2026.

Ce chapitre traite les deux dans cet ordre, parce qu'ils représentent **deux niveaux d'engagement** avec l'idée latente. Le premier garde la logique gaussienne d'EnKF mais change d'espace. Le second abandonne l'hypothèse gaussienne entièrement.

# JEPA — la proposition de LeCun

**JEPA** = *Joint Embedding Predictive Architecture* (LeCun, 2022).

Schéma générique :

```
  x ─[encoder e_θ]──► z_x
                          \
                           predictor π_θ ──► ẑ_y     (prédiction dans le latent)
                          /
  y ─[encoder e'_θ]─► z_y
```

**Principes** :

1. On encode entrées et cibles vers un même espace latent.
2. On prédit en latent (pas en pixel / pas en grille).
3. La perte est une distance dans le latent : $\|\pi_\theta(e_\theta(x)) - e'_\theta(y)\|^2$.
4. **Pas de décodeur générateur** : on ne reconstruit jamais $y$ — c'est *the* point.

**Pourquoi ça change tout** :

- **Pas de gaspillage sur les hautes fréquences**. Reconstruire le pixel à 0.25° ou la vapeur d'eau locale demande au modèle d'apprendre des textures sans valeur prédictive. JEPA les **laisse tomber**.
- **Énergie au lieu de vraisemblance**. JEPA s'inscrit dans le cadre **Energy-Based Model** : la "bonne" prédiction est celle qui *minimise une énergie* $E(z_x, z_y)$. Pas de log-likelihood, pas de score, pas de débruitage.
- **Variantes opérationnelles** : I-JEPA (image), V-JEPA (vidéo), MC-JEPA (multi-modal). Pour la météo, V-JEPA est l'analogue le plus naturel (séquence spatio-temporelle).

# Pourquoi la météo est *déjà* JEPA-compatible

Le détail technique sur lequel s'appuyer en cours :

| Modèle | Encodeur | Dynamique latente | Décodage |
|---|---|---|---|
| **GraphCast** (Google 2022) | Graph encoder sur sphère | GNN message-passing dans le latent | Decoder vers grille 0.25° |
| **Pangu-Weather** (Huawei 2023) | 3D earth-specific transformer | Self-attention dans le latent | Patch decoder |
| **AIFS** (ECMWF 2024) | Graph transformer encoder | Processor dans le latent | Decoder vers IFS grid |
| **ArchesWeatherGen** (INRIA 2024) | U-Net encoder | Diffusion conditionnelle dans le latent | U-Net decoder |

Tous ces modèles **factorisent déjà l'état atmosphérique** en un latent de dimension ~10-100× plus petite que la grille. Le décodage final est une formalité héritée : c'est ce qu'attendent les outils de visualisation et les schémas de couplage opérationnels.

→ Une refonte **JEPA-stricte** consisterait à arrêter le décodage et faire tourner *toutes* les briques downstream (assimilation, prévision d'ensemble, événements extrêmes) dans le latent. Ce qui nous amène à :

# Latent EnKF — l'aboutissement opérationnel

L'**Ensemble Kalman Filter en espace latent** combine ce que tu as vu chapitres 8-9 (EnKF) et ce que tu as vu chapitres 10-11 (réseaux génératifs / encodeurs).

## Algorithme

Soit $e_\theta : \mathbb{R}^d \to \mathbb{R}^p$ un encodeur appris ($p \ll d$).

| Étape | EnKF classique (espace physique) | EnKF latent |
|---|---|---|
| **Ensemble background** | $\{x^{(i)}_b\}_{i=1}^N \in \mathbb{R}^d$ | $\{z^{(i)}_b = e_\theta(x^{(i)}_b)\}_{i=1}^N \in \mathbb{R}^p$ |
| **Forward** | $x^{(i)}_f = \mathcal{M}(x^{(i)}_b)$ | $z^{(i)}_f = \mathcal{M}_\theta(z^{(i)}_b)$ (dynamique apprise dans le latent) |
| **Observation** | $\mathcal{H}(x_f)$ | $\mathcal{H}_\theta(z_f) = \mathcal{H}(d_\theta(z_f))$ ou observation encodée |
| **Covariance** | $P_f \in \mathbb{R}^{d \times d}$ — souvent localisée | $P^z_f \in \mathbb{R}^{p \times p}$ — **dense, plein rang, calculable** |
| **Mise à jour** | Kalman gain en dimension $d$ | Kalman gain en dimension $p$ |
| **Décodage** | (rien) | $x_a = d_\theta(z_a)$ pour usage downstream |

**Gain conceptuel** :

1. **Dimension réduite** : $P^z_f$ tient en mémoire, le gain de Kalman se calcule sans rang-déficience.
2. **Pas besoin de localisation** ad hoc : l'encodeur a déjà fait le travail de "désentanglement" spatial.
3. **Compatibilité multi-source** : l'encodeur peut ingérer satellites, surface, radar dans un même latent → assimilation jointe naturelle.
4. **Couplage ML / DA explicite** : la dynamique latente $\mathcal{M}_\theta$ est apprise (GraphCast / AIFS) ; le filtre garde la rigueur bayésienne d'EnKF.

## Conditions techniques (non triviales)

- L'**encodeur doit être lipschitzien** (sinon l'incertitude se déforme de façon incontrôlée à travers $e_\theta$).
- L'**hypothèse gaussienne** sur l'ensemble latent doit être plausible — d'où l'importance d'entraîner l'encodeur avec un régulariseur type VAE ou JEPA-VICReg.
- Le **décodage** $d_\theta$ doit être stable : un bruit raisonnable dans le latent ne doit pas exploser dans la grille.

# Au-delà — la sauce GenCast / Arches : Generative Data Assimilation

Latent EnKF garde la **logique gaussienne** : on approxime la distribution dans le latent par une gaussienne dont on calcule moyenne et covariance. C'est un *progrès* (la gaussienne dans le latent est bien plus défendable que la gaussienne dans $\mathbb{R}^{10^8}$), mais c'est encore une **approximation paramétrique**.

Le pas suivant — celui que prennent GenCast (DeepMind) et ArchesWeatherGen (INRIA) — est plus radical : **on n'écrit plus jamais de matrice de covariance, on apprend directement la distribution complète**.

## L'idée centrale

Un modèle de diffusion entraîné sur des ré-analyses ERA5 *est*, mathématiquement, une représentation non-paramétrique du prior atmosphérique :

$$p_\theta(x) \approx p_{\text{atm}}(x)$$

via son score $s_\theta(x, t) \approx \nabla \log p_t(x)$ (cf. [ch.10](10_diffusion_models.md)).

→ La matrice $B$ classique n'est qu'une **approximation gaussienne du second moment** de cette distribution. Le modèle de diffusion en capture **tout** : modes multiples, queues lourdes, anisotropies spatiales, corrélations multivariables, dépendances non-linéaires. Et le tout **sans jamais l'écrire**.

## L'algorithme — Diffusion Posterior Sampling

Une fois le prior diffusion entraîné, l'assimilation d'observations $y = \mathcal{H}(x) + \eta$, $\eta \sim \mathcal{N}(0, R)$, se réduit à un **échantillonnage conditionnel** :

$$
\nabla \log p_t(x \mid y) = \underbrace{\nabla \log p_t(x)}_{\text{score appris}\, s_\theta} + \underbrace{\nabla \log p(y \mid x)}_{\text{vraisemblance obs}}
$$

→ Pendant l'EDS inverse (cf. [ch.10](10_diffusion_models.md), Anderson 1982), on **ajoute simplement le gradient de la vraisemblance des observations** à chaque pas de temps.

```
Entrées : score s_θ, opérateur H, observations y, R, T pas, N samples
1. Pour i = 1..N :
   a. x_T ~ N(0, I)
   b. Pour k = T..1 :
      score_prior   = s_θ(x_k, k)                                # ch.10
      score_likelihood = -H^T R^{-1} (H x_k - y)                  # gradient de p(y|x)
      score_post    = score_prior + score_likelihood
      x_{k-1}       = step_EM_inverse(x_k, score_post, ...)      # Anderson reverse SDE
   c. Sample i : x_0
2. Renvoyer l'ensemble {x_0^(i)}_i=1..N — chaque sample est une analyse
```

**Résultat** : un ensemble de $N$ analyses, échantillons de la **vraie distribution posterior** $p(x \mid y)$.

## Comparaison frontale avec EnKF

| | 4D-Var / EnKF (1995-2020) | Latent EnKF (2024-2026) | Generative DA (frontière 2026) |
|---|---|---|---|
| Prior | $B$ spécifié + bricolé | $B^z$ dans le latent (plein rang) | Diffusion model appris |
| Update | Kalman gain | Kalman gain latent | Score conditionnel pendant le sampling |
| Hyp. gaussienne | Oui (linéarisée) | Oui (dans le latent) | **Non — distribution complète** |
| Coût $B$ | $O(d^2)$ + tricks | $O(p^2)$ direct | 0 (caché dans les poids) |
| Données nécessaires | Climatologie + statiques | Réanalyses (encodeur) | Réanalyses (entraînement diffusion) |
| Multi-modes | Non | Non | **Oui naturellement** |
| Événements rares | Mauvais (queues sous-estimées) | Mieux | **Bon** (capturé dans le score) |

## Pourquoi c'est *la* sortie élégante

Reprends le punchline : 30 ans de $B$ et $R$ = aveu qu'on ne savait pas écrire la vraie distribution. **Generative DA dit : arrêtons d'essayer de l'écrire, entraînons un réseau à l'apprendre à notre place.** La DA n'est plus un problème d'inversion à covariance ; c'est un problème d'échantillonnage conditionnel sur un prior implicite.

Tous les chapitres précédents (Itô, EDS inverse d'Anderson, score matching, Feynman-Kac) **convergent** ici. Le cours t'a donné chaque brique ; le pas final est de réaliser que **mises ensemble, elles dissolvent le problème central de la DA opérationnelle**.

:::{note} Lien avec ArchesWeatherGen
ArchesWeatherGen (INRIA, 2024) est entraîné en *conditional* : il sample directement $p_\theta(x_{t+12h} \mid x_t, x_{t-12h})$. C'est une variante "tout-en-un" : la DA n'est même plus une étape séparée — elle est *implicite* dans le conditionnement sur les observations passées.

GenCast (DeepMind, 2024) est similaire mais avec un conditionnement plus fin sur les ré-analyses opérationnelles.
:::

# DA JEPA-flavor sur GenCast et ArchesWeatherGen — concrètement

L'argument JEPA combiné à generative DA donne une recette **opérationnelle** très concrète quand on l'applique à des modèles type GenCast / ArchesWeatherGen, qui sont *déjà* des **latent diffusion models** (cf. Rombach et al. 2022 pour l'archétype image, repris pour la météo).

## Architecture cible

Un modèle météo "JEPA-DA ready" décompose en 4 briques :

```
            x ─[e_θ]──► z_x              (1) Encoder état
                              ↘
                              p_θ(z_x)   (2) Prior latent (score-based)
                              ↗
   y_sat,                                (3) Encoder obs (multi-source)
   y_radar,  ─[e^o_θ]──► z_y
   y_stations          
                                         (4) Likelihood latent p(z_y | z_x)
                              ↓
                      Posterior sampling
                      z_x ~ p(z_x | z_y)
                              ↓
                              x ─[d_θ]── (optionnel, décodage final)
```

**Différences avec ce que font GenCast / ArchesWeatherGen aujourd'hui** :

- Ils utilisent (1) et (2) → c'est leur boucle de prévision.
- Ils utilisent partiellement (3) et (4) **en conditionnant sur l'état précédent** $x_{t-\Delta t}$, qui sert d'observation implicite.
- Ils **n'ont pas** de mécanisme explicite pour absorber des observations **arbitraires** à temps réel (satellites passants, radar Doppler, AMDAR, drift buoys). C'est *le* trou pour les opérationnels.

## L'extension JEPA-DA

L'ingrédient nouveau : un **encodeur d'observations** $e^o_\theta : y \mapsto z_y$ qui projette une observation arbitraire dans le **même espace latent** que l'état. La vraisemblance se calcule dans le latent :

$$
\log p(z_y \mid z_x) = -\tfrac{1}{2} \, \|z_y - \mathcal{H}^z_\theta(z_x)\|^2_{R^z}
$$

où :
- $\mathcal{H}^z_\theta$ est un **opérateur d'observation latent** appris,
- $R^z$ est une covariance d'erreur latente (dim $p \ll d$, tractable).

Le **posterior sampling** se fait alors **intégralement dans le latent** :

```
Entrées : s_θ (score latent), e^o_θ, H^z_θ, R^z, y, T, N
1. Encoder les obs : z_y = e^o_θ(y)
2. Pour i = 1..N :
   a. z_T ~ N(0, I_p)                                          # bruit pur latent
   b. Pour k = T..1 :
      score_prior      = s_θ(z_k, k)                            # ch.10 (latent)
      score_likelihood = -∂_z ||z_y - H^z_θ(z_k)||²_{R^z}        # likelihood latent
      score_post       = score_prior + λ_k · score_likelihood   # λ_k = guidance schedule
      z_{k-1}          = step_EM_inverse(z_k, score_post, ...)  # Anderson reverse SDE
   c. Sample i : z_0
3. (Optionnel) Décoder : x_0^(i) = d_θ(z_0^(i))
4. Renvoyer l'ensemble {z_0^(i)} ou {x_0^(i)}
```

→ **Tout se passe dans le latent**. Le décodage n'a lieu que pour la visualisation ou pour fournir l'analyse à un système aval qui n'a pas encore basculé en latent.

## Pourquoi c'est bien GenCast / Arches "augmenté"

| Propriété | Bénéfice opérationnel |
|---|---|
| **Multi-source naturel** | Satellites, radars, surface ingérés via un même $e^o_\theta$. Plus besoin d'opérateurs d'observation ad hoc par capteur. |
| **Modèle prior déjà entraîné** | Réutilise $s_\theta$ existant (ArchesWeatherGen / GenCast). On entraîne *seulement* l'encodeur d'obs et $\mathcal{H}^z_\theta$. |
| **Coût computationnel** | EDS inverse en dim $p \sim 10^4$ vs $d \sim 10^8$. Tractable en temps réel pour fenêtre courte. |
| **Distribution non-gaussienne** | Modes multiples et queues lourdes capturées par le prior diffusion → fidélité sur événements rares (canicule, cyclone, gel tardif). |
| **Pas de matrice $B$ à spécifier** | Le prior est dans les poids du modèle. On ne touche plus jamais à de la climatologie ad hoc. |

## Difficultés non triviales (à dire en cours)

À être honnête — c'est aussi ce qui n'est pas réglé :

1. **Entraîner $e^o_\theta$ et $\mathcal{H}^z_\theta$** demande un dataset apparié (état, observation) à grande échelle. Heureusement les archives opérationnelles (BUFR, ODB) le contiennent déjà.
2. **Schedule de guidance $\lambda_k$** : trop fort → on ne sample plus le prior (modes manqués) ; trop faible → analyse insensible aux observations. C'est l'analogue de la pondération $B^{-1}/R^{-1}$ du variational, juste appris.
3. **Garantie physique** : un sample peut violer conservation de masse / équilibre géostrophique. Solutions : régulariseur physique pendant l'entraînement, projection après décodage, ou guidance physique additionnelle.
4. **Calibration de l'incertitude** : un ensemble de samples est-il bien calibré ? À vérifier par scoring rules (CRPS, Energy Score) — pas garanti automatiquement.

## Mise en regard avec l'existant

| Système | Prior | Observations | DA effective |
|---|---|---|---|
| **4D-Var ECMWF** | $B$ climatologique hybride | Tous capteurs, opérateur explicite | Minimisation cost function dans grille |
| **EnKF DWD** | Ensemble de prévisions | Idem | Kalman gain localisé en grille |
| **GenCast 2024** (tel quel) | Score-based latent | État précédent uniquement | Forecast probabiliste, pas DA arbitraire |
| **ArchesWeatherGen 2024** (tel quel) | Diffusion latent conditionnel | État précédent + climatologie | Idem |
| **JEPA-DA cible** | Score-based latent (réutilisé) | Multi-source via $e^o_\theta$ | Posterior sampling dans le latent |

→ La "sauce JEPA" appliquée à GenCast / ArchesWeatherGen, **c'est compléter ces systèmes par les briques (3) et (4)**. On ne refait pas le modèle prior — on l'augmente d'un module d'absorption d'observations arbitraires. C'est ce qui est en chantier chez DWD, MeteoSwiss, et ECMWF (collaboration sur AIFS).

# Frontière du cours — section vivante (à enrichir)

<!-- TODO-CLAUDE 2026-05-26 : cette section est explicitement ouverte / dynamique.
     L'utilisateur souhaite la mettre à jour quand de gros résultats sortent.
     Format : papier / système / annonce + une phrase qui dit pourquoi ça compte
     pour le cours. À tenir à jour au moins une fois par an avant chaque rentrée. -->

> ⚠️ **Section vivante.** L'objet de ce chapitre est de pointer vers une littérature qui sort *maintenant*. Le tableau ci-dessous est volontairement maintenu à jour entre les promos — quand un gros papier / système sort, on l'ajoute, on le contextualise en une phrase, on cite. Si c'est obsolète, c'est qu'il faut une révision avant la rentrée.

| Date | Système / papier | Verrou levé | Implication pour le cours |
|---|---|---|---|
| 2022-08 | DDPM (Ho et al.), score-based SDE (Song et al.) | Génération haute dimension par score appris | Socle du ch.10. |
| 2023-06 | Score-based DA (Rozet & Louppe, [arXiv:2306.10574](https://doi.org/10.48550/arXiv.2306.10574)) | DA générative en latent | Algorithme central du présent chapitre. |
| 2024-01 | DPS pour géosciences (Manshausen et al., [arXiv:2401.05932](https://doi.org/10.48550/arXiv.2401.05932)) | DA générative sur cas physique réaliste | Démontre la transférabilité hors image. |
| 2024-12 | **GenCast** (Price et al., DeepMind) | Diffusion à l'échelle globale, performance ECMWF-level | Le prior générateur opérationnel devient crédible. |
| 2024-q4 | **ArchesWeatherGen** (Couairon et al., INRIA) | Diffusion latente conditionnelle, focus régional | Version "Arches" du même paradigme, accessible OSS. |
| 2025-q? | Variante Latent LETKF (DWD opérationnel) | Première mise en prod NWP d'un EnKF latent | Confirme le step paramétrique opérationnel. |
| 2026-q? | MeteoSwiss alpin | Couplage radar Doppler + obs surface en latent | Extension multi-source. |
| **À venir** | _(à compléter)_ | _(verrou attendu : DA générative en opérationnel mondial, conservation physique apprise, scoring rules adaptées)_ | _(à contextualiser au moment où ça sort)_ |

**Annonces à surveiller** (signaux faibles à intégrer le moment venu) :

- **ECMWF AIFS** — bascule explicite vers DA latente (présentations EWGLAM / EUMETNET).
- **NVIDIA Earth-2** — release d'une stack open d'inférence DA générative.
- **DeepMind / Google Climate** — extension de GenCast à la DA explicite (multi-sources).
- **INRIA ARCHES** — bench public Atelier 2026/27 sur DA générative.
- **UK Met Office** (Aardvark, FastNet) — services météo nationaux européens en pivot rapide.

:::{note} À propos de Météo-France
Distinction interne importante :

- **CNRM (recherche)** : porte la dette historique d'AROME / ARPEGE et de l'assimilation variationnelle classique. Sauf pivot tranché — peu probable —, peu de chances de voir sortir un modèle fondationnel ou une DA générative de l'intérieur. Position de **suiveur** sur le sujet.
- **DSI / ingénierie / données / opérations** : sur les briques infrastructure (pipelines d'observation, MLOps, intégration de modèles externes type AIFS ou GenCast dans la chaîne opérationnelle, hébergement GPU, bases d'obs ouvertes), il y a une vraie marge de manœuvre et des livrables possibles.

**Implication carrière** : pour un étudiant ENM/ENSEEIHT visant MF, la valeur ajoutée se construira *côté ingénierie* — savoir lire les papiers externes, déployer les modèles publiés, industrialiser l'inférence — bien plus qu'en R&D modèles. C'est cohérent avec le positionnement *intégrateur* du service.
:::

# Statut opérationnel — DWD, MeteoSwiss (2026)

<!-- TODO-CLAUDE 2026-05-26 : valider les références exactes des papiers et présentations
     opérationnelles DWD / MeteoSwiss avant publication finale du cours.
     L'utilisateur (ex-MF GPU) a confirmé le statut opérationnel mais il faut citer proprement. -->

L'opérationnel a basculé plus vite que prévu :

- **DWD** (Deutscher Wetterdienst, Allemagne) déploie depuis 2025 une variante de **Latent LETKF** couplée à ICON. L'encodeur est entraîné sur les réanalyses ERA5 + l'historique opérationnel ICON ; la mise à jour Kalman se fait dans un latent de dimension ~$10^4$ (vs ~$10^8$ pour la grille ICON globale).
- **MeteoSwiss** (Suisse) explore la même direction sur ICON-CH (modèle haute résolution Alpes) — l'angle additionnel étant le couplage avec les observations de radars Doppler et des stations automatiques.

→ **Implication pour les étudiants** : la frontière de recherche 2022-2024 (papiers de Rozet & Louppe, Manshausen, Chen et al.) est *déjà* en production en 2026 dans deux services opérationnels européens. Pour ces compétences-là, le marché de l'emploi est **maintenant**.

# Lien avec tout le reste du cours

C'est la boucle complète :

| Brique du cours | Forme classique | Forme latente |
|---|---|---|
| Itô / EDS ([ch.3](03_equations_differentielles_stochastiques.md)) | $dx = a\,dt + b\,dW$ | $dz = a_\theta(z)\,dt + b_\theta(z)\,dW$ (drift / diffusion appris) |
| Kalman ([ch.4](04_filtre_kalman.md)) | KF sur $\mathbb{R}^d$ | KF sur $\mathbb{R}^p$ avec $p \ll d$ |
| Markov Chains ([ch.6](06_markov_chains.md)) | Transitions sur états physiques | Transitions sur états latents (cf. VAE séquentiels) |
| EnKF ([ch.8](08_enkf.md)) | Ensemble sur grille | Ensemble dans le latent → Latent EnKF |
| Fokker-Planck ([ch.10](10_diffusion_models.md)) | $\partial_t p(x) = \mathcal{L}^* p$ en espace pixel | Idem dans le latent (Latent Diffusion Models, Rombach 2022) |
| Feynman-Kac ([ch.11b](11b_feynman_kac.md)) | $u(x,t) = \mathbb{E}[g(X_T)]$ via EDS sur $\mathbb{R}^d$ | Idem via EDS sur le latent |

→ Le cours t'a donné chaque brique en *espace physique*. **La frontière 2026 est simplement de les composer dans un latent appris.**

# Synthèse — Quitter l'espace physique

**Le diagnostic** : 30 ans d'assimilation opérationnelle ont bricolé des matrices $B$ et $R$ en dimension $10^8$. Ce bricolage est l'aveu qu'on s'est trompé d'espace.

**JEPA (LeCun 2022)** : prédire en espace d'embedding plutôt que reconstruire en espace pixel. Économie de modélisation des hautes fréquences sans valeur prédictive.

**Météo déjà JEPA-compatible** : GraphCast, AIFS, Pangu, ArchesWeatherGen ont tous un encodeur + dynamique latente + decodeur. Le décodeur est une formalité héritée.

**Trois niveaux d'engagement avec l'idée latente** :

| Niveau | Forme | Statut |
|---|---|---|
| 1. **Latent EnKF** | Gaussien dans un latent appris ; encode $x$, fait EnKF sur $z$ | Opérationnel DWD / MeteoSwiss 2025-2026 |
| 2. **Generative DA** (sauce GenCast / Arches) | Prior diffusion appris + posterior sampling guidé par obs | Frontière de recherche 2024-2026, déploiement attendu |
| 3. **End-to-end conditional** (ArchesWeatherGen-style) | Diffusion directement conditionnel sur observations passées | Recherche active, pas d'oprationnel |

**Le punchline** : *generative DA dissout le problème central de l'assimilation*. On n'estime plus $B$, on l'apprend implicitement. La DA devient un échantillonnage conditionnel sur un prior implicite — ce qui rend toute la machinerie variationnelle / EnKF caduque dans son rôle de cœur algorithmique.

**Ce que vous savez maintenant faire** : lire un papier de score-based DA, de Latent EnKF, ou de diffusion posterior sampling appliqué à la météo, identifier les hypothèses, juger ce qui est solide et ce qui est plausible. C'est la compétence pro de cette fin de décennie — et c'est exactement le manque que MF, ECMWF, DWD, MeteoSwiss veulent combler en interne.

# Pour aller plus loin

- **JEPA** : LeCun, *A Path Towards Autonomous Machine Intelligence* (2022). Et la série I-JEPA / V-JEPA / MC-JEPA sur arXiv (Assran et al., 2023-2024).
- **Latent EnKF / score-based DA** : Rozet & Louppe (2023, [arXiv:2306.10574](https://doi.org/10.48550/arXiv.2306.10574)) ; Manshausen et al. (2024, [arXiv:2401.05932](https://doi.org/10.48550/arXiv.2401.05932)) ; Chen et al. (2024) sur le latent EnKF appliqué au global NWP.
- **Latent Diffusion** : Rombach et al. (2022), *High-Resolution Image Synthesis with Latent Diffusion Models* — c'est l'archétype "espace latent + diffusion" qui inspire les variantes météo.
- **Opérationnel** : présentations DWD / MeteoSwiss aux meetings ECMWF (EWGLAM, EUMETNET) 2025-2026. Références exactes à compléter — l'enseignant a un accès direct à cette littérature.

:::{note} Sujet de projet ouvert (pour le projet build `weather_routing`)
Implémenter un **Latent EnKF jouet** sur le système de Lorenz-96 :

1. Entraîner un autoencoder de Lorenz-96 (dim 40 → latent dim 4-6).
2. Apprendre la dynamique latente $\mathcal{M}_\theta$ par régression sur des trajectoires.
3. Implémenter LETKF dans le latent (cf. [ch.8](08_enkf.md)).
4. Comparer à EnKF classique sur Lorenz-96 (espace physique) : robustesse, coût calcul, qualité d'analyse.

C'est un mini-projet d'une semaine qui touche *toutes* les briques du cours — idéal comme évaluation finale ou portfolio piece.
:::
