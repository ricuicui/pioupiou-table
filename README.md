# Pioupiou Table

**A tabular foundation model written in Rust, pre-trained only on real tables.**

*[Version française plus bas](#version-française)*

Pioupiou Table predicts the missing cells of a table in a single pass. Give it a table where one
column has empty cells (prices, sales, diagnoses, risk…): it reads the filled rows as examples and
fills in the rest, without retraining on your table.

| | Pioupiou Table v0.23 |
|---|---|
| Architecture | Transformer, one token per cell |
| Parameters | 51.6 million |
| Layers | 12 |
| Columns read at once | up to 64, with their names |
| Output | class probabilities, or a distribution over intervals for numbers |
| Training data | more than 10,000 real public tables, all free to reuse, no generated tables |
| Written in | Rust: model, GPU kernels and training, no machine-learning library |
| Runs | locally, or through an API |
| Available in | [Ricuicui](https://ricuicui.com/en), Foundation seed (Engineer edition) |

## How it works

- **A foundation model**: trained once on many tables, then used as is on new ones.
- **Each cell is a token.** Each layer applies two attentions:
  - between the columns of a row, to capture the links between columns;
  - between the rows of a column, to compare the row to predict with the known rows.
- **Values are read in two ways**: at several scales, and by their rank in the column, so that
  extreme values do not throw the model off.
- **Numbers are predicted as a distribution** over intervals, which gives a range, not just a value.
- **Faster than PyTorch**: on a Mac chip, our Rust training step runs 2 to 3.5 times faster than
  the same step in PyTorch, with identical results (measured on an earlier, smaller version).

## With Pioupiou Language

Pioupiou Table is combined with **Pioupiou Language**, a small Transformer also written in Rust, to
answer questions about a table in English, French, Spanish and German ("how many customers are
likely to leave?", "what drives the price up?"). The language model understands the question; the
figures always come from Pioupiou Table's computation. Off-topic questions are politely declined.

## Results

Evaluated on **88 tables it has never seen**, with 5-fold cross-validation (accuracy for
classification, R² for regression, averaged):

| Method | Score |
|---|---|
| Pioupiou Table v0.20 (previous version) | 0.423 |
| Neural network | 0.441 |
| **Pioupiou Table v0.23** | **0.464** |
| Gradient boosting | 0.479 |
| Random forest | 0.492 |

v0.23 clearly improves on the previous version, especially on wide tables (more than 16 columns).
It does not yet match the best tree-based methods: that is the next step.

## Paper

- English: [pioupiou-table-v0.23-en.pdf](pioupiou-table-v0.23-en.pdf)
- Français : [pioupiou-table-v0.23.pdf](pioupiou-table-v0.23.pdf)

## About

Pioupiou Table is developed by Tony Belnoue (Tony dev web, France) for
[Ricuicui](https://ricuicui.com/en), a professional workstation with a browser and AI.
The source code and weights are not published.

Contact: coucou@ricuicui.com

---

## Version française

**Un modèle de fondation tabulaire écrit en Rust, pré-entraîné uniquement sur des tableaux réels.**

Pioupiou Table prédit les cases manquantes d'un tableau en une seule passe. On lui donne un tableau
dont une colonne a des cases vides (prix, ventes, diagnostic, risque…) : il lit les lignes remplies
comme des exemples et complète le reste, sans réapprendre sur votre tableau.

| | Pioupiou Table v0.23 |
|---|---|
| Architecture | Transformer, un jeton par case |
| Paramètres | 51,6 millions |
| Couches | 12 |
| Colonnes lues à la fois | jusqu'à 64, avec leurs noms |
| Sortie | probabilités par catégorie, ou répartition sur des intervalles pour un nombre |
| Données d'entraînement | plus de 10 000 vrais tableaux publics, libres de réutilisation, aucun tableau inventé |
| Écrit en | Rust : modèle, calculs sur carte graphique et entraînement, sans bibliothèque d'apprentissage |
| Fonctionne | en local ou par API |
| Disponible dans | [Ricuicui](https://ricuicui.com), graine Fondation (édition Ingénieur) |

**Comment il marche** :
- chaque case est un jeton, avec deux attentions par couche : entre les colonnes d'une ligne, puis
  entre les lignes d'une colonne ;
- les valeurs sont lues sur plusieurs échelles et par leur rang ;
- les nombres sont prédits en fourchette ;
- il est 2 à 3,5 fois plus rapide que PyTorch à l'entraînement sur la puce d'un Mac.

**Avec Pioupiou Langage**, il répond aux questions sur un tableau en français, anglais, espagnol et
allemand. Les chiffres viennent toujours du calcul.

**Résultats** sur 88 tableaux jamais vus : 0,464, contre 0,423 pour la version précédente (tableau
ci-dessus).

**Article** : [pioupiou-table-v0.23.pdf](pioupiou-table-v0.23.pdf)

Le code source et les poids ne sont pas publiés. Contact : coucou@ricuicui.com
