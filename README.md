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

Créer les dossiers nécessaires :

```powershell
New-Item -ItemType Directory -Force data, models, registry, logs, src
```

### Étape 2 : Préparer l'Environnement Python

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

### Étape 6 : Inspecter la Registry et le Modèle Courant

Afficher le modèle actif :

```powershell
Get-Content registry/current_model.txt
```

Afficher les métadonnées :

```powershell
Get-Content registry/metadata.json
```

Afficher les statistiques d'entraînement :

```powershell
Get-Content registry/train_stats.json
```

### Étape 7 : Créer une API /predict qui Utilise le Modèle Courant

Lancer l'API FastAPI :

```powershell
uvicorn src.api:app --reload
```

L'API est accessible sur : `http://127.0.0.1:8000`

**Endpoints disponibles :**
- `GET /` - Documentation
- `POST /predict` - Prédiction de churn
- `GET /model-info` - Informations sur le modèle actif

**Exemple de requête :**

```powershell
curl -X POST "http://127.0.0.1:8000/predict" `
  -H "Content-Type: application/json" `
  -d '{
    "tenure": 12,
    "monthly_charges": 65.5,
    "total_charges": 786.0,
    "contract_type": "Month-to-month",
    "payment_method": "Electronic check"
  }'
```

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

### Étape 9 : Gérer les Versions du Modèle et Revenir en Arrière

Revenir à une version précédente :

```powershell
python src/rollback.py
```

Le script permet :
- Lister toutes les versions disponibles
- Restaurer une version antérieure
- Mettre à jour la registry
- Valider le rollback

## 📊 Métriques et Monitoring

Les performances des modèles sont suivies via :
- **Accuracy** : Précision globale
- **Precision, Recall, F1-Score** : Métriques par classe
- **AUC-ROC** : Aire sous la courbe ROC
- **Drift Score** : Détection de dérive des données

## 🔧 Technologies Utilisées

- **Python 3.x**
- **pandas** : Manipulation de données
- **scikit-learn** : Machine Learning
- **FastAPI** : API REST
- **Uvicorn** : Serveur ASGI
- **joblib** : Sérialisation des modèles

## 📝 Notes Importantes

- Tous les modèles sont versionnés automatiquement avec timestamp
- La registry maintient un historique complet des modèles
- Les logs d'inférence permettent le monitoring en production
- Le rollback est possible à tout moment vers n'importe quelle version

## 🎯 Bonnes Pratiques MLOps Implémentées

1. **Versioning** : Tous les modèles sont versionnés et traçables
2. **Registry** : Gestion centralisée des métadonnées
3. **Validation** : Quality checks sur les données
4. **API** : Interface standardisée pour l'inférence
5. **Monitoring** : Détection de dérive des données
6. **Rollback** : Capacité de revenir en arrière rapidement

## 🚦 Prochaines Étapes

- [ ] Ajouter des tests unitaires
- [ ] Implémenter CI/CD
- [ ] Conteneurisation avec Docker
- [ ] Orchestration avec Kubernetes
- [ ] Monitoring avancé avec Prometheus/Grafana
- [ ] A/B Testing des modèles

---

**Date de création :** 14 décembre 2025  
**Auteur :** MLOps Lab  
**Version :** 1.0
