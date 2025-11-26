# Projet de Pipeline de Données : Analyse de l'Énergie Éolienne avec Microsoft Fabric

Ce projet met en œuvre un pipeline de données complet pour le traitement et l'analyse de données de production d'énergie éolienne. L'architecture suit le modèle **Médaillon (Bronze, Silver, Gold)**, une approche standard dans l'ingénierie des données pour organiser et raffiner les données par étapes successives.

Le pipeline est conçu pour être exécuté dans l'écosystème **Microsoft Fabric**, en utilisant des Lakehouses et des notebooks pour l'ingestion, la transformation et l'agrégation des données.

## Architecture Médaillon

Le pipeline est structuré en trois couches distinctes, chacune représentée par une étape dans les notebooks :

### 1. Couche Bronze (Données Brutes)
- **Objectif** : Ingérer les données depuis leur source et les stocker dans leur format original, sans transformation majeure. C'est la zone d'atterrissage des données.
- **Notebook associé** : `NB_Get_Daily_Data_Python.ipynb`

### 2. Couche Silver (Données Nettoyées)
- **Objectif** : Nettoyer, valider, dédupliquer et standardiser les données provenant de la couche Bronze. Les données sont enrichies et structurées pour être fiables et prêtes à l'emploi pour des analyses plus poussées.
- **Notebooks associés** : 
  - `NB_Bronze_To_Silver_Transformations_Python.ipynb`
  - `NB_Bronze_To_Silver_Transformations_SQL.ipynb` (propose une alternative en SQL pour les mêmes transformations)

### 3. Couche Gold (Données Agrégées)
- **Objectif** : Agréger les données de la couche Silver pour créer des vues spécifiques aux besoins métier (Business Intelligence, Analytics, Machine Learning). Les données sont optimisées pour la performance des requêtes et la facilité d'utilisation.
- **Notebook associé** : `NB_Silver_To_Gold_Transformations_Python.ipynb`

## Source de Données

Le jeu de données initial utilisé pour ce projet est `data/20240615_wind_power_data.csv`. Il contient des informations sur la production d'énergie éolienne, probablement avec des colonnes telles que :
- Un identifiant de turbine
- Un horodatage
- La vitesse du vent
- La direction du vent
- La puissance générée (en kW ou MW)

## Description des Notebooks

Chaque notebook représente une étape logique du pipeline :

#### 1. `NB_Get_Daily_Data_Python.ipynb`
- **Rôle** : Ingestion des données.
- **Processus** :
  - Charge les données brutes (par exemple, depuis un fichier CSV ou une API).
  - Écrit les données dans la table/le répertoire `Bronze` du Lakehouse sans modification.

#### 2. `NB_Bronze_To_Silver_Transformations_Python.ipynb`
- **Rôle** : Transformation et nettoyage des données avec PySpark.
- **Processus** :
  - Lit les données depuis la couche `Bronze`.
  - Applique des transformations telles que :
    - Renommage et standardisation des colonnes.
    - Conversion des types de données (ex: string vers timestamp).
    - Gestion des valeurs nulles ou aberrantes.
    - Création de nouvelles colonnes (ex: extraction de l'heure, du jour, du mois).
  - Écrit les données nettoyées dans la table/le répertoire `Silver`.

#### 3. `NB_Bronze_To_Silver_Transformations_SQL.ipynb`
- **Rôle** : Alternative au notebook Python pour le passage de Bronze à Silver, en utilisant la puissance de Spark SQL.
- **Processus** :
  - Exécute des requêtes `CREATE OR REPLACE TABLE AS SELECT ...` pour transformer les données de la couche `Bronze` et les charger dans la couche `Silver`.
  - Démontre comment effectuer les mêmes opérations de nettoyage et de standardisation via des commandes SQL.

#### 4. `NB_Silver_To_Gold_Transformations_Python.ipynb`
- **Rôle** : Agrégation et création de vues métier.
- **Processus** :
  - Lit les données nettoyées depuis la couche `Silver`.
  - Effectue des agrégations pour répondre à des questions métier, par exemple :
    - Production moyenne par heure/jour/turbine.
    - Corrélation entre la vitesse du vent et la puissance générée.
    - Calcul de la production totale par parc éolien.
  - Écrit les résultats dans la table/le répertoire `Gold`, prêts à être consommés par des outils de BI comme Power BI ou pour des modèles de Machine Learning.

## Prérequis et Utilisation

Pour exécuter ce projet, vous avez besoin de :
1. Un espace de travail (workspace) Microsoft Fabric.
2. Un **Lakehouse** configuré pour stocker les données des couches Bronze, Silver et Gold.
3. Le jeu de données `20240615_wind_power_data.csv` téléversé dans la section `Files` du Lakehouse.

L'ordre d'exécution des notebooks est le suivant :
1. `NB_Get_Daily_Data_Python.ipynb`
2. `NB_Bronze_To_Silver_Transformations_Python.ipynb` (ou la version SQL)
3. `NB_Silver_To_Gold_Transformations_Python.ipynb`

## Axes d'Amélioration
- **Automatisation** : Utiliser les **Pipelines de Données Fabric** pour orchestrer et planifier l'exécution automatique de ces notebooks.
- **Monitoring** : Mettre en place des alertes pour surveiller la qualité des données et les échecs potentiels du pipeline.
- **Reporting** : Connecter un rapport Power BI à la couche `Gold` pour visualiser les indicateurs de performance clés (KPIs).
- **Scalabilité** : Adapter les notebooks pour traiter des volumes de données plus importants en optimisant les configurations Spark.