# Prédiction du churn client (Telco)

> Identifier à l'avance les clients d'un opérateur télécom qui risquent de résilier, comprendre pourquoi, et décider à qui proposer une offre de rétention pour maximiser le gain financier.

## 1. Contexte et problématique

Acquérir un nouveau client coûte beaucoup plus cher que d'en garder un. Ce projet répond à trois questions :

1. **Prédire** : quels clients vont partir ? (classification binaire)
2. **Expliquer** : quels facteurs poussent au départ ? (SHAP)
3. **Décider** : à qui faire une offre, sachant qu'elle a un coût ? (seuil basé sur le coût)

## 2. Données

- **Source** : Telco Customer Churn (IBM, version Cognos), disponible sur Kaggle (fichier `.xlsx`)
- **Taille** : 7 043 clients, 33 colonnes
- **Cible** : `Churn Value` (0/1) ou `Churn Label` (Yes/No), avec environ 26,5 % de départs (≈ 1 870 clients partis), donc des classes déséquilibrées
- **Variables utiles** : profil client, services souscrits, type de contrat, ancienneté, facturation
- **Variables exclues pour éviter la fuite de données** : `Churn Score`, `CLTV`, `Churn Reason` (connues après le départ ou calculées à partir de la cible)
- **Variables exclues car sans valeur prédictive** : `CustomerID`, `Count`, `Country`, `State`, `City`, `Zip Code`, `Lat Long`, `Latitude`, `Longitude`

## 3. Méthodologie

1. Exploration des données (EDA) : `notebooks/01_eda.ipynb`
2. Nettoyage et préparation : traitement de `Total Charges`, encodage, séparation train/test stratifiée avant toute transformation qui apprend des données
3. Modélisation : `notebooks/02_modeling.ipynb`
   - Baseline : régression logistique
   - Modèles avancés : Random Forest, XGBoost / LightGBM
   - Comparaison par validation croisée stratifiée
4. Évaluation : recall, precision, F1, PR-AUC, matrice de confusion (pas l'accuracy seule)
5. Explicabilité : SHAP
6. Optimisation du seuil de décision selon le coût métier

## 4. Analyse exploratoire (EDA)

### Qualité des données
- `Total Charges` était lue comme du texte (`object`) ; après conversion avec `pd.to_numeric(errors="coerce")`, **11 valeurs manquantes** apparaissent. [Cause à confirmer : vérifier si ces clients ont `Tenure Months = 0`, puis décision : remplacer par 0 ou supprimer.]
- [Doublons : à compléter]

### Variables numériques
| | Ancienneté (mois, médiane) | Frais mensuels (médiane) |
|---|---|---|
| Clients restés | 38 | 64,4 |
| Clients partis | 10 | 79,6 |

- Le churn est **concentré sur les premiers mois** : la moitié des clients partis ont quitté l'entreprise avant 10 mois.
- `Total Charges` reflète surtout l'ancienneté (≈ ancienneté × frais mensuels) : variable redondante à surveiller pour la régression logistique.

### Variables catégorielles (taux de churn, moyenne globale : 26,5 %)
| Variable | Groupe à risque | Taux | Groupe fidèle | Taux |
|---|---|---|---|---|
| Contrat | Mensuel (3 875) | 42,7 % | 2 ans (1 695) | 2,8 % |
| Paiement | Chèque électronique (2 365) | 45,3 % | Carte bancaire auto (1 522) | 15,2 % |
| Internet | Fibre optique (3 096) | 41,9 % | Sans internet (1 526) | 7,4 % |
| Senior | Oui (1 142) | 41,7 % | Non (5 901) | 23,6 % |
| Facture dématérialisée | Oui (4 171) | 33,6 % | Non (2 872) | 16,3 % |
| Support technique | Non (3 473) | 41,6 % | Oui (2 044) | 15,2 % |
| Sécurité en ligne | Non (3 498) | 41,8 % | Oui (2 019) | 14,6 % |

Tous les groupes comparés ont plus de 1 100 clients : les écarts ne sont pas dus à de petits effectifs.

### Résultat nuancé : l'effet du prix
- Globalement, les clients partis paient plus cher (médiane 79,6 contre 64,4).
- Mais le prix dépend surtout du type d'internet (médiane : 20,2 sans internet, 56,2 en DSL, 91,7 en fibre).
- **À type d'internet égal, les partis ne paient pas plus cher** (DSL : 49,2 contre 59,8 ; fibre : 87,6 contre 94,8 ; sans internet : 20,0 contre 20,2).
- L'écart global est donc un effet de composition (les partis sont surtout en fibre, la formule la plus chère) : **le prix seul n'explique pas le churn**.

### Profil à risque identifié
Contrat mensuel, fibre optique, paiement par chèque électronique, sans support technique ni sécurité en ligne, ancienneté courte.

> Ces résultats sont des associations, pas des relations de cause à effet. Les variables se chevauchent (par exemple, « No internet service » est le même groupe de 1 526 clients dans plusieurs colonnes). [Croisement contrat × type d'internet : à compléter.]

## 5. Résultats de la modélisation

| Modèle | Recall | Precision | PR-AUC |
|--------|--------|-----------|--------|
| Régression logistique | [ ] | [ ] | [ ] |
| Random Forest | [ ] | [ ] | [ ] |
| XGBoost | [ ] | [ ] | [ ] |

**Principaux facteurs de churn (SHAP)** : [à compléter]

**Impact métier** : avec un coût de [500] par client perdu et [50] par offre de rétention, le seuil optimal est de [ ], pour un gain estimé de [ ].

## 6. Recommandations métier

- [Cible prioritaire : clients en contrat mensuel, fibre, ancienneté inférieure à 12 mois, à confirmer avec le modèle]
- [Action proposée et gain estimé]

## 7. Structure du dépôt

```
churn-prediction/
├── data/          # données brutes (voir section Données)
├── notebooks/     # analyses et modélisation
├── src/           # fonctions réutilisables
├── README.md
└── requirements.txt
```

## 8. Installation et reproduction

```bash
git clone https://github.com/[ton-pseudo]/churn-prediction.git
cd churn-prediction
python -m venv venv
venv\Scripts\activate          # Windows
pip install -r requirements.txt
jupyter notebook
```

Télécharge le dataset depuis Kaggle (version `.xlsx`) et place-le dans `data/Telco_customer_churn.xlsx`.

## 9. Limites et améliorations possibles

- Dataset fictif d'IBM, de taille modérée (7 043 clients), sans dimension temporelle ni données d'usage : résultats non transposables tels quels à une entreprise réelle
- Incertitude des métriques estimée par validation croisée (jeu de test limité à environ 1 400 clients)
- Analyse exploratoire basée sur des associations, sans preuve de causalité
- [Ex. tester d'autres techniques de rééquilibrage, calibrer les probabilités]
- [Ex. déployer le modèle avec FastAPI et une démo Streamlit]

## 10. Ce que j'ai appris

- Vérifier les types de colonnes (type attendu contre type obtenu) et mesurer avant de modifier
- Repérer les fuites de données (`Churn Score`, `CLTV`, `Churn Reason`)
- Comparer un taux à sa moyenne globale et toujours regarder l'effectif
- Se méfier des effets de composition : une tendance globale peut s'inverser dans les sous-groupes
- [À compléter : gestion du déséquilibre, choix des métriques, SHAP]

## Auteur

**HASSNA LAHDILI** : [lien LinkedIn] | hassna.hlahdili@gmail.com