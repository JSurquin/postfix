---
layout: new-section
---

# 🎯 Exercice Unifié : Application Docker Complète

---
routeAlias: 'exercice-unifie-docker'
---

<a name="EXERCICE_UNIFIE_DOCKER" id="EXERCICE_UNIFIE_DOCKER"></a>

# 🎯 Exercice Unifié : Application Docker Complète

### De zéro à une stack web professionnelle

Cet exercice progressif vous mène **étape par étape** de la création d'une simple page HTML à une application web complète avec base de données, réseau et persistance. **L'application évolue** à chaque étape !

---

# 🚀 ÉTAPE 1 : Premier site web statique

### Mission : Créer votre premier Dockerfile

Nous commençons simple : un site web statique avec Nginx.

```bash
# Créer le projet
mkdir mon-projet-docker
cd mon-projet-docker
```

---

# Création du contenu web 📝

```bash
# Créer une page HTML simple
cat << EOF > index.html
<!DOCTYPE html>
<html>
<head>
    <title>Mon Projet Docker - V1</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 50px; }
        .version { color: #4CAF50; font-size: 24px; }
    </style>
</head>
<body>
    <h1>🐳 Mon Premier Site Docker</h1>
    <div class="version">Version 1.0 - Site Statique</div>
    <p>Cette page est servie depuis un container Docker !</p>
</body>
</html>
EOF
```

---

# Premier Dockerfile 🏗️

```dockerfile
# Dockerfile - Version 1
FROM nginx:alpine

# Copier notre page
COPY index.html /usr/share/nginx/html/

# Exposer le port
EXPOSE 80

# Nginx démarre automatiquement
```

---

# Test de la V1 ✅

```bash
# Construire l'image
docker build -t mon-projet:v1 .

# Lancer le container
docker run -d -p 8080:80 --name site-v1 mon-projet:v1

# Tester
curl http://localhost:8080
# Ou ouvrir http://localhost:8080 dans le navigateur

# Nettoyage pour la suite
docker stop site-v1 && docker rm site-v1
```

**✅ Checkpoint** : Vous avez un site web qui fonctionne !

---

# 🌐 ÉTAPE 2 : Ajouter les réseaux personnalisés

### Mission : Préparer pour une architecture multi-containers

L'application évolue : nous allons préparer le terrain pour ajouter une base de données.

---

# Création du réseau 🔗

```bash
# Créer un réseau personnalisé
docker network create mon-projet-net

# Vérifier
docker network ls
docker network inspect mon-projet-net
```

---

# Nouvelle version avec réseau 🌐

```bash
# Mettre à jour la page HTML
cat << EOF > index.html
<!DOCTYPE html>
<html>
<head>
    <title>Mon Projet Docker - V2</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 50px; }
        .version { color: #2196F3; font-size: 24px; }
        .network { color: #FF9800; }
    </style>
</head>
<body>
    <h1>🐳 Mon Site avec Réseau</h1>
    <div class="version">Version 2.0 - Réseau Personnalisé</div>
    <p class="network">Container sur le réseau 'mon-projet-net'</p>
    <p>Prêt pour une base de données !</p>
</body>
</html>
EOF
```

---

# Rebuild et test avec réseau 🧪

```bash
# Reconstruire
docker build -t mon-projet:v2 .

# Lancer sur le réseau personnalisé
docker run -d -p 8080:80 \
  --name site-v2 \
  --network mon-projet-net \
  mon-projet:v2

# Tester
curl http://localhost:8080

# Vérifier la connectivité réseau
docker exec site-v2 ping google.com
```

**✅ Checkpoint** : Site avec réseau personnalisé opérationnel !

---

# 💾 ÉTAPE 3 : Ajouter la persistance avec volumes

### Mission : Préparer les logs et données persistantes

L'application évolue encore : nous allons ajouter la gestion des logs.

---

# Création des volumes 📁

```bash
# Créer les volumes
docker volume create mon-projet-logs
docker volume create mon-projet-data

# Vérifier
docker volume ls
docker volume inspect mon-projet-logs
```

---

# Configuration Nginx avec logs 📊

```bash
# Créer une config Nginx personnalisée
cat << EOF > nginx.conf
server {
    listen 80;
    
    # Configuration des logs
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    
    root /usr/share/nginx/html;
    index index.html;
    
    location / {
        try_files \$uri \$uri/ =404;
        # Ajouter des headers pour debug
        add_header X-Container-Name "mon-projet-v3";
        add_header X-Version "3.0";
    }
    
    # Page de stats simple
    location /stats {
        return 200 '{"version": "3.0", "status": "running", "container": "avec volumes"}';
        add_header Content-Type application/json;
    }
}
EOF
```

---

# Dockerfile V3 avec volumes 🏗️

```dockerfile
# Dockerfile - Version 3
FROM nginx:alpine

# Copier la configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copier le site
COPY index.html /usr/share/nginx/html/

# Créer le répertoire de logs
RUN mkdir -p /var/log/nginx

# Exposer le port
EXPOSE 80

# Nginx démarre automatiquement
```

---

# Page HTML V3 avec stats 📈

```bash
# Mettre à jour la page
cat << EOF > index.html
<!DOCTYPE html>
<html>
<head>
    <title>Mon Projet Docker - V3</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 50px; }
        .version { color: #9C27B0; font-size: 24px; }
        .feature { color: #4CAF50; margin: 10px; }
        button { padding: 10px 20px; font-size: 16px; margin: 10px; }
    </style>
</head>
<body>
    <h1>🐳 Mon Site avec Volumes</h1>
    <div class="version">Version 3.0 - Persistance des Données</div>
    
    <div class="feature">🌐 Réseau personnalisé</div>
    <div class="feature">💾 Volumes persistants</div>
    <div class="feature">📊 Logs sauvegardés</div>
    
    <button onclick="fetch('/stats').then(r=>r.json()).then(d=>alert(JSON.stringify(d)))">
        Voir les stats
    </button>
    
    <p>Prêt pour une base de données !</p>
</body>
</html>
EOF
```

---

# Test V3 avec volumes 🧪

```bash
# Arrêter V2
docker stop site-v2 && docker rm site-v2

# Construire V3
docker build -t mon-projet:v3 .

# Lancer avec volumes
docker run -d -p 8080:80 \
  --name site-v3 \
  --network mon-projet-net \
  -v mon-projet-logs:/var/log/nginx \
  -v mon-projet-data:/data \
  mon-projet:v3

# Tester
curl http://localhost:8080
curl http://localhost:8080/stats

# Générer des logs
for i in {1..10}; do curl http://localhost:8080; done
```

---

# Vérification de la persistance 🔍

```bash
# Voir les logs générés
docker exec site-v3 cat /var/log/nginx/access.log

# Redémarrer le container
docker restart site-v3

# Vérifier que les logs persistent
docker exec site-v3 cat /var/log/nginx/access.log

# Les logs sont toujours là ! 🎉
```

**✅ Checkpoint** : Application avec persistance des données !

---

# 🗄️ ÉTAPE 4 : Ajouter une base de données

### Mission : Stack complète avec PostgreSQL

L'application finale : site web + base de données + tout ce qu'on a appris !

---

# Lancement de PostgreSQL 🐘

```bash
# Créer un volume pour la DB
docker volume create postgres-data

# Lancer PostgreSQL sur notre réseau
docker run -d \
  --name database \
  --network mon-projet-net \
  -v postgres-data:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=monmotdepasse \
  -e POSTGRES_DB=monprojet \
  postgres:15-alpine

# Vérifier que ça fonctionne
docker logs database
```

---

# Test de connectivité DB 🔗

```bash
# Tester la connexion depuis notre site
docker exec site-v3 ping database

# Se connecter à la DB pour créer une table
docker exec -it database psql -U postgres -d monprojet

# Dans psql, créer une table simple :
# CREATE TABLE visiteurs (id SERIAL PRIMARY KEY, ip VARCHAR(50), timestamp TIMESTAMP DEFAULT NOW());
# \q pour quitter
```

---

# Application finale avec DB 🏆

```bash
# Page HTML finale avec simulation DB
cat << EOF > index.html
<!DOCTYPE html>
<html>
<head>
    <title>Mon Projet Docker - FINAL</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 50px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; }
        .version { color: #FFD700; font-size: 28px; font-weight: bold; }
        .feature { background: rgba(255,255,255,0.2); margin: 10px; padding: 15px; border-radius: 10px; }
        .stack { display: flex; justify-content: center; gap: 20px; flex-wrap: wrap; }
        .component { background: rgba(255,255,255,0.3); padding: 20px; border-radius: 15px; min-width: 150px; }
        button { padding: 15px 30px; font-size: 18px; margin: 10px; border: none; border-radius: 25px; background: #FFD700; color: #333; cursor: pointer; }
        button:hover { background: #FFA500; }
    </style>
</head>
<body>
    <h1>🏆 Projet Docker Complet</h1>
    <div class="version">Version FINALE - Stack Complète</div>
    
    <div class="stack">
        <div class="component">
            <h3>🌐 Nginx</h3>
            <p>Serveur Web</p>
        </div>
        <div class="component">
            <h3>🐘 PostgreSQL</h3>
            <p>Base de Données</p>
        </div>
        <div class="component">
            <h3>🌉 Réseau</h3>
            <p>Communication</p>
        </div>
        <div class="component">
            <h3>💾 Volumes</h3>
            <p>Persistance</p>
        </div>
    </div>
    
    <div class="feature">✅ Dockerfile optimisé</div>
    <div class="feature">✅ Réseau personnalisé</div>
    <div class="feature">✅ Volumes persistants</div>
    <div class="feature">✅ Base de données connectée</div>
    
    <button onclick="fetch('/stats').then(r=>r.json()).then(d=>alert('Stack complète opérationnelle!\\n' + JSON.stringify(d, null, 2)))">
        🎯 Voir le statut complet
    </button>
    
    <p>🎉 Félicitations ! Vous maîtrisez Docker !</p>
</body>
</html>
EOF
```

---

# Déploiement final 🚀

```bash
# Construire la version finale
docker build -t mon-projet:final .

# Arrêter l'ancienne version
docker stop site-v3 && docker rm site-v3

# Lancer la version finale
docker run -d -p 8080:80 \
  --name site-final \
  --network mon-projet-net \
  -v mon-projet-logs:/var/log/nginx \
  -v mon-projet-data:/data \
  mon-projet:final

# Test complet
curl http://localhost:8080
curl http://localhost:8080/stats
```

---

# 🐳 BONUS : Docker Compose pour tout automatiser

### Une seule commande pour déployer toute la stack !

```yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8080:80"
    networks:
      - mon-projet-net
    volumes:
      - mon-projet-logs:/var/log/nginx
      - mon-projet-data:/data
    depends_on:
      - database

  database:
    image: postgres:15-alpine
    networks:
      - mon-projet-net
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: monmotdepasse
      POSTGRES_DB: monprojet

networks:
  mon-projet-net:
    driver: bridge

volumes:
  mon-projet-logs:
  mon-projet-data:
  postgres-data:
```

---

# Déploiement avec Compose 🎼

```bash
# Tout arrêter
docker stop site-final database
docker rm site-final database

# Déployer avec Compose
docker compose up -d

# Voir les logs
docker compose logs

# Tester
curl http://localhost:8080

# Tout arrêter proprement
docker compose down
```

---

# 🎯 Récapitulatif de votre parcours

### Ce que vous avez accompli

**Étape 1** ✅ : Premier Dockerfile + Container web  
**Étape 2** ✅ : Réseaux personnalisés + Communication  
**Étape 3** ✅ : Volumes + Persistance des données  
**Étape 4** ✅ : Stack complète avec base de données  
**Bonus** ✅ : Orchestration avec Docker Compose  

---

# 🧪 Tests de validation

### Checklist finale

```bash
# Votre application doit répondre à tous ces tests :

# 1. Le site web fonctionne
curl -s http://localhost:8080 | grep "Stack Complète"

# 2. Les stats API fonctionnent
curl -s http://localhost:8080/stats | jq .

# 3. La DB est accessible depuis le web
docker exec $(docker ps -q -f name=web) ping database

# 4. Les volumes persistent
docker restart $(docker ps -q -f name=web)
docker exec $(docker ps -q -f name=web) ls -la /var/log/nginx/

# 5. Le réseau isole bien
docker network inspect mon-projet-net | grep -A 5 "Containers"
```

---

# 🏆 Félicitations !

### Compétences acquises

🎓 **Dockerfile** : Création d'images personnalisées  
🎓 **Réseaux** : Communication inter-containers  
🎓 **Volumes** : Persistance des données  
🎓 **Orchestration** : Gestion de stacks complexes  
🎓 **Debug** : Diagnostiquer et résoudre les problèmes  

---

# 🚀 Prochaines étapes

Vous êtes maintenant prêt(e) pour :
- **Ansible** : Automatiser le déploiement de vos stacks
- **Kubernetes** : Orchestration à grande échelle
- **CI/CD** : Automatisation complète des déploiements
- **Monitoring** : Surveiller vos applications en production

**Pro tip** : Gardez ce projet comme template pour vos futurs développements ! 🌟 