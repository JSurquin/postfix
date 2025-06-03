---
layout: new-section
routeAlias: 'docker-compose-orchestration'
---

<a name="DOCKER_COMPOSE" id="DOCKER_COMPOSE"></a>

# Docker Compose - Orchestration Multi-Containers

---

### Orchestrez vos applications multi-containers

**Docker Compose** permet de définir et gérer des applications multi-containers avec un seul fichier de configuration. Fini les commandes `docker run` interminables !

---

# Pourquoi Docker Compose ? 🤔

### Problème sans Compose

```bash
# Lancer une stack web manuellement
docker network create app-network
docker run -d --name database --network app-network postgres:15
docker run -d --name redis-cache --network app-network redis:alpine
docker run -d --name web-app --network app-network -p 3000:3000 mon-app
docker run -d --name nginx-proxy --network app-network -p 80:80 nginx
```

---

# Problème sans Compose (suite) 🚨

**🚨 Problèmes** : Complexe, répétitif, difficile à maintenir !

---

# Solution avec Compose ✨

### Un seul fichier = toute votre infrastructure

```yaml
# docker-compose.yml
version: '3.8'
services:
  database:
    image: postgres:15
    environment:
      POSTGRES_DB: myapp
      POSTGRES_PASSWORD: secret
  
  redis:
    image: redis:alpine
  
  web:
    build: .
    ports:
      - '3000:3000'
    depends_on:
      - database
      - redis
```

---

# Solution avec Compose (résultat) 🚀

**Une seule commande** : `docker compose up` 🚀

---

# Syntaxe Moderne 2025 ⚡

### Nouvelle syntaxe (recommandée)

```bash
# ✅ Syntaxe moderne Docker 2025
docker compose up -d
docker compose down
docker compose logs -f
docker compose restart web
```

---

# Syntaxe Moderne 2025 (suite) ❌

### Ancienne syntaxe (dépréciée)

```bash
# ❌ Ancienne syntaxe (à éviter)
docker-compose up -d
docker-compose down
```

---

# Syntaxe Moderne 2025 (conclusion) 📝

**Docker intègre maintenant Compose nativement !**

---

# Structure d'un docker-compose.yml 📋

### Anatomie d'un fichier Compose

```yaml
version: '3.8'

services: # Définition des containers
  web:
    build: .
    ports:
      - '3000:3000'
```

---

# Structure docker-compose.yml (réseaux et volumes) 🔧

```yaml
networks: # Réseaux personnalisés
  app-network:
    driver: bridge

volumes: # Volumes partagés
  db-data:
    driver: local
```

---

# Structure docker-compose.yml (secrets) 🔐

```yaml
secrets: # Gestion des secrets (optionnel)
  db-password:
    file: ./secrets/db_password.txt
```

---

# Commandes Essentielles 🎯

### Cycle de vie complet

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker compose up` | Démarrer les services | `docker compose up -d` |
| `docker compose down` | Arrêter et supprimer | `docker compose down` |
| `docker compose ps` | Status des services | `docker compose ps` |

---

# Commandes Essentielles (suite) 🎯

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker compose logs` | Voir les logs | `docker compose logs -f web` |
| `docker compose exec` | Exécuter dans un service | `docker compose exec web bash` |
| `docker compose restart` | Redémarrer | `docker compose restart web` |

---

# Exemple Pratique : Stack Web 🌐

### Application complète Node.js + PostgreSQL + Redis

```yaml
version: '3.8'

services:
  # Base de données PostgreSQL
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: webapp
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret123
    volumes:
      - postgres_data:/var/lib/postgresql/data
```

---

# Exemple Pratique : Stack Web (healthcheck) 🏥

```yaml
    healthcheck:
      test: ['CMD-SHELL', 'pg_isready -U app']
      interval: 30s
      timeout: 10s
      retries: 3

  # Cache Redis
  cache:
    image: redis:7-alpine
    volumes:
      - redis_data:/data
```

---

# Exemple Pratique : Stack Web (application) 📝

```yaml
  # Application Node.js
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - '3000:3000'
    environment:
      NODE_ENV: production
      DATABASE_URL: postgresql://app:secret123@db:5432/webapp
      REDIS_URL: redis://cache:6379
```

---

# Exemple Pratique : Stack Web (dépendances) 🔗

```yaml
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:

networks:
  default:
    name: webapp-network
```

---

# Cas Concret : Build Custom 🛠️

### Application Express.js personnalisée

**Dockerfile** pour notre app :

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

---

# Cas Concret : Application Express 📦

**package.json** :

```json
{
  "name": "mon-app-express",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "pg": "^8.11.0"
  }
}
```

---

# Cas Concret : Code de l'application 💻

**server.js** :

```javascript
const express = require('express');
const { Client } = require('pg');

const app = express();
const port = 3000;

const db = new Client({
  connectionString: process.env.DATABASE_URL
});

app.get('/', async (req, res) => {
  try {
    await db.connect();
    const result = await db.query('SELECT NOW()');
    res.json({
      message: 'App Express + PostgreSQL',
      time: result.rows[0].now,
      environment: process.env.NODE_ENV
    });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(port, () => {
  console.log(`App running on port ${port}`);
});
```

---

# Cas Concret : Docker Compose 🐳

**docker-compose.yml** qui build notre app :

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - '3000:3000'
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://app:secret@db:5432/webapp
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: webapp
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

---

# Cas Concret : Magie de Compose ✨

**Une seule commande** :

```bash
docker compose up --build
```

**Compose fait tout automatiquement** :
1. 🏗️ **Build** l'image à partir du Dockerfile
2. 🚀 **Lance** PostgreSQL
3. 🔗 **Connecte** les services via le réseau
4. ⚡ **Démarre** l'application Express

**Résultat** : Stack complète fonctionnelle !

---

# Variables d'environnement 🔧

### Fichier `.env` pour la configuration

```bash
# .env
NODE_ENV=development
DB_NAME=webapp
DB_USER=app
DB_PASSWORD=secret123
REDIS_HOST=cache
WEB_PORT=3000
```

---

# Variables d'environnement (utilisation) 🔧

```yaml
# docker-compose.yml
services:
  web:
    ports:
      - '${WEB_PORT}:3000'
    environment:
      NODE_ENV: ${NODE_ENV}
      DB_NAME: ${DB_NAME}
```

---

# Variables d'environnement (automatique) ⚡

**Compose charge automatiquement le fichier `.env` !**

---

# Profiles et environnements 🎭

### Gestion multi-environnements

```yaml
services:
  web:
    image: mon-app:latest
    
  # Service de développement uniquement
  dev-tools:
    image: adminer
    ports:
      - '8080:8080'
    profiles:
      - dev
```

---

# Profiles et environnements (prod) 🎭

```yaml      
  # Service de monitoring en production
  monitoring:
    image: grafana/grafana
    profiles:
      - prod
```

---

# Profiles et environnements (commandes) 💻

```bash
# Développement
docker compose --profile dev up

# Production
docker compose --profile prod up
```

---

# Scaling et Load Balancing ⚖️

### Mise à l'échelle facile

```bash
# Lancer 3 instances du service web
docker compose up --scale web=3

# Avec un load balancer
docker compose up --scale web=3 --scale worker=5
```

---

# Load Balancing (configuration) ⚖️

```nginx
# nginx.conf pour load balancing
upstream backend {
    server web_1:3000;
    server web_2:3000;
    server web_3:3000;
}
```

---

# Override et environments 🔄

### Fichiers de surcharge

```yaml
# docker-compose.yml (base)
services:
  web:
    image: mon-app
    environment:
      NODE_ENV: production
```

---

# Override et environments (développement) 🔄

```yaml
# docker-compose.override.yml (développement)
services:
  web:
    build: .
    volumes:
      - .:/app
    environment:
      NODE_ENV: development
```

---

# Override et environments (conclusion) 🔄

**Compose merge automatiquement les fichiers !**

---

# Bonnes Pratiques 2025 ✅

### Recommandations modernes

**🔒 Sécurité** :

```yaml
services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db-password
    secrets:
      - db-password
```

---

# Bonnes Pratiques 2025 (monitoring) ✅

**📊 Monitoring** :

```yaml
services:
  web:
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:3000/health']
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 60s
```

---

# Debugging et Troubleshooting 🔍

### Commandes utiles pour déboguer

```bash
# Voir les services en cours
docker compose ps

# Logs en temps réel
docker compose logs -f

# Inspecter un service spécifique
docker compose logs web
```

---

# Debugging et Troubleshooting (suite) 🔍

```bash
# Reconstruire les images
docker compose build --no-cache

# Valider la configuration
docker compose config

# Nettoyer complètement
docker compose down -v --remove-orphans
```

---

# Intégration CI/CD 🚀

### Docker Compose en production

```yaml
# Production avec secrets externes
services:
  web:
    image: registry.company.com/mon-app:${VERSION}
    environment:
      DATABASE_URL: ${DATABASE_URL}
    deploy:
      replicas: 3
      resources:
        limits:
          memory: 512M
        reservations:
          memory: 256M
```

---

# Intégration CI/CD (déploiement) 🚀

```bash
# Déploiement CI/CD
export VERSION=v1.2.3
docker compose -f docker-compose.prod.yml up -d
```

---

# Comparaison avec Kubernetes 🆚

### Quand utiliser quoi ?

| Docker Compose | Kubernetes |
|----------------|------------|
| **Développement local** | **Production complexe** |
| **Applications simples** | **Microservices à grande échelle** |
| **Prototypage rapide** | **Auto-scaling automatique** |

---

# Comparaison avec Kubernetes (suite) 🆚

| Docker Compose | Kubernetes |
|----------------|------------|
| **Équipes petites/moyennes** | **Équipes DevOps expertes** |
| **Single host** | **Multi-host/cloud** |

**Compose = simplicité, K8s = puissance** 💡

---

# Récapitulatif 📚

### Ce que vous maîtrisez maintenant

✅ **Orchestration multi-containers** avec un seul fichier
✅ **Syntaxe moderne** Docker Compose 2025
✅ **Gestion des environnements** avec profiles et .env
✅ **Build d'images personnalisées** avec Dockerfile

---

# Récapitulatif (suite) 📚

✅ **Scaling** et load balancing
✅ **Bonnes pratiques** de sécurité et monitoring
✅ **Debugging** et troubleshooting

### 🚀 **Prêt pour l'exercice pratique !**

Vous pouvez maintenant créer des applications multi-containers complètes ! 