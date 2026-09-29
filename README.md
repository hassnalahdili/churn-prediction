# Prédiction du churn client (Telco)

> Identifier à l'avance les clients d'un opérateur télécom qui risquent de résilier, comprendre pourquoi, et décider à qui proposer une offre de rétention pour maximiser le gain financier.

## 1. Contexte et problématique

Acquérir un nouveau client coûte beaucoup plus cher que d'en garder un. Ce projet répond à trois questions :

1. **Prédire** : quels clients vont partir ? (classification binaire)
2. **Expliquer** : quels facteurs poussent au départ ? (SHAP)
3. **Décider** : à qui faire une offre, sachant qu'elle a un coût ? (seuil basé sur le coût)

## 2. Données

- **Source** : Telco Customer Churn (IBM), disponible sur Kaggle
- **Taille** : environ 7 000 clients, 21 variables
- **Cible** : `Churn` (Yes/No), avec environ 26 % de départs (classes déséquilibrées)
- **Variables** : profil client, services souscrits, type de contrat, ancienneté, facturation

## 3. Méthodologie

1. Exploration des données (EDA) : `notebooks/01_eda.ipynb`
2. Nettoyage et préparation : conversion de `TotalCharges`, encodage, séparation train/test avant toute transformation
3. Modélisation : `notebooks/02_modeling.ipynb`
   - Baseline : régression logistique
   - Modèles avancés : Random Forest, XGBoost / LightGBM
4. Évaluation : recall, precision, F1, PR-AUC, matrice de confusion
5. Explicabilité : SHAP
6. Optimisation du seuil de décision selon le coût métier

## 4. Résultats

| Modèle | Recall | Precision | PR-AUC |
|--------|--------|-----------|--------|
| Régression logistique | [ ] | [ ] | [ ] |
| Random Forest | [ ] | [ ] | [ ] |
| XGBoost | [ ] | [ ] | [ ] |

**Principaux facteurs de churn** : [ex. type de contrat, ancienneté, frais mensuels]

**Impact métier** : avec un coût de [500] par client perdu et [50] par offre de rétention, le seuil optimal est de [ ], pour un gain estimé de [ ].

## 5. Recommandations métier

- [Cible prioritaire identifiée, par exemple les clients en contrat mensuel avec faible ancienneté]
- [Action proposée et gain estimé]

## 6. Structure du dépôt

```
churn-prediction/
├── data/          # données brutes (voir section Données)
├── notebooks/     # analyses et modélisation
├── src/           # fonctions réutilisables
├── README.md
└── requirements.txt
```

## 7. Installation et reproduction

```bash
git clone https://github.com/[ton-pseudo]/churn-prediction.git
cd churn-prediction
python -m venv venv
venv\Scripts\activate          # Windows
pip install -r requirements.txt
jupyter notebook
```

Télécharge le dataset depuis Kaggle et place le CSV dans `data/`.

## 8. Limites et améliorations possibles

- [Ex. dataset unique, pas de données temporelles]
- [Ex. tester d'autres techniques de rééquilibrage, calibrer les probabilités]
- [Ex. déployer le modèle avec FastAPI et une démo Streamlit]

## 9. Ce que j'ai appris

- [Ex. gestion du déséquilibre, choix des métriques, interprétation avec SHAP]

## Auteur

**HASSNA LAHDILI** : [lien LinkedIn] | hassna.hlahdili@gmail.com
