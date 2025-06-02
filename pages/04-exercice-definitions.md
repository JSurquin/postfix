---
layout: new-section
---

# Exercice de définitions Docker

---
routeAlias: 'exercice-definitions'
---

<a name="EXERCICE_DEFINITIONS" id="EXERCICE_DEFINITIONS"></a>

# 🎯 Exercice Pratique : Maîtriser les Définitions Docker

### Objectif pédagogique
Valider votre compréhension des concepts fondamentaux de Docker à travers des exercices concrets et des manipulations pratiques.

---

# Exercice 1 : Exploration de votre environnement 🔍

### Mission
Découvrir et analyser votre installation Docker actuelle.

### Instructions
```bash
# 1. Vérifiez votre version Docker
docker --version
docker-compose --version

# 2. Affichez les informations système Docker
docker system info

# 3. Vérifiez l'espace disque utilisé
docker system df

# 4. Listez toutes les images présentes
docker images

# 5. Listez tous les containers (actifs et arrêtés)
docker ps -a
```

---

### Questions de réflexion
- Quelle version de Docker utilisez-vous ?
- Combien d'espace disque Docker utilise-t-il actuellement ?
- Y a-t-il des containers ou images non utilisés ?

---

# Exercice 2 : Premier container interactif 🚀

### Mission
Créer et interagir avec votre premier container Docker.

---

### Instructions

```bash
# 1. Lancer un container Ubuntu interactif
docker run -it ubuntu:latest bash

# À l'intérieur du container, exécutez :
ls -la
cat /etc/os-release
pwd
whoami

# 2. Installez un package (nano par exemple)
apt update
apt install -y nano

# 3. Créez un fichier
echo "Bonjour Docker!" > /tmp/test.txt
cat /tmp/test.txt

# 4. Sortez du container
exit
```

---

### Analyse

```bash
# 5. Vérifiez l'état du container
docker ps -a

# 6. Redémarrez le container
docker start [CONTAINER_ID]

# 7. Reconnectez-vous
docker exec -it [CONTAINER_ID] bash

# 8. Vérifiez si votre fichier existe toujours
cat /tmp/test.txt
```

---

# Exercice 3 : Gestion des images 📦

### Mission
Comprendre la gestion des images Docker.

### Instructions

```bash
# 1. Recherchez des images Node.js sur Docker Hub
docker search node

# 2. Téléchargez différentes versions de Node.js
docker pull node:18-alpine
docker pull node:20-alpine
docker pull node:latest

# 3. Comparez les tailles des images
docker images node

# 4. Inspectez une image en détail
docker inspect node:18-alpine

# 5. Affichez l'historique des couches
docker history node:18-alpine
```

---

### Questions d'analyse
- Quelle image Node.js est la plus légère et pourquoi ?
- Combien de couches contient l'image `node:18-alpine` ?
- Quelle est la différence de taille entre `node:latest` et `node:18-alpine` ?

---

# Exercice 4 : Cycle de vie des containers 🔄

### Mission
Maîtriser les états et transitions des containers.

---

### Instructions

```bash
# 1. Créez un container nginx en arrière-plan
docker run -d --name mon-nginx nginx:alpine

# 2. Vérifiez qu'il fonctionne
docker ps
docker logs mon-nginx

# 3. Arrêtez le container
docker stop mon-nginx

# 4. Redémarrez-le
docker start mon-nginx

# 5. Pausez le container
docker pause mon-nginx

# 6. Reprenez l'exécution
docker unpause mon-nginx

# 7. Supprimez le container (il doit être arrêté)
docker stop mon-nginx
docker rm mon-nginx
```

---

### Schéma des états
```
Créé → Démarré → En cours → Arrêté → Supprimé
  ↑        ↓        ↑↓         ↑
  └──── Pausé ──────┘          │
                              │
                         Redémarré
```

---

# Exercice 5 : Volumes et persistance 💾

### Mission
Comprendre la persistance des données avec les volumes.

### Instructions

```bash
# 1. Créez un volume nommé
docker volume create mon-volume-test

# 2. Lancez un container avec ce volume
docker run -it --name container-test \
  -v mon-volume-test:/data \
  ubuntu:latest bash

# Dans le container :
echo "Données persistantes" > /data/important.txt
ls -la /data/
exit

# 3. Supprimez le container
docker rm container-test

# 4. Créez un nouveau container avec le même volume
docker run -it --name nouveau-container \
  -v mon-volume-test:/data \
  ubuntu:latest bash

# Vérifiez que les données sont toujours là :
cat /data/important.txt
exit

# 5. Nettoyage
docker rm nouveau-container
docker volume rm mon-volume-test
```

---

# Exercice 6 : Mapping de ports 🌐

### Mission
Comprendre l'exposition et le mapping des ports.

### Instructions

```bash
# 1. Lancez nginx avec mapping de port
docker run -d -p 8080:80 --name web-server nginx:alpine

# 2. Testez l'accès
curl http://localhost:8080
# ou ouvrez http://localhost:8080 dans votre navigateur

# 3. Inspectez la configuration réseau
docker port web-server
docker inspect web-server | grep -A 10 "NetworkSettings"

# 4. Lancez un second nginx sur un port différent
docker run -d -p 8081:80 --name web-server-2 nginx:alpine

# 5. Vérifiez que les deux fonctionnent
curl http://localhost:8080
curl http://localhost:8081

# 6. Nettoyage
docker stop web-server web-server-2
docker rm web-server web-server-2
```

---

# 🏆 Défi Final : Mini-projet intégré

### Mission
Combiner tous les concepts appris dans un mini-projet.

### Objectif
Créer un environnement de développement web simple avec :
- Un serveur web (nginx)
- Un volume pour les fichiers HTML
- Accès via le port 3000

### Instructions détaillées

```bash
# 1. Créez un volume pour vos fichiers web
docker volume create site-web

# 2. Créez un fichier HTML temporaire
mkdir -p /tmp/mon-site
echo '<h1>Mon Premier Site Docker!</h1><p>Bravo, ça fonctionne! 🎉</p>' > /tmp/mon-site/index.html

# 3. Copiez le fichier dans le volume via un container temporaire
docker run --rm -v site-web:/usr/share/nginx/html -v /tmp/mon-site:/source alpine cp /source/index.html /usr/share/nginx/html/

# 4. Lancez le serveur web
docker run -d -p 3000:80 -v site-web:/usr/share/nginx/html --name mon-site-web nginx:alpine

# 5. Testez votre site
curl http://localhost:3000
# ou ouvrez http://localhost:3000 dans votre navigateur

# 6. Vérifiez les logs
docker logs mon-site-web

# 7. Modifier le contenu du site
docker exec -it mon-site-web sh -c 'echo "<h1>Site Modifié! ✨</h1>" > /usr/share/nginx/html/index.html'

# 8. Vérifiez la modification
curl http://localhost:3000
```

---

### Validation
✅ Votre site est accessible sur http://localhost:3000  
✅ Vous pouvez modifier le contenu  
✅ Les données persistent après redémarrage du container  

---

# 📝 Auto-évaluation

### Checklist de compétences acquises

**Concepts fondamentaux** :
- [ ] Je comprends la différence entre image et container
- [ ] Je sais expliquer l'architecture Docker
- [ ] Je maîtrise le cycle de vie d'un container

**Manipulation pratique** :
- [ ] Je sais lancer un container interactif
- [ ] Je sais gérer les images (pull, list, inspect)
- [ ] Je sais mapper les ports correctement
- [ ] Je sais utiliser les volumes pour la persistance

**Debugging** :
- [ ] Je sais consulter les logs d'un container
- [ ] Je sais inspecter l'état d'un container
- [ ] Je sais nettoyer les ressources inutilisées

---

### Score d'évaluation
- **12/12 coché** : Expert Docker débutant! 🏆
- **9-11 coché** : Très bon niveau, quelques révisions 📚
- **6-8 coché** : Bases acquises, pratiquez encore 💪
- **< 6 coché** : Reprenez les concepts théoriques 📖

---

# 🎯 Prochaines étapes

Une fois ces exercices maîtrisés, vous êtes prêt(e) pour :
- Créer vos propres Dockerfiles
- Travailler avec Docker Compose
- Gérer les réseaux Docker avancés
- Déployer en production

**Astuce** : Gardez ces commandes sous la main, elles vous serviront tout au long de votre parcours Docker ! 
