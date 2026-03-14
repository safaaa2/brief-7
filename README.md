# WorkForce360 — HR Analytics Dashboard

> **Power BI · DAX · Star Schema**  
> Tableau de bord RH — Page Headcount & Mesures DAX

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![Status](https://img.shields.io/badge/Status-Livré%20✔-2E7D32?style=for-the-badge)

---

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
    FILTER(
        'Fact Table',
        'Fact Table'[hire_date] <= LASTDATE('Dates (Big)'[Date])
        &&
        (
            'Fact Table'[term_date] > LASTDATE('Dates (Big)'[Date])
            ||
            ISBLANK('Fact Table'[term_date])
        )
    )
)
```
Employés dont la **date d'embauche** est antérieure à la fin de la période sélectionnée,
et dont la **date de départ** est soit postérieure, soit vide (encore actifs).
`ISBLANK()` est la syntaxe DAX correcte pour tester une valeur vide.

---

### 3️⃣ Retention %
```dax
Retention % = 
DIVIDE(
    CALCULATE(
        [All Employees],
        FILTER(
            'Fact Table',
            'Fact Table'[hire_date] <= LASTDATE('Dates (Big)'[Date])
            &&
            (
                'Fact Table'[term_date] > LASTDATE('Dates (Big)'[Date])
                ||
                ISBLANK('Fact Table'[term_date])
            )
        )
    ),
    CALCULATE(
        [All Employees],
        FILTER(
            'Fact Table',
            'Fact Table'[hire_date] <= FIRSTDATE('Dates (Big)'[Date])
        )
    ),
    0
)
```
- **Numérateur** = employés encore actifs à la **fin** de la période (`LASTDATE`)
- **Dénominateur** = employés présents au **début** de la période (`FIRSTDATE`)
- `DIVIDE(..., 0)` évite la division par zéro

---

### 4️⃣ Turnover %
```dax
Turnover % = 
1 - [Retention %]
```
Complément direct de la Retention.  
Exemple : Retention = **90,69 %** → Turnover = **9,31 %**

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
