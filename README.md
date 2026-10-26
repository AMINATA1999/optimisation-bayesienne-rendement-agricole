# 🌾 Optimisation bayésienne et modèles bayésiens à noyau : rendement agricole

Projet réalisé dans le cadre du Master Data Science (UPEC), module *Analyse et Traitement de Données Numériques*.

L'objectif est d'appliquer des **méthodes bayésiennes** à des données agricoles pour :

1. trouver les conditions (humidité, température) qui maximisent le rendement ;
2. régler les hyperparamètres d'un modèle de régression plus efficacement qu'avec les méthodes classiques ;
3. tester des **processus gaussiens** en régression et en classification, et analyser l'incertitude de leurs prédictions.

## 🎯 Résultats clés

**Réglage des hyperparamètres d'une forêt aléatoire** (`n_estimators`, `max_depth`), erreur quadratique moyenne (MSE) sur le jeu de test :

| Méthode | MSE |
|---|---|
| Grid Search | 0,898 |
| Random Search | 0,892 |
| Optimisation bayésienne (`BayesSearchCV`) | 0,885 |
| **Optimisation bayésienne (`gp_minimize`)** | **0,878** |

L'optimisation bayésienne obtient l'erreur la plus faible et converge en une dizaine d'itérations. Elle retient une forêt peu profonde (`max_depth = 3`), ce qui limite le sur-apprentissage.

**Maximisation du rendement** : l'optimisation bayésienne trouve un rendement estimé de **8,79 t/ha**, pour une humidité de 89,6 % et une température de 35,0 °C, en 15 évaluations seulement.

**Processus gaussiens** : la régression à noyau (RBF + bruit) capte la tendance moyenne et fournit un intervalle de confiance. En classification du type de sol, ni le modèle bayésien à noyau ni le SVM ne dépassent 31 % de bonnes prédictions.

## 📊 Données

500 parcelles décrites par 6 variables : humidité (%), température (°C), pH du sol, précipitations (mm), type de sol (argileux, limoneux, sableux) et rendement agricole (t/ha).

## 🛠️ Démarche

### Partie 1 : optimisation bayésienne

- **Principe** : construire un modèle probabiliste de la fonction à optimiser (processus gaussien) et choisir le prochain point à tester avec une fonction d'acquisition (Expected Improvement, UCB), qui équilibre exploration et exploitation.
- **Maximisation du rendement** avec `gp_minimize` (scikit-optimize), sur un modèle de rendement en fonction de l'humidité et de la température.
- **Réglage d'hyperparamètres** d'un `RandomForestRegressor` avec `gp_minimize` et `BayesSearchCV`, comparé à `GridSearchCV` et `RandomizedSearchCV`.
- **Analyse de la convergence** et des avantages et limites de la méthode.

### Partie 2 : modèles bayésiens à noyau

- **Régression par processus gaussien** (`GaussianProcessRegressor`, noyau RBF + WhiteKernel) avec intervalle de confiance.
- **Classification du type de sol** (`GaussianProcessClassifier`) comparée à un SVM.
- **Comparaison de noyaux** : RBF, linéaire (DotProduct), Rational Quadratic.
- **Analyse de l'incertitude** : incertitude épistémique (manque de données) et aléatoire (bruit).

## 🔍 Analyse critique et limites

- **Écarts faibles entre méthodes** : l'optimisation bayésienne gagne, mais les MSE restent proches (environ 2 % d'écart).
- **Évaluation** : la fonction objectif de `gp_minimize` est calculée sur le jeu de test, ce qui avantage cette méthode. Une validation croisée sur l'entraînement serait plus rigoureuse.
- **Optimum en bordure** : le rendement est modélisé par une régression linéaire, donc l'optimum se situe forcément aux limites de l'espace de recherche (humidité et température maximales). Un modèle non linéaire donnerait un optimum plus réaliste.
- **Régression par processus gaussien** : les prédictions sont presque constantes, signe que l'humidité et la température seules expliquent peu le rendement. Le réglage du noyau (longueur d'échelle) atteint sa borne.
- **Classification du type de sol** : 31 % de bonnes prédictions pour 3 classes, soit le niveau du hasard. L'humidité et la température ne suffisent pas à prédire le type de sol. Ajouter le pH et les précipitations, et standardiser les variables, sont les premières pistes.

## 🧰 Outils

Python · pandas · NumPy · scikit-learn · scikit-optimize · Matplotlib · Jupyter

## 📁 Fichiers

```
├── optimisation_bayesienne.ipynb   # Notebook complet
├── rapport_analyse.pdf             # Rapport d'analyse
└── donnees_agricoles.csv           # Données
```

## 👩‍💻 Autrice

**Ndeye Aminata Gadiaga**
[LinkedIn](https://linkedin.com/in/ndeye-aminata-gadiaga-26a97725b) · [GitHub](https://github.com/AMINATA1999)
