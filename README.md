# Project_ML_Injury_Prevention
rugbypy fournit jusqu'à 59 colonnes d'informations par joueur et par match.
Voici le détail complet des variables disponibles dans la base de données, classées par catégories utilisables pour la création de tes variables (feature engineering) :
1. Profil biométrique et contexte
Métadonnées joueur : player_id, name, height (taille), weight (poids), position / role (poste).
Contexte match : game_date, competition, team (équipe du joueur), team_vs (adversaire), home_or_away (domicile/extérieur).
2. Volume de jeu et charge de déplacement
Temps de jeu : minutes_played (minutes disputées).
Déplacements avec ballon : runs / carries (ballons portés), metres_carried (mètres parcourus), clean_breaks / line_breaks (franchissements).
3. Impacts physiques et contacts (indispensables pour modéliser la fatigue)
Plaquages : tackles (plaquages réussis), missed_tackles (plaquages manqués).
Rucks & Duels : rucks_won, rucks_lost, defenders_beaten (défenseurs battus), offloads.
4. Phases arrêtées (spécifiques aux avants)
Mêlées : scrums_total, scrums_won.
Touches : total_lineouts, lineouts_won.
5. Événements et discipline
Fautes & Cartons : yellow_cards, red_cards, total_free_kicks_conceded, turnovers_conceded.
Points & Actions : tries, try_assists, kicks, metres_kicked.
Comment exploiter tout ça pour ton modèle de blessures ?
En médecine du sport (sports science), la blessure ne dépend pas seulement des minutes courues, mais du volume d'impacts cumulés et de la fatigue physique.
Avec rugbypy, tu peux construire des variables explicatives (X) très riches :
Volume d'impacts cumulés (7j et 28j) :
Collision Load=tackles+carries+scrums_total
Ratio ACWR de collisions : Ratio entre les impacts des 7 derniers jours (charge aiguë) et la moyenne sur 28 jours (charge chronique).
Temps de récupération efficace : Nombre de jours de repos entre deux matchs d'intensité élevée (>60 minutes jouées).
Indice d'usure physique : Ratio  
poids (kg)
m 
e
ˋ
 tres parcourus+plaquages
​	