---
layout: new-section
routeAlias: 'reseaux-volumes-docker'
---

<a name="reseaux-volumes-docker" id="reseaux-volumes-docker"></a>

# Réseaux & Volumes Docker 🌐💾

---

# Réseaux & Volumes Docker 🌐💾

### Communication entre containers et persistance des données

Docker intègre un système de **réseaux virtuels** pour la communication entre containers et des **volumes** pour persister les données au-delà du cycle de vie des containers.

---

# Réseaux Docker - Les essentiels 🌐

### Types de réseaux

- **bridge** : Réseau par défaut, isolation modérée
- **host** : Utilise le réseau de l'hôte directement  
- **none** : Isolation réseau complète

### Communication par nom DNS

```bash
# Créer un réseau personnalisé
docker network create app-network

# Les containers se joignent par leur nom
docker run -d --name api --network app-network node-api
docker run -d --name web --network app-network nginx

# Dans le container 'web' : curl http://api:3000
```

---

# Stack applicative complète 🏗️

### Application web + Base de données

```bash
# 1. Réseau et volumes
docker network create webapp-net
docker volume create postgres-data

# 2. Base de données avec persistance
docker run -d --name db \
  --network webapp-net \
  -v postgres-data:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres:15-alpine

# 3. Application web
docker run -d --name api \
  --network webapp-net \
  -p 3000:3000 \
  -e DATABASE_URL=postgresql://postgres:secret@db:5432/app \
  mon-api

# 4. Reverse proxy
docker run -d --name proxy \
  --network webapp-net \
  -p 80:80 \
  nginx:alpine
```

---

# Types de volumes 📂

### Persistance et partage de données

| Type | Usage | Exemple |
|------|-------|---------|
| **Named Volume** | Production, gérés par Docker | `docker volume create app-data` |
| **Bind Mount** | Développement, lier dossier hôte | `-v $(pwd):/app` |
| **tmpfs** | Données temporaires en RAM | `--tmpfs /tmp` |

---

# Volumes en pratique 💾

### Gestion moderne des données

```bash
# Volume géré par Docker (recommandé production)
docker volume create mysql-data
docker run -d -v mysql-data:/var/lib/mysql mysql:8

# Bind mount pour développement
docker run -d -v $(pwd)/src:/app/src -p 3000:3000 node:20-alpine

# Lecture seule
docker run -d -v /host/config:/app/config:ro nginx

# Partage de volumes entre containers
docker run -d --name data-container -v shared-vol:/data alpine
docker run -d --volumes-from data-container nginx
```

---

# Docker Compose : Configuration déclarative 📝

### Stack complète en YAML

```yaml
# docker-compose.yml
version: '3.8'

services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - backend

  api:
    image: node:20-alpine
    environment:
      DATABASE_URL: postgresql://postgres:secret@db:5432/app
    depends_on:
      - db
    networks:
      - backend
      - frontend
    ports:
      - "3000:3000"

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    networks:
      - frontend

networks:
  frontend:
  backend:

volumes:
  postgres_data:
```

---

# Commandes de débogage et monitoring 🔍

### Diagnostic réseau et volumes

```bash
# === RÉSEAUX ===
docker network ls                        # Lister les réseaux
docker network inspect mon-reseau       # Détails du réseau
docker exec container1 ping container2  # Test connectivité

# === VOLUMES ===
docker volume ls                         # Lister les volumes
docker volume inspect mon-volume        # Détails du volume
docker system df                         # Utilisation de l'espace

# === CONTAINERS ===
docker port mon-container               # Mapping des ports
docker exec -it container bash          # Shell interactif
```

---

# Bonnes pratiques 2025 🛡️

### Sécurité et performance

**Réseaux** :
- Créez des réseaux séparés (frontend/backend)
- Utilisez `--internal` pour isoler du réseau externe
- Évitez le réseau `bridge` par défaut en production

**Volumes** :
- Privilégiez les **named volumes** en production
- Utilisez les **bind mounts** uniquement en développement
- Ajoutez `:ro` (lecture seule) quand possible

**Compose** :
- Séparez les environnements avec des fichiers `.env`
- Utilisez `depends_on` pour l'ordre de démarrage
- Configurez des healthchecks pour la robustesse 