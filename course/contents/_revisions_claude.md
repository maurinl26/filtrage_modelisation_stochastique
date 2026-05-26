---
title: Notes de révision (Claude, 2026-05-26)
---

<!--
Ce fichier n'est PAS destiné à figurer dans le cours publié.
Il regroupe les modifications proposées par Claude et les points
à arbitrer cet été. À supprimer ou exclure du `myst.yml` une fois
les arbitrages faits.
-->

# But

Étendre le cours vers :
- **EDS Itô / Stratonovitch** + dérivés climatiques (assurance paramétrique),
- **Fokker-Planck** + modèles de diffusion (illustration GenCast),
- **Feynman-Kac** + équation d'advection-diffusion (illustration ArchesWeatherGen).

# Contraintes prises en compte

1. Cours **pragmatique** orienté implémentation / TP — pas de math à la française.
2. **Pas de redondance** avec l'autre prof : Monte Carlo, Kalman Filter, EnKF sont déjà couverts.
3. Niveau visé : **compact mais autonome** (un élève absent doit pouvoir rattraper sans le TP).

# Contexte d'intégration (mise à jour 2026-05-26)

Au moment du push, un `git pull --rebase` a ramené ~40 fichiers de travail upstream non locaux :
renumérotation complète (`09_enkf.md` → `08_enkf.md`, `12/13_diffusion_models` → `10/11`, `11_data_assimilation` → `97_data_assimilation`),
suppression de `08_markov_chain_monte_carlo.md`, étoffement substantiel de `04_filtre_kalman.md`, vrai contenu sur diffusion (`10_diffusion_models.md`, `11_tp_diffusion_models.ipynb`), nouveau `98_processus_gaussiens.md`.

**Décision** : ne garder que les **additions pures** + bandeaux de redondance. Les modifications qui auraient conflit avec le travail upstream restent dans le stash git `claude-extension-aside-2026-05-26` pour récupération ultérieure.

# Fichiers ajoutés (commités)

| Fichier | Contenu |
|---|---|
| `03b_tp_derives_climatiques.ipynb` | Placeholder TP assurance paramétrique : OU saisonnalisé, simulation EDS, pricing par Monte-Carlo, sensibilités climat. |
| `11b_feynman_kac.md` | Nouveau chapitre : formule de Feynman-Kac, EDP backward Kolmogorov, lien advection-diffusion, illustration ArchesWeatherGen, récap. Compact mais autonome. |
| `11c_tp_feynman_kac.ipynb` | Placeholder TP : équation de la chaleur par Monte-Carlo, comparaison MC vs DF, Black-Scholes, malédiction de la dimension. |

# Bandeaux de redondance ajoutés (à arbitrer cet été)

Bandeau `:::{warning} ⚠️ Redondance` ajouté en tête de :

- `04_filtre_kalman.md` — couvert par l'autre prof. À décider : supprimer / réduire en annexe.
- `08_enkf.md` — couvert par l'autre prof. À décider : supprimer / réduire en annexe.

Le chapitre MCMC (`08_markov_chain_monte_carlo.md`) avait été marqué en stash mais a été **supprimé upstream** entretemps — décision déjà prise.

# Modifications dans le stash NON appliquées (à examiner cet été)

Stash git : `claude-extension-aside-2026-05-26` (à inspecter avec `git stash list`).

| Fichier | Modif stashée |
|---|---|
| `02_processus_stochastiques.md` | Suppression de 2 paragraphes dupliqués "En reprenant les sauts de ±1 case". À vérifier si toujours présents dans l'upstream. |
| `03_equations_differentielles_stochastiques.md` | **Section Stratonovitch complète** (définition, propriétés comparées, conversion Itô↔Strato, Wong-Zakai) + **section Dérivés climatiques** (Ornstein-Uhlenbeck saisonnalisé, HDD/CDD, pricing) + **récap synthétique** + correction coquilles (`Stratanovitch`, `trapéoïdale`, `\mathbb[…]`). À récupérer cet été. |
| `12_diffusion_models.md` → renommé `10_diffusion_models.md` upstream | Réécriture complète : Fokker-Planck forward, EDS inverse d'Anderson 1982, score matching, illustration GenCast, récap. À comparer / fusionner avec le contenu upstream (qui a déjà un cadrage DDPM/forward/reverse). |
| `13_tp_diffusion_models.ipynb` → renommé `11_tp_diffusion_models.ipynb` upstream | Placeholder structuré. Le upstream a déjà du vrai contenu (598 lignes) — placeholder à **ne pas** appliquer en l'état. |

# Récupérer le contenu stashé

```bash
git stash list                                     # repérer l'index du stash
git stash show -p stash@{0} > /tmp/claude.patch    # vue d'ensemble
# Récupérer un fichier précis (sans appliquer le reste) :
git show stash@{0}:course/contents/03_equations_differentielles_stochastiques.md > /tmp/03_eds_claude.md
diff /tmp/03_eds_claude.md course/contents/03_equations_differentielles_stochastiques.md
```

# Points ouverts (TODO cet été)

- [ ] **Récupérer Stratonovitch + dérivés climatiques** depuis le stash et les intégrer dans le `03_equations_differentielles_stochastiques.md` actuel.
- [ ] **Comparer** la version stashée de Fokker-Planck (Anderson, score-matching, GenCast) avec l'actuel `10_diffusion_models.md` (DDPM, forward/reverse) et fusionner.
- [ ] Rédiger les notebooks TP `03b` et `11c` (placeholders actuellement).
- [ ] Arbitrer le sort des chapitres marqués redondants (`04_filtre_kalman.md`, `08_enkf.md`).
- [ ] Mettre à jour les slides (`slides/`) — non touché.
- [ ] Vérifier références : Anderson 1982, GenCast (Price et al. 2024), ArchesWeatherGen (Couairon et al. 2024).

# Conventions d'annotation utilisées

- `:::{important} 📝 Révision Claude (2026-05-26)` en tête de chaque fichier modifié.
- `:::{warning} ⚠️ Redondance avec l'autre cours` en tête des chapitres marqués pour suppression.
- `<!-- TODO-CLAUDE: ... -->` dans le source markdown pour les passages à valider.
