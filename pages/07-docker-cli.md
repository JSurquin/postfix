---
layout: new-section
routeAlias: 'le-cli-docker'
---

<a name="le-cli-docker" id="le-cli-docker"></a>

# Le CLI Docker

---

# Le CLI Docker 🖥️

### Votre outil de travail quotidien

Le **Docker CLI** est votre interface principale pour interagir avec Docker. Maîtrisons les commandes essentielles pour être productifs au quotidien.

---

# Structure des commandes 📋

### Syntaxe de base

```bash
docker [OPTIONS] COMMAND [ARG...]
```

**Exemples pratiques** :
- `docker run -d -p 80:80 nginx` : Lance un serveur web
- `docker ps -a` : Liste tous les containers
- `docker build -t myapp .` : Construit une image

---

# Gestion des containers - Essentiel 🚀

### Commandes incontournables

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker run` | Créer et démarrer | `docker run -d -p 80:80 --name web nginx` |
| `docker ps` | Containers actifs | `docker ps` |
| `docker ps -a` | Tous les containers | `docker ps -a` |
| `docker stop` | Arrêter | `docker stop web` |
| `docker start` | Redémarrer | `docker start web` |
| `docker rm` | Supprimer | `docker rm web` |

---

# Options run les plus utiles 🔧

### Paramètres essentiels pour docker run

```bash
# Détaché avec nom et port
docker run -d --name mon-app -p 8080:80 nginx

# Variables d'environnement
docker run -e NODE_ENV=production -e PORT=3000 node-app

# Volumes et répertoire de travail
docker run -v $(pwd):/app -w /app node:18 npm install

# Limite de ressources
docker run --memory=512m --cpus=1 mon-app
```

---

# Gestion des images 📦

### Images : télécharger, construire, gérer

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker pull` | Télécharger | `docker pull nginx:alpine` |
| `docker build` | Construire | `docker build -t myapp:v1.0 .` |
| `docker images` | Lister | `docker images` |
| `docker tag` | Tagger | `docker tag myapp:v1.0 myapp:latest` |
| `docker rmi` | Supprimer | `docker rmi myapp:v1.0` |

---

# Inspection et débogage 🔍

### Comprendre ce qui se passe

| Commande | Usage | Exemple |
|----------|--------|---------|
| `docker logs` | Voir les logs | `docker logs -f --tail 100 mon-app` |
| `docker exec` | Exécuter dans le container | `docker exec -it mon-app bash` |
| `docker inspect` | Détails complets | `docker inspect mon-app` |
| `docker stats` | Utilisation ressources | `docker stats` |

---

# Commandes d'inspection pratiques 🎯

### Débogage rapide

```bash
# Accès shell interactif
docker exec -it mon-container bash

# Logs en temps réel
docker logs -f mon-container

# Monitoring des ressources
docker stats --no-stream

# Processus dans le container
docker top mon-container
```

---

# Volumes et réseaux 🌐

### Gestion des ressources

**Volumes** :
```bash
docker volume create mon-volume
docker volume ls
docker volume inspect mon-volume
docker volume rm mon-volume
```

**Réseaux** :
```bash
docker network create mon-reseau
docker network ls
docker network connect mon-reseau mon-container
```

---

# Nettoyage et maintenance 🧹

### Libérer l'espace disque

| Commande | Action | Impact |
|----------|--------|--------|
| `docker system prune` | Nettoyage général | Containers arrêtés, réseaux, images dangling |
| `docker container prune` | Containers arrêtés | Libère l'espace containers |
| `docker image prune` | Images non utilisées | Nettoie les images orphelines |
| `docker volume prune` | Volumes non utilisés | Supprime les volumes inutiles |

---

# Nettoyage avancé 🗑️

### Surveillance et nettoyage

```bash
# Voir l'utilisation de l'espace
docker system df

# Nettoyage complet (ATTENTION !)
docker system prune -a --volumes

# Forcer la suppression
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -q)
```

---

# Docker Compose - Syntaxe 2025 ✨

### Évolution importante

**Nouvelle syntaxe** (recommandée) :
```bash
docker compose up -d
docker compose down
docker compose logs -f
docker compose restart
```

**Ancienne syntaxe** (deprecated) :
```bash
docker-compose up -d
```

---

# Workflow quotidien optimal 🔄

### Séquence type de développement

```bash
# 1. Build de l'image
docker build -t mon-app:dev .

# 2. Lancement en mode développement
docker run -d -p 3000:3000 -v $(pwd):/app --name dev-app mon-app:dev

# 3. Monitoring
docker logs -f dev-app

# 4. Debug si besoin
docker exec -it dev-app bash

# 5. Nettoyage
docker stop dev-app && docker rm dev-app
```

---

# Aliases utiles pour développeurs 💡

### Optimiser votre productivité

```bash
# Ajoutez dans votre .bashrc ou .zshrc
alias dps='docker ps'
alias dpsf='docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"'
alias di='docker images'
alias dlog='docker logs -f'
alias dexec='docker exec -it'
alias dclean='docker system prune -f'
```

---

# Commandes avancées pour pros 🚀

### Techniques avancées

```bash
# Copier fichiers container ↔ hôte
docker cp mon-file.txt mon-container:/app/
docker cp mon-container:/app/result.txt ./

# Créer image depuis container
docker commit mon-container mon-image:v2

# Export/Import d'images
docker save -o mon-app.tar mon-app:latest
docker load -i mon-app.tar
```

---

# Bonnes pratiques CLI 📋

### Conseils pour être efficace

✅ **Utilisez des noms explicites** : `--name web-frontend`  
✅ **Toujours spécifier les tags** : `nginx:1.25-alpine`  
✅ **Nettoyez régulièrement** : `docker system prune`  
✅ **Utilisez les aliases** pour les commandes fréquentes  
✅ **Logs avec limites** : `docker logs --tail 50`  

❌ Évitez `latest` en production  
❌ N'oubliez pas de supprimer les containers de test
