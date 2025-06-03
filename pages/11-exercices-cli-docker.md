---
layout: new-section
routeAlias: 'exercices-cli-docker'
---

<a name="EXERCICES_CLI" id="EXERCICES_CLI"></a>

# Exercices CLI Docker 🎯

### 3 niveaux DevOps progressifs

Maîtrisez Docker avec des images existantes, sans développement !

---

# 🟢 Exercice Niveau Simple

### Déployer un serveur web basique

**Objectif** : Maîtriser les commandes Docker de base

**Consignes** :
1. Lancer un serveur nginx avec une page personnalisée
2. Modifier le contenu en temps réel avec un volume
3. Tester différents ports et configurations

---

# 🟢 Correction Niveau Simple - Étape 1

```bash
# 1. Créer un dossier avec du contenu web
mkdir mon-site
echo '<h1>🐳 Mon premier site Docker</h1>' > mon-site/index.html

# 2. Lancer nginx avec volume
docker run -d \
  --name site-web \
  -p 8080:80 \
  -v $(pwd)/mon-site:/usr/share/nginx/html \
  nginx:alpine

# 3. Tester
curl http://localhost:8080
```

---

# 🟢 Correction Niveau Simple - Étape 2

```bash
# 4. Modifier le contenu en temps réel
echo '<h2>🚀 Modification en direct !</h2>' >> mon-site/index.html

# 5. Vérifier les logs
docker logs site-web

# 6. Inspecter le container
docker inspect site-web

# 7. Nettoyage
docker stop site-web && docker rm site-web
```

**✅ Résultat** : Site web accessible et modifiable en temps réel

---

# 🟡 Exercice Niveau Intermédiaire

### Stack LAMP avec base de données

**Objectif** : Orchestrer plusieurs containers avec réseau

**Consignes** :
1. Déployer MySQL avec phpMyAdmin
2. Créer un réseau personnalisé
3. Configurer la persistence des données
4. Gérer les variables d'environnement

---

# 🟡 Correction Niveau Intermédiaire - MySQL

```bash
# 1. Créer un réseau
docker network create lamp-network

# 2. Lancer MySQL
docker run -d \
  --name mysql-db \
  --network lamp-network \
  -e MYSQL_ROOT_PASSWORD=root123 \
  -e MYSQL_DATABASE=testdb \
  -v mysql-data:/var/lib/mysql \
  mysql:8.0
```

---

# 🟡 Correction Niveau Intermédiaire - phpMyAdmin

```bash
# 3. Lancer phpMyAdmin
docker run -d \
  --name phpmyadmin \
  --network lamp-network \
  -e PMA_HOST=mysql-db \
  -e PMA_USER=root \
  -e PMA_PASSWORD=root123 \
  -p 8080:80 \
  phpmyadmin:latest

# 4. Tester la connexion
echo "✅ Accès phpMyAdmin : http://localhost:8080"
echo "👤 User: root | Password: root123"
```

---

# 🟡 Correction Niveau Intermédiaire - Tests

```bash
# 5. Vérifier les services
docker ps
docker network ls
docker volume ls

# 6. Tester la connectivité réseau
docker exec phpmyadmin ping mysql-db

# 7. Nettoyage complet
docker stop phpmyadmin mysql-db
docker rm phpmyadmin mysql-db
docker network rm lamp-network
docker volume rm mysql-data
```

**✅ Résultat** : Stack LAMP fonctionnelle avec persistence

---

# 🔴 Exercice Niveau Avancé

### Site web avec base de données simple

**Objectif** : Faire communiquer 2 containers ensemble

**Consignes** :
1. Lancer une base de données MySQL
2. Lancer phpMyAdmin pour la gérer
3. Créer des fichiers HTML personnalisés
4. Faire fonctionner le tout ensemble

---

# 🔴 Correction Niveau Avancé - Base de données

```bash
# 1. Lancer MySQL avec données persistantes
docker run -d \
  --name ma-base \
  -e MYSQL_ROOT_PASSWORD=monmotdepasse \
  -e MYSQL_DATABASE=monsite \
  -v donnees-mysql:/var/lib/mysql \
  -p 3306:3306 \
  mysql:8.0

echo "⏳ MySQL démarre... (10 secondes)"
sleep 10
echo "✅ MySQL prêt !"
```

---

# 🔴 Correction Niveau Avancé - Interface web

```bash
# 2. Lancer phpMyAdmin pour gérer la base
docker run -d \
  --name interface-db \
  -e PMA_HOST=host.docker.internal \
  -e PMA_PORT=3306 \
  -p 8080:80 \
  --add-host host.docker.internal:host-gateway \
  phpmyadmin:latest

echo "🌐 Interface base de données: http://localhost:8080"
echo "👤 User: root"
echo "🔑 Password: monmotdepasse"
```

---

# 🔴 Correction Niveau Avancé - Site web

```bash
# 3. Créer du contenu HTML
mkdir mon-contenu
cat > mon-contenu/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>Mon Site DevOps</title>
    <style>
        body { font-family: Arial; text-align: center; margin: 50px; }
        .container { max-width: 600px; margin: 0 auto; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🐳 Mon Site Docker</h1>
        <p>Site web avec base de données MySQL</p>
        <p><a href="http://localhost:8080" target="_blank">📊 Gérer la base de données</a></p>
        <hr>
        <small>Exercice Docker niveau avancé</small>
    </div>
</body>
</html>
EOF

# 4. Lancer le serveur web
docker run -d \
  --name mon-site \
  -v $(pwd)/mon-contenu:/usr/share/nginx/html \
  -p 80:80 \
  nginx:alpine

echo "🌐 Mon site: http://localhost"
```

---

# 🔴 Correction Niveau Avancé - Vérifications

```bash
# 5. Vérifier que tout fonctionne
echo "🔍 Vérification des services..."

# Vérifier les containers
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

echo ""
echo "✅ Services disponibles:"
echo "🌐 Site web: http://localhost"
echo "📊 Base de données: http://localhost:8080"
echo ""
echo "📋 Pour arrêter tout:"
echo "docker stop mon-site interface-db ma-base"
```

---

# 🔴 Correction Niveau Avancé - Nettoyage

```bash
# 6. Script de nettoyage
cat > nettoyer.sh << 'EOF'
#!/bin/bash
echo "🧹 Nettoyage..."
docker stop mon-site interface-db ma-base
docker rm mon-site interface-db ma-base
docker volume rm donnees-mysql
rm -rf mon-contenu nettoyer.sh
echo "✅ Terminé !"
EOF

chmod +x nettoyer.sh

echo "🎯 Exercice terminé !"
echo "🧹 Pour nettoyer: ./nettoyer.sh"
```

**✅ Résultat** : Site web + base de données qui fonctionnent ensemble

---

# Récapitulatif Exercices CLI 📋

### Compétences DevOps acquises

**🟢 Niveau Simple** :
- Commandes Docker essentielles
- Volumes et mapping de ports
- Inspection et logs

**🟡 Niveau Intermédiaire** :
- Variables d'environnement
- Gestion multi-containers basique
- Communication via ports

---

# Récapitulatif Exercices CLI (suite) 📋

**🔴 Niveau Avancé** :
- Faire communiquer plusieurs containers
- Persistance des données avec volumes
- Variables d'environnement multiples
- Interface web pour base de données

### 🚀 **Compétences Docker CLI maîtrisées !**

Prêt pour apprendre les Dockerfile ! 🐳
