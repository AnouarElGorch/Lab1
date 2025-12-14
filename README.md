# MLOps Lab 01 - Projet de Machine Learning avec Gestion de Modèles

Ce projet illustre un pipeline MLOps complet pour la prédiction de churn clients, incluant la génération de données, l'entraînement de modèles, le versioning, le déploiement via API et la détection de dérive.

## 📁 Structure du Projet

```
mlops-lab-01/
├── data/                      # Données brutes et traitées
│   ├── raw.csv
│   └── processed.csv
├── models/                    # Modèles entraînés versionnés
│   ├── churn_model_v1_*.joblib
│   └── churn_model_v2_*.joblib
├── registry/                  # Registre des modèles
│   ├── current_model.txt      # Référence au modèle actif
│   ├── metadata.json          # Métadonnées des modèles
│   └── train_stats.json       # Statistiques d'entraînement
├── logs/                      # Logs d'inférence et monitoring
├── src/                       # Code source
│   ├── generate_data.py       # Génération du dataset
│   ├── prepare_data.py        # Préparation et validation
│   ├── train.py               # Entraînement et versioning
│   ├── api.py                 # API FastAPI
│   ├── evaluate.py            # Évaluation des modèles
│   ├── monitor_drift.py       # Détection de dérive
│   └── rollback.py            # Gestion des versions
└── venv_mlops/               # Environnement virtuel Python
```

## 🚀 Guide de Mise en Place

### Étape 1 : Initialiser la Structure du Projet
<img width="619" height="528" alt="image" src="https://github.com/user-attachments/assets/51fb6008-cefa-44f6-ab4f-721554af70db" />

Créer les dossiers nécessaires :

```powershell
New-Item -ItemType Directory -Force data, models, registry, logs, src
```

### Étape 2 : Préparer l'Environnement Python
<img width="945" height="255" alt="image" src="https://github.com/user-attachments/assets/6d89ca56-6eeb-493f-8de6-c615225d6bc3" />
<img width="945" height="245" alt="image" src="https://github.com/user-attachments/assets/e74a388d-7a9d-4dba-97ec-32a75cbadad9" />

Créer et activer un environnement virtuel :

```powershell
python -m venv venv_mlops
.\venv_mlops\Scripts\Activate.ps1
```

Installer les dépendances :

```powershell
pip install pandas scikit-learn fastapi uvicorn joblib
```

### Étape 3 : Générer le Dataset

Créer le fichier de génération de données :

```powershell
New-Item src/generate_data.py
```

Générer le dataset synthétique de churn clients :

```powershell
python src/generate_data.py
```

**Fichier produit :** `data/raw.csv`
<img width="945" height="324" alt="image" src="https://github.com/user-attachments/assets/9cc8f8a9-a41a-4b88-95a6-fb2f9453ef9e" />

### Étape 4 : Préparer les Données + Quality Checks

Préparer et valider les données :

```powershell
python src/prepare_data.py
```

Cette étape effectue :
- Validation de la qualité des données
- Nettoyage et transformation
- Vérification des valeurs manquantes
- Encodage des variables catégorielles

**Fichier produit :** `data/processed.csv`
<img width="945" height="314" alt="image" src="https://github.com/user-attachments/assets/c1de605e-0d70-4ab4-ac27-321f720d395f" />

### Étape 5 : Entraîner, Versionner et Valider le Modèle

Entraîner le premier modèle :

```powershell
python src/train.py
```

Le script effectue :
- Entraînement du modèle (RandomForest)
- Versioning automatique avec timestamp
- Sauvegarde dans `models/`
- Enregistrement dans la registry
- Validation des performances

**Fichiers produits :**
- `models/churn_model_v1_YYYYMMDD_HHMMSS.joblib`
- `registry/current_model.txt`
- `registry/metadata.json`
- `registry/train_stats.json`
<img width="945" height="406" alt="image" src="https://github.com/user-attachments/assets/fcf0dd01-e6d4-413a-a107-176faab1a4c3" />

### Étape 6 : Inspecter la Registry et le Modèle Courant

Afficher le modèle actif :

```powershell
Get-Content registry/current_model.txt
```
<img width="945" height="414" alt="image" src="https://github.com/user-attachments/assets/ab534302-19fe-4e01-849f-695d20b065bd" />

Afficher les métadonnées :

```powershell
Get-Content registry/metadata.json
```

Afficher les statistiques d'entraînement :

```powershell
Get-Content registry/train_stats.json
```
<img width="531" height="175" alt="image" src="https://github.com/user-attachments/assets/f549244c-91f6-497a-90ef-a5b626e28789" />
<img width="945" height="896" alt="image" src="https://github.com/user-attachments/assets/05fdba8b-1b1d-42ec-8ce5-e2678cea879e" />

### Étape 7 : Créer une API /predict qui Utilise le Modèle Courant

Lancer l'API FastAPI :

```powershell
uvicorn src.api:app --reload
```

L'API est accessible sur : `http://127.0.0.1:8000`
<img width="945" height="276" alt="image" src="https://github.com/user-attachments/assets/fe3f5a34-ac74-4321-a4a4-0d60e1b64016" />

**Endpoints disponibles :**
- `GET /` - Documentation
- `POST /predict` - Prédiction de churn
- `GET /model-info` - Informations sur le modèle actif

**Exemple de requête :**


<img width="945" height="406" alt="image" src="https://github.com/user-attachments/assets/004e6656-46f9-4068-94ed-53648120b1b1" />
<img width="945" height="147" alt="image" src="https://github.com/user-attachments/assets/34a4778c-c04b-4d4b-b6b9-ae8e48a97887" />
<img width="945" height="136" alt="image" src="https://github.com/user-attachments/assets/c59714d8-be5e-4f2f-9069-7313a9f80c43" />


### Étape 8 : Détecter une Dérive des Données via les Logs

Surveiller la dérive des données :

```powershell
python src/monitor_drift.py
```

Cette étape :
- Analyse les logs d'inférence
- Compare avec les données d'entraînement
- Détecte les changements de distribution
- Alerte en cas de dérive significative
<img width="945" height="485" alt="image" src="https://github.com/user-attachments/assets/6a936564-4afc-43fd-8118-787daa5a83dd" />

### Étape 9 : Gérer les Versions du Modèle et Revenir en Arrière

Revenir à une version précédente :

```powershell
python src/rollback.py
```
<img width="945" height="261" alt="image" src="https://github.com/user-attachments/assets/4b623084-cd2d-49a1-8752-cc8914649d26" />

Le script permet :
- Lister toutes les versions disponibles
- Restaurer une version antérieure
- Mettre à jour la registry
- Valider le rollback
<img width="945" height="134" alt="image" src="https://github.com/user-attachments/assets/6b916ba2-8a2f-4c0e-a2f0-42f916706be3" />
<img width="788" height="213" alt="image" src="https://github.com/user-attachments/assets/02064c5d-206f-43d4-842d-72a218d3262a" />
## 📊 Métriques et Monitoring

Les performances des modèles sont suivies via :
- **Accuracy** : Précision globale
- **Precision, Recall, F1-Score** : Métriques par classe
- **AUC-ROC** : Aire sous la courbe ROC
- **Drift Score** : Détection de dérive des données


---


## 🧪 Lab 2 — Workflow Git (10 étapes)

Ce deuxième lab documente le flux Git réalisé pour gérer le cycle de vie du code et des modèles. Les étapes ci‑dessous ont été exécutées et validées dans ce dépôt.

- **Étape 1: Initialiser Git**
	- Commande: `git init` dans la racine du projet.
<img width="945" height="506" alt="image" src="https://github.com/user-attachments/assets/45515612-8a42-4493-a9bb-bfb26fd8f5ad" />
    - Commande: `git status` dans la racine du projet.
<img width="945" height="288" alt="image" src="https://github.com/user-attachments/assets/c711548f-a716-4592-94e9-483f7cc527db" />

- **Étape 2: Premier commit**
	- Ajout des fichiers initiaux et commit de base.
 - <img width="945" height="157" alt="image" src="https://github.com/user-attachments/assets/f41e89b4-8f4f-4aa6-9226-4e7755991f48" />

	- Exemple: `git log --oneline` .
<img width="945" height="191" alt="image" src="https://github.com/user-attachments/assets/98e23388-e063-402c-93fe-0b66afa96654" />

- **Étape 3: Observer une modification avec git diff**
	- Modification de `src/monitor_drift.py` (ajustement `z_threshold`, ex. 2.5 → 2.0) puis `git diff` pour visualiser les changements.
   <img width="945" height="70" alt="image" src="https://github.com/user-attachments/assets/0067a547-1035-43e5-98df-9bad9d22f2cd" />
<img width="945" height="70" alt="image" src="https://github.com/user-attachments/assets/d9245e15-0936-4bbc-944c-0a6901e8c155" />

<img width="945" height="463" alt="image" src="https://github.com/user-attachments/assets/5c74b29e-129b-42f7-b8dc-d0049cfcf982" />
   - Visualisation Modification Afficher les différences en staging : git diff --staged.
  <img width="945" height="64" alt="image" src="https://github.com/user-attachments/assets/f7cb3e76-c279-4005-99fa-d74e054a0226" />
  <img width="945" height="748" alt="image" src="https://github.com/user-attachments/assets/3dd94deb-b124-4e45-9123-1fd9cc1fef2c" />
   - Commit
   - <img width="945" height="102" alt="image" src="https://github.com/user-attachments/assets/9de5c3c6-8f53-439e-89c3-ba0f6c81bfbf" />

   
- **Étape 4: Créer une branche feature et ajouter une logique**
	- Branche: `git checkout -b feature/api-request-id`.
<img width="945" height="80" alt="image" src="https://github.com/user-attachments/assets/8b81a3fe-3150-476f-8586-b45f5f54951e" />

	- Modification de `src/api.py`: génération automatique d’un `request_id` (UUID hex) quand non fourni, propagation dans la réponse et les logs, et import de `uuid`.
<img width="945" height="80" alt="image" src="https://github.com/user-attachments/assets/7516e016-6257-42fd-ab9f-833ddf1fa3e9" />
<img width="945" height="153" alt="image" src="https://github.com/user-attachments/assets/10609768-a886-433d-b837-b3a85b8546eb" />
<img width="945" height="153" alt="image" src="https://github.com/user-attachments/assets/10052644-2bcb-4a79-9cab-f4f8be89dbdf" />

- **Étape 5: Fusionner la branche feature**
	- Retour sur la branche principale et merge de la feature: `git checkout main` puis `git merge feature/api-request-id`.
<img width="945" height="222" alt="image" src="https://github.com/user-attachments/assets/c321a5e4-2243-4bed-ac49-40f3a03d948b" />
<img width="945" height="215" alt="image" src="https://github.com/user-attachments/assets/f9836309-d85a-43b2-ab9b-da9032bf26f7" />


- **Étape 6: Créer et résoudre un conflit de merge sur `src/train.py`**
<img width="945" height="58" alt="image" src="https://github.com/user-attachments/assets/89504cde-7a9c-48b7-8ae2-fcc1ed37d612" />

	- Modifications concurrentes de `gate_f1` (ex. 0.50 vs 0.62) et résolution à une valeur choisie (ex. 0.60) dans `src/train.py`.
<img width="945" height="367" alt="image" src="https://github.com/user-attachments/assets/df3d4f9b-fbd9-43e2-a334-b764eb2a6511" />
<img width="945" height="367" alt="image" src="https://github.com/user-attachments/assets/c717991f-4a2a-4a0f-996a-1f2fc7c7cdbe" />
<img width="945" height="80" alt="image" src="https://github.com/user-attachments/assets/8c9d3031-d632-43dc-8a58-a8bb75413dd8" />



- **Étape 7: Utiliser git stash**
<img width="945" height="185" alt="image" src="https://github.com/user-attachments/assets/0749c970-430e-46bf-b361-b0761cb3ca69" />
	- Ajout d’un commentaire TODO dans `src/rollback.py`, puis `git stash` pour mettre de côté les changements temporaires.
<img width="945" height="381" alt="image" src="https://github.com/user-attachments/assets/884c1e47-4765-47b2-b2b9-292a89358e54" />
<img width="945" height="333" alt="image" src="https://github.com/user-attachments/assets/4eaf2d32-72a0-4be8-a033-7ee3b3497064" />
<img width="945" height="448" alt="image" src="https://github.com/user-attachments/assets/306a450c-a16d-4f8e-a3c7-2810e1ddce99" />


- **Étape 8: Tester git reset sur un fichier d’expérimentation**

<img width="945" height="372" alt="image" src="https://github.com/user-attachments/assets/4cb112d6-9de9-45a1-a59a-2691dcc35a46" />


	- Utilisation de `git reset` (soft/mixed/hard selon besoin) pour revenir sur un état souhaité d’un fichier de test.

<img width="945" height="206" alt="image" src="https://github.com/user-attachments/assets/dfab1ea7-8c48-42e0-a21b-10259064ec07" />
-  `git reset --soft` 
<img width="945" height="206" alt="image" src="https://github.com/user-attachments/assets/45531e4d-7cc8-4200-b532-79ba8d57e79d" />
-  `git reset HEAD~1 MIXED` 
<img width="945" height="408" alt="image" src="https://github.com/user-attachments/assets/2624dd5a-e4f9-448a-aa4c-824f113f7389" />

-  `git reset --hard` .
<img width="945" height="269" alt="image" src="https://github.com/user-attachments/assets/435ca61a-927a-4a1e-aa50-36daf07063eb" />



- **Étape 9: Annuler un commit avec git revert**
<img width="945" height="337" alt="image" src="https://github.com/user-attachments/assets/47c055ec-6c46-4ba2-9e72-1d662ddca95b" />

	- Ajout d’un changement non souhaité dans `src/api.py` (ex. `# BAD CHANGE`) puis annulation via `git revert <commit>` pour préserver l’historique.
<img width="945" height="371" alt="image" src="https://github.com/user-attachments/assets/f9ff8b8b-158f-4463-a9c3-52785d281d65" />

<img width="945" height="468" alt="image" src="https://github.com/user-attachments/assets/ec4a6054-7f48-46f7-9115-dc528cb25357" />


- **Étape 10: Rebase d’une branche feature sur la branche principale**
<img width="945" height="87" alt="image" src="https://github.com/user-attachments/assets/048fb3e7-8bd0-4eaf-bbfc-8ab39c1561d7" />

<img width="945" height="99" alt="image" src="https://github.com/user-attachments/assets/343b0fdd-7570-4631-808b-6bf0eee2b8d4" />

	- Rebase pour réappliquer proprement la feature sur l’historique linéaire: `git checkout feature/...` puis `git rebase main`.
<img width="945" height="102" alt="image" src="https://github.com/user-attachments/assets/ebd1cea1-6c10-4ecd-bea1-1fe3854c2dea" />
<img width="945" height="210" alt="image" src="https://github.com/user-attachments/assets/0a0b55f1-010b-4e12-b87f-35a2f9f5db08" />
<img width="945" height="462" alt="image" src="https://github.com/user-attachments/assets/0b1d239f-5e08-48d9-9fdd-b01754e066e2" />


### Commandes utiles (récapitulatif)

```powershell
git init
git status
git add .
git commit -m "Initial commit"
git checkout -b feature/api-request-id
git diff
git merge feature/api-request-id
git stash
git reset --soft HEAD~1
git revert <commit_sha>
git rebase main
```

**Date de création :** 14 décembre 2025  
**Auteur :** Anouar El Gorch 
**Master:** SDIA  
**Version :** 1.0
