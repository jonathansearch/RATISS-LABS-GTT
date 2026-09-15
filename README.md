# RATISS-LABS-GTT

## Geological Topological Tech — dépôt principal de RATISS Labs

[![CI](https://github.com/jonathansearch/RATISS-LABS-GTT/actions/workflows/gtt.yml/badge.svg)](https://github.com/jonathansearch/RATISS-LABS-GTT/actions/workflows/gtt.yml)
![Tests](docs/badges/tests.svg) ![Dépendances](docs/badges/stdlib.svg) ![Licence](docs/badges/license.svg) ![R7](docs/badges/r7.svg) ![Juge](docs/badges/juge.svg)

> **RATISS-LABS-GTT** est la plateforme expérimentale principale de RATISS Labs. Elle fournit une architecture en neuf couches pour mesurer, documenter et réduire les violations d’invariants dans les modèles du monde appris, tout en conservant une traçabilité complète des paramètres, des exécutions et des verdicts.

![RATISS Labs](docs/assets/logo-ratiss-labs.png)

---

## 1. Présentation générale

RATISS-LABS-GTT, pour **Geological Topological Tech**, est un dépôt de recherche appliquée consacré à l’audit reproductible des modèles du monde appris. Le projet s’intéresse notamment aux modèles de la famille JEPA (*Joint-Embedding Predictive Architecture*) et aux modèles latents qui peuvent produire des représentations utiles sans respecter nativement les invariants physiques, les symétries ou certaines contraintes de causalité.

Le dépôt ne prétend pas résoudre définitivement ces problèmes. Il fournit un dispositif d’instrumentation qui mesure la violation, applique une correction en aval sans modifier le modèle audité, publie le delta observé et conserve les éléments nécessaires à une reproduction indépendante. Cette distinction entre mesure, correction et revendication est essentielle pour l’interprétation des résultats.

Le projet repose sur quatre principes opérationnels :

1. **Un juge mécanique de couche 1.** Le dépôt [RATISS-Framework](https://github.com/jonathansearch/RATISS-Framework) est intégré comme dépendance Git scellée et analysé en intégration continue.
2. **Le scellement avant exécution.** Les paramètres et manifestes sont figés avant l’exécution conformément à la règle R6.
3. **La reproductibilité R7.** Toute valeur publiée doit être accompagnée d’un reçu rejouable.
4. **La séparation constructeur-auditeur.** Le protocole rouge/bleu distingue la construction des artefacts et leur audit conformément à la règle N2.

> **Règle R7 :** aucune valeur publiée sans qu’un tiers puisse la rejouer en une commande.

## 2. Résumé exécutif

À la date du **2026-09-13**, la suite hors ligne compte **108 tests** réussis. Le juge mécanique retourne **exit 0**. Les vérifications déterministes affichent **5 vérifications déterministes sur 5 conformes**. Le premier run externe réel sur le LeWorldModel officiel, dans l’environnement TwoRooms, mesure une violation en boucle ouverte de **0.840789108872**, ramenée à **0.214657575488** par correction en aval. Le delta d’ablation publié est **0.626131533384**, avec un verdict **APPROVED**.

La portée déclarée reste strictement limitée à **une trajectoire**, **une graine aléatoire** et une **métrique en espace latent**. L’auditeur indépendant demeure en attente. Les preuves Lean demeurent en attente pour le run IBM M2. Aucune généralisation au-delà de cette portée n’est revendiquée dans ce dépôt.

## 3. Résultats vérifiables

| Mesure | Valeur | Source vérifiable |
|---|---:|---|
| Suite de tests | 108 passed (bibliothèque standard seule) | intégration continue `gtt.yml` |
| Juge mécanique couche 1 | exit 0 (sceaux intacts) | job CI `judge` |
| Vérifications déterministes | 5/5 CONFORME (world_models, quantum, forecast, agents, examples) | `scripts/*_check.py` |
| Run externe LeWM/TwoRooms — violation ouverte | 0.840789108872 | `proofs/WM-EXTERNE-LEWM-*.md` |
| Run externe — violation après correction aval | 0.214657575488 | mêmes preuves |
| Delta d’ablation publié | 0.626131533384 — verdict APPROVED | mêmes preuves |
| Ablation phase 4 (substitut synthétique) | 0.104622125411 puis ≈3.33e-16 après correction | `proofs/PHASE4-REPLAY*.md` |
| Scellés R6 | 5 manifestes version 0.2.0 figés avant tout code (commit `9990d7e`) | `SEALS.json` |
| Auditeur indépendant | en attente — kit de rejeu en une commande | `docs/AUDIT-INDEPENDANT-KIT.md` |

Ces valeurs sont reproduites sans modification par rapport aux artefacts présents dans le dépôt. Elles doivent être lues avec leur périmètre expérimental et leurs limites documentées.

## 4. Objectif scientifique et limites

L’objectif de long terme est d’étudier la correction des violations de lois physiques dans les modèles du monde appris. Cet objectif constitue une vision de recherche et non une affirmation de résultat définitif. Le dépôt privilégie une publication prudente : chaque chiffre doit être rattaché à un reçu, chaque correction doit être comparée à une ablation et chaque limite doit être explicitement conservée.

Le run externe sur LeWorldModel est exécuté en inférence seule. Les gradients sont désactivés, le chargement du `state_dict` est strict et le modèle audité n’est pas modifié. La violation mesurée correspond au contrat d’entraînement JEPA lui-même, exprimé par une distance L2 relative entre la prédiction et l’embedding ancré. La correction aval utilise un ancrage observationnel de type commande prédictive à horizon glissant, sans fine-tuning.

La certification correspondante est disponible dans [`docs/certifications/external/2026-09-13_CERT-GTT-WM-LEWM-EXT-v1.md`](docs/certifications/external/2026-09-13_CERT-GTT-WM-LEWM-EXT-v1.md). Elle est verrouillée par un test gardien et demeure interprétée selon la portée déclarée : une trajectoire, une graine aléatoire et une métrique en espace latent.

## 5. Architecture en neuf couches

L’architecture est organisée en neuf couches évaluées individuellement. Chaque couche dispose de son propre périmètre fonctionnel, de ses tests et de ses éléments de traçabilité.

| Couche | Fonction | État |
|---|---|---|
| `gtt/core` | topologie, thermodynamique, invariants et hypothèse LCT — cohérence topologique locale | implémentée (phase 2) |
| `gtt/quantum` | Lanczos, MPS/DMRG, décohérence T1/T2 et connecteur QPU en dry-run | implémentée (phase 3) |
| `gtt/world_models` | pont LEWM, audit de cohérence, correction aval et ablation | implémentée (phase 4) + run externe réel |
| `gtt/forecast` | calibration Brier/ECE, prédictions scellées R5, journal chaîné et bot Metaculus désarmé | implémentée (phase 5) |
| `gtt/agents` | orchestrateur sans autorité, wrappers rouge/bleu et protocole 1.0 | implémentée (phase 6) |
| `gtt/receipts` | hash rejouable et vérification des reçus ; preuve Lean en attente | implémentée (phase 6) |
| `gtt/audit` | hooks du juge, provenance et journal des déviations | implémentée (phase 1) |
| `gtt/viz` | topologie 3D isométrique en SVG, atlas de cohérence et graphe DOT | implémentée (phase 6) |
| `gtt/io` | PDB wwPDB, synchronisations OSF/GitHub en dry-run et gestion des jetons par variables d’environnement | implémentée (phase 6) |

Les terrains d’épreuve comprennent `examples/TERRAIN-02`, basé sur un modèle SIR avec conservation exacte, et `examples/TERRAIN-03`, consacré à la chaleur 1D de Neumann avec régime stable décroissant, régime instable explosif et graine de von Neumann documentée.

Les dépôts historiques gelés sont décrits dans [`docs/ORPHELINS.md`](docs/ORPHELINS.md). Ce document contient **14 tombstones SHA-256**. Aucun code n’est copié et aucun dépôt n’est supprimé dans le cadre de ce mécanisme de conservation.

## 6. Protocole rouge/bleu et gouvernance de l’audit

L’orchestrateur ne détient aucune autorité décisionnelle. Il tient un registre et coordonne les flux de vérification. L’équipe rouge applique le juge et publie les verdicts bruts. L’équipe bleue répond par des reproductions rejouables. Cette séparation permet de distinguer la production d’un résultat de l’évaluation de sa conformité.

Les phases 2 à 7 ont été construites par l’équipe Rouge en relais, sur ordre explicite du chef de laboratoire, avec divulgation complète. La colonne « auditeur indépendant » de chaque reçu demeure **EN ATTENTE** conformément à la règle N2. Le rejeu externe s’effectue avec [`docs/AUDIT-INDEPENDANT-KIT.md`](docs/AUDIT-INDEPENDANT-KIT.md), tandis que le journal public des états d’audit est tenu dans [`docs/AUDIT_TRAIL.md`](docs/AUDIT_TRAIL.md).

Cette organisation ne transforme pas un auto-audit en validation indépendante. Elle documente au contraire la séparation attendue et rend visible ce qui reste à vérifier.

## 7. Installation et reproduction

Le dépôt exige Python **>=3.9**. L’installation minimale est la suivante :

```bash
git clone https://github.com/jonathansearch/RATISS-LABS-GTT.git
cd RATISS-LABS-GTT
pip install "ratiss-framework @ git+https://github.com/jonathansearch/RATISS-Framework.git"
pip install -e .
python -m pytest -q            # 108 passed (dont 6 tests gardiens hors-ligne)
python -m gtt.judge --ci       # exit 0 = sceaux intacts
bash scripts/audit_independant.sh   # rejeu complet + formulaire de verdict
```

Le run externe réel nécessite des dépendances optionnelles plus lourdes et dure environ **5 minutes** :

```bash
pip install torch --index-url https://download.pytorch.org/whl/cpu
pip install "transformers<5" stable-worldmodel einops
python scripts/wm_externe_lewm.py    # reçu horodaté dans proofs/
```

Les scripts de vérification sont conçus pour produire des sorties consultables. Les jetons d’accès sont fournis par variables d’environnement et ne sont jamais affichés par les modules d’intégration.

## 8. Organisation du dépôt

```text
gtt/            les neuf couches, avec un cœur en bibliothèque standard seule
examples/       TERRAIN-02, TERRAIN-03 et expected.json scellés
scripts/        vérifications déterministes, rejeux R7 et run externe LeWM
tests/          108 tests hors ligne, dont les gardiens d’intégrité
proofs/         reçus R7 horodatés et reçus principaux par phase
docs/           protocole, kit d’audit, journal public, certifications et cartographie
gtt/audit/      PROVENANCE : source et commit de chaque ligne de code
```

## 9. Intégrité, provenance et transparence

Aucune valeur n’est publiée sans reçu rejouable. Aucune publication n’intervient sans visa du chef de laboratoire conformément à la règle R3. Les certifications des phases 2 à 7 sont des auto-audits divulgués ; l’auditeur indépendant est en attente et son kit de reproduction est fourni.

`lean_proof` est un stub marqué **EN ATTENTE**. Aucune preuve Lean n’est fabriquée conformément à la règle C5. La complétude est prévue lors du run IBM M2 sur preuves machine réelles.

Le bot Metaculus est désarmé par construction. Il fonctionne hors ligne, avec un plafond de **200 questions**, et toute soumission réelle est interdite dans le dépôt.

La provenance intégrale est disponible dans `gtt/audit/PROVENANCE.md`. Le scan anti-copie de blocs de **11 lignes ou plus** contre les dépôts sources n’a identifié aucun bloc commun.

## 10. Relation avec RATISS-Framework

[RATISS-Framework](https://github.com/jonathansearch/RATISS-Framework) constitue la couche de méthode et le juge de couche 1. Il audite RATISS-LABS-GTT par dépendance Git scellée en intégration continue. GTT apporte le terrain expérimental en neuf couches ; Framework fournit les mécanismes de scellement, de vérification et de décision.

Cette relation est volontairement asymétrique : la couche 1 juge la couche 2. Les résultats GTT ne doivent donc pas être interprétés indépendamment des preuves et du verdict produits par Framework.

## 11. Laboratoire et responsabilité scientifique

**Jonathan Evina** est fondateur et chef de laboratoire de RATISS Labs, à Yaoundé, Cameroun. Il assure la vision, l’arbitrage et le visa conformément à la règle R3. Son identifiant ORCID est [0009-0000-4092-5313](https://orcid.org/0009-0000-4092-5313). Le compte GitHub de référence est [jonathansearch](https://github.com/jonathansearch). Le contact professionnel est `jonathan.ratisslabs@zohomail.com`.

Les agents de construction et l’équipe Rouge sont documentés dans les artefacts de provenance. Le veto de l’auditeur prime sur le chef de laboratoire. Cette règle a permis de détecter la fabrication présente dans des artefacts internes en septembre 2026.

## 12. Licence et citation

Le projet est distribué sous licence MIT. Copyright (c) **2026 Jonathan Evina, RATISS Labs**. Le texte complet figure dans [`LICENSE`](LICENSE) et les informations de citation dans [`CITATION.cff`](CITATION.cff).

## Références

[1]: https://github.com/jonathansearch/RATISS-Framework "RATISS-Framework — protocole d’audit exécutable"
[2]: https://github.com/jonathansearch/RATISS-LABS-GTT "RATISS-LABS-GTT — dépôt principal de RATISS Labs"
[3]: https://orcid.org/0009-0000-4092-5313 "ORCID de Jonathan Evina"

---

**La traçabilité des écarts et des échecs renforce la crédibilité des résultats publiés.**
