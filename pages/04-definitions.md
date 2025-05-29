---
routeAlias: 'des-definitions-avant-tout'
---

<a name="DEFINITIONS" id="DEFINITIONS"></a>

# Vocabulaire Docker Essentiel 📚

### Les concepts de base à maîtriser

Avant de plonger dans la pratique, il est crucial de comprendre le vocabulaire Docker. Ces termes reviendront constamment dans votre apprentissage et votre utilisation quotidienne de Docker.

---

# Définitions fondamentales 📝

### Définitions fondamentales

- **Container** : Un environnement d'exécution isolé et portable qui contient tout ce dont une application a besoin pour fonctionner (code, runtime, outils système, bibliothèques)

- **Image** : Un modèle en lecture seule qui sert de blueprint pour créer des containers. C'est un snapshot figé d'un système de fichiers avec toutes les dépendances

---

# Dockerfile 📄

### Dockerfile

- **Dockerfile** : Un fichier texte contenant une série d'instructions pour construire automatiquement une image Docker personnalisée

---

# Écosystème Docker 🐳

### Services et registres

- **Docker Hub** : Le registre public officiel où sont stockées et partagées des millions d'images Docker prêtes à l'emploi

- **Docker Registry** : Un service de stockage et de distribution d'images Docker, peut être privé ou public

- **Docker Compose** : Un outil de définition et d'exécution d'applications multi-containers via des fichiers YAML déclaratifs

---

# Nouveautés 2025 ✨

### Nouveautés 2025

- **Docker Scout** : Analyse de sécurité et de vulnérabilités intégrée
- **Docker Build Cloud** : Service de build dans le cloud pour des performances optimales
- **Docker AI Agent** : Assistant IA pour l'optimisation des containers

---

# Architecture Docker 🏗️

### Composants principaux

- **Docker Engine** : Le cœur de Docker qui gère le cycle de vie des containers (création, exécution, arrêt, suppression)

- **Docker Daemon (dockerd)** : Service système qui s'exécute en arrière-plan et gère les objets Docker

- **Docker CLI** : L'interface en ligne de commande qui permet d'interagir avec le Docker Daemon via des commandes

---

# Interfaces utilisateur 🖥️

### Interfaces utilisateur

- **Docker Desktop** : Application graphique complète pour Windows et macOS avec interface visuelle intuitive

- **Docker Extension** : Plugins pour étendre les fonctionnalités de Docker Desktop

- **Portainer** : Interface web pour la gestion des containers en production

---

# Concepts avancés 🚀

### Réseautage et stockage

- **Docker Network** : Réseau virtuel permettant la communication sécurisée entre containers

- **Docker Volume** : Mécanisme de persistance des données qui survit au cycle de vie des containers

- **Docker Secret** : Gestion sécurisée des informations sensibles (mots de passe, clés API, certificats)

---

# Orchestration et déploiement 🎭

### Orchestration et déploiement

- **Docker Swarm** : Solution d'orchestration native pour gérer des clusters de containers

- **Kubernetes** : Plateforme d'orchestration avancée pour le déploiement et la gestion de containers à grande échelle

- **Docker Stack** : Déploiement d'applications multi-services dans un cluster Swarm

---

# Principes fondamentaux 💡

### Isolation et sécurité

- **Isolation** : Chaque container s'exécute dans son propre environnement isolé, séparé des autres containers et du système hôte

- **Namespaces** : Mécanisme Linux qui isole les ressources système (PID, réseau, système de fichiers)

- **Cgroups** : Limitation et contrôle des ressources système (CPU, mémoire, I/O) allouées aux containers

---

# Portabilité et reproductibilité 🔄

### Portabilité et reproductibilité

- **Portabilité** : Les containers s'exécutent de manière identique sur tous les environnements supportant Docker (développement, test, production)

- **Immutabilité** : Les images Docker sont immuables, garantissant la reproductibilité des déploiements

- **Infrastructure as Code** : La configuration de l'infrastructure est définie dans du code versionnable et reproductible

---

# Philosophie Container-First 🎯

### Stateless par défaut

Les containers Docker suivent le principe **stateless** :

- **Données éphémères** : Le container peut être détruit et recréé sans perte de fonctionnalité
- **État externalisé** : Les données persistantes sont stockées dans des volumes ou bases de données externes
- **Configuration externalisée** : Variables d'environnement et fichiers de configuration montés depuis l'extérieur

---

# Avantages pratiques ✅

### Avantages pratiques

- **Scalabilité horizontale** : Multiplication facile des instances
- **Mise à jour sans interruption** : Remplacement transparent des containers
- **Récupération rapide** : Redémarrage instantané en cas de problème
- **Testing simplifié** : Environnements de test identiques à la production 