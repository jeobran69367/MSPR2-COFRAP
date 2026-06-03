# MSPR2-COFRAP — Infrastructure (Mac)

Setup complet sur **ton Mac** avec Docker Desktop + Kind.
Pas de VM nécessaire.

---

## Prérequis — À installer AVANT tout

### 1. Docker Desktop
Télécharge et installe depuis : https://www.docker.com/products/docker-desktop/

> Après installation, ouvre Docker Desktop et attends que l'icône 🐳 soit verte dans ta barre de menu en haut.

### 2. Homebrew (si pas déjà installé)
Ouvre un Terminal et colle :
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

---

## Installation — Dans l'ordre

> ⚠️ **Tout se passe dans le Terminal de ton Mac.**
> Ouvre Terminal (Applications → Utilitaires → Terminal)

### Étape 1 — Cloner le repo
```bash
git clone https://github.com/jeobran69367/MSPR2-COFRAP
cd MSPR2-COFRAP
chmod +x scripts/*.sh
```

### Étape 2 — Installer les outils (Kind, kubectl, Helm, faas-cli)
```bash
bash scripts/01_install_tools.sh
```

### Étape 3 — Créer le cluster Kubernetes
```bash
bash scripts/02_create_cluster.sh
```

### Étape 4 — Déployer OpenFaaS
```bash
bash scripts/03_install_openfaas.sh
```

### Étape 5 — Déployer PostgreSQL
```bash
bash scripts/04_install_postgresql.sh
```

### Étape 6 — Vérifier que tout fonctionne
```bash
bash scripts/05_verify.sh
```

---

## Ce que tu auras à la fin

| Composant  | Accès depuis ton Mac        |
|------------|-----------------------------|
| OpenFaaS   | http://localhost:31112       |
| PostgreSQL | localhost:30432              |

---

## Résumé visuel

```
Ton Mac
  ├── Docker Desktop (fait tourner des conteneurs)
  │       └── Kind (Kubernetes dans Docker)
  │               ├── OpenFaaS  → localhost:31112
  │               └── PostgreSQL → localhost:30432
  └── Terminal (tu lances les scripts ici)
```

---

## Commandes utiles

```bash
# Voir les pods qui tournent
kubectl get pods -A

# Voir les logs OpenFaaS
kubectl -n openfaas logs deploy/gateway

# Accéder à la BDD directement
kubectl -n cofrap-db exec -it \
  $(kubectl -n cofrap-db get pod -l app=postgresql -o jsonpath='{.items[0].metadata.name}') \
  -- psql -U cofrap_user -d cofrap

# Supprimer tout le cluster
kind delete cluster --name cofrap
```

---

## Schéma table users

| Colonne  | Type         | Description                 |
|----------|--------------|-----------------------------|
| id       | SERIAL       | Clé primaire                |
| username | VARCHAR(255) | Identifiant unique          |
| password | TEXT         | Mot de passe haché (bcrypt) |
| mfa      | TEXT         | Secret TOTP chiffré         |
| gendate  | BIGINT       | Timestamp UNIX de création  |
| expired  | SMALLINT     | 0 = actif, 1 = expiré       |

# Accédes a la bd
docker run -d \
  --name pgadmin \
  --network kind \
  -e PGADMIN_DEFAULT_EMAIL=admin@cofrap.fr \
  -e PGADMIN_DEFAULT_PASSWORD=admin \
  -p 5050:80 \
  dpage/pgadmin4

  Ensuite ouvrir : http://localhost:5050/
  onnexion avec :

Email : admin@cofrap.fr
Password : admin

Ensuite Add Server :
ChampValeurHostpostgresql.cofrap-db.svc.cluster.localPort5432Usercofrap_userPasswordcofrap_secure_pass_2024Databasecofrap


