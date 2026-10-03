# Prédiction du churn client (Telco)

> Un opérateur télécom perd des clients chaque mois. Ce projet cherche à repérer à l'avance ceux qui risquent de partir, à comprendre pourquoi, et à décider à qui proposer une offre de rétention.

## 1. Le problème

Garder un client coûte moins cher que d'en trouver un nouveau. Je cherche donc à répondre à trois questions :

1. **Qui va partir ?** (classification binaire)
2. **Pourquoi ?** (explication avec SHAP)
3. **À qui proposer une offre**, sachant qu'elle coûte de l'argent ? (choix du seuil selon le coût)

## 2. Les données

- **Source** : Telco Customer Churn (IBM, version Cognos, données d'exemple fictives), [Kaggle](https://www.kaggle.com/datasets/yeanzc/telco-customer-churn-ibm-dataset), licence : « Other » sur Kaggle (voir la description de la page). Les données ne sont pas incluses dans ce dépôt : il faut les télécharger depuis Kaggle.
- **Taille** : 7 043 clients et 33 colonnes, dans un fichier `.xlsx` à placer dans `data/Telco_customer_churn.xlsx`.
- **Cible** : `Churn Value` (0 ou 1). Environ 26,5 % des clients sont partis, donc les classes sont déséquilibrées.
- **Colonnes écartées** : `Churn Reason` n'existe que pour les clients partis, donc elle contient la réponse. J'ai aussi écarté `Churn Score` et `CLTV` par prudence, ainsi que les identifiants et la géographie. J'ai retiré `Gender` : le churn est presque le même chez les femmes et les hommes (26,9 % contre 26,2 %).
- Il reste 18 variables.

## 3. La démarche

1. Explorer les données : [`notebooks/01_eda.ipynb`](notebooks/01_eda.ipynb)
2. Préparer : découpage train/test stratifié, puis encodage ajusté sur le train seulement, pour éviter toute fuite de données.
3. Modéliser : une régression logistique comme point de départ, puis Random Forest et XGBoost, comparés par validation croisée.
4. Évaluer avec le recall, la precision et la PR-AUC. L'accuracy serait trompeuse, puisque 73 % des clients restent.
5. Expliquer avec SHAP et choisir le seuil de décision d'après le coût.

## 4. Ce que l'exploration m'a appris

- **Un client sur quatre part.** Un modèle qui répondrait toujours « il reste » aurait 73,5 % de bonnes réponses sans rien apprendre. Je juge donc les modèles sur autre chose que l'accuracy.
- **Le départ se joue au début.** 47,4 % des clients partent la première année, contre 9,5 % après quatre ans.
- **Le contrat est le signal le plus net.** 42,7 % de churn en contrat mensuel, 2,8 % sur deux ans.
- **La fibre part plus que le DSL, quel que soit le contrat** (54,6 % contre 32,2 % en contrat mensuel). Ce n'est donc pas seulement parce que ses clients sont plus souvent en contrat mensuel.
- **Le prix n'explique pas tout.** Les clients partis paient plus cher en global, mais à type d'internet égal ils ne paient pas plus. Ils sont surtout en fibre, la formule la plus chère.
- **Les options de service demandent de la prudence.** Les clients sans option sont surtout en contrat mensuel, ce qui gonfle l'écart. Une fois le contrat fixé, `Device Protection` n'a presque plus d'écart (47,6 % contre 43,6 % en mensuel). Sans `Tech Support` ou `Online Security`, le churn en contrat mensuel reste environ 20 points plus haut (50 à 51 % contre 30 %).
- **Les clients en couple ou avec des personnes à charge partent moins** (19,7 % contre 33,0 %, et 6,5 % contre 32,6 %).
- **Le chèque électronique (45,3 %) et les seniors (41,7 %) ressortent aussi**, mais je n'ai pas vérifié si c'est un effet du contrat.
- **Côté données** : aucun doublon. Les 11 valeurs manquantes de `Total Charges` sont des clients à 0 mois d'ancienneté, pas encore facturés, donc remplacées par 0. Cette colonne est corrélée à 0,83 avec l'ancienneté : elle fait presque doublon.

![Taux de churn par contrat, type d'internet et mode de paiement](images/churn_par_contrat.png)
![Taux de churn par tranche d'ancienneté](images/churn_par_anciennete.png)

> Ce sont des associations, pas des causes. Le détail de chaque analyse est dans le notebook.

## 5. Résultats de la modélisation

| Modèle | Recall | Precision | PR-AUC |
|--------|--------|-----------|--------|
| Régression logistique | [ ] | [ ] | [ ] |
| Random Forest | [ ] | [ ] | [ ] |
| XGBoost | [ ] | [ ] | [ ] |

**Principaux facteurs (SHAP)** : [à compléter]

**Impact métier** : avec un coût de [500] par client perdu et [50] par offre, le seuil optimal est [ ], pour un gain estimé de [ ].

## 6. Recommandations métier

- [À compléter après la modélisation]

## 7. Reproduire le projet

```bash
git clone https://github.com/hassnalahdili/churn-prediction.git
cd churn-prediction
python -m venv venv
venv\Scripts\activate          # Windows
pip install -r requirements.txt
```

Télécharge ensuite le fichier depuis la page Kaggle indiquée plus haut, place-le dans `data/Telco_customer_churn.xlsx`, puis lance `jupyter notebook`.

## 8. Limites

- Les données sont fictives (IBM), assez petites, sans dimension temporelle ni données d'usage : les résultats ne se transposent pas tels quels à une vraie entreprise.
- Mes analyses montrent des associations, pas des causes. Je n'ai fait aucun test statistique, et je n'ai contrôlé qu'une variable à la fois (le contrat).
- `Senior Citizen` est une variable sensible : s'en servir pour cibler des offres pose une question d'équité. J'ai retiré `Gender`, qui n'apportait rien.

## Auteur

**HASSNA LAHDILI** : [LinkedIn](https://www.linkedin.com/in/hassna-lahdili-2b2422321) | hassna.hlahdili@gmail.com