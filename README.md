# MASTER_FIDA_mini_projet_data_analysis 

## Docummentationm  
- [Rapport du mini projet](RAPPORT.md)  

# Événement de cours

## Mini Projet

Le but de cet exercice est d'extraire un sous-ensemble de données à partir d'une base de données plus grande.

Supposons que vous ayez besoin de réaliser une application qui prend en entrée, des noms de lieux du Burkina Faso, avec des informations telles que la latitude/longitude.

Pour ce faire, nous décidons d'extraire ces informations à partir du server de référencement géoname ([http://www.geonames.org/](http://www.geonames.org/)). Vous allez procéder de la façon suivante :

## Étapes du projet

### 1 - Export de la base de données

Exporter la base de données qui recense les informations sur le Burkina Faso ([https://download.geonames.org/export/dump/](https://download.geonames.org/export/dump/)).

Pour ce faire, reférez-vous au Readme, décrit à la fin de la page pour identifier le code iso correspondant au Burkina Faso.

### 2 - Téléchargement

Télécharger le fichier zip correspondant.

### 3 - Prétraitement et filtrage

Appliquer les opérations de prétraitement et filtres nécessaires à ce fichier, pour ne garder que les colonnes correspondantes :
- Identifiants
- Noms de lieux 
- Latitudes
- Longitudes

Renommez les avec les noms suivants : `'ID'`, `'location_name'`, `'lat'`, `'long'`

Sauvegarder ces données dans un fichier CSV, nommez-le `burkina_location.csv`

### 4 - Opérations sur le fichier CSV burkina_location.csv

#### 4.1
Extraire les données contenant le nom 'gounghin', enregistrez-le sous le fichier `gounghin.csv`

#### 4.2
Extraire la sous-partie de la base de données (fichier `burkina_location.csv`), dont les noms les premières lettres des noms de lieux sont compris entre 'A' et 'P' (ordre alphabétique)

#### 4.3
Identifiez respectivement, la latitude, la longitude minimale et les noms de lieux correspondants

#### 4.4
Quels sont les lieux dont les coordonnées sont comprises entre (lat >= 11 et lon <= 0.5)

### 5 - Sorties Excel

À partir des extractions de l'étape 4 :

- Créer un fichier Excel et nommer le : `mini_projet`
- Créer une feuille dans ce fichier, du nom `gounghin` et enregistrer les données contenant le nom 'gounghin' obtenues dans 4.1
- Créer une seconde feuille dans ce même fichier, du nom `A_to_P` et enregistrer les données de 4.2

## Livrables

Chacun devrait rendre :

1. Un fichier (Jupyter ou Google Colab) à partir duquel, le code entré et la sortie seront bien visibles.

2. Un répertoire github (lien d'accès) associé au nom (`mini_projet_data_analysis`), dans lequel il mettra les données utilisées, le codes produits, et les données issues des différentes manipulations.

---

*Data Processing - Data Viz*
