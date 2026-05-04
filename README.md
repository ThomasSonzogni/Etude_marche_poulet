# 🐔 La Poule Qui Chante — Étude d'expansion internationale

> **Projet Data Analyst** — Analyse des groupements de pays pour l'exportation de poulet biologique français.

---

## 📌 Sommaire

1. [Contexte & enjeu métier](#-contexte--enjeu-métier)
2. [Objectifs du projet](#-objectifs-du-projet)
3. [Livrables attendus](#-livrables-attendus)
4. [Parties prenantes](#-parties-prenantes)
5. [Planning & jalons](#-planning--jalons)
6. [Démarche méthodologique](#-démarche-méthodologique)
7. [Données utilisées](#-données-utilisées)
8. [Architecture du projet](#-architecture-du-projet)
9. [Installation & exécution](#-installation--exécution)
10. [Résultats & recommandations](#-résultats--recommandations)
11. [Risques & points d'attention](#-risques--points-dattention)

---

## 🎯 Contexte & enjeu métier

**La Poule Qui Chante** est une entreprise française d'agroalimentaire spécialisée dans l'élevage et la vente de poulets sous le label **« Poulet Agriculture Biologique »**. Son activité actuelle est franco-française.

**Patrick, le PDG**, souhaite évaluer l'opportunité d'une expansion internationale. À ce stade :

- Aucun pays ni continent n'est ciblé.
- Tous les pays du monde sont des candidats potentiels.
- La décision finale s'appuiera sur une **étude de marché ultérieure**, dont ce projet constitue le **socle analytique**.

**Enjeu stratégique** : sécuriser la croissance future de l'entreprise en identifiant les marchés les plus pertinents pour l'export de poulet bio.

---

## 🎯 Objectifs du projet

| # | Objectif | Critère de succès |
|---|----------|-------------------|
| 1 | Constituer une base de données multi-sources (≥ 8 variables PESTEL) | ≥ 100 pays couvrant ≥ 60 % de la population mondiale |
| 2 | Nettoyer, harmoniser et joindre les sources | Un fichier unique exploitable pour l'ACP |
| 3 | Réaliser une **ACP** (réduction de dimensions) | Cercle des corrélations + projection des individus interprétables |
| 4 | Réaliser un **clustering** (CAH puis K-means) | Groupes de pays cohérents et interprétables |
| 5 | Recommander une short-list de pays cibles | Argumentaire data-driven validé par le COMEX |

---

## 📦 Livrables attendus

- ✅ **Notebook 1 — `nettoyage.ipynb`** : collecte, préparation et fusion des données.
- ✅ **Notebook 2 — `analyse.ipynb`** : exploration, ACP, clustering, profilage des clusters.
- ✅ **Datasets nettoyés** (`data_clean/`) : `df_BAO.csv`, `df_FAO.csv`, `df_ACP.csv`.
- 🎤 **Support de présentation COMEX** (15 min de soutenance + 10 min Q&A).
- 📝 **Recommandations** : pays cibles + méthodologie vulgarisée.

---

## 👥 Parties prenantes

| Rôle | Personne | Responsabilité |
|------|----------|----------------|
| Sponsor / PDG | Patrick | Commanditaire, décideur final |
| Data Analyst | Moi | Collecte, analyse, recommandations |
| COMEX | Directeurs | Audience de la soutenance — niveau non-technique |

> ⚠️ **Posture de communication** : la présentation au COMEX doit rester **non-technique**. Les directeurs n'ont pas le niveau data — privilégier la pédagogie et les visuels parlants.

---

## 🗓️ Planning & jalons

| Phase | Étape | Statut | Livrable |
|-------|-------|--------|----------|
| 1 | Cadrage & analyse PESTEL | ✅ | Liste des variables retenues |
| 2 | Collecte multi-sources (FAO, BM, OWID) | ✅ | Fichiers bruts dans `data_raw/` |
| 3 | Nettoyage & jointure | ✅ | `df_ACP.csv` (156 pays > 80 % rempl.) |
| 4 | Exploration & feature engineering | ✅ | Stats descriptives, corrélations |
| 5 | ACP (réduction de dimensions) | ✅ | Cercle des corrélations PC1–PC4 |
| 6 | Clustering CAH puis K-means | ✅ | 7 clusters identifiés |
| 7 | Profilage & recommandations | ✅ | Cluster 1 = cible prioritaire |
| 8 | **Soutenance COMEX** | 🟡 À venir | Présentation 15 min + Q&A 10 min |

---

## 🧭 Démarche méthodologique

### 1️⃣ Cadrage par l'analyse PESTEL

Pour structurer le choix des variables, application de la grille **PESTEL** afin d'élargir le périmètre au-delà des seules données FAO :

| Dimension | Variables retenues |
|-----------|--------------------|
| **P**olitique | Indice de stabilité politique (BM) |
| **É**conomique | PIB/hab. (PPA), dépenses des ménages, tarifs douaniers |
| **S**ocial | Population totale, taux d'urbanisation |
| **T**echnologique | Indice de performance logistique (BM) |
| **E**nvironnemental | Émissions N₂O agricoles/hab., part bio des terres arables |
| **L**égal | Tarifs douaniers moyens (proxy) |
| **Métier** | Production / import / export / dispo. de volailles, prix producteur, stocks vivants, dispo. céréalière |

### 2️⃣ Préparation des données (`nettoyage.ipynb`)

1. Chargement des **11 sources** (CSV Banque Mondiale, FAO, OWID).
2. Standardisation : passage au **format long**, harmonisation pays/année, ajout du code **ISO3** (via `country_converter`).
3. Filtrage sur l'**année 2022** (couverture optimale toutes sources confondues).
4. Suppression des entités non-pays (agrégats régionaux, codes ISO3 invalides).
5. Jointures successives : sources Banque Mondiale → `df_BAO` ; sources FAO/OWID → `df_FAO`.
6. Fusion finale `df_BAO` ⨝ `df_FAO` sur ISO3.
7. Filtre qualité : conservation des pays avec **taux de remplissage > 80 %** → **156 pays** retenus.
8. Déduplication (cas de la Chine, Taïwan).

### 3️⃣ Exploration & feature engineering (`analyse.ipynb`)

- Analyse des valeurs manquantes → exclusion des variables avec trop de NaN (logistique, prix, organic, etc.).
- Imputation par la **médiane** pour les rares valeurs manquantes restantes.
- Histogrammes, matrice de corrélation, scatter plots.
- **Feature engineering** : transformation `log(1+x) - log(1+population)` pour les variables de volume (production, import, export). Objectif : passer en **per capita** + atténuer l'asymétrie sans supprimer d'observation.

### 4️⃣ Analyse en Composantes Principales (ACP)

- Standardisation (`StandardScaler`).
- ACP sur **13 variables** retenues.
- **Variance expliquée** : PC1 ≈ 35 %, PC2 ≈ 15 %, PC3 ≈ 11 %, PC4 ≈ 9 % — cumul ≈ **71 %** sur 4 axes.
- **Cercle des corrélations** PC1×PC2 et PC3×PC4 → identification des dimensions latentes (richesse/urbanisation, intensité productive, profil environnemental, etc.).
- **Projection des individus** sur les 4 premiers axes pour repérer visuellement les groupes de pays.

### 5️⃣ Clustering

- **CAH** (méthode de Ward) → dendrogramme pour identifier le nombre de clusters.
- **K-means** sur les composantes PC1 à PC4 → confirmation par la **méthode du coude**.
- Choix retenu : **k = 7 clusters**.
- Validation par **ANOVA** : variables les plus discriminantes = `population`, `food_balance_production_per_capita`, `livestock_production_per_capita`.

### 6️⃣ Profilage des clusters

- Moyennes par cluster sur variables originales et standardisées.
- **Radar charts** (population exclue car écrasante).
- **Boxplots** de la population par cluster (échelle log).
- Interprétation business cluster par cluster.

---

## 📚 Données utilisées

| Source | Fichier brut | Variable(s) extraite(s) | Année |
|--------|--------------|-------------------------|-------|
| Banque Mondiale | `API_LP.LPI...` | Indice perf. logistique | 2022 |
| Banque Mondiale | `API_NY.GDP.PCAP.PP...` | PIB/hab. PPA | 2022 |
| Banque Mondiale | `API_PV.EST...` | Stabilité politique | 2022 |
| Banque Mondiale | `API_SP.POP.TOTL...` | Population totale | 2022 |
| Banque Mondiale | `API_SP.URB.TOTL...` | Taux d'urbanisation | 2022 |
| Banque Mondiale | `API_TM.TAX.MRCH...` | Tarifs douaniers moyens | 2022 |
| Banque Mondiale | `API_NE.CON.PRVT.PC.KD...` | Dépenses ménages/hab. | 2022 |
| FAO | `food_balances_poultry.csv` | Production / import / export volailles | 2022 |
| FAO | `livestock_production.csv` | Stocks, animaux abattus, rendement | 2022 |
| FAO | `prices.csv` | Prix producteur poulet (LCU/t) | 2022 |
| FAO | `trade.csv` | Import/export poulets | 2022 |
| FAO | `dispo_alimentaire.csv` | Dispo céréalière (blé, riz, maïs) | 2022 |
| OWID | `share-of-arable-land-which-is-organic.csv` | Part bio des terres | 2022 |
| OWID | `per-capita-nitrous-oxide-agriculture.csv` | Émissions N₂O agricoles/hab. | 2022 |

**Couverture finale** : **156 pays** avec un taux de remplissage > 80 % (objectif ≥ 100 pays atteint).

---

## 🗂️ Architecture du projet

```
la_poule_qui_chante/
├── data_raw/              # Données brutes téléchargées (FAO, BM, OWID)
│   ├── API_*.csv
│   ├── food_balances_poultry.csv
│   ├── livestock_production.csv
│   ├── prices.csv
│   ├── trade.csv
│   ├── dispo_alimentaire.csv
│   ├── share-of-arable-land-which-is-organic.csv
│   └── per-capita-nitrous-oxide-agriculture.csv
│
├── data_clean/            # Données nettoyées et fusionnées
│   ├── df_BAO.csv         # Sources Banque Mondiale jointes
│   ├── df_FAO.csv         # Sources FAO + OWID jointes
│   └── df_ACP.csv         # Dataset final pour l'ACP (156 pays)
│
├── notebooks/
│   ├── nettoyage.ipynb    # Étape 1 : préparation des données
│   └── analyse.ipynb      # Étape 2 : exploration, ACP, clustering
│
├── livrables/
│   └── presentation_comex.pdf  # Support de soutenance
│
└── README.md              # Ce fichier
```

---

## ⚙️ Installation & exécution

### Prérequis

- Python ≥ 3.10
- Jupyter Notebook ou JupyterLab

### Installation des dépendances

```bash
pip install numpy pandas matplotlib seaborn plotly \
            scikit-learn scipy pycountry country_converter
```

### Exécution

```bash
# 1. Lancer d'abord le nettoyage (génère df_ACP.csv)
jupyter notebook notebooks/nettoyage.ipynb

# 2. Puis l'analyse (consomme df_ACP.csv)
jupyter notebook notebooks/analyse.ipynb
```

---

## 🏆 Résultats & recommandations

### Synthèse des 7 clusters

| Cluster | Taille | Profil | Intérêt export |
|---------|--------|--------|----------------|
| **1** | 43 pays | PIB/hab. élevé, urbanisation forte, importateurs nets de volailles, stabilité politique | ⭐ **Cible prioritaire** |
| 6 | 13 pays | Marchés premium concurrentiels et réglementés | ⭐ Alternative intéressante |
| 4 | 34 pays | Pays intermédiaires | À étudier au cas par cas |
| 0 | 26 pays | Faible PIB, instabilité politique | ❌ Peu favorable |
| 2 | 13 pays | Profil intermédiaire | À étudier |
| 3 | 23 pays | Petits pays, faible volume | ❌ Trop petits |
| 5 | 2 pays | Géants démographiques | À traiter à part |

### Recommandation principale

> Le **cluster 1** apparaît comme le marché le plus favorable à l'exportation de poulet biologique :
> - 📈 Pouvoir d'achat élevé
> - 🛒 Importations de volailles par habitant déjà significatives
> - 🛡️ Stabilité politique favorable
> - 🌆 Taux d'urbanisation élevé → demande de produits différenciés à forte valeur ajoutée

**Pays prioritaires identifiés** (top 10 du cluster 1 sur score composite) : Pays-Bas, Allemagne, France, Royaume-Uni, Belgique, Danemark, Finlande, Canada, Slovénie, Croatie, etc.

### Questions anticipées du PDG

| Question attendue | Réponse synthétique |
|-------------------|---------------------|
| *Un pays sort-il particulièrement ?* | **Pays-Bas** et **Allemagne** en tête du score composite intra-cluster 1. |
| *Pourquoi utiliser une ACP ?* | Réduire 13 variables corrélées en 4 axes indépendants → visualisation et clustering plus stables. |
| *CAH et K-means donnent-ils le même résultat ?* | Cohérence globale ; le K-means a affiné le découpage du dendrogramme à k = 7. |

---

## ⚠️ Risques & points d'attention

- **Biais de l'année 2022** : choix d'une année unique pour maximiser la couverture. Une analyse multi-annuelle serait pertinente en phase 2.
- **Variables exclues** (logistique, prix, part bio…) en raison de NaN trop nombreux : à réintégrer si données disponibles.
- **Imputation par la médiane** sur quelques cellules : à surveiller, ne pas généraliser.
- **L'ACP cumule ~71 % de la variance sur 4 axes** : ~29 % d'information non capturée → garder un esprit critique sur les frontières des clusters.
- **L'analyse est exploratoire** : elle ne remplace pas une étude de marché terrain (concurrence, régulations sanitaires, accords commerciaux bilatéraux, logistique du froid…).

---

## 📞 Contact

**Data Analyst** — projet interne La Poule Qui Chante
**Sponsor** : Patrick, PDG

---

*Dernière mise à jour : Mai 2026*
