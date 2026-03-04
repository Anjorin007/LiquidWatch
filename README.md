# LiquidWatch
### Système de Surveillance du Risque de Liquidité — Banques Commerciales UEMOA

**🔴 Live Demo :**
- Surveillance Opérationnelle : https://liquidwatch.grafana.net/goto/fff0mn2xcwhdse?orgId=stacks-1546089
- Analyse Prédictive IA : https://liquidwatch.grafana.net/goto/aff0mr29i58g0b?orgId=stacks-1546089
- Contexte Macro UEMOA : https://liquidwatch.grafana.net/goto/cff0ms27e1se8d?orgId=stacks-1546089

**GitHub :** https://github.com/Anjorin007/LiquidWatch

---

## 1. Le problème

Dans de nombreuses équipes trésorerie, le suivi du risque de liquidité reste partiellement manuel :
- reporting Excel consolidé avec retard ;
- faible fréquence de mise à jour ;
- détection tardive des signaux de tension.

Cela réduit la réactivité opérationnelle face aux franchissements de seuil et à la dégradation progressive des ratios.

## 2. La solution LiquidWatch

LiquidWatch est une plateforme de surveillance du risque de liquidité orientée **banques commerciales UEMOA**.

Fonctionnalités clés :
- ingestion de données BCEAO/Banque Mondiale ;
- calcul journalier des indicateurs de liquidité ;
- prévisions Prophet (J+1 à J+30) ;
- moteur d'alertes (`SEUIL_CRITIQUE`, `PREDICTION_RISQUE`, `TENDANCE_BAISSE`) ;
- dashboards Grafana métiers (Ops, Predictif, Macro).

## 3. Périmètre des données

Version actuelle : données publiques BCEAO/Banque Mondiale agrégées au niveau national — proxy du comportement moyen du secteur bancaire de chaque pays UEMOA.

Version bank-level : nécessite un accès aux données internes ALM/Trésorerie/Core Banking d'une banque commerciale.

## 4. Architecture pipeline

1. Ingestion BCEAO (exports CSV/XLSX) + Banque Mondiale (API officielle).
2. Normalisation et transformations journalières.
3. Calcul du ratio de liquidité et enrichissement macro.
4. Stockage PostgreSQL.
5. Prédiction Prophet et insertion des horizons J+30.
6. Génération d'alertes.
7. Visualisation et pilotage dans Grafana.

## 5. Stack technique

- Python 3.11 (`pandas`, `sqlalchemy`, `prophet`, `apscheduler`)
- PostgreSQL
- Grafana
- Docker / Docker Compose
- Supabase (PostgreSQL managé)
- Grafana Cloud (dashboards hébergés)
- Render (worker Python)

## 6. Lancer en local : docker-compose up

1. Créer le fichier `.env` depuis l'exemple :

```powershell
Copy-Item .env.example .env
```

2. Placer les exports BCEAO officiels dans `data/raw/bceao/` (`.csv`, `.xlsx`, `.xls`).

3. Démarrer la stack :

```powershell
docker compose up -d --build
```

4. Initialiser la base et exécuter le pipeline :

```powershell
docker compose exec liquidwatch python -m liquidwatch init-db
docker compose exec liquidwatch python -m liquidwatch pipeline
```

5. Ouvrir Grafana local :
- URL : `http://localhost:3000`
- User : `admin`
- Password : `admin`

## 7. Déploiement cloud : Supabase + Grafana Cloud

- **Supabase Free** : base PostgreSQL hébergée.
- **Render Free** : worker Python (`scheduler`) pour ingestion, prédiction, alertes, simulation.
- **Grafana Cloud Free** : dashboards hébergés et partageables.

Variables d'environnement cloud minimales :
- `DATABASE_URL=postgresql://...?...sslmode=require`
- `ENABLE_SIMULATION=true`
- `PYTHONPATH=/app/src`

Notes :
- En Grafana Cloud, vérifier l'UID de datasource PostgreSQL et l'aligner dans les JSON de dashboards.
- En Supabase, utiliser la connection pooling transaction (`port 6543`) si la connexion directe n'est pas disponible.

## CLI

```powershell
python -m liquidwatch init-db
python -m liquidwatch ingest
python -m liquidwatch predict
python -m liquidwatch alerts
python -m liquidwatch pipeline
python -m liquidwatch scheduler
```
