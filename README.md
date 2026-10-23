# Project_ML_Injury_Prevention

**Modèle prédictif de risque de blessures et retours de blessure pour les joueurs de rugby du Top 14 (2013–2026) basé sur XGBoost et les sciences du sport.**

---

## Présentation du Projet

Ce projet a pour objectif de modéliser et de prédire le risque de retour de blessure (*Time-Loss Injury*) chez les joueurs professionnels de rugby du Top 14. 

En s'appuyant sur un historique de **11 saisons (2013 à 2026)** et **38 177 logs de matchs**, le modèle évalue l'impact de la fatigue cumulée, de la congestion du calendrier et du profil biométrique du joueur avant chaque rencontre.

---

## Source & Qualité des Données

* **Origine des données** : Scraping et nettoyage propriétaire à partir des logs de matchs officiels de `all.rugby` (2013–2026).
* **Abandon de `rugbypy`** : La librairie `rugbypy` a été écartée en raison de la forte rareté (*missingness*) des statistiques d'impacts détaillés (plaquages, rucks) sur le Top 14 historique.
* **Intégrité scientifique** : **0 % de données synthétiques / imputation**. Seules les données réelles vérifiées et les biométries extraites des fiches joueurs (*Gold Profiles*) sont conservées.

---

## Définition de la Cible (`is_injury_return`)

La variable cible $Y$ est définie selon les critères internationaux du *Time-Loss Injury* :
* **Définition** : Match de reprise officiel ($Y=1$) faisant suite à une absence clinique réelle pour blessure.
* **Fenêtre d'arrêt retenue** : Entre **21 et 300 jours d'absence effectifs**.
* **Correction des biais (Faux Positifs)** :
  * **Trêve estivale** : Déduction automatique d'un forfait de 60 jours pour les arrêts à cheval sur juillet/août afin de ne pas confondre pause inter-saison et blessure.
  * **Rotations tactiques** : Exclusions des arrêts de moins de 21 jours.
  * **Blessures graves** : Intégration des indisponibilités de longue durée (jusqu'à 10 mois / croisés, Achille).

---

## Feature Engineering (Sciences du Sport & Machine Learning)

Le jeu de données comprend **26 variables prédictives** réparties en 4 catégories fondamentales :

### 1. Charge Exp. & Décroissance Physiologique (EWMA)
* `ewma_acute_7d` : Fatigue immédiate sur 7 jours avec pondération exponentielle.
* `ewma_chronic_28d` : Capacité physique sur 28 jours avec lissage physiologique.
* `acwr_ewma` : Ratio *Acute:Chronic Workload Ratio* basé sur l'EWMA (évite les artefacts à zéro lors des reprises).

### 2. Charge Rolling Classique & Congestion
* `acute_workload_7d` / `chronic_workload_28d` / `acwr` : Métriques de charge classiques.
* `minutes_last_14d` : Indicateur de congestion de calendrier à très court terme.
* `minutes_last_30d` / `minutes_last_60d` : Trajectoire du volume de jeu sur 1 et 2 mois.
* `matches_count_30d` : Nombre de feuilles de match enchaînées sur 30 jours.

### 3. Rythme & Contexte
* `days_since_last_match` : Jours de récupération écoulés depuis la dernière apparition.
* `minutes_played` : Temps de jeu sur le match.
* `match_month` : Prise en compte de la saisonnalité (état des terrains, météo).

### 4. Biométrie Réelle & Morphologie
* `height_cm` / `weight_kg` : Taille et poids réels issus des profils validés.
* `bmi` : Indice de masse corporelle ($\frac{\text{poids}}{\text{taille}^2}$) traduisant l'intensité des impacts.
* `position` : Poste du joueur (contraintes physiques et sollicitations spécifiques).

---

## Stratégie de Validation & Modélisation

* **Algorithme** : XGBoost Classifier.
* **Découpage temporel strict (Anti-Data Leakage)** :
  * **Train Set** : Juillet 2013 à Juin 2024 (~80 % des données / 11 saisons d'apprentissage).
  * **Test Set** : Juillet 2024 à Octobre 2026 (~20 % des données / 2 saisons de validation en conditions réelles).
* **Gestion de l'imbalancement** : Optimisation du paramètre `scale_pos_weight` pour refléter la prévalence réelle des retours de blessure (~11,5 %).
* **Métriques de performance** : ROC-AUC score, PR-AUC (Precision-Recall AUC) et matrice de confusion.

---

## Structure du Projet

```text
├── CSV/
│   ├── top14_matches_processed.csv   # Logs de matchs nettoyés
│   ├── top14_gold_players.csv        # Profils biométriques vérifiés
│   └── top14_ml_features.csv         # Matrice de 26 variables prédictives
├── notebooks/
│   ├── 01_data_cleaning.ipynb        # Nettoyage et validation des données
│   ├── 02_feature_engineering.ipynb   # Calcul de l'ACWR, EWMA et IMC
│   └── 03_model_training.ipynb       # Entraînement et évaluation XGBoost
└── README.md