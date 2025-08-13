# Rapport Mini Projet - Data Processing
## Extraction et analyse des données géographiques du Burkina Faso

---

## 1. Téléchargement de la base GeoNames pour le Burkina Faso

### 1.1 Identification du code ISO
- **Code ISO du Burkina Faso** : `BF`
- **Source** : [https://download.geonames.org/export/dump/](https://download.geonames.org/export/dump/)
- **Fichier cible** : `BF.zip`

### 1.2 Structure des données
Selon le README de GeoNames, le fichier contient les colonnes suivantes :

| Position | Nom de colonne | Description |
|----------|----------------|-------------|
| 0 | geonameid | Identifiant unique |
| 1 | name | Nom du lieu |
| 2 | asciiname | Nom ASCII |
| 3 | alternatenames | Noms alternatifs |
| 4 | latitude | Latitude |
| 5 | longitude | Longitude |
| 6 | feature class | Classe de caractéristique |
| 7 | feature code | Code de caractéristique |
| 8 | country code | Code pays |
| 9 | cc2 | Code pays alternatif |
| 10 | admin1 code | Code administratif niveau 1 |
| 11 | admin2 code | Code administratif niveau 2 |
| 12 | admin3 code | Code administratif niveau 3 |
| 13 | admin4 code | Code administratif niveau 4 |
| 14 | population | Population |
| 15 | elevation | Élévation |
| 16 | dem | Modèle numérique d'élévation |
| 17 | timezone | Fuseau horaire |
| 18 | modification date | Date de modification |

### 1.3 Téléchargement
- **Status** : Terminé 
- **Date de téléchargement** : 13 Aout 2025 a 12∶34∶37 PM GMT
- **Taille du fichier** : 314.6 KB

---
## 2. Prétraitement des données

### 2.1 Extraction et nettoyage
- **Fichier source** : `BF.txt` (extrait de `BF.zip`)
- **Séparateur** : Tabulation (`\t`)
- **Colonnes conservées** :
  - `geonameid` (colonne 0) → `ID`
  - `name` (colonne 1) → `location_name` 
  - `latitude` (colonne 4) → `lat`
  - `longitude` (colonne 5) → `long`

### 2.2 Code utilisé
```python
# Dézipper le fichier dans un dossier data
with zipfile.ZipFile("BF.zip", "r") as zip_ref:
    zip_ref.extractall("data")

# Charger le fichier BF.txt et conserver les colonnes nécessaires
df = pd.read_csv("data/BF.txt", sep="\t", header=None, dtype=str)
df = df[[0, 1, 4, 5]]
df.columns = ["ID", "location_name", "lat", "long"]
df.to_csv("burkina_location.csv", index=False)
```

### 2.3 Résultats du prétraitement
- **Nombre total de lieux** : 11 958
- **Fichier généré** : [`burkina_location.csv`](./burkina_location.csv)
- **Validation** : Succès

---
## 3. Opérations d'extraction et d'analyse

### 3.1 Extraction des lieux contenant "gounghin"
- **Critère de recherche** : Nom contenant "gounghin" (insensible à la casse)
- **Code utilisé** :
```python
gounghin_df = df[df['location_name'].str.contains("gounghin", case=False, na=False)]
gounghin_df.to_csv("gounghin.csv", index=False)
```
- **Nombre de résultats** : 10 lieux
- **Fichier généré** : [`gounghin.csv`](./gounghin.csv)

**Détail des lieux trouvés :**
| ID | Nom du lieu | Latitude | Longitude |
|---|---|---|---|
| 2353306 | Gounghin | 12.06677 | -1.42134 |
| 2360473 | Gounghin | 12.62488 | -1.36398 |
| 2570204 | Gounghin | 12.31436 | -1.379 |
| 10342749 | Gounghin | 12.06667 | -0.15 |
| 10629032 | BICIAB // Gounghin | 12.35921 | -1.54273 |
| 11257296 | Gounghin Department | 12.06671 | -0.15484 |
| 11900526 | Gounghin Nord | 12.3612 | -1.55055 |
| 11900528 | Zone Industrielle de Gounghin | 12.36631 | -1.54137 |
| 11900619 | Gounghin Sud | 12.35298 | -1.54342 |
| 11900680 | Gounghin | 12.35895 | -1.54442 |

