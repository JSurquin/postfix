---
layout: new-section
routeAlias: 'exercices-dockerfile'
---

<a name="EXERCICES_DOCKERFILE" id="EXERCICES_DOCKERFILE"></a>

# Exercices Dockerfile 📝

### 3 niveaux progressifs simples

Apprenez Docker étape par étape !

---

## 🎮 Exercices Express (Mise en jambes)

### 3 exercices rapides pour créer ses premières images

Avant les exercices principaux, des Dockerfiles simples pour s'échauffer !

---

## 🟢 Exercice Express 1 : Custom Nginx Page

### Personnaliser une page nginx (20 min)

**Ce qu'on apprend** : FROM, COPY, instructions de base

```bash
# 1. Créer une page personnalisée
mkdir my-nginx && cd my-nginx

# 2. Créer le fichier HTML : index.html
<!DOCTYPE html>
<html>
<head><title>Mon Docker Custom</title></head>
<body style="font-family: Arial; text-align: center; padding: 50px; background: #e3f2fd;">
    <h1>🐳 Ma Première Image Custom</h1>
    <p>J'ai créé cette page avec Dockerfile !</p>
    <p>Version: <strong>1.0</strong></p>
</body>
</html>

# 2. Créer le Dockerfile
FROM nginx:alpine
LABEL author="moi"
COPY index.html /usr/share/nginx/html/

# 3. Build et test
docker build -t my-nginx:v1 .
docker run -d --name test-nginx -p 8080:80 my-nginx:v1
echo "🌐 Test: http://localhost:8080"

# 4. Cleanup
docker stop test-nginx && docker rm test-nginx
```

**Mission** : Voir votre page personnalisée !

---

## 🟡 Exercice Express 2 : App Node.js Simple

### Containeriser une app web basique (25 min)

**Ce qu'on apprend** : WORKDIR, RUN, CMD, workflow complet

```bash
# 1. Créer l'app Node.js
mkdir my-app && cd my-app

# 2. Créer le fichier package.json
{
  "name": "docker-app",
  "version": "1.0.0",
  "main": "server.js",
  "dependencies": {
    "express": "^4.18.0"
  }
}

# 3. Créer le fichier server.js
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
    res.send(`
        <h1>🚀 App Node.js Dockerisée</h1>
        <p>Serveur Express dans un container</p>
        <p>Port: ${PORT}</p>
    `);
});

app.listen(PORT, () => {
    console.log(`Serveur sur le port ${PORT}`);
});

# 2. Dockerfile pour Node.js
FROM node:18-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY server.js .
EXPOSE 3000
CMD ["node", "server.js"]

# 3. Build et test
docker build -t my-app:v1 .
docker run -d --name test-app -p 3000:3000 my-app:v1
echo "🌐 App: http://localhost:3000"

# 4. Cleanup
docker stop test-app && docker rm test-app
```

**Mission** : Voir votre app web fonctionner !

---

## 🔴 Exercice Express 3 : Multi-stage Optimisé

### Optimiser avec un build en 2 étapes (30 min)

**Ce qu'on apprend** : Multi-stage build, optimisation de taille

```bash
# 1. Préparer le projet
mkdir optimized-app && cd optimized-app

# App simple qui génère du contenu statique
const fs = require('fs');

const html = `
<!DOCTYPE html>
<html>
<head><title>App Optimisée</title></head>
<body style="font-family: Arial; text-align: center; padding: 50px; background: #f3e5f5;">
    <h1>⚡ Image Multi-Stage</h1>
    <p>Cette image a été optimisée !</p>
    <p>Générée à: ${new Date().toLocaleString()}</p>
</body>
</html>
`;

fs.writeFileSync('dist/index.html', html);
console.log('✅ Contenu généré');

{
  "name": "builder-app",
  "scripts": {
    "build": "mkdir -p dist && node build.js"
  }
}

# 2. Dockerfile multi-stage
# Étape 1: Builder
FROM node:18-alpine AS builder
WORKDIR /app
COPY package.json build.js ./
RUN npm run build

# Étape 2: Production légère
FROM nginx:alpine AS production
LABEL stage="production"
COPY --from=builder /app/dist/ /usr/share/nginx/html/

# 3. Comparaison avec version simple
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm run build
COPY dist/ /usr/share/nginx/html/

# 4. Build des 2 versions
docker build -t optimized:multistage .
docker build -f Dockerfile.simple -t optimized:simple .

# 5. Comparer les tailles
echo "📊 Comparaison des tailles:"
docker images | grep optimized

# 6. Test
docker run -d --name test-opt -p 8080:80 optimized:multistage
echo "🌐 App optimisée: http://localhost:8080"

# 7. Cleanup
docker stop test-opt && docker rm test-opt
```

**Mission** : Comparer les tailles d'images !

---

## 🎯 Exercices Principaux Détaillés

---

# 🟢 Exercice Niveau Simple

### Personnaliser une page web

**Objectif** : Customiser une image nginx avec votre propre page

**Ce qu'on apprend** :
- `FROM` : Choisir une image de base
- `COPY` : Ajouter nos fichiers
- `ENV` : Variables d'environnement

**Consignes** :
1. Partir de `nginx:alpine`
2. Ajouter votre page web personnalisée
3. Tester le résultat

---

# 🟢 Correction Niveau Simple

```bash
# 1. Créer le projet
mkdir mon-site
cd mon-site

# 2. Créer une page web simple
# 2. Créer le fichier HTML : index.html
<!DOCTYPE html>
<html>
<head>
    <title>Mon Site Docker</title>
    <style>
        body {
            font-family: Arial;
            text-align: center;
            padding: 50px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }
        .card {
            background: rgba(255,255,255,0.1);
            padding: 30px;
            border-radius: 15px;
            max-width: 500px;
            margin: 0 auto;
        }
    </style>
</head>
<body>
    <div class="card">
        <h1>🐳 Mon Premier Dockerfile</h1>
        <p>J'ai créé ma première image personnalisée !</p>
        <p><strong>Environnement:</strong> <span id="env">Production</span></p>
        <hr style="border: 1px solid rgba(255,255,255,0.3);">
        <small>Propulsé par Docker 🚀</small>
    </div>
</body>
</html>
```

---

# 🟢 Dockerfile Simple

```dockerfile
# 3. Créer le Dockerfile
# Image de base
FROM nginx:alpine

# Informations sur l'image
LABEL maintainer="moi@formation.fr"
LABEL description="Mon premier site personnalisé"

# Variables d'environnement
ENV SITE_NAME="Mon Site Docker"
ENV VERSION="1.0"

# Copier ma page web dans nginx
COPY index.html /usr/share/nginx/html/

# Le port 80 est déjà exposé par nginx

# 4. Build de l'image
docker build -t mon-site:v1 .

# 5. Test
docker run -d --name test-site -p 8080:80 mon-site:v1

echo "🌐 Votre site: http://localhost:8080"

# 6. Vérification
curl -I http://localhost:8080

# 7. Nettoyage
docker stop test-site && docker rm test-site
```

**✅ Résultat** : Votre première image Docker personnalisée !

---

# 🟡 Exercice Niveau Intermédiaire

### Ajouter des outils utiles

**Objectif** : Créer une image avec quelques outils pratiques

**Ce qu'on apprend** :
- `RUN` : Installer des packages
- `WORKDIR` : Définir le répertoire de travail
- `CMD` : Commande par défaut

**Outils ajoutés** :
- `curl` : Pour tester des URLs
- `nano` : Éditeur de texte
- `htop` : Voir les processus

---

# 🟡 Correction Niveau Intermédiaire

```bash
# 1. Créer le projet
mkdir outils-docker
cd outils-docker

# 2. Script d'aide simple
#!/bin/sh
echo "🛠️ Outils disponibles:"
echo "  curl - Tester des URLs"
echo "  nano - Éditer des fichiers"
echo "  htop - Voir les processus"
echo ""
echo "Exemples:"
echo "  curl https://httpbin.org/json"
echo "  nano test.txt"
echo "  htop"

chmod +x aide.sh
```

---

# 🟡 Dockerfile Intermédiaire

```dockerfile
# 3. Dockerfile avec outils
# Image de base légère
FROM alpine:latest

# Infos
LABEL description="Image avec outils utiles"
LABEL version="2.0"

# Installer les outils
RUN apk update && apk add --no-cache \
    curl \
    nano \
    htop \
    bash

# Copier le script d'aide
COPY aide.sh /usr/local/bin/aide

# Rendre exécutable
RUN chmod +x /usr/local/bin/aide

# Répertoire de travail
WORKDIR /workspace

# Message de bienvenue
RUN echo 'echo "Tapez: aide"' >> /etc/profile

# Commande par défaut
CMD ["sh", "-l"]

# 4. Build et test
docker build -t outils:v2 .

# 5. Test interactif
docker run -it --name test-outils outils:v2

# Dans le container:
# aide
# curl https://httpbin.org/json
# exit

# 6. Nettoyage
docker rm test-outils
```

**✅ Résultat** : Image avec outils pratiques pour tester et débugger

---

# 🔴 Exercice Niveau Avancé

### Multi-stage simple

**Objectif** : Optimiser la taille avec un build en 2 étapes

**Ce qu'on apprend** :
- Multi-stage build
- `COPY --from=`
- Optimisation des images

**Concept** :
- Étape 1 : Préparer les fichiers
- Étape 2 : Image finale légère

---

# 🔴 Correction Multi-stage

```bash
# 1. Créer le projet
mkdir site-optimise
cd site-optimise

# 2. Créer plusieurs pages
<!DOCTYPE html>
<html>
<head>
    <title>Site Optimisé</title>
    <style>
        body { font-family: Arial; padding: 20px; background: #f0f8ff; }
        .container { max-width: 600px; margin: 0 auto; background: white; padding: 20px; border-radius: 10px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 Site Multi-Stage</h1>
        <p>Cette image a été optimisée avec un build multi-stage !</p>
        <a href="about.html">À propos</a>
    </div>
</body>
</html>

<!DOCTYPE html>
<html>
<head>
    <title>À propos</title>
    <style>
        body { font-family: Arial; padding: 20px; background: #f0f8ff; }
        .container { max-width: 600px; margin: 0 auto; background: white; padding: 20px; border-radius: 10px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>À propos</h1>
        <p>Site créé avec Docker multi-stage build</p>
        <a href="index.html">Retour</a>
    </div>
</body>
</html>
```

---

# 🔴 Dockerfile Multi-stage

```dockerfile
# 3. Dockerfile optimisé
# ================================
# Étape 1: Préparation
# ================================
FROM alpine:latest AS builder

# Installer des outils pour préparer
RUN apk add --no-cache curl

# Copier les fichiers sources
WORKDIR /src
COPY *.html ./

# Simuler une optimisation (minification)
RUN mkdir /dist && \
    cp *.html /dist/

# ================================
# Étape 2: Image finale
# ================================
FROM nginx:alpine AS production

# Métadonnées
LABEL stage="production"
LABEL optimized="true"

# Copier SEULEMENT les fichiers finaux
COPY --from=builder /dist/ /usr/share/nginx/html/

# nginx:alpine est déjà optimisé

# 4. Construire les deux versions pour comparer
docker build -t site-optimise:multistage .

# Version non-optimisée pour comparaison
FROM nginx:alpine
RUN apk add --no-cache curl
COPY *.html /usr/share/nginx/html/

docker build -f Dockerfile.simple -t site-optimise:simple .

# 5. Comparer les tailles
echo "📊 Comparaison des tailles:"
docker images | grep site-optimise

# 6. Test
docker run -d --name site-opt -p 8080:80 site-optimise:multistage
echo "🌐 Site: http://localhost:8080"

# 7. Nettoyage
docker stop site-opt && docker rm site-opt
```

**✅ Résultat** : Image optimisée plus petite grâce au multi-stage !

---

# Récapitulatif Dockerfile 📋

### Ce qu'on a appris simplement

**🟢 Niveau Simple** :
- `FROM` : Choisir une image de base
- `COPY` : Ajouter nos fichiers
- `ENV` : Variables d'environnement
- `LABEL` : Métadonnées

**🟡 Niveau Intermédiaire** :
- `RUN` : Installer des packages
- `WORKDIR` : Répertoire de travail
- `CMD` : Commande par défaut
- Scripts d'aide basiques

---

# Récapitulatif Dockerfile (suite) 📋

**🔴 Niveau Avancé** :
- Multi-stage build (2 étapes)
- `COPY --from=` : Copier depuis une étape
- Optimisation de taille
- Comparaison d'images

### 🎯 **Progression logique maîtrisée !**

**Prochaine étape** : Docker Compose pour orchestrer plusieurs containers !

---

# 💡 Points clés à retenir

### Instructions Dockerfile essentielles

```dockerfile
FROM image:tag          # Image de base
LABEL key="value"       # Métadonnées
ENV VAR=value          # Variables d'environnement
RUN commande           # Exécuter pendant le build
COPY source dest       # Copier fichiers
WORKDIR /path          # Répertoire de travail
CMD ["commande"]       # Commande par défaut
```

### Bonnes pratiques simples

1. **Images de base légères** (`alpine`)
2. **Une seule responsabilité** par image
3. **Multi-stage** pour optimiser
4. **Labels** pour la documentation

### 🚀 **Docker maîtrisé progressivement !** 