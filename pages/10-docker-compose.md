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

### Anatomie d'un fichier Compose

```yaml
version: '3.8'

services: # Définition des containers
  web:
    build: .
    ports:
      - '3000:3000'
networks: # Réseaux personnalisés
  app-network:
    driver: bridge

volumes: # Volumes partagés
  db-data:
    driver: local
secrets: # Gestion des secrets
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
    healthcheck:
      test: ['CMD-SHELL', 'pg_isready -U app']
      interval: 30s
      timeout: 10s
      retries: 3

  # Application Next.js
  web:
    build:
      context: .
      dockerfile: Dockerfile
    environment:
      NODE_ENV: production
      DATABASE_URL: postgresql://app:secret123@db:5432/webapp
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  # Nginx reverse proxy
  nginx:
    image: nginx:alpine
    ports:
      - '80:80'
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - web
    restart: unless-stopped

volumes:
  postgres_data:

networks:
  default:
    name: webapp-network
```

---

**Dockerfile** pour notre app Next.js :

```dockerfile
FROM node:18-alpine AS base

# Installer les dépendances seulement quand nécessaire
FROM base AS deps
WORKDIR /app

# Installer les dépendances basées sur le gestionnaire de packages préféré
COPY package.json package-lock.json ./
RUN npm ci; 
# npm install

# Rebuild le code source seulement quand nécessaire
# 2 eme stage, permet de différencier le build de l'app et le runner
FROM base AS builder
# je me base sur base et je crée un stage builder
WORKDIR /app
# on copie les dépendances de l'app
COPY --from=deps /app/node_modules ./node_modules
# on copie le code source de l'app
COPY . .

RUN npm run build

# Image de production
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production

# on crée un user et un group
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
# on copie les fichiers public de l'app
COPY --from=builder /app/public ./public
# on copie les fichiers de build de l'app
# Copier les fichiers de build Next.js
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

# on change de user
USER nextjs

# on expose le port 3000
EXPOSE 3000

# on définit le port et le hostname
ENV PORT 3000
ENV HOSTNAME "0.0.0.0"

CMD ["node", "server.js"]
```

---

## **nginx.conf** pour le reverse proxy :

```nginx
events {
    worker_connections 1024;
}
#
# on définit le upstream
http {
    upstream nextjs {
        server web:3000;
    }

    # on définit le server
    server {
        listen 80;
        server_name localhost;

        # on définit la location
        location / {
            # on définit le proxy
            proxy_pass http://nextjs;
            proxy_http_version 1.1;
            # on définit les headers
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            # on définit le host
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;
        }
    }
}
```

---

# Créer l'application Next.js 🚀

**Commandes pour créer et préparer l'app** :

```bash
# Créer l'application Next.js avec TypeScript
npx create-next-app@latest mon-app-nextjs --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"

# Aller dans le dossier
cd mon-app-nextjs

# Ajouter la configuration pour standalone
echo 'module.exports = {
  output: "standalone",
  experimental: {
    outputFileTracingRoot: require("path").join(__dirname, "../../"),
  },
}' > next.config.js

# Ajouter la dépendance PostgreSQL
npm install pg @types/pg
```

---

# Cas Concret : Magie de Compose ✨

**Une seule commande** :

```bash
docker compose up --build
```

**Compose fait tout automatiquement** :
1. 🏗️ **Build** l'image Next.js à partir du Dockerfile
2. 🚀 **Lance** PostgreSQL avec healthcheck
3. 🔗 **Connecte** l'app à la base via le réseau
4. 🌐 **Configure** Nginx comme reverse proxy
5. ⚡ **Démarre** toute la stack sur le port 80

**Résultat** : Stack complète Next.js + PostgreSQL + Nginx fonctionnelle !

---

# Variables d'environnement 🔧

<small>

### Fichier `.env` pour la configuration

</small>

```bash
# .env
NODE_ENV=development
POSTGRES_DB=webapp
POSTGRES_USER=app
POSTGRES_PASSWORD=secret123
WEB_PORT=80
```

<small>

**Utilisation dans docker-compose.yml** :

</small>

```yaml
services:
  web:
    environment:
      NODE_ENV: ${NODE_ENV}
      DATABASE_URL: postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db:5432/${POSTGRES_DB}
  
  nginx:
    ports:
      - '${WEB_PORT}:80'
```

<div class="-mt-2">

**Compose charge automatiquement le fichier `.env` !**

</div>


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

  # Service de monitoring en production
  monitoring:
    image: grafana/grafana
    profiles:
      - prod
```

---

**Commandes** :

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

**Configuration Nginx pour load balancing** :

```nginx
upstream nextjs {
    server web_1:3000;
    server web_2:3000;
    server web_3:3000;
}
```

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

**📊 Monitoring** :

```yaml
services:
  web:
    healthcheck:
      test: [CMD, curl, -f, http://localhost:3000/health]
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

**Suite des commandes** :

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

**Production avec secrets externes** :

```yaml
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

**Déploiement CI/CD** :

```bash
# Déploiement CI/CD
export VERSION=v1.2.3
docker compose -f docker-compose.prod.yml up -d
```

---

# Récapitulatif 📚

### Ce que vous maîtrisez maintenant

✅ **Orchestration multi-containers** avec un seul fichier

✅ **Syntaxe moderne** Docker Compose 2025

✅ **Gestion des environnements** avec profiles et .env

✅ **Build d'images personnalisées** avec Dockerfile Next.js

✅ **Scaling** et load balancing

✅ **Bonnes pratiques** de sécurité et monitoring

✅ **Debugging** et troubleshooting

### 🚀 **Prêt pour l'exercice pratique !**

Vous pouvez maintenant créer des applications multi-containers complètes ! 