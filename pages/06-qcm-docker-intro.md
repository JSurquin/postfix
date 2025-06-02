---
layout: new-section
---

# QCM : Introduction à Docker

---
routeAlias: 'qcm-docker-intro'
---

<a name="QCM_DOCKER_INTRO" id="QCM_DOCKER_INTRO"></a>   

# 📝 QCM : Introduction à Docker

### Testez vos connaissances acquises !

Ce QCM vous permet de valider votre compréhension des concepts fondamentaux de Docker. Prenez le temps de réfléchir à chaque question.

---

# Question 1 : Définition de Docker 🤔

**Qu'est-ce que Docker principalement ?**

A) Un langage de programmation pour créer des applications  
B) Une plateforme de conteneurisation pour isoler et déployer des applications  
C) Un système d'exploitation léger pour serveurs  
D) Un outil de versioning comme Git  

---

# Question 2 : Architecture Docker 🏗️

**Quels sont les trois composants principaux de l'architecture Docker ?**

A) Image, Container, Registry  
B) Client, Daemon, Hub  
C) Client, Daemon, Registry  
D) Image, Volume, Network  

---

# Question 3 : Images vs Containers 📦

**Quelle est la différence principale entre une image et un container Docker ?**

A) Une image est en lecture seule, un container est l'instance exécutable  
B) Une image est temporaire, un container est permanent  
C) Une image est locale, un container est distant  
D) Aucune différence, ce sont des synonymes  

---

# Question 4 : Avantages de Docker 💡

**Quel n'est PAS un avantage de Docker ?**

A) Isolation des applications  
B) Portabilité entre environnements  
C) Amélioration automatique des performances  
D) Facilité de déploiement  

---

# Question 5 : Docker vs Machines Virtuelles ⚖️

**Quelle est la principale différence entre Docker et les VMs ?**

A) Docker partage le kernel de l'hôte, les VMs ont leur propre OS  
B) Docker est plus lourd que les VMs  
C) Les VMs sont plus sécurisées par défaut  
D) Docker ne peut pas fonctionner sur Windows  

---

# Question 6 : Docker Hub 🌐

**Qu'est-ce que Docker Hub ?**

A) L'interface graphique de Docker  
B) Le registry public officiel pour les images Docker  
C) L'outil de monitoring de Docker  
D) Le système de fichiers de Docker  

---

# Question 7 : Commande Docker Run 🚀

**Que fait la commande `docker run -it ubuntu bash` ?**

A) Lance un container Ubuntu en arrière-plan  
B) Télécharge l'image Ubuntu sans la lancer  
C) Lance un container Ubuntu interactif avec terminal  
D) Supprime un container Ubuntu existant  

---

# Question 8 : Persistance des données 💾

**Comment conserver des données après la suppression d'un container ?**

A) Les données sont automatiquement sauvegardées  
B) Utiliser des volumes Docker  
C) Redémarrer le container  
D) Impossible, les données sont toujours perdues  

---

# Question 9 : Layers Docker 🍰

**Qu'est-ce qu'une "layer" dans une image Docker ?**

A) Un fichier de configuration  
B) Une instruction du Dockerfile qui crée une couche  
C) Un container en cours d'exécution  
D) Une sauvegarde automatique  

---

# Question 10 : Philosophie DevOps 🔄

**Comment Docker s'intègre-t-il dans la philosophie DevOps ?**

A) Il remplace complètement les pratiques DevOps  
B) Il facilite l'intégration continue et la livraison continue  
C) Il est uniquement destiné aux développeurs  
D) Il n'a aucun rapport avec DevOps  

---

# 🎯 Scénario Pratique

### Question 11 : Diagnostic d'erreur 🔧

**Un développeur vous dit : "Mon application fonctionne sur ma machine mais pas en production". Comment Docker peut-il résoudre ce problème ?**

A) Docker ne peut pas résoudre ce type de problème  
B) En standardisant l'environnement d'exécution avec des containers  
C) En installant automatiquement les bonnes versions  
D) En accélérant l'application  

---

# Question 12 : Architecture microservices 🏗️

**Pourquoi Docker est-il particulièrement adapté aux architectures microservices ?**

A) Il rend les applications plus rapides  
B) Il permet d'isoler, déployer et mettre à l'échelle chaque service indépendamment  
C) Il supprime le besoin de bases de données  
D) Il automatise le code  

---

# 🏆 Question Bonus : Écosystème Docker

**Quels outils complètent l'écosystème Docker pour la production ?**

A) Docker Compose, Docker Swarm, Kubernetes  
B) Git, Jenkins, Maven  
C) Apache, MySQL, PHP  
D) Visual Studio, IntelliJ, Eclipse  

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

# Réponses avancées 🚀

**Questions 11-13 (Avancé)**
11. **B** - Standardisation de l'environnement
12. **B** - Isolation et indépendance des services
13. **A** - Docker Compose, Swarm, Kubernetes

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