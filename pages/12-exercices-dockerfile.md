---
layout: new-section
routeAlias: 'exercices-dockerfile'
---

<a name="EXERCICES_DOCKERFILE" id="EXERCICES_DOCKERFILE"></a>

# Exercices Dockerfile 📝

### 3 niveaux progressifs simples

Apprenez Docker étape par étape !

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
cat > index.html << 'EOF'
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
EOF
```

---

# 🟢 Dockerfile Simple

```dockerfile
# 3. Créer le Dockerfile
cat > Dockerfile << 'EOF'
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
EOF

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
cat > aide.sh << 'EOF'
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
EOF

chmod +x aide.sh
```

---

# 🟡 Dockerfile Intermédiaire

```dockerfile
# 3. Dockerfile avec outils
cat > Dockerfile << 'EOF'
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
EOF

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
cat > index.html << 'EOF'
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
EOF

cat > about.html << 'EOF'
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
EOF
```

---

# 🔴 Dockerfile Multi-stage

```dockerfile
# 3. Dockerfile optimisé
cat > Dockerfile << 'EOF'
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
EOF

# 4. Construire les deux versions pour comparer
docker build -t site-optimise:multistage .

# Version non-optimisée pour comparaison
cat > Dockerfile.simple << 'EOF'
FROM nginx:alpine
RUN apk add --no-cache curl
COPY *.html /usr/share/nginx/html/
EOF

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