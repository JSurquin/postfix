---
routeAlias: 'le-cli-docker'
---

<a name="le-cli-docker" id="le-cli-docker"></a>

# Le CLI Docker 🖥️

### Votre outil de travail quotidien

Le **Docker CLI** (Command Line Interface) est votre interface principale pour interagir avec Docker. C'est un outil puissant qui vous permet de gérer containers, images, volumes et réseaux avec des commandes simples et intuitives.

---

# Structure des commandes 📋

### Structure des commandes Docker

```bash
docker [OPTIONS] COMMAND [ARG...]
```

---

# Exemples de commandes 💡

**Exemples** :
- `docker run nginx` : Lance un container Nginx
- `docker ps -a` : Liste tous les containers
- `docker build -t myapp .` : Construit une image depuis le répertoire courant

---

# Commandes essentielles pour débuter 🚀

### Gestion des containers

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker run` | Crée et démarre un nouveau container | `docker run -d -p 80:80 nginx` |
| `docker ps` | Liste les containers en cours | `docker ps` |
| `docker ps -a` | Liste tous les containers | `docker ps -a` |

---

# Suite gestion containers 📦

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker stop` | Arrête un container | `docker stop mon_container` |
| `docker start` | Démarre un container arrêté | `docker start mon_container` |
| `docker rm` | Supprime un container | `docker rm mon_container` |

---

# Exemples pratiques containers 🎯

### Exemples pratiques

```bash
# Lancer un serveur web Nginx
docker run -d -p 8080:80 --name mon-nginx nginx

# Voir les containers actifs
docker ps

# Arrêter le container
docker stop mon-nginx
```

---

# Gestion des images Docker 📦

### Commandes de base pour les images

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker pull` | Télécharge une image | `docker pull ubuntu:22.04` |
| `docker images` | Liste les images locales | `docker images` |
| `docker rmi` | Supprime une image | `docker rmi ubuntu:22.04` |

---

# Suite gestion images 🏗️

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker build` | Construit une image | `docker build -t myapp:v1.0 .` |
| `docker tag` | Ajoute un tag à une image | `docker tag myapp:v1.0 myapp:latest` |

---

# Workflow typique images 🔄

### Workflow typique

```bash
# 1. Télécharger une image de base
docker pull node:18-alpine

# 2. Lister les images disponibles
docker images

# 3. Construire votre application
docker build -t mon-app:1.0 .

# 4. Tagger pour la production
docker tag mon-app:1.0 mon-app:latest
```

---

# Inspection et débogage 🔍

### Commandes d'analyse

| Commande | Description | Utilisation |
|----------|-------------|-------------|
| `docker logs` | Affiche les logs d'un container | `docker logs -f mon_container` |
| `docker inspect` | Détails complets d'un objet | `docker inspect mon_container` |
| `docker exec` | Exécute une commande dans un container | `docker exec -it mon_container bash` |

---

# Suite débogage 🔧

| Commande | Description | Utilisation |
|----------|-------------|-------------|
| `docker top` | Processus en cours dans un container | `docker top mon_container` |
| `docker stats` | Utilisation des ressources | `docker stats mon_container` |

---

# Débogage en action 🎯

### Débogage en action

```bash
# Accéder au shell d'un container
docker exec -it mon-nginx /bin/bash

# Surveiller les logs en temps réel
docker logs -f mon-nginx

# Voir l'utilisation CPU/Mémoire
docker stats mon-nginx
```

---

# Commandes avancées et utilitaires 🔧

### Gestion des volumes

```bash
# Créer un volume persistant
docker volume create mon-volume

# Lister les volumes
docker volume ls

# Inspecter un volume
docker volume inspect mon-volume

# Supprimer un volume
docker volume rm mon-volume
```

---

# Gestion des réseaux 🌐

### Gestion des réseaux

```bash
# Créer un réseau custom
docker network create mon-reseau

# Lister les réseaux
docker network ls

# Connecter un container à un réseau
docker network connect mon-reseau mon-container
```

---

# Maintenance et nettoyage 🧹

### Commandes de nettoyage

| Commande | Description | Impact |
|----------|-------------|--------|
| `docker system prune` | Supprime les ressources inutilisées | Containers arrêtés, réseaux, images dangling |
| `docker container prune` | Supprime les containers arrêtés | Libère l'espace des containers |

---

# Suite nettoyage 🗑️

| Commande | Description | Impact |
|----------|-------------|--------|
| `docker image prune` | Supprime les images non utilisées | Nettoie les images dangling |
| `docker volume prune` | Supprime les volumes non utilisés | Libère l'espace des volumes |

---

# Surveillance espace disque 📊

### Surveillance de l'espace disque

```bash
# Voir l'utilisation de l'espace Docker
docker system df

# Informations détaillées du système
docker info

# Nettoyage complet (ATTENTION !)
docker system prune -a --volumes
```

---

# Docker Compose : Nouvelle syntaxe 2025 🎯

### Évolution de la commande

**Ancienne syntaxe** (encore supportée) :
```bash
docker-compose up -d
docker-compose down
docker-compose logs
```

---

# Nouvelle syntaxe Compose ✨

**Nouvelle syntaxe** (recommandée depuis Docker 2.0) :
```bash
docker compose up -d
docker compose down
docker compose logs
```

---

# Commandes Compose essentielles 📋

### Commandes Compose essentielles

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker compose up` | Démarre tous les services | `docker compose up -d` |
| `docker compose down` | Arrête et supprime les services | `docker compose down` |
| `docker compose ps` | État des services | `docker compose ps` |

---

# Suite Compose 🔄

| Commande | Description | Exemple |
|----------|-------------|---------|
| `docker compose logs` | Logs des services | `docker compose logs -f web` |
| `docker compose exec` | Commande dans un service | `docker compose exec web bash` |

---

# Astuces et bonnes pratiques 💡

### Raccourcis utiles

```bash
# Forcer l'arrêt d'un container
docker kill mon_container

# Copier des fichiers container ↔ hôte
docker cp mon_container:/app/logs.txt ./logs.txt
docker cp ./config.json mon_container:/app/

# Sauvegarder une image
docker save -o mon-image.tar mon_image:tag

# Restaurer une image
docker load -i mon-image.tar
```

---

# Variables d'environnement 🔧

### Variables d'environnement et formatage

```bash
# Obtenir l'IP d'un container
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mon_container

# Lister les ports exposés
docker port mon_container

# Filtrer les containers par statut
docker ps -f "status=running"
```

---

# Surveillance et monitoring 📊

### Métriques en temps réel

```bash
# Statistiques détaillées de tous les containers
docker stats

# Statistiques d'un container spécifique
docker stats mon_container --no-stream

# Événements Docker en temps réel
docker events

# Historique d'une image
docker history mon_image:tag
```

---

# Diagnostic avancé 🔬

### Commandes de diagnostic avancées

```bash
# Vérifier la santé d'un container
docker inspect --format='{{.State.Health.Status}}' mon_container

# Voir les processus dans tous les containers
docker exec mon_container ps aux

# Analyser les changements dans un container
docker diff mon_container
```
