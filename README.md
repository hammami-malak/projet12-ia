# Prédiction du délai de traitement des demandes administratives

Mini-projet d'intelligence artificielle (Sujet 12) — Faculté des Sciences de Tunis (FST), année universitaire 2026-2027.

- **Réalisé par :** Hammami Malak
- **Encadrante :** Ghrissi Imen

## 1. Objectif

Quand on dépose une demande administrative, on ne sait pas combien de jours elle va prendre.
Ce projet utilise l'IA pour **prédire le nombre de jours** nécessaires au traitement d'une demande, à partir de :

- son **type**,
- sa **complexité** (de 1 à 5),
- son **nombre de pièces justificatives**.

**Technique d'IA :** apprentissage supervisé, plus précisément une **régression** (on prédit un nombre, pas une catégorie).
Deux modèles sont entraînés et comparés : la **régression linéaire** et le **Random Forest**.

## 2. Contenu du dépôt

```
├── README.md
├── requirements.txt
├── notebooks/
│   ├── projet12_hammami-malak.ipynb             # données, modèles, résultats, démonstration
│   └── projet12_hammami-malak-AppGRADIO.ipynb   # application web (Gradio)
├── data/
│   └── demandes_hammami-malak.xlsx              # 1000 demandes simulées
├── tests/
│   ├── test1_fichier_valide.xlsx
│   ├── test2_titres_accentues.xlsx
│   ├── test3_point_virgule.csv
│   ├── test4_colonne_manquante.xlsx
│   └── test5_lignes_incompletes.xlsx
└── docs/
    ├── projet12_hammami-malak.pptx              # présentation
    └── projet12_hammami-malak.pdf
```

## 3. Les données

Je n'ai pas de vraies données : les **1000 demandes sont simulées** à partir de 12 services d'une mairie (délai habituel + un petit hasard).
Le fichier `data/demandes_hammami-malak.xlsx` est généré par le notebook `projet12_hammami-malak.ipynb`.

Colonnes du fichier :

| Colonne | Signification |
|---|---|
| `type` | service demandé (12 services) |
| `complexite` | de 1 à 5 : 1 + nombre de critères remplis (étude technique, visite sur place, accord d'une autre administration, examen juridique) |
| `nb_pieces` | nombre de pièces justificatives |
| `delai` | délai de traitement en jours (valeur à prédire) |

Un délai de 0 jour signifie que la demande est traitée le jour même.
Les délais marqués « supposés » (enseigne publicitaire, autorisation d'installation, exploitation local commercial, permis de lotir, régularisation construction) sont des valeurs choisies, sans délai officiel fixe.

## 4. Installation et lancement

### Option A — Google Colab (recommandé, rien à installer)

1. Ouvrir Google Colab, puis **Fichier > Ouvrir le notebook > GitHub** et coller l'adresse de ce dépôt.
2. Ouvrir `notebooks/projet12_hammami-malak.ipynb` et exécuter les cellules dans l'ordre (**Exécution > Tout exécuter**).
3. Pour l'application web : ouvrir `notebooks/projet12_hammami-malak-AppGRADIO.ipynb` et l'exécuter.

### Option B — Sur un ordinateur

```bash
pip install -r requirements.txt
jupyter notebook
```

Puis ouvrir les notebooks du dossier `notebooks/`.

## 5. Utilisation de l'application

1. **Charger un fichier** de demandes (CSV ou Excel) avec les colonnes `type`, `complexite`, `nb_pieces`, `delai`. Les titres accentués, les majuscules et le séparateur `;` sont acceptés.
   Sans fichier : le bouton « Générer un fichier exemple » en crée un.
2. **Entraîner les modèles** : l'application affiche le nombre de demandes lues, les erreurs (MAE, RMSE, R²) et le graphique prédit contre réel.
3. **Prédire** : choisir le type de demande, la complexité et le nombre de pièces, puis cliquer sur « Prédire ».

## 6. Bibliothèques utilisées et justification des choix

| Outil | À quoi il sert | Choisi plutôt que | Pourquoi |
|---|---|---|---|
| **pandas** + **openpyxl** | lire et préparer les tableaux (CSV, Excel) | base de données SQL | un fichier CSV ou Excel suffit et l'utilisateur le connaît déjà |
| **NumPy** | calcul et génération aléatoire des 1000 demandes | — | outil de base du calcul en Python |
| **scikit-learn** | les deux modèles et les mesures d'erreur | TensorFlow, PyTorch | modèles simples et rapides, adaptés à un petit tableau de 1000 lignes |
| Régression linéaire + Random Forest | prédire le délai | réseau de neurones | deux modèles faciles à expliquer et à comparer |
| **Matplotlib** + **Seaborn** | graphiques | Excel | graphiques faits directement dans le code, faciles à refaire |
| **Gradio** | interface web de l'application | Flask, React | interface en quelques lignes de Python, sans HTML ni JavaScript |
| **Google Colab** | environnement d'exécution et lien public | installation sur le PC | gratuit, rien à installer |

## 7. Résultats

Évaluation sur 200 demandes de test (80 % apprentissage, 20 % test) :

| Modèle | MAE (jours) | RMSE (jours) | R² | Prédictions à ±1 jour |
|---|---|---|---|---|
| Régression linéaire | 1,68 | 2,92 | 0,979 | 59,5 % |
| Random Forest | 1,69 | 2,92 | 0,979 | 60,0 % |

- Les deux modèles se trompent d'environ **1,7 jour** en moyenne et obtiennent des résultats presque identiques.
- Une simple moyenne par type de demande donne aussi une erreur de 1,68 jour : les données suivent des règles claires, faciles à retrouver.
- Dans ces données simulées, la complexité est la variable la plus importante (91 % de l'importance dans le Random Forest), et le nombre de pièces n'influence pas le délai.

## 8. Tests

Les fichiers du dossier `tests/` servent à vérifier l'application : on les charge un par un, puis on clique sur « Entraîner les modèles ».

| Test | Fichier | Résultat attendu | Résultat observé |
|---|---|---|---|
| Fichier valide | `test1_fichier_valide.xlsx` | 1000 demandes lues, MAE 1,68 | OK |
| Titres accentués | `test2_titres_accentues.xlsx` | titres reconnus (`Type`, `Complexité`, `Nb pièces`, `Délai`), même résultat | OK |
| Séparateur `;` | `test3_point_virgule.csv` | fichier lu correctement, même résultat | OK |
| Colonne manquante | `test4_colonne_manquante.xlsx` | message : « Colonnes manquantes : delai » | OK |
| Lignes incomplètes | `test5_lignes_incompletes.xlsx` | 990 demandes lues, 10 lignes ignorées | OK |
| Aucun fichier chargé | — | message d'avertissement | OK |
| Prédire avant d'entraîner | — | message d'avertissement | OK |

## 9. Problèmes rencontrés

- **Fichiers variés** : séparateurs et accents différents. L'application détecte le séparateur et nettoie les titres des colonnes.
- **Valeurs vides** : les lignes incomplètes sont ignorées et leur nombre est affiché.
- **Complexité** : le nombre de pièces ne suffit pas à décrire une demande ; la complexité est donc définie par 4 critères.
- **Résultats presque parfaits** (R² = 0,98) parce que les données sont simulées : avec de vraies données, ils seraient moins bons.

## 10. Limites et perspectives

- Les données sont **simulées** : les délais sont des valeurs de référence ou supposées, ils ne mesurent pas les retards réels.
- Dans les données simulées, chaque service a **une seule complexité**. Dans l'application, la complexité se règle à la main, mais une combinaison jamais vue (par exemple une copie conforme de complexité 5) donne un résultat peu fiable.
- Seulement 3 informations sont utilisées
