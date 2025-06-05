---
layout: new-section
routeAlias: 'qcm-docker-intro'
---

<a name="QCM_DOCKER_INTRO" id="QCM_DOCKER_INTRO"></a>   

# QCM : Parlons de Docker

---

<small>

## QCM sur l'introduction à Docker

<br>

### 1. Qu'est-ce que Docker principalement ?

<br>

- [ ]   Un langage de programmation pour créer des applications
- [ ]   Une plateforme de conteneurisation pour isoler et déployer des applications
- [ ]   Un système d'exploitation léger pour serveurs
- [ ]   Un outil de versioning comme Git

<br>

### 2. Quels sont les trois composants principaux de l'architecture Docker ?

<br>

- [ ]   Image, Container, Registry
- [ ]   Client, Daemon, Hub
- [ ]   Client, Daemon, Registry
- [ ]   Image, Volume, Network

<br>

</small>

---

<small>

### 3. Quelle est la différence principale entre une image et un container Docker ?

<br>

- [ ]   Une image est en lecture seule, un container est l'instance exécutable
- [ ]   Une image est temporaire, un container est permanent
- [ ]   Une image est locale, un container est distant
- [ ]   Aucune différence, ce sont des synonymes

<br>

### 4. Quel n'est PAS un avantage de Docker ?

<br>

- [ ]   Isolation des applications
- [ ]   Portabilité entre environnements
- [ ]   Amélioration automatique des performances
- [ ]   Facilité de déploiement

</small>

---

<small>

### 5. Quelle est la principale différence entre Docker et les VMs ?

<br>

- [ ]   Docker partage le kernel de l'hôte, les VMs ont leur propre OS
- [ ]   Docker est plus lourd que les VMs
- [ ]   Les VMs sont plus sécurisées par défaut
- [ ]   Docker ne peut pas fonctionner sur Windows

<br>

</small>

---

<small>

### 6. Qu'est-ce que Docker Hub ?

<br>

- [ ]   L'interface graphique de Docker
- [ ]   Le registry public officiel pour les images Docker
- [ ]   L'outil de monitoring de Docker
- [ ]   Le système de fichiers de Docker

<br>

### 7. Que fait la commande `docker run -it ubuntu bash` ?

<br>

- [ ]   Lance un container Ubuntu en arrière-plan
- [ ]   Télécharge l'image Ubuntu sans la lancer
- [ ]   Lance un container Ubuntu interactif avec terminal
- [ ]   Supprime un container Ubuntu existant

<br>

</small>

---

<small>

### 8. Comment conserver des données après la suppression d'un container ?

<br>

- [ ]   Les données sont automatiquement sauvegardées
- [ ]   Utiliser des volumes Docker
- [ ]   Redémarrer le container
- [ ]   Impossible, les données sont toujours perdues

</small>

---

<small>

### 9. Qu'est-ce qu'une "layer" dans une image Docker ?

<br>

- [ ]   Un fichier de configuration
- [ ]   Une instruction du Dockerfile qui crée une couche
- [ ]   Un container en cours d'exécution
- [ ]   Une sauvegarde automatique

<br>

</small>

---

<small>

### 10. Comment Docker s'intègre-t-il dans la philosophie DevOps ?

<br>

- [ ]   Il remplace complètement les pratiques DevOps
- [ ]   Il facilite l'intégration continue et la livraison continue
- [ ]   Il est uniquement destiné aux développeurs
- [ ]   Il n'a aucun rapport avec DevOps

<br>

</small>

---

<small>

### 11. Un développeur dit : "Mon application fonctionne sur ma machine mais pas en production". Comment Docker peut-il résoudre ce problème ?

<br>

- [ ]   Docker ne peut pas résoudre ce type de problème
- [ ]   En standardisant l'environnement d'exécution avec des containers
- [ ]   En installant automatiquement les bonnes versions
- [ ]   En accélérant l'application

<br>

### 12. Pourquoi Docker est-il particulièrement adapté aux architectures microservices ?

<br>

- [ ]   Il rend les applications plus rapides
- [ ]   Il permet d'isoler, déployer et mettre à l'échelle chaque service indépendamment
- [ ]   Il supprime le besoin de bases de données
- [ ]   Il automatise le code

</small>

---

# Réponses

<small>

1. Une plateforme de conteneurisation pour isoler et déployer des applications
2. Client, Daemon, Registry
3. Une image est en lecture seule, un container est l'instance exécutable
4. Amélioration automatique des performances
5. Docker partage le kernel de l'hôte, les VMs ont leur propre OS
6. Le registry public officiel pour les images Docker
7. Lance un container Ubuntu interactif avec terminal
8. Utiliser des volumes Docker
9. Une instruction du Dockerfile qui crée une couche
10. Il facilite l'intégration continue et la livraison continue
11. En standardisant l'environnement d'exécution avec des containers
12. Il permet d'isoler, déployer et mettre à l'échelle chaque service indépendamment

</small>

---

# 📊 Barème et Correction

### Réponses Correctes

**Questions 1-6 (Fondamentaux)**
1. **B** - Docker est une plateforme de conteneurisation
2. **C** - Client, Daemon, Registry
3. **A** - Image = template, Container = instance

---

# Suite des réponses 📝

4. **C** - Docker n'améliore pas automatiquement les performances
5. **A** - Docker partage le kernel, VMs ont leur OS
6. **B** - Docker Hub est le registry public officiel

---

# Réponses pratiques 🔧

**Questions 7-10 (Pratique)**
7. **C** - Lance un container interactif
8. **B** - Utiliser des volumes Docker
9. **B** - Une instruction Dockerfile = une layer
10. **B** - Facilite CI/CD

---

# Score d'évaluation 📊

### Score d'évaluation
- **12-13 bonnes réponses** : 🏆 Expert Docker !
- **10-11 bonnes réponses** : 🥇 Très bon niveau
- **8-9 bonnes réponses** : 🥈 Bon niveau, quelques révisions
- **6-7 bonnes réponses** : 🥉 Niveau correct, approfondissez
- **< 6 bonnes réponses** : 📚 Reprenez les concepts de base

---

# 💡 Explications Détaillées

### Pourquoi ces réponses ?

**Question 3** - La distinction image/container est fondamentale :
- **Image** : Template en lecture seule (comme une classe en programmation)
- **Container** : Instance exécutable (comme un objet)

---

# Suite explications 💡

**Question 5** - Architecture différente :
- **Docker** : Containers partagent le kernel de l'hôte
- **VMs** : Chaque VM a son propre système d'exploitation

**Question 8** - Persistance cruciale :
- Par défaut, les données dans un container sont éphémères
- Les volumes permettent la persistance au-delà du cycle de vie du container

---

# Points à retenir ✅

### Points à retenir
✅ Docker révolutionne le déploiement d'applications  
✅ La conteneurisation != virtualisation  
✅ Les volumes sont essentiels pour la persistance  
✅ Docker facilite DevOps et microservices  

---

# 🚀 Suite du parcours

Une fois ce QCM maîtrisé, vous êtes prêt(e) pour :
- **Dockerfiles** : Créer vos propres images
- **Docker CLI** : Maîtriser la ligne de commande  
- **Réseaux Docker** : Faire communiquer vos containers
- **Orchestration** : Docker Compose et Kubernetes

**Conseil** : Si vous avez des difficultés, reprenez la partie théorique et refaites les exercices pratiques ! 