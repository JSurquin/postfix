---
layout: new-section
routeAlias: 'premier-contact-docker'
---

<a name="PREMIER_CONTACT" id="PREMIER_CONTACT"></a>

# Votre premier exercice avec Docker 🎯

---

### Votre première expérience pratique

Maintenant que vous connaissez les concepts **ET** les commandes CLI, mettons les mains dans le cambouis ! Cet exercice vous fait pratiquer ce que vous venez d'apprendre.

---

# Vérification de votre installation 🔍

### Étape 1 : Vérifier que Docker fonctionne

```bash
# Vérifiez votre version Docker
docker --version

# Affichez les informations de base
docker info | head -10
```

### Questions simples :
- Quelle version de Docker avez-vous ?
- Docker est-il bien démarré ?

---

# Premier container : Hello World! 👋

### Étape 2 : Votre tout premier container

```bash
# Lancez le container de test officiel
docker run hello-world
```

### Ce qui vient de se passer :
1. Docker a **téléchargé** l'image `hello-world`
2. Il a **créé** un container à partir de cette image
3. Le container a **affiché** un message de bienvenue
4. Le container s'est **arrêté** automatiquement

---

# Explorer ce qui existe 📦

### Étape 3 : Regarder ce que vous avez maintenant

```bash
# Listez les images téléchargées
docker images

# Listez tous les containers (même arrêtés)
docker ps -a
```

### Questions d'observation :
- Combien d'images avez-vous maintenant ?
- Quel est l'état de votre container `hello-world` ?

---

# Deuxième container : Un serveur web! 🌐

### Étape 4 : Lancer quelque chose d'utile

```bash
# Lancez un serveur web Nginx (en arrière-plan avec un nom)
docker run -d --name mon-premier-site nginx:alpine

# Vérifiez qu'il fonctionne
docker ps
```

### Points d'apprentissage :
- **`-d`** : Lance en arrière-plan (détaché)
- **`--name`** : Donne un nom au container
- **`nginx:alpine`** : Version légère de Nginx

---

# Interagir avec votre container 💬

### Étape 5 : Regarder ce qui se passe

```bash
# Voir les logs du serveur
docker logs mon-premier-site

# Voir les processus qui tournent dans le container
docker top mon-premier-site

# Voir l'utilisation des ressources
docker stats mon-premier-site --no-stream
```

---

# Nettoyer après vous 🧹

### Étape 6 : Arrêter et supprimer

```bash
# Arrêter le serveur web
docker stop mon-premier-site

# Supprimer le container
docker rm mon-premier-site

# Vérifier que c'est bien parti
docker ps -a
```

---

# Test de maîtrise : À votre tour ! 🎯

### Étape 7 : Exercice autonome

**Mission** : Lancez un container Nginx en arrière-plan avec le nom "mon-nginx"

```bash
# À vous de jouer ! Essayez sans regarder la solution...
# Indice : nginx:alpine

# Solution (ne regardez qu'après avoir essayé) :
docker run -d --name mon-nginx nginx:alpine

# Vérification
docker ps
docker logs mon-nginx

# Nettoyage
docker stop mon-nginx && docker rm mon-nginx
```

---

# 🎉 Félicitations !

### Vous venez de maîtriser :

✅ **Vérifier** votre installation Docker  
✅ **Lancer** votre premier container (`docker run`)  
✅ **Lister** les containers et images (`docker ps`, `docker images`)  
✅ **Surveiller** vos containers (`docker logs`, `docker stats`)  
✅ **Gérer le cycle de vie** (`docker stop`, `docker rm`)  
✅ **Pratiquer** de façon autonome  

### 🚀 **Prêt pour les Dockerfile !**

Vous maîtrisez maintenant les commandes de base. On peut passer aux **images personnalisées** avec les Dockerfile !
