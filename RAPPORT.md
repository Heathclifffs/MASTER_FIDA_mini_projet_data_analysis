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

### 3.2 Extraction des lieux A-P
- **Critère** : Première lettre du nom entre 'A' et 'P' (ordre alphabétique, insensible à la casse)
- **Code utilisé** :
```python
# Filtrer les lieux dont la première lettre est entre A et P (insensible à la casse)
A_to_P_df = df[df['location_name'].str[0].str.upper().between('A', 'P')]
# Trier par ordre alphabétique (insensible à la casse)
A_to_P_df = A_to_P_df.sort_values(by='location_name', key=lambda x: x.str.upper())
```
- **Nombre de résultats** : 8 306 lieux
- **Pourcentage du dataset** : 69.4% (8306/11958)

**Échantillon des résultats (premiers et derniers) :**

| ID | Nom du lieu | Latitude | Longitude |
|---|---|---|---|
| 6913771 | Abanda | 15.06808 | -0.59805 |
| 2363251 | Abanga | 13.32429 | 0.31151 |
| 11980339 | Abassi | 12.27728 | -1.13662 |
| 6874881 | Abaye | 13.4408 | -3.9019 |
| 2363249 | Abra | 13.0914 | -1.34752 |
| ... | ... | ... | ... |
| 2570015 | Pézinga | 12.05298 | -1.47002 |
| 2356655 | Pê | 11.3 | -3.53333 |
| 2356453 | Pô | 12.3 | -2.61667 |
| 2356454 | Pô | 11.16972 | -1.145 |
| 6296406 | Pô Airport | 11.17854 | -1.14498 |

*Note : 8 306 lignes au total dans le dataset A-P*

### 3.3 Coordonnées minimales
- **Code utilisé** :
```python
# Conversion en float pour les calculs
A_to_P_df['lat'] = A_to_P_df['lat'].astype(float)
A_to_P_df['long'] = A_to_P_df['long'].astype(float)

# Recherche des valeurs minimales
min_lat = A_to_P_df['lat'].min()
min_long = A_to_P_df['long'].min()

# Identification des lieux correspondants
lieux_min = A_to_P_df[
    (A_to_P_df['lat'].astype(float) == min_lat) |
    (A_to_P_df['long'].astype(float) == min_long)
]
```

**Résultats :**
- **Latitude minimale** : 5.21609° (point le plus au sud)
- **Longitude minimale** : -5.65968° (point le plus à l'ouest)

| ID | Nom du lieu | Latitude | Longitude | Position géographique |
|---|---|---|---|---|
| 2359210 | **Komoé** | **5.21609** | -3.71793 | Latitude la plus au **sud** |
| 2357400 | **Banifing** | 12.01147 | **-5.65968** | Longitude la plus à l'**ouest** |

**Remarques géographiques :**
- **Komoé** représente le point le plus méridional (sud) du Burkina Faso dans notre dataset A-P
- **Banifing** représente le point le plus occidental (ouest) du Burkina Faso dans notre dataset A-P

### 3.4 Filtrage par coordonnées
- **Critère** : `lat >= 11` ET `lon <= 0.5`
- **Code utilisé** :
```python

df['lat'] = df['lat'].astype(float)
df['long'] = df['long'].astype(float)

coord_df = df[
    (df['lat'] >= 11) & 
    (df['long'] <= 0.5)
]
```
- **Nombre de lieux trouvés** : 9 466 lieux
- **Pourcentage du dataset** : 79.2% (9466/11958)

**Échantillon des lieux trouvés :**

| ID | Nom du lieu | Latitude | Longitude |
|---|---|---|---|
| 2353158 | Zyonguen | 12.36667 | -0.45000 |
| 2353159 | Zyiliwèlè | 12.38333 | -2.73333 |
| 2353160 | Zyanko | 12.78333 | -0.41667 |
| 2353161 | Zouta | 13.14908 | -1.28197 |
| 2353162 | Zourtenga | 12.95741 | -1.28745 |
| ... | ... | ... | ... |
| 13494828 | Tounougou | 11.21462 | -0.06965 |
| 13494829 | Karmé | 11.66346 | 0.08201 |
| 13494830 | Damdamkom | 11.14989 | 0.43655 |
| 13494831 | Dazenré | 11.43801 | 0.21293 |
| 13494832 | Pogoyoaguen | 11.44725 | 0.21074 |

*Note : 9 466 lignes au total dans le dataset A-P*

## 5. Export des résultats vers Excel
**Fichier généré** : [``mini_projet.xlsx``](./mini_projet.xlsx)
**Contenu** :
- Feuille `gounghin` : Données de la section 3.1 
- Feuille `A_to_P` : Données de la section 3.2 

- **Code utilisé** :
```python

with pd.ExcelWriter("mini_projet.xlsx") as writer:
    gounghin_df.to_excel(writer, sheet_name="gounghin", index=False)
    A_to_P_df.to_excel(writer, sheet_name="A_to_P", index=False)
print("Fichier Excel 'mini_projet.xlsx' créé avec succès !")

]
```
**Resultats export**

| Métrique | Valeur |
|----------|--------|
| Fichier généré | `mini_projet.xlsx` |
| Taille du fichier | 262.5 KB |
| Total feuilles | 2 |
| Lignes (gounghin) | 10 |
| Lignes (A_to_P) | 8 306 |
| Format | Excel (.xlsx) |

---

## 6. Visualisation cartographique (Optionnel)

### 6.1 Carte des lieux "Gounghin"
- **Code utilisé** :
```python
import folium

# Créer une carte centrée sur le Burkina Faso
m = folium.Map(location=[12.2383, -1.5616], zoom_start=6)

# Ajouter des marqueurs pour chaque lieu contenant "gounghin"
for _, row in gounghin_df.iterrows():
    folium.Marker(
        location=[float(row['lat']), float(row['long'])],
        popup=row['location_name']
    ).add_to(m)

# Sauvegarder la carte
m.save("gounghin_map.html")
```
- **Fichier généré** : [`gounghin_map.html`](./gounghin_map.html)
- **Description** : Carte interactive avec marqueurs précis pour les 10 lieux contenant "gounghin"
- **Fix appliqué** : Conversion explicite des coordonnées en `float()` pour éviter les erreurs de type²

### 6.2 Carte générale du Burkina Faso
- **Code utilisé** :
```python
# Créer la carte centrée sur le Burkina Faso
burkina_map = folium.Map(location=[12.2383, -1.5616], zoom_start=6)

# Ajouter tous les lieux comme marqueurs circulaires
for _, row in df.iterrows():
    folium.CircleMarker(
        location=[row['lat'], row['long']],
        radius=3,
        popup=row['location_name'],
        color='green',
        fill=True,
        fill_color='green'
    ).add_to(burkina_map)

# Sauvegarder la carte
burkina_map.save("burkina_map.html")
```
- **Fichier généré** : [`burkina_map.html`](./burkina_map.html)
- **Description** : Carte interactive complète avec tous les 11 958 lieux du Burkina Faso
- **Style** : Marqueurs circulaires verts (radius=3) pour une meilleure lisibilité

---

## 7. Fichiers produits

- [`burkina_location.csv`](./burkina_location.csv) - Dataset principal nettoyé
- [`gounghin.csv`](./gounghin.csv) - Lieux contenant "gounghin"
- [`mini_projet.xlsx`](./mini_projet.xlsx) - Fichier Excel avec 2 feuilles
- [`gounghin_map.html`](./gounghin_map.html) - Carte interactive des lieux Gounghin
- [`burkina_map.html`](./burkina_map.html) - Carte interactive complète du Burkina Faso
- [`Extraction_sous-ensemble_de_datas.ipynb`](./Extraction_sous-ensemble_de_datas.ipynb) - Code source (Jupyter)
- Repos GitHub : [`MASTER_FIDA_mini_projet_data_analysis`](https://github.com/Heathclifffs/MASTER_FIDA_mini_projet_data_analysis)

---

## 8. Références et ressources

### 8.1 Sources de données
1. **GeoNames** - Base de données géographique mondiale 
   URL: https://www.geonames.org/ 
   Documentation: https://download.geonames.org/export/dump/readme.txt

### 8.2 Documentation technique
2. **Pandas Documentation** - Manipulation de données 
   URL: https://pandas.pydata.org/docs/ 
   Référence spécifique: `str.contains()`, `str.between()`, `astype()`

3. **Folium Documentation** - Visualisation cartographique 
   URL: https://python-visualization.github.io/folium/ 
   Tutoriel: https://folium.readthedocs.io/en/latest/quickstart.html

4. **Excel Export avec Pandas** 
   URL: https://pandas.pydata.org/docs/reference/api/pandas.ExcelWriter.html 
   Guide: https://realpython.com/working-with-large-excel-files-in-pandas/

### 8.3 Résolution des problèmes techniques
5. **Gestion des types de données (Fix 1.0.1)**
   Problème: Comparaisons échouent avec des chaînes 
   Solution: Conversion explicite avec `astype(float)` 
   Référence: https://stackoverflow.com/questions/15891038/change-data-type-of-columns-in-pandas

6. **Tri insensible à la casse (Fix 1.0.1)** 
   Problème: Ordre alphabétique incorrect avec majuscules/minuscules 
   Solution: Utilisation du paramètre `key=lambda x: x.str.upper()` 
   Référence: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html

7. **Filtrage de chaînes avec Pandas** 
   Méthode: `str.contains(pattern, case=False, na=False)` 
   Référence: https://pandas.pydata.org/docs/reference/api/pandas.Series.str.contains.html

### 8.4 Ressources géographiques
8. **Coordonnées du Burkina Faso** 
   Centre approximatif: 12.2383°N, 1.5616°W 
   Source: https://www.latlong.net/country/burkina-faso-35.html

9. **Codes ISO des pays** 
   Burkina Faso: BF (ISO 3166-1 alpha-2) 
   Source: https://www.iso.org/obp/ui/#iso:code:3166:BF

---

*Rédigé par Yipene Harold Ezekiel BASSOLE - Data Processing & Data Visualization - 13 Août 2025*

