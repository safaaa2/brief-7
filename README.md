# WorkForce360 — HR Analytics Dashboard

> **Power BI · DAX · Star Schema**  
> Tableau de bord RH — Page Headcount & Mesures DAX

## 📋 Informations Générales

| Élément | Détail |
|--------|--------|
| **Titre** | WorkForce360 — HR Analytics Dashboard |
| **Auteur** | Omar Hitar |
| **Formation** | [INT-Maroc] DATA Analyst |
| **Outil** | Microsoft Power BI Desktop + DAX |
| **Période** | 09/03/2026 → 13/03/2026 |
| **Sources** | `people_data.csv` · `people_employment_history.csv` |

---

## 📁 Structure du Projet

```
WorkForce360/
│
├── WorkForce360.pbix                  # Fichier Power BI principal
│
├── data/
│   ├── people_data.csv                # Données employés
│   └── people_employment_history.csv  # Historique d'emploi
│
├── screenshots/
│   └── headcount.png                  # Capture de la page Headcount
│
└── README.md                          # Ce fichier
```

---

## 🗄️ Modélisation — Star Schema

Le modèle est structuré en **étoile** avec une Fact Table centrale reliée aux dimensions.

**Fact Table :** `People Fact` — métriques quantitatives + clés étrangères

| Dimension | Colonnes principales | Clé primaire |
|-----------|---------------------|--------------|
| `Department Dim` | Department, Sub-Department | DeptKey |
| `Job Level Dim` | Job Level | JobLevelKey |
| `Demographic Dim` | Gender, Race, Age | DemoKey |
| `Education Dim` | Education Level | EduKey |
| `Location Dim` | City, Country | LocationKey |
| `Marital Dim` | Marital Status | MaritalKey |
| `Date Dim` | Year, Month, Quarter | DateKey |

---

## 📐 Mesures DAX

> Toutes les mesures sont centralisées dans la table **`_Measures`**.

---

### 1️⃣ All Employees
```dax
All Employees =
COUNTROWS(People_Fact)
```
Compte le nombre **total de lignes** dans la Fact Table, toutes années et tous statuts confondus.

---

### 2️⃣ Headcount
```dax
Headcount =
CALCULATE(
    [All Employees],
    People_Fact[Active] = "Yes"
)
```
Filtre uniquement les employés **actifs**. Réagit dynamiquement aux slicers (Year, Department, Job Level…).

---

### 3️⃣ Turnover %
```dax
Turnover % =
DIVIDE(
    CALCULATE([All Employees], People_Fact[Active] = "No"),
    [All Employees],
    0
)
```
Ratio entre les employés **partis** et l'effectif total.  
Le `0` dans `DIVIDE()` évite toute erreur de division par zéro.

---

### 4️⃣ Retention %
```dax
Retention % =
1 - [Turnover %]
```
Complément direct du Turnover.  
Exemple : Turnover = **5,66 %** → Retention = **94,34 %**

---

## 📊 Page Headcount

### KPIs

| Mesure | Valeur exemple (2013) |
|--------|-----------------------|
| All Employees | 4 000 |
| Headcount | 757 |
| Turnover annuel | 5,66 % |
| Retention annuelle | 90,69 % |

### Visualisations

| Visuel | Indicateur |
|--------|------------|
| Bar chart | Headcount par Département (drill-down) |
| Bar chart | Headcount par Job Level |
| Stacked bar | On-site vs Remote |
| Map | Headcount par ville |
| Donut | Genre |
| Donut | Statut Marital |
| Horizontal bar | Race / Ethnie |
| Horizontal bar | Niveau d'éducation |
| Histogramme | Groupes d'âge |

### Slicers
`Year` · `Age` · `Gender` · `Race` · `Marital Status` · `Education` · `Location` · `Job Level` · `Department`

---

## ⚙️ Installation

```bash
# 1. Cloner le dépôt
git clone https://github.com/<votre-username>/WorkForce360.git

# 2. Ouvrir WorkForce360.pbix dans Power BI Desktop

# 3. Actualiser les données : Accueil → Actualiser tout
```

---

## 👤 Auteur

**Omar Hitar** · Formation [INT-Maroc] DATA Analyst · Mars 2026

---
*WorkForce360 · Power BI HR Analytics · © 2026*
