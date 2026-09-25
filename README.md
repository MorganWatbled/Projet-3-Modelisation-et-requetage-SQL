# Base de données contrats d'assurance habitation — Modélisation et requêtage SQL

Modélisation d'une base de données relationnelle à partir de deux fichiers CSV (contrats et référentiel géographique), puis exploitation via des requêtes SQL pour répondre à des questions métier.

---

## Contexte / besoin métier

Le projet consiste à structurer, en base de données relationnelle, un jeu de données de contrats — au vu des champs disponibles (type de contrat : résidence principale/secondaire/mise en location, formule classique/intégrale, valeur déclarée des biens, prix de cotisation mensuel), il s'agit vraisemblablement de contrats d'**assurance habitation**, chaque contrat étant rattaché à une adresse et donc à un référentiel géographique français (région, académie, département, commune).

L'objectif est double :
- construire une base de données propre et normalisée à partir de deux fichiers sources (contrat.csv et région.csv) ;
- permettre, via des requêtes SQL, de répondre à des questions d'analyse : répartition géographique des contrats, profils de biens assurés, cotisations moyennes par territoire, etc.

## Données (source, qualité, limites)

**Sources :**
- Fichier **contrat.csv** : un contrat par ligne, avec l'adresse du bien assuré (numéro, type et nom de voie, code commune, code postal), sa surface, son type de local, son occupation, son type de contrat, sa formule, la valeur déclarée des biens et le prix de cotisation mensuel.
- Fichier **région.csv** : référentiel géographique français, avec code région, nom de région, nom d'académie, nom de département, nom de commune et code département, rattaché à chaque contrat via une clé commune (code département + code commune).

**Qualité :**
- La base chargée contient l'intégralité des données sources : **30 335 lignes** dans la table contrat et **38 916 lignes** dans la table région, confirmées par capture d'écran.
- Les 12 requêtes SQL produites renvoient toutes un résultat exploitable, sans erreur.

**Limites :**
- Le dictionnaire des données ne précise pas explicitement la nature exacte de l'activité (assurance habitation déduite des champs, non confirmée par un document de cadrage métier).
- La clé de rattachement contrat/région repose sur la concaténation code département + code commune : sa fiabilité dépend de la cohérence de ce format entre les deux fichiers sources.

## Démarche (choix, outils, étapes)

1. Observation du contenu des deux fichiers CSV pour comprendre les données disponibles et leurs typologies.
2. Rédaction du **dictionnaire des données** pour les tables contrat et région (nom des colonnes, type, taille, clé, description), avec ajout de contraintes lorsque nécessaire.
3. Modélisation en **MCD** (modèle conceptuel de données) : une table contrat rattachée à une table région via la clé `Code_dep_code_commune`, en relation plusieurs-à-un (plusieurs contrats pour une même zone géographique).
4. Création des deux tables dans une base MySQL (`projet3`), avec définition des clés primaires et de la contrainte de clé étrangère entre contrat et région.
5. Chargement des données et vérification du nombre de lignes chargées (contrat : 30 335, région : 38 916).
6. Rédaction de **12 requêtes SQL** pour répondre aux questions d'analyse (voir résultats ci-dessous), sous forme de vues, avec vérification systématique de l'absence d'erreur et de la présence d'un résultat.
7. Préparation d'un support de présentation de la méthodologie (étapes suivies, dictionnaire des données, schéma relationnel normalisé, capture d'écran de la base chargée).

**Outil :** MySQL Workbench (modélisation MCD et requêtage SQL).

## Résultats + impact / recommandations

Principaux résultats obtenus via les requêtes SQL :

- **Cotisation mensuelle moyenne** (toute la base) : 19,33 €.
- **Valeur déclarée des biens** : très forte concentration sur les tranches basses — 22 720 contrats entre 0 et 25 000 €, 6 815 entre 25 000 et 50 000 €, 696 entre 50 000 et 100 000 €, et seulement 104 au-delà de 100 000 €.
- **Top départements par cotisation mensuelle moyenne** : Paris (36,40 €), Hauts-de-Seine (26,27 €), Val-de-Marne (19,82 €), Yvelines (18,89 €), Rhône (18,49 €) — une prime nettement plus élevée en Île-de-France.
- **Répartition régionale des contrats** : les régions les plus représentées sont Provence-Alpes-Côte d'Azur (3 279 contrats), Auvergne-Rhône-Alpes (3 042) et Nouvelle-Aquitaine (2 038).
- **Communes concentrant le plus de contrats** (≥150) : Nice (387), Bordeaux (302), Nantes (291), Grenoble (220), Toulon (170), Toulouse (187), Courbevoie (163), Lille (161).
- **Top 5 des biens par surface** : jusqu'à 815 m² pour le contrat le plus grand.
- **Surface moyenne assurée en académie de Paris** : 51,77 m².

**Impact attendu :** une base de données propre, normalisée et interrogeable, permettant à l'entreprise de piloter finement son activité par territoire (cotisations, volumes de contrats, typologie de biens) et d'identifier les zones à forte valeur (Paris, petite couronne) ou à fort volume (grandes métropoles du Sud et de l'Ouest).

## Limites + prochaines pistes

- Les requêtes actuelles couvrent des indicateurs ponctuels (moyennes, tops, comptages) ; une vision consolidée par tableau de bord permettrait un suivi continu plutôt que des extractions au coup par coup.
- L'analyse reste centrée sur la dimension géographique et la valeur des biens ; croiser ces résultats avec le type de contrat ou la formule (classique/intégrale) permettrait d'affiner la lecture (ex. quelles formules sont privilégiées dans les zones à cotisation élevée).
- Le schéma actuel repose sur deux tables ; l'ajout de données socio-économiques par région (déjà évoqué dans d'autres missions data de l'entreprise) pourrait enrichir l'analyse du risque ou du potentiel commercial par territoire.

---

*Projet de modélisation et de requêtage SQL réalisé à partir des fichiers contrat.csv et région.csv.*
