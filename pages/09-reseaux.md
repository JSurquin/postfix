---
routeAlias: 'reseaux-docker'
---

<a name="reseaux-docker" id="reseaux-docker"></a>

# Les Réseaux Docker 🌐

### Comprendre la communication entre containers

Les **réseaux Docker** constituent l'épine dorsale de la communication entre containers.

---

# Communication sécurisée 🔒

Ils permettent aux applications conteneurisées de communiquer entre elles de manière sécurisée, isolée et performante, tout en offrant une flexibilité architectural remarquable.

---

# Pourquoi les réseaux sont-ils cruciaux ? 🤔

### Isolation

**Isolation** : Séparation logique entre différentes applications

---

# Pourquoi les réseaux - Sécurité 🔒

### Sécurité

**Sécurité** : Contrôle granulaire des communications

---

# Pourquoi les réseaux - Scalabilité 📈

### Scalabilité

**Scalabilité** : Facilite l'ajout de nouveaux services

---

# Pourquoi les réseaux - Portabilité 🚀

### Portabilité

**Portabilité** : Configuration réseau reproductible entre environnements

---

# Architecture réseau Docker 🏗️

### Vue d'ensemble du système

```mermaid
graph TB
    HOST[Host Network Interface]
    BRIDGE[Docker Bridge<br/>docker0]
    
    HOST --> BRIDGE
    
    BRIDGE --> NET1[Network 1<br/>app-frontend]
    BRIDGE --> NET2[Network 2<br/>app-backend]
    BRIDGE --> NET3[Network 3<br/>monitoring]
    
    NET1 --> C1[Container 1<br/>Web Server]
    NET1 --> C2[Container 2<br/>Load Balancer]
    
    NET2 --> C3[Container 3<br/>API Server]
    NET2 --> C4[Container 4<br/>Database]
    
    NET3 --> C5[Container 5<br/>Prometheus]
```

---

# Composants fondamentaux 🧱

### Éléments clés

- **Docker daemon** : Gère les réseaux et le routage
- **Bridge networks** : Réseaux internes isolés

---

# Composants fondamentaux - Suite 🧱

### Autres éléments

- **Host networking** : Accès direct au réseau de l'hôte
- **Overlay networks** : Communication multi-hôtes (Swarm/Kubernetes)

---

# Types de réseaux Docker 📋

### Bridge Network (par défaut)

Le réseau **bridge** est le type par défaut qui crée un réseau privé interne sur l'hôte Docker.

---

# Bridge Network - Diagramme 📊

```mermaid
graph LR
    HOST[Host: 192.168.1.100]
    BRIDGE[Bridge Network<br/>172.17.0.0/16]
    
    HOST --> BRIDGE
    BRIDGE --> C1[Container 1<br/>172.17.0.2]
    BRIDGE --> C2[Container 2<br/>172.17.0.3]
    BRIDGE --> C3[Container 3<br/>172.17.0.4]
```

---

# Bridge Network - Caractéristiques ✨

### Fonctionnalités

- Isolation des containers du réseau hôte
- Communication inter-containers possible

---

# Bridge Network - Caractéristiques suite ✨

### Autres fonctionnalités

- Mapping de ports nécessaire pour l'accès externe
- DNS interne automatique

---

# Host Network 🏠

### Supprime l'isolation réseau

Supprime l'isolation réseau entre le container et l'hôte Docker.

```bash
# Container utilisant le réseau de l'hôte
docker run -d --network host nginx
```

---

# Host Network - Avantages/Inconvénients ⚖️

### Avantages
**Performance maximale**

### Inconvénients
**Pas d'isolation, conflits de ports possibles**

---

# None Network 🚫

### Isolation complète

Isole complètement le container du réseau.

```bash
docker run -d --network none alpine sleep 3600
```

---

# Overlay Network 🌐

### Docker Swarm

Pour la communication entre nodes dans un cluster.

```bash
# Créer un réseau overlay
docker network create -d overlay mon-overlay
```

---

# Macvlan Network 🔗

### Adresses MAC uniques

Permet aux containers d'avoir des adresses MAC uniques et d'apparaître comme des devices physiques.

```bash
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 \
  macvlan-network
```

---

# Gestion des réseaux 🔧

### Création simple

```bash
# Créer un réseau bridge personnalisé
docker network create mon-app-network
```

---

# Gestion des réseaux - Avancée 🔧

### Création avec configuration avancée

```bash
docker network create \
  --driver bridge \
  --subnet=172.20.0.0/16 \
  --ip-range=172.20.240.0/20 \
  --gateway=172.20.0.1 \
  mon-reseau-avance
```

---

# Gestion des réseaux - Inspection 🔍

### Lister et inspecter

```bash
# Lister tous les réseaux
docker network ls
```

---

# Gestion des réseaux - Inspection détaillée 🔍

### Inspecter un réseau en détail

```bash
# Inspecter un réseau en détail
docker network inspect mon-app-network
```

---

# Connexions réseau - Connecter 🔗

### Connecter un container existant

```bash
# Connecter un container existant à un réseau
docker network connect mon-app-network mon-container
```

---

# Connexions réseau - Déconnecter ❌

### Déconnecter un container

```bash
# Déconnecter un container d'un réseau
docker network disconnect mon-app-network mon-container
```

---

# Connexions réseau - Création directe 🚀

### Créer un container directement connecté

```bash
docker run -d --name web-server \
  --network mon-app-network \
  nginx:alpine
```

---

# Communication inter-containers 💬

### Résolution DNS automatique

Docker fournit un DNS interne qui permet aux containers de se trouver par leur nom.

---

# DNS - Exemple pratique 📋

### Créer un réseau et des containers

```bash
# Créer un réseau et des containers
docker network create app-network
```

---

# DNS - Base de données 🗄️

### Créer container database

```bash
docker run -d --name database \
  --network app-network \
  postgres:13
```

---

# DNS - Backend API 🔙

### Créer container backend

```bash
docker run -d --name backend \
  --network app-network \
  mon-api:latest
```

---

# DNS - Communication 💬

### Dans le container backend, on peut faire :

```bash
# curl http://database:5432
```

---

# Test de connectivité - Ping 🏓

### Tester la connexion entre containers

```bash
# Tester la connexion entre containers
docker exec backend ping database
```

---

# Test de connectivité - IP 🌐

### Obtenir l'IP d'un container

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' database
```

---

# Test de connectivité - Netcat 🔧

### Tester avec netcat

```bash
# Tester avec netcat
docker exec backend nc -zv database 5432
```

---

# Architecture multi-tiers 🏢

### Exemple : Application e-commerce

```mermaid
graph TB
    subgraph "Public Network"
        LB[Load Balancer<br/>:80, :443]
    end
    
    subgraph "Frontend Network"
        LB --> WEB1[Web Server 1<br/>:3000]
        LB --> WEB2[Web Server 2<br/>:3000]
    end
    
    subgraph "Backend Network"
        WEB1 --> API1[API Server 1<br/>:8080]
        WEB2 --> API1
        WEB1 --> API2[API Server 2<br/>:8080]
        WEB2 --> API2
    end
    
    subgraph "Data Network"
        API1 --> DB[(PostgreSQL<br/>:5432)]
        API2 --> DB
        API1 --> REDIS[(Redis Cache<br/>:6379)]
        API2 --> REDIS
    end
    
    subgraph "Monitoring Network"
        PROM[Prometheus<br/>:9090]
        GRAF[Grafana<br/>:3000]
        PROM --> GRAF
    end
```

---

# Implémentation - Créer les réseaux 🔨

### Configuration des réseaux

```bash
# Créer les réseaux isolés
docker network create frontend-net
docker network create backend-net
```

---

# Implémentation - Réseaux data/monitoring 🔨

### Configuration suite

```bash
docker network create data-net
docker network create monitoring-net
```

---

# Déploiement frontend - Load Balancer 🔨

### Load Balancer (accès public)

```bash
docker run -d --name nginx-lb \
  --network frontend-net \
  -p 80:80 -p 443:443 \
  nginx:alpine
```

---

# Déploiement frontend - Serveurs Web 🔨

### Serveurs Web (frontend + backend)

```bash
docker run -d --name web-1 \
  --network frontend-net \
  mon-webapp:latest

docker network connect backend-net web-1
```

---

# Déploiement backend - API 🔨

### Serveurs API (backend + data)

```bash
docker run -d --name api-1 \
  --network backend-net \
  mon-api:latest

docker network connect data-net api-1
```

---

# Base de données 💾

### Configuration PostgreSQL

```bash
docker run -d --name postgres-db \
  --network data-net \
  -e POSTGRES_DB=ecommerce \
  -e POSTGRES_USER=app \
  -e POSTGRES_PASSWORD=secretpassword \
  -v postgres-data:/var/lib/postgresql/data \
  postgres:13
```

---

# Services cache 💾

### Cache Redis

```bash
docker run -d --name redis-cache \
  --network data-net \
  -v redis-data:/data \
  redis:alpine
```

---

# Services monitoring 📊

### Service de monitoring

```bash
docker run -d --name prometheus \
  --network monitoring-net \
  -p 9090:9090 \
  -v prometheus-config:/etc/prometheus \
  prom/prometheus
```

---

# Monitoring multi-réseaux 📊

### Connecter Prometheus aux autres réseaux

```bash
# Connecter Prometheus aux autres réseaux pour scraping
docker network connect frontend-net prometheus
docker network connect backend-net prometheus
docker network connect data-net prometheus
```

---

# Docker Compose - Stack complète 📝

### Version et Load Balancer

```yaml
version: '3.8'

services:
  # Load Balancer
  nginx:
    image: nginx:alpine
    networks:
      - frontend
    ports:
      - '80:80'
      - '443:443'
```

---

# Docker Compose - Application Web 📝

### Service Web

```yaml
  # Application Web
  webapp:
    image: mon-webapp:latest
    networks:
      - frontend
      - backend
    depends_on:
      - api
```

---

# Docker Compose - API Backend 📝

### Service API

```yaml
  # API Backend
  api:
    image: mon-api:latest
    networks:
      - backend
      - data
    environment:
      - DATABASE_URL=postgresql://app:secret@postgres:5432/ecommerce
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis
```

---

# Docker Compose - Base de données 📝

### Service PostgreSQL

```yaml
  # Base de données
  postgres:
    image: postgres:13
    networks:
      - data
    environment:
      - POSTGRES_DB=ecommerce
      - POSTGRES_USER=app
      - POSTGRES_PASSWORD=secret
    volumes:
      - postgres_data:/var/lib/postgresql/data
```

---

# Docker Compose - Cache Redis 📝

### Service Redis

```yaml
  # Cache Redis
  redis:
    image: redis:alpine
    networks:
      - data
    volumes:
      - redis_data:/data
```

---

# Docker Compose - Réseaux 📝

### Configuration réseaux

```yaml
networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
  data:
    driver: bridge
    internal: true  # Pas d'accès externe
```

---

# Docker Compose - Volumes 📝

### Configuration volumes

```yaml
volumes:
  postgres_data:
  redis_data:
```

---

# Sécurité et isolation 🔒

### Réseau interne

```bash
# Réseau interne (pas d'accès Internet)
docker network create --internal secure-backend
```

---

# Sécurité - Subnet personnalisé 🔒

### Réseau avec subnet

```bash
docker network create \
  --subnet=10.0.1.0/24 \
  --gateway=10.0.1.1 \
  isolated-network
```

---

# Sécurité - Container isolé 🔒

### Container sans accès réseau

```bash
docker run -d --network none \
  --name isolated-worker \
  mon-worker:latest
```

---

# Stratégies d'isolation 🔒

### Approches de sécurité

1. **Séparation par couches** : Frontend, Backend, Data
2. **Isolation par application** : Chaque app sur son réseau

---

# Stratégies d'isolation - Suite 🔒

### Autres approches

3. **Réseaux internes** : Services de données isolés d'Internet
4. **Monitoring séparé** : Réseau dédié pour l'observabilité

---

# Débogage réseau - tcpdump 🔍

### Analyser le trafic réseau

```bash
# Analyser le trafic réseau
docker exec -it mon-container tcpdump -i eth0
```

---

# Débogage réseau - Routes 🔍

### Vérifier les routes

```bash
# Vérifier les routes
docker exec mon-container ip route
```

---

# Débogage réseau - Scan ports 🔍

### Scanner les ports ouverts

```bash
# Scanner les ports ouverts
docker exec mon-container nmap -p 1-1000 target-container
```

---

# Diagnostic avancé - Connexions 🔍

### Analyser les connexions

```bash
# Analyser les connexions
docker exec mon-container netstat -tulpn
```

---

# Diagnostic avancé - DNS 🔍

### Tester la résolution DNS

```bash
# Tester la résolution DNS
docker exec mon-container nslookup database-server
```

---

# Métriques performance - Stats 📊

### Statistiques réseau en temps réel

```bash
# Statistiques réseau en temps réel
docker stats --format "table {{.Container}}\t{{.NetIO}}"
```

---

# Métriques performance - Bande passante 📊

### Bande passante entre containers

```bash
# Bande passante entre containers
docker exec container1 iperf3 -c container2
```

---

# Métriques performance - Latence 📊

### Latence réseau

```bash
# Latence réseau
docker exec container1 ping -c 10 container2
```

---

# Troubleshooting - DNS 🛠️

### Container ne peut pas résoudre les noms DNS

```bash
# Vérifier que les containers sont sur le même réseau
docker network inspect mon-reseau
```

---

# Troubleshooting - Redémarrage 🛠️

### Redémarrer le daemon Docker si nécessaire

```bash
# Redémarrer le daemon Docker si nécessaire
sudo systemctl restart docker
```

---

# Solutions - Conflit de ports 🛠️

### Lister les ports utilisés

```bash
# Lister les ports utilisés
docker ps --format "table {{.Names}}\t{{.Ports}}"
```

---

# Solutions - Ports différents 🛠️

### Utiliser des ports différents

```bash
# Utiliser des ports différents
docker run -p 8081:80 mon-app  # Au lieu de 8080:80
```

---

# Optimisation - MTU 🛠️

### Vérifier la MTU

```bash
# Vérifier la MTU
docker exec container ip link show eth0
```

---

# Optimisation - Grandes charges 🛠️

### Optimiser pour les grandes charges

```bash
docker network create --opt com.docker.network.bridge.name=docker1 \
  --opt com.docker.network.bridge.enable_ip_masquerade=true \
  optimized-network
```

---

# Exercice pratique avancé 🎯

### Mission : Stack complète avec monitoring

**Objectif** : Déployer une application web avec base de données, cache et monitoring sur réseaux séparés.

---

# Exercice - Architecture requise 🎯

### Architecture requise

- Frontend isolé (accessible publiquement)
- Backend sécurisé (communication interne)

---

# Exercice - Architecture suite 🎯

### Suite de l'architecture

- Base de données isolée (accès restreint)
- Monitoring (visibilité sur tous les réseaux)

---

# Exercice - Contraintes 🎯

### Contraintes techniques

- Utiliser Docker Compose
- Implémenter des healthchecks

---

# Exercice - Contraintes suite 🎯

### Autres contraintes

- Configurer la résolution DNS custom
- Ajouter SSL/TLS termination 