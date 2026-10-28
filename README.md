# Prévision du nombre de logements individuels autorisés en France

Projet de Master (Techniques de prévision et conjoncture), réalisé sur R et Quarto.

## Objectif
Analyser puis prévoir sur un an (2020) le nombre mensuel de **logements individuels autorisés à la construction** en France (hors Mayotte), et comparer onze méthodes de prévision.

## Données
Série mensuelle INSEE, janvier 2001 à décembre 2020 (240 observations), fichier `data/valeurs_mensuellesS.csv`.

## Démarche
1. **Exploration** : tendance, saisonnalité (graphique saisonnier, tests de Webel-Ollech et variables saisonnières, périodogramme).
2. **Points atypiques** (`tsoutliers`, Chen et Liu) : 6 points détectés. Les trois plus forts sont liés à la réglementation thermique RT 2012 (AO 2013:02, LS 2013:04) et au premier confinement (TC 2020:04).
3. **Désaisonnalisation** X13-ARIMA-SEATS (`RJDemetra`) : schéma multiplicatif, RegARIMA(0,1,1)(1,1,1)<sub>12</sub>, effets de calendrier.
4. **Prévision** sur 2020, modèles estimés sur 2001-2019 : naïve saisonnière, X13, STL, STS, Holt-Winters, ETS, TBATS, ADAM ETS, ADAM ETS-SARIMA, SARIMA, SSARIMA.
5. **Évaluation** : MSE, R²<sub>OOS</sub> par rapport à la prévision naïve, erreur quadratique cumulée (CSPE), test de Diebold-Mariano contre un AR(1).

## Résultats
Évaluation hors échantillon sur 2020 (modèles estimés sur 2001-2019, cible : série corrigée des points atypiques).

| Modèle | MSE | R²<sub>OOS</sub> | p-value DM (vs AR(1)) |
|---|---|---|---|
| Naïve saisonnière | 1 233 516 | référence | 0,093 |
| Holt-Winters | 1 307 014 | -0,060 | 0,133 |
| ADAM ETS | 1 378 820 | -0,118 | 0,128 |
| STS | 1 577 125 | -0,279 | 0,187 |
| SSARIMA | 1 619 605 | -0,313 | 0,216 |
| STL | 1 638 873 | -0,329 | 0,217 |
| ADAM ETS-SARIMA | 1 735 375 | -0,407 | 0,219 |
| X13-SARIMA-SEATS | 1 735 862 | -0,407 | 0,241 |
| TBATS | 1 814 810 | -0,471 | 0,234 |
| SARIMA | 1 890 119 | -0,532 | 0,282 |
| ETS | 1 944 454 | -0,576 | 0,274 |

- **Aucun modèle ne bat la prévision naïve saisonnière** (aucun R²<sub>OOS</sub> positif). Sur la série brute, qui contient le choc d'avril 2020, le classement par MSE est le même pour la tête (naïf, puis Holt-Winters, puis ADAM ETS).
- **Aucun modèle n'est significativement meilleur que l'AR(1) au seuil de 5 %** ; seul le naïf approche le seuil de 10 % (p = 0,093).
- Les modèles les plus complexes (SARIMA, TBATS, ETS, ADAM-SARIMA) ne gagnent rien sur 2020, année dominée par un choc imprévisible. Les critères d'information (AIC) ne sont pas comparables entre échelles et familles de modèles.

## Limites
- Un seul horizon de test (12 mois), sur une année exceptionnelle (COVID-19) : le classement des modèles est peu robuste.
- La détection des points atypiques utilise toute la période, y compris 2020 ; la cible de test est la série corrigée (une vérification sur la série brute est incluse).
- Test de Diebold-Mariano fondé sur 12 observations : p-values indicatives.
- Les explications économiques des points atypiques sont des hypothèses, non testées.

## Contenu du dépôt
| Fichier | Description |
|---|---|
| `projet_prevision_permis.qmd` | Rapport Quarto (code et texte) |
| `projet_prevision_permis.pdf` | Rapport rendu |
| `data/valeurs_mensuellesS.csv` | Données INSEE |

## Reproduire
1. Installer R, Quarto et **Java** (requis par `RJDemetra`).
2. Installer les packages : `readr`, `tsoutliers`, `forecast`, `smooth`, `TSA`, `moments`, `fBasics`, `ggplot2`, `RJDemetra`, `tidyr`, `dplyr`, `purrr`, `tibble`, `seastests`.
3. Ouvrir le dossier du dépôt dans RStudio et rendre `projet_prevision_permis.qmd`.
