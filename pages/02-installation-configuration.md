---
layout: new-section
routeAlias: 'installation-configuration'
---

<a name="installation-configuration" id="installation-configuration"></a>

# Installation et Configuration ⚙️

Installer et configurer PostgreSQL sur différentes plateformes

---

# Plan du module 📋

- Installation sur différents systèmes d'exploitation
- Configuration de base
- Outils d'administration
- Premier démarrage
- Configuration réseau
- Optimisation initiale

---

# Installation sur Ubuntu/Debian 🐧

**C'est comme installer une application sur votre smartphone ! 📱**

**Étape 1 : Préparer le terrain** 🏗️

```bash
# Ajouter le "magasin" officiel PostgreSQL
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
```

💡 **Pourquoi ?** Les dépôts Ubuntu de base ont des versions anciennes.
On ajoute le dépôt officiel pour avoir la toute dernière version !

---

**Étape 2 : Vérifier que c'est bien officiel** 🔐

```bash
# Importer la clé de sécurité
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

*Analogie* : Comme vérifier le certificat SSL d'un site (le cadenas vert)

---

**Étape 3 : Installation !** ⬇️

```bash
# Mettre à jour la liste des applications disponibles
sudo apt update

# Installer PostgreSQL 17 (version 2025)
sudo apt install postgresql-17 postgresql-contrib-17

# Vérifier que c'est bien installé
psql --version
```

💡 **Que fait chaque commande ?**
- `apt update` = Rafraîchir le catalogue
- `postgresql-17` = Le moteur PostgreSQL
- `postgresql-contrib-17` = Extensions et outils bonus
- `psql --version` = Vérifier que l'installation a fonctionné

✅ **Si vous voyez "psql (PostgreSQL) 17.X", c'est gagné !**

---

**Le service démarre automatiquement** 🎉

**PostgreSQL = un service qui tourne en arrière-plan**

*Analogie* : Comme Spotify qui tourne en arrière-plan sur votre téléphone

**Vérifier si PostgreSQL tourne** 🔍 :
```bash
sudo systemctl status postgresql
```

💡 **Ce que vous verrez** :
- 🟢 `active (running)` = ✅ Tout va bien !
- 🔴 `inactive (dead)` = ❌ Il ne tourne pas

---

**Contrôler PostgreSQL** 🎮 :
```bash
# Démarrer PostgreSQL
sudo systemctl start postgresql

# Arrêter PostgreSQL  
sudo systemctl stop postgresql

# Redémarrer PostgreSQL (après un changement de config)
sudo systemctl restart postgresql
```

🎯 **En pratique** :
- 90% du temps, vous n'aurez jamais besoin de ces commandes
- PostgreSQL démarre automatiquement au démarrage du serveur
- Vous redémarrerez seulement après avoir modifié la configuration

⚠️ **Attention** : Redémarrer = couper toutes les connexions en cours !
- Faites-le quand personne n'utilise la base
- *Analogie* : Comme redémarrer votre box internet : ça coupe tout le monde

---

# Installation sur macOS 🍎

**Méthode 1 : Homebrew (recommandé)**

```bash
# Installer Homebrew si nécessaire
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Installer PostgreSQL
brew install postgresql@17

# Démarrer PostgreSQL
brew services start postgresql@17
```

---

**Méthode 2 : Postgres.app**

- Télécharger depuis https://postgresapp.com/
- Glisser l'application dans le dossier Applications
- Lancer Postgres.app
- Interface graphique simple et intuitive

---

# Installation sur Windows 🪟

**Méthode : Installeur officiel**

1. Télécharger depuis https://www.postgresql.org/download/windows/
2. Exécuter l'installeur EDB
3. Suivre l'assistant d'installation
4. Choisir les composants :
   - PostgreSQL Server
   - pgAdmin 4
   - Stack Builder (optionnel)

---

5. Définir le mot de passe pour l'utilisateur `postgres`
6. Choisir le port (5432 par défaut)
7. Sélectionner la locale (fr_FR.UTF-8 recommandé)
8. Finaliser l'installation

---

# Installation avec Docker 🐳

**Méthode moderne et portable**

```bash
# Télécharger l'image PostgreSQL 17
docker pull postgres:17

# Créer un conteneur PostgreSQL
docker run --name postgres-dev \
  -e POSTGRES_PASSWORD=monmotdepasse \
  -e POSTGRES_USER=monuser \
  -e POSTGRES_DB=mabase \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  -d postgres:17
```

---

**Vérifier que le conteneur fonctionne**

```bash
# Liste des conteneurs
docker ps

# Se connecter au conteneur
docker exec -it postgres-dev psql -U monuser -d mabase

# Arrêter le conteneur
docker stop postgres-dev

# Démarrer le conteneur
docker start postgres-dev
```

---

# Docker Compose pour PostgreSQL 🐳

**Fichier docker-compose.yml**

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:17
    container_name: postgres-dev
    restart: always
    environment:
      POSTGRES_USER: monuser
      POSTGRES_PASSWORD: monmotdepasse
      POSTGRES_DB: mabase
```

---

```yaml
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - postgres-network

volumes:
  pgdata:

networks:
  postgres-network:
    driver: bridge
```

---

**Démarrer avec Docker Compose**

```bash
# Démarrer les services
docker-compose up -d

# Voir les logs
docker-compose logs -f postgres

# Arrêter les services
docker-compose down

# Arrêter et supprimer les volumes
docker-compose down -v
```

---

# Premier accès à PostgreSQL 🔐

**Sur Linux/macOS**

```bash
# Se connecter en tant qu'utilisateur postgres
sudo -u postgres psql

# Ou directement
psql -U postgres
```

---

**Sur Windows**

```cmd
# Ouvrir SQL Shell (psql) depuis le menu Démarrer
# Ou depuis l'invite de commandes
psql -U postgres
```

---

**Créer un nouvel utilisateur**

```sql
-- Se connecter en tant que postgres
CREATE USER monuser WITH PASSWORD 'monmotdepasse';

-- Donner les privilèges
ALTER USER monuser WITH SUPERUSER;

-- Ou plus restreint
GRANT ALL PRIVILEGES ON DATABASE mabase TO monuser;
```

---

# Fichiers de configuration 📄

**Principaux fichiers**

- **postgresql.conf** : Configuration principale du serveur
- **pg_hba.conf** : Authentification et accès réseau
- **pg_ident.conf** : Mapping des utilisateurs système

---

**Localisation des fichiers**

```sql
-- Trouver le répertoire de configuration
SHOW config_file;

-- Trouver le répertoire de données
SHOW data_directory;

-- Voir tous les paramètres
SHOW ALL;
```

---

# Configuration de postgresql.conf ⚙️

**Paramètres importants**

```bash
# Connexions
max_connections = 100          # Nombre max de connexions
port = 5432                    # Port d'écoute

# Mémoire
shared_buffers = 256MB         # Cache partagé
effective_cache_size = 1GB     # Cache système estimé
work_mem = 4MB                 # Mémoire par opération de tri
```

---

```bash
# WAL (Write-Ahead Logging)
wal_level = replica            # Niveau de journalisation
max_wal_size = 1GB            # Taille max des WAL
min_wal_size = 80MB           # Taille min des WAL

# Logging
logging_collector = on         # Activer la collecte de logs
log_directory = 'log'         # Répertoire des logs
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
```

---

```bash
# Performances
effective_io_concurrency = 200 # Opérations I/O parallèles
random_page_cost = 1.1        # Coût de lecture aléatoire (SSD)

# Locale
lc_messages = 'fr_FR.UTF-8'   # Langue des messages
lc_monetary = 'fr_FR.UTF-8'   # Format monétaire
lc_numeric = 'fr_FR.UTF-8'    # Format numérique
lc_time = 'fr_FR.UTF-8'       # Format date/heure
```

---

# Configuration de pg_hba.conf 🔒

**Format du fichier**

```bash
# TYPE  DATABASE    USER        ADDRESS         METHOD

# Local connections
local   all         all                         peer

# IPv4 local connections
host    all         all         127.0.0.1/32    scram-sha-256

# IPv6 local connections
host    all         all         ::1/128         scram-sha-256
```

---

**Méthodes d'authentification**

- **trust** : Aucune authentification (à éviter en production)
- **peer** : Authentification par utilisateur système (Linux/macOS)
- **scram-sha-256** : Mot de passe chiffré (recommandé)
- **md5** : Mot de passe MD5 (obsolète, éviter)
- **password** : Mot de passe en clair (jamais en production)

---

**Autoriser les connexions réseau**

```bash
# Autoriser un sous-réseau spécifique
host    all         all         192.168.1.0/24  scram-sha-256

# Autoriser toutes les connexions (développement uniquement!)
host    all         all         0.0.0.0/0       scram-sha-256
```

---

**Recharger la configuration**

```bash
# Linux/macOS
sudo systemctl reload postgresql

# Ou depuis psql
SELECT pg_reload_conf();

# Docker
docker exec postgres-dev pg_ctl reload
```

---

# pgAdmin 4 - Interface graphique 🖥️

**Installation**

- **Windows/macOS** : Inclus dans l'installeur PostgreSQL
- **Linux** :

```bash
# Ubuntu/Debian
sudo apt install pgadmin4

# Ou via Docker
docker pull dpage/pgadmin4
```

---

**Lancer pgAdmin avec Docker**

```bash
docker run -p 5050:80 \
  -e PGADMIN_DEFAULT_EMAIL=admin@admin.com \
  -e PGADMIN_DEFAULT_PASSWORD=admin \
  -d dpage/pgadmin4
```

---

**Se connecter à pgAdmin**

1. Ouvrir http://localhost:5050 dans le navigateur
2. Se connecter avec les identifiants
3. Ajouter un nouveau serveur :
   - **Name** : Serveur local
   - **Host** : localhost (ou nom du conteneur)
   - **Port** : 5432
   - **Username** : postgres
   - **Password** : votre mot de passe

---

# DBeaver - Client universel 🦫

**Installation**

- Télécharger depuis https://dbeaver.io/download/
- Support multi-SGBD (PostgreSQL, MySQL, SQLite, etc.)
- Version Community gratuite et open source

---

**Créer une connexion PostgreSQL**

1. Nouveau → Connexion à une base de données
2. Sélectionner PostgreSQL
3. Renseigner les paramètres :
   - **Host** : localhost
   - **Port** : 5432
   - **Database** : postgres
   - **Username** : postgres
   - **Password** : votre mot de passe
4. Tester la connexion

---

# psql - Client en ligne de commande 💻

**Commandes de base**

```bash
# Se connecter à une base
psql -U username -d database -h localhost -p 5432

# Se connecter avec URL
psql postgresql://username:password@localhost:5432/database

# Exécuter un fichier SQL
psql -U username -d database -f script.sql
```

---

**Commandes internes psql**

```sql
-- Lister les bases de données
\l

-- Se connecter à une base
\c nombase

-- Lister les tables
\dt

-- Décrire une table
\d nomtable
```

---

```sql
-- Lister les utilisateurs
\du

-- Voir les schémas
\dn

-- Exécuter un fichier
\i chemin/vers/fichier.sql

-- Aide
\?

-- Quitter
\q
```

---

# Variables d'environnement 🌍

**Variables utiles**

```bash
# Fichier .bashrc ou .zshrc
export PGHOST=localhost
export PGPORT=5432
export PGUSER=monuser
export PGPASSWORD=monmotdepasse
export PGDATABASE=mabase

# Connexion simplifiée
psql  # Utilise les variables d'environnement
```

---

# Fichier .pgpass pour l'authentification 🔑

**Format : ~/.pgpass (Linux/macOS) ou %APPDATA%\postgresql\pgpass.conf (Windows)**

```
hostname:port:database:username:password
localhost:5432:*:monuser:monmotdepasse
192.168.1.100:5432:production:admin:motdepasse123
```

---

**Permissions du fichier**

```bash
# Linux/macOS : le fichier doit être lisible uniquement par l'utilisateur
chmod 600 ~/.pgpass

# Vérifier
ls -la ~/.pgpass
# -rw------- 1 user user 85 jan 15 10:30 .pgpass
```

---

# Optimisation initiale 🚀

**Calcul des paramètres selon la RAM**

Pour un serveur avec **8 GB de RAM** :

```bash
shared_buffers = 2GB          # 25% de la RAM
effective_cache_size = 6GB    # 75% de la RAM
work_mem = 10MB               # RAM / max_connections / 4
maintenance_work_mem = 512MB  # RAM / 16
```

---

**Pour un serveur avec 16 GB de RAM**

```bash
shared_buffers = 4GB
effective_cache_size = 12GB
work_mem = 20MB
maintenance_work_mem = 1GB
```

---

**Optimisation pour SSD**

```bash
# SSD : coût de lecture aléatoire proche du séquentiel
random_page_cost = 1.1        # Au lieu de 4.0 (HDD)
effective_io_concurrency = 200 # Au lieu de 1 (HDD)

# Nombre de workers
max_worker_processes = 8
max_parallel_workers = 8
max_parallel_workers_per_gather = 4
```

---

# Backup de la configuration 💾

**Sauvegarder les fichiers de config**

```bash
# Créer un répertoire de sauvegarde
mkdir -p ~/postgres-backup

# Sauvegarder postgresql.conf
sudo cp /etc/postgresql/17/main/postgresql.conf ~/postgres-backup/

# Sauvegarder pg_hba.conf
sudo cp /etc/postgresql/17/main/pg_hba.conf ~/postgres-backup/

# Avec date
sudo cp postgresql.conf postgresql.conf.$(date +%Y%m%d)
```

---

# Logs PostgreSQL 📝

**Activer les logs détaillés**

```bash
# postgresql.conf
logging_collector = on
log_destination = 'stderr'
log_directory = 'log'
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_rotation_age = 1d
log_rotation_size = 100MB
```

---

```bash
# Que logger ?
log_connections = on
log_disconnections = on
log_duration = off
log_statement = 'none'        # 'none', 'ddl', 'mod', 'all'
log_line_prefix = '%t [%p]: [%l-1] user=%u,db=%d,app=%a,client=%h '
```

---

**Consulter les logs**

```bash
# Linux
sudo tail -f /var/log/postgresql/postgresql-17-main.log

# macOS (Homebrew)
tail -f /usr/local/var/log/postgres.log

# Docker
docker logs -f postgres-dev
```

---

# Extensions essentielles 🧩

**Installer des extensions**

```sql
-- Voir les extensions disponibles
SELECT * FROM pg_available_extensions;

-- Installer une extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";
CREATE EXTENSION IF NOT EXISTS "hstore";
```

---

**Extensions populaires**

```sql
-- Statistiques de requêtes
CREATE EXTENSION pg_stat_statements;

-- Fonctions de chiffrement
CREATE EXTENSION pgcrypto;

-- Recherche full-text améliorée
CREATE EXTENSION unaccent;
```

---

# Vérification de l'installation ✅

**Tests de base**

```sql
-- Version de PostgreSQL
SELECT version();

-- Bases de données existantes
\l

-- Créer une base de test
CREATE DATABASE test_db;

-- Se connecter
\c test_db
```

---

```sql
-- Créer une table de test
CREATE TABLE test_table (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Insérer des données
INSERT INTO test_table (nom) VALUES ('Test 1'), ('Test 2');

-- Vérifier
SELECT * FROM test_table;
```

---

# Sécurité initiale 🔐

**Bonnes pratiques**

1. **Changer le mot de passe postgres** :

```sql
ALTER USER postgres WITH PASSWORD 'nouveau_mot_de_passe_fort';
```

---

2. **Créer des utilisateurs dédiés** :

```sql
-- Utilisateur lecture seule
CREATE USER lecteur WITH PASSWORD 'motdepasse';
GRANT CONNECT ON DATABASE mabase TO lecteur;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO lecteur;

-- Utilisateur complet
CREATE USER dev WITH PASSWORD 'motdepasse';
GRANT ALL PRIVILEGES ON DATABASE mabase TO dev;
```

---

3. **Limiter les connexions réseau** dans pg_hba.conf :

```bash
# N'autoriser que les IP nécessaires
host    all         all         192.168.1.0/24  scram-sha-256

# Éviter 0.0.0.0/0 en production !
```

---

4. **Configurer le pare-feu** :

```bash
# Ubuntu : autoriser uniquement le réseau local
sudo ufw allow from 192.168.1.0/24 to any port 5432

# Bloquer les autres
sudo ufw deny 5432
```

---

# Commandes d'administration utiles 🛠️

```sql
-- Recharger la configuration
SELECT pg_reload_conf();

-- Voir les connexions actives
SELECT * FROM pg_stat_activity;

-- Terminer une connexion
SELECT pg_terminate_backend(pid);

-- Taille d'une base de données
SELECT pg_size_pretty(pg_database_size('mabase'));
```

---

```sql
-- Taille d'une table
SELECT pg_size_pretty(pg_total_relation_size('matable'));

-- Liste des tables avec leur taille
SELECT 
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;
```

---

# Dépannage courant 🔧

**Problème : PostgreSQL ne démarre pas**

```bash
# Vérifier les logs
sudo tail -100 /var/log/postgresql/postgresql-17-main.log

# Vérifier le port
sudo netstat -tlnp | grep 5432

# Vérifier les permissions du répertoire de données
ls -la /var/lib/postgresql/17/main
```

---

**Problème : Impossible de se connecter**

```bash
# Vérifier que PostgreSQL écoute
sudo netstat -tlnp | grep 5432

# Vérifier postgresql.conf
listen_addresses = 'localhost'  # Ou '*' pour toutes les interfaces

# Vérifier pg_hba.conf
# Autoriser la méthode d'authentification appropriée
```

---

**Problème : Mot de passe refusé**

```sql
-- Réinitialiser le mot de passe (en tant que superuser)
ALTER USER monuser WITH PASSWORD 'nouveaumotdepasse';

-- Vérifier les rôles
\du
```

---

# Monitoring de base 📊

**Statistiques en temps réel**

```sql
-- Activité en cours
SELECT pid, usename, application_name, client_addr, state, query
FROM pg_stat_activity
WHERE state != 'idle';

-- Nombre de connexions par base
SELECT datname, count(*) as connections
FROM pg_stat_activity
GROUP BY datname;
```

---

```sql
-- Cache hit ratio (doit être > 90%)
SELECT 
    sum(heap_blks_read) as heap_read,
    sum(heap_blks_hit) as heap_hit,
    sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) as ratio
FROM pg_statio_user_tables;

-- Tables les plus utilisées
SELECT schemaname, tablename, seq_scan, seq_tup_read, idx_scan, idx_tup_fetch
FROM pg_stat_user_tables
ORDER BY seq_tup_read DESC
LIMIT 10;
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Installation sur Linux, macOS, Windows et Docker
✅ Configuration de postgresql.conf et pg_hba.conf
✅ Outils d'administration : psql, pgAdmin, DBeaver
✅ Optimisation initiale selon la RAM disponible
✅ Sécurité de base : utilisateurs, authentification, réseau
✅ Logs et monitoring
✅ Extensions essentielles

---

# Exercice pratique 🎯

**Installation complète**

1. Installer PostgreSQL sur votre système
2. Créer un utilisateur `dev` avec mot de passe
3. Créer une base de données `formation`
4. Configurer pg_hba.conf pour autoriser les connexions locales
5. Se connecter avec psql et vérifier que tout fonctionne
6. Installer les extensions : uuid-ossp, pg_trgm, hstore

---

# Questions ? 🙋

Des questions sur l'installation et la configuration ?

**À suivre** : Premiers pas avec PostgreSQL

