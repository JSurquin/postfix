---
layout: default
routeAlias: 'exercice-dockerfile'
---

<a name="EXERCICE_DOCKERFILE" id="EXERCICE_DOCKERFILE"></a>

# 🛠️ Exercice Pratique : Maîtriser les Dockerfiles

### Objectif pédagogique
Créer des Dockerfiles optimisés et sécurisés pour différents types d'applications, en appliquant les bonnes pratiques 2025.

---

# Exercice 1 : Premier Dockerfile simple 🚀

### Mission
Créer un Dockerfile pour une application web statique simple.

### Instructions

```bash
# 1. Créez la structure du projet
mkdir mon-premier-dockerfile
cd mon-premier-dockerfile
```

---

# Création du fichier HTML 🚀

```bash
# 2. Créez un fichier HTML simple
cat << EOF > index.html
<!DOCTYPE html>
<html>
<head>
    <title>Mon Premier Docker</title>
</head>
<body>
    <h1>🐳 Hello Docker World!</h1>
    <p>Cette page est servie depuis un container Docker.</p>
</body>
</html>
EOF
```

---

# Dockerfile à créer 🚀

```dockerfile
# Votre mission : créer ce Dockerfile
FROM nginx:alpine

# Copier votre fichier HTML
COPY index.html /usr/share/nginx/html/

# Exposer le port
EXPOSE 80

# La commande est héritée de l'image de base
```

---

# Test du premier Dockerfile 🚀

```bash
# 3. Construisez l'image
docker build -t mon-site-web:v1 .

# 4. Lancez le container
docker run -d -p 8080:80 --name mon-site mon-site-web:v1

# 5. Testez
curl http://localhost:8080
# ou ouvrez dans votre navigateur
```

---

# Nettoyage Exercice 1 🚀

```bash
# 6. Nettoyage
docker stop mon-site
docker rm mon-site
```

---

# Exercice 2 : Application Node.js optimisée 📦

### Mission
Créer un Dockerfile optimisé pour une application Node.js avec gestion du cache.

### Préparation

```bash
# 1. Nouveau projet
mkdir app-nodejs-optimisee
cd app-nodejs-optimisee
```

---

# Création du package.json 📦

```bash
# 2. Créez package.json
cat << EOF > package.json
{
  "name": "mon-app-docker",
  "version": "1.0.0",
  "description": "Application Node.js pour Docker",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
EOF
```

---

# Création du server.js 📦

```bash
# 3. Créez server.js
cat << EOF > server.js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
    res.json({
        message: '🚀 Application Node.js dans Docker!',
        timestamp: new Date().toISOString(),
        version: process.env.NODE_ENV || 'development'
    });
});
```

---

# Suite server.js 📦

```bash
app.get('/health', (req, res) => {
    res.status(200).json({ status: 'healthy' });
});

app.listen(PORT, () => {
    console.log(\`🚀 Serveur démarré sur le port \${PORT}\`);
});
EOF
```

---

# Dockerfile optimisé - Base 📦

```dockerfile
# Image de base légère
FROM node:20-alpine

# Métadonnées
LABEL maintainer="votre-email@example.com" \
      version="1.0.0" \
      description="Application Node.js optimisée"

# Variables d'environnement
ENV NODE_ENV=production \
    PORT=3000

# Répertoire de travail
WORKDIR /app
```

---

# Dockerfile optimisé - Dépendances 📦

```dockerfile
# Copie des fichiers de dépendances AVANT le code source (cache)
COPY package*.json ./

# Installation des dépendances de production seulement
RUN npm ci --only=production && \
    npm cache clean --force

# Copie du code source
COPY . .
```

---

# Dockerfile optimisé - Sécurité 📦

```dockerfile
# Créer un utilisateur non-root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
RUN chown -R appuser:appgroup /app
USER appuser
```

---

# Dockerfile optimisé - Healthcheck 📦

```dockerfile
# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD node -e "const http = require('http'); \
    const options = { host: 'localhost', port: process.env.PORT, path: '/health', timeout: 2000 }; \
    const req = http.get(options, (res) => { \
        if (res.statusCode === 200) { process.exit(0); } \
        else { process.exit(1); } \
    }); \
    req.on('error', () => process.exit(1)); \
    req.on('timeout', () => process.exit(1));"

# Port d'exposition
EXPOSE 3000

# Commande de démarrage
CMD ["npm", "start"]
```

---

# Test avancé Node.js 📦

```bash
# Construire et tester
docker build -t node-app-optimisee:v1 .
docker run -d -p 3000:3000 --name node-app node-app-optimisee:v1

# Tester l'application
curl http://localhost:3000
curl http://localhost:3000/health
```

---

# Vérifications sécurité 📦

```bash
# Vérifier le healthcheck
docker ps  # Le status doit être "healthy"

# Vérifier la sécurité (utilisateur non-root)
docker exec node-app whoami  # Doit afficher "appuser"
```

---

# Exercice 3 : Multi-stage build Python 🐍

### Mission
Créer un Dockerfile multi-stage pour optimiser une application Python.

### Préparation

```bash
# 1. Nouveau projet
mkdir app-python-multistage
cd app-python-multistage
```

---

# Création requirements.txt 🐍

```bash
# 2. Créez requirements.txt
cat << EOF > requirements.txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
pydantic==2.5.0
requests==2.31.0
EOF
```

---

# Création main.py partie 1 🐍

```bash
# 3. Créez main.py
cat << EOF > main.py
from fastapi import FastAPI
from pydantic import BaseModel
import uvicorn
import os

app = FastAPI(title="App Docker Multi-stage", version="1.0.0")

class HealthResponse(BaseModel):
    status: str
    message: str
```

---

# Création main.py partie 2 🐍

```bash
@app.get("/")
async def root():
    return {
        "message": "🐍 Application Python FastAPI dans Docker!",
        "environment": os.getenv("ENVIRONMENT", "production"),
        "multi_stage": True
    }

@app.get("/health", response_model=HealthResponse)
async def health():
    return HealthResponse(status="healthy", message="Application fonctionne correctement")

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
EOF
```

---

# Dockerfile multi-stage

```dockerfile
# Stage 1: Build - Image complète avec outils de build
FROM python:3.11-slim as builder

# Installation des outils de build
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        && rm -rf /var/lib/apt/lists/*

# Copie des requirements
COPY requirements.txt .

# Installation des dépendances dans un dossier séparé
RUN pip install --user --no-cache-dir -r requirements.txt

# Stage 2: Runtime - Image minimale pour la production
FROM python:3.11-slim

# Métadonnées
LABEL maintainer="votre-email@example.com" \
      description="Application FastAPI multi-stage optimisée"

# Variables d'environnement
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PATH=/home/appuser/.local/bin:$PATH \
    ENVIRONMENT=production

# Création de l'utilisateur non-root
RUN useradd -m -u 1000 appuser

# Copie des dépendances depuis le stage builder
COPY --from=builder /root/.local /home/appuser/.local

# Répertoire de travail
WORKDIR /app

# Copie du code source
COPY --chown=appuser:appuser . .

# Changement vers utilisateur non-root
USER appuser

# Healthcheck
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health', timeout=5)"

# Port d'exposition
EXPOSE 8000

# Commande de démarrage
CMD ["python", "main.py"]
```

### Comparaison des tailles

```bash
# Construire et comparer
docker build -t python-app-multistage:v1 .

# Vérifier la taille de l'image finale
docker images python-app-multistage:v1

# Tester l'application
docker run -d -p 8000:8000 --name python-app python-app-multistage:v1
curl http://localhost:8000
curl http://localhost:8000/health
```

---

# Exercice 4 : Dockerfile Go ultra-optimisé ⚡

### Mission
Créer l'image Docker la plus légère possible pour une application Go.

### Préparation

```bash
# 1. Nouveau projet
mkdir app-go-minimal
cd app-go-minimal

# 2. Créez go.mod
cat << EOF > go.mod
module docker-app

go 1.21
EOF

# 3. Créez main.go
cat << EOF > main.go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "time"
)

type Response struct {
    Message   string \`json:"message"\`
    Timestamp string \`json:"timestamp"\`
    Language  string \`json:"language"\`
}

type HealthResponse struct {
    Status string \`json:"status"\`
}

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        response := Response{
            Message:   "⚡ Application Go ultra-légère dans Docker!",
            Timestamp: time.Now().Format(time.RFC3339),
            Language:  "Go",
        }
        json.NewEncoder(w).Encode(response)
    })

    http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        w.WriteHeader(http.StatusOK)
        json.NewEncoder(w).Encode(HealthResponse{Status: "healthy"})
    })

    fmt.Println("🚀 Serveur Go démarré sur le port 8080")
    log.Fatal(http.ListenAndServe(":8080", nil))
}
EOF
```

### Dockerfile ultra-optimisé

```dockerfile
# Stage 1: Build
FROM golang:1.21-alpine AS builder

# Installation des certificats SSL (nécessaires pour les requêtes HTTPS)
RUN apk --no-cache add ca-certificates

# Répertoire de travail
WORKDIR /app

# Copie des fichiers Go
COPY go.mod go.sum* ./
RUN go mod download

COPY . .

# Build statique sans dépendances dynamiques
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -ldflags '-extldflags "-static"' -o main .

# Stage 2: Runtime minimal avec scratch
FROM scratch

# Copie des certificats SSL
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Copie de l'exécutable
COPY --from=builder /app/main /main

# Port d'exposition
EXPOSE 8080

# Point d'entrée
ENTRYPOINT ["/main"]
```

### Test de l'optimisation

```bash
# Construire l'image ultra-légère
docker build -t go-app-minimal:v1 .

# Vérifier la taille (devrait être < 20MB!)
docker images go-app-minimal:v1

# Tester l'application
docker run -d -p 8080:8080 --name go-app go-app-minimal:v1
curl http://localhost:8080
curl http://localhost:8080/health

# Comparer avec une version non-optimisée
# (créez un Dockerfile avec FROM golang:1.21 sans multi-stage)
```

---

# 🏆 Défi Final : Application Full-Stack

### Mission
Créer un Dockerfile pour une application full-stack avec frontend et backend.

### Structure du projet

```bash
mkdir app-fullstack-docker
cd app-fullstack-docker

# Frontend (React simple)
mkdir frontend
cat << EOF > frontend/package.json
{
  "name": "frontend",
  "version": "1.0.0",
  "scripts": {
    "build": "echo 'Build simulé' && mkdir -p build && echo '<h1>Frontend React</h1><p>Build via Docker</p>' > build/index.html"
  }
}
EOF

# Backend (Node.js)
mkdir backend
cat << EOF > backend/package.json
{
  "name": "backend",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.18.2"
  }
}
EOF

cat << EOF > backend/server.js
const express = require('express');
const path = require('path');
const app = express();

// Servir les fichiers statiques du frontend
app.use(express.static('/app/frontend'));

app.get('/api/health', (req, res) => {
    res.json({ status: 'healthy', service: 'fullstack' });
});

app.get('/api/info', (req, res) => {
    res.json({
        message: 'Full-stack app dans Docker!',
        frontend: 'React (simulé)',
        backend: 'Node.js + Express'
    });
});

app.listen(3000, () => {
    console.log('🚀 Full-stack app sur le port 3000');
});
EOF
```

### Dockerfile Full-Stack

```dockerfile
# Stage 1: Build Frontend
FROM node:20-alpine AS frontend-builder
WORKDIR /app/frontend
COPY frontend/package.json ./
RUN npm install
COPY frontend/ ./
RUN npm run build

# Stage 2: Build Backend
FROM node:20-alpine AS backend-builder
WORKDIR /app/backend
COPY backend/package.json ./
RUN npm ci --only=production

# Stage 3: Production
FROM node:20-alpine
LABEL description="Application Full-Stack optimisée"

# Variables d'environnement
ENV NODE_ENV=production

# Création utilisateur non-root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Répertoire de travail
WORKDIR /app

# Copie du backend
COPY --from=backend-builder /app/backend /app/backend
COPY --from=backend-builder /app/backend/node_modules /app/backend/node_modules

# Copie du frontend build
COPY --from=frontend-builder /app/frontend/build /app/frontend

# Changement des permissions
RUN chown -R appuser:appgroup /app
USER appuser

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
    CMD node -e "require('http').get('http://localhost:3000/api/health', (res) => process.exit(res.statusCode === 200 ? 0 : 1)).on('error', () => process.exit(1))"

EXPOSE 3000

# Démarrage
CMD ["node", "/app/backend/server.js"]
```

---

# 📝 Auto-évaluation avancée

### Checklist des compétences Dockerfile

**Optimisation** :
- [ ] J'utilise des images de base appropriées (alpine, slim)
- [ ] J'applique le principe des multi-stage builds
- [ ] Je gère correctement le cache des layers
- [ ] Je minimise le nombre d'instructions RUN

**Sécurité** :
- [ ] Je crée des utilisateurs non-root
- [ ] Je gère les permissions correctement
- [ ] J'évite d'exposer des informations sensibles
- [ ] J'utilise des images officielles vérifiées

**Bonnes pratiques** :
- [ ] Je structure logiquement mon Dockerfile
- [ ] J'ajoute des métadonnées (LABEL)
- [ ] J'implémente des healthchecks
- [ ] Je gère les variables d'environnement

**Performance** :
- [ ] Mes images sont de taille optimale
- [ ] Les temps de build sont raisonnables
- [ ] Le cache Docker est efficace
- [ ] Les applications démarrent rapidement

### Résultats attendus

- **Image simple** : ~10-50 MB
- **Image Node.js optimisée** : ~100-200 MB  
- **Image Python multi-stage** : ~150-300 MB
- **Image Go minimal** : ~10-20 MB
- **Image Full-stack** : ~200-400 MB

---

# 🚀 Prochaines étapes

Maîtrisez maintenant :
- **Docker Compose** : Orchestrer plusieurs containers
- **Réseaux Docker** : Communication inter-containers  
- **Volumes** : Persistance avancée
- **Optimisation** : Techniques de production
- **Sécurité** : Hardening et scanning

**Pro tip** : Gardez ces Dockerfiles comme templates pour vos futurs projets ! 