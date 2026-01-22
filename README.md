# Déploiement d'un classifieur de sentiment NLP

## 1. Choix technologique

- **API** : FastAPI (léger, rapide, Python)
- **Serveur** : Uvicorn pour exécuter FastAPI
- **Stockage modèle** : fichier .h5 ou pickle pour modèle Deep Learning / TF-IDF
- **Base de données** : PostgreSQL ou MongoDB pour stocker les requêtes et résultats

## 2. Architecture et étapes

### a) Prétraitement des textes

- **Nettoyage** : minuscules, suppression ponctuation/chiffres, suppression tokens spéciaux
- **Tokenizer + Padding** (pour Deep Learning) ou TF-IDF vectorizer (pour modèle classique)
- Même pipeline que pour l'entraînement → garantir cohérence

### b) Chargement du modèle

- Charger le modèle Deep Learning (.h5) ou TF-IDF + LogisticRegression (pickle) en mémoire
- Vérifier que le modèle est prêt avant de servir les requêtes

### c) API Endpoint `/predict`

- Reçoit un texte (POST)
- Applique prétraitement + tokenization / TF-IDF
- Fait la prédiction
- Retourne la probabilité et la classe (positive/negative)

### d) Stockage et logging

- Stocker chaque requête et sa prédiction dans une DB ou fichier log
- Utile pour le monitoring et la dérive des données

## 3. Métriques à monitorer

- **Performance du modèle** : accuracy, F1-score sur un petit batch de contrôle (si étiquettes disponibles)
- **Taux d'erreur** / incohérences
- **Distribution des classes** des requêtes entrantes (pour détecter un biais ou dérive)
- **Temps de réponse API** (latence)

## 4. Gestion de la dérive (Data Drift)

- Comparer la distribution des nouveaux textes avec celle des données d'entraînement
- **Exemples** : longueur moyenne des textes, fréquence des mots, proportion positif/négatif
- Détecter si le modèle devient moins précis → déclencher réentraînement périodique
- Automatiser le retraining sur nouvelles données étiquetées (ex: feedback utilisateurs)

## 🔹 Schéma logique simple

```
Texte utilisateur
       │
       ▼
Prétraitement (clean + tokenizer/pad ou TF-IDF)
       │
       ▼
Modèle NLP (Deep Learning ou TF-IDF+LR)
       │
       ▼
Probabilité / Classe
       │
       ▼
Retour API + Logging
```