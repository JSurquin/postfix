---
routeAlias: 'introduction-ansible'
---

# Introduction à Ansible

<img class="mt-20 mx-auto" src="https://www.ansible.com/hubfs/2016_Images/Assets/Ansible-Mark-Large-RGB_Pool.png" alt="Ansible Logo" width="300"/>

---

# Qu'est-ce qu'Ansible ?

**"Imaginez pouvoir gérer des centaines de serveurs avec une seule commande..."** C'est la promesse d'Ansible !

Ansible est un outil d'automatisation open-source qui permet de gérer la configuration, le déploiement et l'orchestration d'applications.

---

# Historique d'Ansible

- Créé en 2012 par Michael DeHaan
- Racheté par Red Hat en 2015
- Plus de 50 000 contributeurs sur GitHub
- Utilisé par plus de 2000 entreprises

---

# Plan du cours (1/2)

**"Un voyage progressif dans l'automatisation avec Ansible"**

## 1. Introduction et concepts de base
   - Qu'est-ce qu'Ansible ?
   - Pourquoi utiliser Ansible ?
   - Les concepts fondamentaux
   - **Exercice : Installation d'une machine virtuelle**

## 2. Installation et configuration
   - Installation sur Ubuntu
   - Installation sur CentOS
   - Premières commandes CLI
   - Configuration de l'environnement
   - **Exercice : Configuration d'un environnement de test**

---

# Plan du cours (2/2)

## 3. Fondamentaux
   - Configuration de l'inventaire
   - Test de l'inventaire
   - Création de playbooks
   - Variables dans les playbooks
   - **Exercice : Déploiement d'un serveur web simple**

## 4. Modules et fonctionnalités
   - Modules Ansible
   - Exemples de modules (apt, service, copy)
   - Handlers
   - Tags
   - **Exercice : Automatisation d'une stack LAMP**

---

# Plan du cours (3/3)

## 5. Concepts avancés
   - Rôles Ansible
   - Templates Jinja2
   - Ansible Vault
   - Inventaires dynamiques
   - **Exercice : Déploiement d'une application Node.js**

## 6. Tests et bonnes pratiques
   - Tests avec Molecule
   - Intégration continue
   - Bonnes pratiques
   - Checklist de sécurité
   - **Exercice : Mise en place d'un pipeline CI/CD**

## 7. Ressources
   - Documentation
   - Communauté
   - Formation

---

# Parallèle Ansible & Docker (1/2)

**"De la théorie à la pratique : un parcours progressif"**

| Ansible | Docker | Objectif |
|---------|--------|----------|
| Machine virtuelle | Container | Environnement isolé |
| Inventaire | Docker Compose | Orchestration |
| Playbook | Dockerfile | Configuration |
| Rôles | Images | Réutilisabilité |

---

# Parallèle Ansible & Docker (2/2)

| Ansible | Docker | Objectif |
|---------|--------|----------|
| Variables | Variables d'environnement | Configuration dynamique |
| Templates | .env files | Personnalisation |
| Vault | Docker Secrets | Sécurité |

---

# Progression des exercices (1/2)

1. **Environnement de base**
   - VM Ubuntu (Ansible)
   - Container Ubuntu (Docker)
   - Objectif : Préparer l'environnement

2. **Premiers pas**
   - Configuration SSH (Ansible)
   - Docker daemon (Docker)
   - Objectif : Communication sécurisée

3. **Déploiement simple**
   - Serveur web Nginx (Ansible)
   - Container Nginx (Docker)
   - Objectif : Premier déploiement

---

# Progression des exercices (2/2)

4. **Stack complète**
   - LAMP (Ansible)
   - Multi-container (Docker)
   - Objectin : Architecture complexe

5. **Application réelle**
   - Node.js + MongoDB (Ansible)
   - Microservices (Docker)
   - Objectif : Application moderne

6. **Automatisation**
   - CI/CD (Ansible)
   - Docker Hub / Registry
   - Objectif : Pipeline complet

---

# Objectifs pédagogiques

1. **Comprendre les similitudes**
   - Configuration vs Containerisation
   - Orchestration vs Composition
   - Sécurité vs Isolation

2. **Maîtriser les différences**
   - Agentless vs Daemon
   - YAML vs Dockerfile
   - Inventaire vs Compose

3. **Savoir choisir**
   - Quand utiliser Ansible ?
   - Quand utiliser Docker ?
   - Comment les combiner ?

---

# Pourquoi utiliser Ansible ?

**"Pourquoi se fatiguer à faire manuellement ce qu'une machine peut faire pour vous ?"**

## Avantages clés

- **Agentless** : Pas besoin d'installer d'agent sur les machines cibles
- **SSH** : Utilise SSH pour la communication
- **YAML** : Configuration en YAML, facile à lire et écrire
- **Idempotent** : Les mêmes commandes peuvent être exécutées plusieurs fois sans effet secondaire

---

# Avantages d'Ansible (suite)

- **Modulaire** : Grande bibliothèque de modules
- **Communauté active** : Support et contributions régulières

---

# Exercice 1 : Installation d'une machine virtuelle

**"Premiers pas : comprendre l'environnement de travail"**

## Contexte
Dans cet exercice, nous allons simuler un environnement de production en créant une machine virtuelle (VM) qui servira de serveur cible pour nos déploiements Ansible. Cette VM représentera un serveur distant sur lequel nous allons déployer nos applications.

## Pourquoi une VM ?
- Simuler un environnement de production
- Isoler notre environnement de test
- Pratiquer sans risque sur un serveur réel
- Comprendre les concepts de déploiement distant

---

# Exercice 1 : Objectifs détaillés

## Objectif principal
- Créer un environnement de test sécurisé pour Ansible
- Comprendre la communication entre la machine de contrôle (votre PC) et le serveur cible (la VM)

## Pourquoi SSH ?
- Ansible utilise SSH pour communiquer avec les serveurs
- C'est un protocole sécurisé et standard
- Permet l'automatisation sans intervention manuelle
- Évite d'avoir à installer un agent sur le serveur cible

---

# Exercice 1 : Préparation de la VM (1/2)

## Étape 1 : Création de la VM
```bash
# Télécharger Ubuntu Server 22.04 LTS
# Lien : https://ubuntu.com/download/server

# Configuration recommandée de la VM :
# - 2 CPU
# - 4GB RAM
# - 20GB disque
# - Interface réseau en mode bridge (pour avoir une IP dans votre réseau local)
```

## Étape 2 : Installation d'Ubuntu Server
- Suivre l'installation standard
- Noter l'IP attribuée à la VM
- Créer un utilisateur avec des droits sudo

---

# Exercice 1 : Préparation de la VM (2/2)

## Étape 3 : Configuration réseau
```bash
# Éditer le fichier de configuration réseau
sudo nano /etc/netplan/00-installer-config.yaml

# Configuration type pour une IP statique :
network:
  ethernets:
    ens33:  # Nom de l'interface (à adapter)
      dhcp4: no
      addresses: [192.168.1.100/24]  # IP de la VM
      gateway4: 192.168.1.1  # IP de votre routeur
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]  # DNS Google
```

## Étape 4 : Application de la configuration
```bash
# Appliquer la configuration réseau
sudo netplan apply

# Vérifier l'IP
ip addr show
```

---

# Exercice 1 : Configuration SSH (1/2)

## Pourquoi configurer SSH ?
- Ansible utilise SSH pour se connecter aux serveurs
- Les clés SSH permettent une authentification sécurisée sans mot de passe
- C'est plus sûr que l'authentification par mot de passe
- Permet l'automatisation des déploiements

## Installation de SSH sur la VM
```bash
# Mise à jour des paquets
sudo apt update

# Installation du serveur SSH
sudo apt install -y openssh-server

# Vérification que le service est actif
sudo systemctl status ssh
```

---

# Exercice 1 : Configuration SSH (2/2)

## Génération des clés SSH
```bash
# Sur votre machine locale (pas la VM)
# Générer une paire de clés RSA
ssh-keygen -t rsa -b 4096
# -t : type de clé (RSA)
# -b : taille de la clé (4096 bits pour plus de sécurité)

# Copier la clé publique vers la VM
ssh-copy-id utilisateur@ip_de_la_vm
# Remplacez 'utilisateur' et 'ip_de_la_vm' par vos valeurs

# Tester la connexion
ssh utilisateur@ip_de_la_vm
# Si tout est configuré correctement, vous devriez vous connecter sans mot de passe
```

---

# Exercice 1 : Vérification finale

## Test de la configuration
```bash
# Sur votre machine locale
# Tester la connexion SSH
ssh utilisateur@ip_de_la_vm

# Vérifier que vous pouvez exécuter des commandes sudo
ssh utilisateur@ip_de_la_vm 'sudo whoami'
# Devrait afficher 'root'
```

## Pourquoi ces vérifications ?
- S'assurer que la communication SSH fonctionne
- Vérifier que Ansible pourra exécuter des commandes avec les droits nécessaires
- Confirmer que l'automatisation sera possible

---

# Exercice 1 : Prochaines étapes

## Ce que nous avons accompli
- Une VM Ubuntu Server fonctionnelle
- Une configuration réseau stable
- Une authentification SSH sécurisée
- Un environnement prêt pour Ansible

## Ce qui nous attend
- Installation d'Ansible
- Configuration de l'inventaire
- Premier playbook
- Déploiement d'applications

---

# Exercice 2 : Installation et configuration d'Ansible

**"Mise en place de l'outil d'automatisation"**

## Contexte
Maintenant que nous avons notre serveur cible (la VM), nous allons installer et configurer Ansible sur notre machine locale. Ansible sera notre outil principal pour automatiser les déploiements et la configuration des serveurs.

## Pourquoi installer Ansible sur notre machine locale ?
- Ansible fonctionne en mode "push" : il envoie les commandes depuis notre machine
- Pas besoin d'installer d'agent sur les serveurs cibles
- Plus simple à maintenir et à mettre à jour
- Meilleure sécurité (pas de service à écouter sur les serveurs)

---

# Exercice 2 : Installation d'Ansible (1/2)

## Étape 1 : Installation des dépendances
```bash
# Mise à jour du système
sudo apt update

# Installation des dépendances nécessaires
sudo apt install -y software-properties-common

# Ajout du dépôt Ansible
sudo apt-add-repository --yes --update ppa:ansible/ansible

# Installation d'Ansible
sudo apt install -y ansible
```

## Vérification de l'installation
```bash
# Vérifier la version installée
ansible --version
# Devrait afficher la version d'Ansible (ex: 2.9.6)
```

---

# Exercice 2 : Installation d'Ansible (2/2)

## Configuration de base
```bash
# Création du répertoire pour nos playbooks
mkdir -p ~/ansible

# Création du fichier de configuration global
sudo nano /etc/ansible/ansible.cfg

# Configuration recommandée :
[defaults]
inventory = ~/ansible/hosts
remote_user = ubuntu  # Utilisateur par défaut pour les connexions
host_key_checking = False  # Désactive la vérification des clés hôtes (en développement)
```

## Pourquoi ces paramètres ?
- `inventory` : définit où Ansible trouvera la liste des serveurs
- `remote_user` : utilisateur par défaut pour les connexions SSH
- `host_key_checking` : simplifie le développement (à désactiver en production)

---

# Exercice 2 : Configuration de l'inventaire (1/2)

## Qu'est-ce qu'un inventaire ?
- Liste des serveurs à gérer
- Organisation en groupes logiques
- Définition des variables par serveur/groupe
- Point central de la configuration Ansible

## Création de l'inventaire
```bash
# Création du fichier d'inventaire
nano ~/ansible/hosts

# Structure de base :
[webservers]
# Groupe des serveurs web
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[databases]
# Groupe des serveurs de base de données
db1 ansible_host=192.168.1.20
```

---

# Exercice 2 : Configuration de l'inventaire (2/2)

## Variables globales
```ini
# Ajout des variables communes à tous les serveurs
[all:vars]
# Utilisateur pour les connexions SSH
ansible_user=ubuntu

# Chemin vers la clé privée SSH
ansible_ssh_private_key_file=~/.ssh/id_rsa

# Délai de connexion (en secondes)
ansible_ssh_timeout=30

# Nombre de tentatives de connexion
ansible_connection_retries=3
```

## Pourquoi ces variables ?
- `ansible_user` : utilisateur pour les connexions SSH
- `ansible_ssh_private_key_file` : chemin vers la clé privée pour l'authentification
- `ansible_ssh_timeout` : évite les blocages en cas de problème réseau
- `ansible_connection_retries` : permet de gérer les problèmes de connexion temporaires

---

# Exercice 2 : Test de connexion

## Vérification de la configuration
```bash
# Tester la connexion avec tous les serveurs
ansible all -m ping

# Résultat attendu :
# web1 | SUCCESS => {
#     "changed": false,
#     "ping": "pong"
# }

# Exécuter une commande simple sur tous les serveurs
ansible all -m command -a "uname -a"
```

## Pourquoi ces tests ?
- `ping` : vérifie que la connexion SSH fonctionne
- `command` : teste l'exécution de commandes à distance
- Ces tests confirment que notre configuration est correcte

---

# Exercice 2 : Bonnes pratiques

## Organisation des fichiers
```
~/ansible/
├── hosts              # Inventaire des serveurs
├── group_vars/        # Variables par groupe
│   ├── all.yml        # Variables communes
│   ├── webservers.yml # Variables spécifiques aux webservers
│   └── databases.yml  # Variables spécifiques aux databases
├── host_vars/         # Variables par serveur
│   ├── web1.yml
│   └── db1.yml
└── ansible.cfg        # Configuration locale (optionnel)
```

## Pourquoi cette structure ?
- Séparation claire des configurations
- Facilité de maintenance
- Réutilisation des variables
- Meilleure organisation du code

---

# Exercice 2 : Prochaines étapes

## Ce que nous avons accompli
- Installation d'Ansible
- Configuration de base
- Création de l'inventaire
- Tests de connexion

## Ce qui nous attend
- Création de notre premier playbook
- Déploiement d'une application simple
- Gestion des variables
- Utilisation des modules Ansible

---

# Exercice 3 : Premier playbook

**"Création de notre première automatisation"**

## Contexte
Maintenant que nous avons configuré notre environnement Ansible, nous allons créer notre premier playbook. Un playbook est un fichier YAML qui décrit les tâches à exécuter sur nos serveurs. Dans cet exercice, nous allons installer et configurer un serveur web Nginx.

## Pourquoi commencer par Nginx ?
- C'est un serveur web léger et performant
- Configuration simple et claire
- Bon point de départ pour comprendre les playbooks
- Utile pour la plupart des déploiements web

---

# Exercice 3 : Structure du playbook (1/2)

## Qu'est-ce qu'un playbook ?
- Fichier YAML décrivant l'automatisation
- Séquence de tâches à exécuter
- Configuration des serveurs
- Gestion des dépendances

## Création du playbook
```yaml
# nginx.yml
---
# Définition du playbook
- name: Installation et configuration de Nginx
  # Serveurs cibles (groupe webservers)
  hosts: webservers
  # Exécution en mode sudo
  become: yes
  # Variables utilisées dans le playbook
  vars:
    nginx_user: www-data
    nginx_group: www-data
```

---

# Exercice 3 : Structure du playbook (2/2)

## Tâches du playbook
```yaml
  tasks:
    # Installation de Nginx
    - name: Installation du paquet Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes
      notify: restart nginx

    # Configuration du service
    - name: Démarrage et activation de Nginx
      service:
        name: nginx
        state: started
        enabled: yes

    # Configuration du site par défaut
    - name: Configuration de la page d'accueil
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
        owner: "{{ nginx_user }}"
        group: "{{ nginx_group }}"
        mode: '0644'
```

---

# Exercice 3 : Handlers et templates (1/2)

## Qu'est-ce qu'un handler ?
- Tâche exécutée uniquement si notifiée
- Souvent utilisé pour redémarrer les services
- Évite les redémarrages inutiles
- Meilleure gestion des dépendances

## Configuration des handlers
```yaml
  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

---

# Exercice 3 : Handlers et templates (2/2)

## Création du template
```html
# templates/index.html.j2
<!DOCTYPE html>
<html>
<head>
    <title>Serveur Nginx - {{ ansible_hostname }}</title>
</head>
<body>
    <h1>Bienvenue sur {{ ansible_hostname }}</h1>
    <p>Ce serveur a été configuré avec Ansible</p>
    <p>Date de déploiement : {{ ansible_date_time.date }}</p>
</body>
</html>
```

## Pourquoi utiliser des templates ?
- Personnalisation dynamique
- Réutilisation du code
- Gestion des variables
- Séparation de la configuration et du contenu

---

# Exercice 3 : Exécution du playbook

## Préparation
```bash
# Création du répertoire des templates
mkdir -p templates

# Création du template
nano templates/index.html.j2

# Vérification de la syntaxe
ansible-playbook nginx.yml --syntax-check
```

## Exécution
```bash
# Exécution du playbook
ansible-playbook nginx.yml

# Exécution avec plus de détails
ansible-playbook nginx.yml -v

# Exécution avec vérification (dry-run)
ansible-playbook nginx.yml --check
```

---

# Exercice 3 : Vérification

## Test du serveur web
```bash
# Vérification du service
ansible webservers -m service -a "name=nginx state=started"

# Test de la page web
curl http://web1
```

## Résultats attendus
- Service Nginx actif
- Page web accessible
- Configuration correcte
- Logs sans erreur

---

# Exercice 3 : Bonnes pratiques

## Organisation du code
- Séparation des tâches
- Utilisation des handlers
- Templates bien structurés
- Variables clairement définies

## Sécurité
- Permissions appropriées
- Utilisation de become
- Gestion des secrets
- Audit des changements

---

# Exercice 3 : Prochaines étapes

## Ce que nous avons accompli
- Création d'un playbook complet
- Installation de Nginx
- Configuration du service
- Gestion des templates

## Ce qui nous attend
- Création de rôles
- Gestion des variables
- Tests automatisés
- Déploiement d'applications

---

# Exercice 4 : Création de rôles Ansible

**"Organisation et réutilisation du code"**

## Contexte
Après avoir créé notre premier playbook, nous allons maintenant apprendre à organiser notre code en rôles. Les rôles permettent de structurer notre automatisation de manière modulaire et réutilisable.

## Pourquoi utiliser des rôles ?
- Meilleure organisation du code
- Réutilisation entre projets
- Partage avec la communauté
- Maintenance simplifiée
- Tests plus faciles

---

# Exercice 4 : Structure d'un rôle (1/2)

## Qu'est-ce qu'un rôle ?
- Collection de tâches, handlers, templates
- Structure standardisée
- Variables et dépendances
- Documentation intégrée

## Structure standard
```
roles/
  mon_role/
    tasks/          # Tâches principales
    handlers/       # Handlers
    templates/      # Templates Jinja2
    files/          # Fichiers statiques
    vars/           # Variables spécifiques
    defaults/       # Variables par défaut
    meta/           # Métadonnées
    tests/          # Tests
```

---

# Exercice 4 : Structure d'un rôle (2/2)

## Création d'un rôle
```bash
# Création de la structure
ansible-galaxy init mon_role

# Structure générée :
roles/mon_role/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── tests/
│   ├── inventory
│   └── test.yml
└── vars/
    └── main.yml
```

---

# Exercice 4 : Configuration du rôle (1/2)

## Variables par défaut
```yaml
# roles/mon_role/defaults/main.yml
---
# Variables par défaut (peuvent être écrasées)
app_name: mon_app
app_user: appuser
app_group: appgroup
app_port: 8080
```

## Variables spécifiques
```yaml
# roles/mon_role/vars/main.yml
---
# Variables spécifiques au rôle
app_version: "1.0.0"
app_dependencies:
  - python3
  - python3-pip
  - git
```

---

# Exercice 4 : Configuration du rôle (2/2)

## Tâches principales
```yaml
# roles/mon_role/tasks/main.yml
---
# Installation des dépendances
- name: Install dependencies
  apt:
    name: "{{ item }}"
    state: present
  with_items: "{{ app_dependencies }}"

# Configuration de l'application
- name: Configure application
  template:
    src: app.conf.j2
    dest: /etc/{{ app_name }}/app.conf
  notify: restart app
```

## Handlers
```yaml
# roles/mon_role/handlers/main.yml
---
- name: restart app
  service:
    name: "{{ app_name }}"
    state: restarted
```

---

# Exercice 4 : Utilisation du rôle

## Dans un playbook
```yaml
# site.yml
---
- name: Déploiement de l'application
  hosts: appservers
  become: yes
  roles:
    - mon_role
  vars:
    app_name: mon_app_prod
    app_port: 80
```

## Avec des tags
```yaml
# site.yml (suite)
  roles:
    - role: mon_role
      tags:
        - app
        - configuration
```

---

# Exercice 4 : Bonnes pratiques

## Organisation
- Un rôle = une fonctionnalité
- Documentation claire
- Tests automatisés
- Variables bien définies

## Sécurité
- Validation des entrées
- Gestion des permissions
- Audit des changements
- Backup des configurations

---

# Exercice 4 : Tests et validation

## Tests avec Molecule
```bash
# Installation
pip install molecule docker

# Création d'un scénario de test
molecule init scenario -r mon_role

# Exécution des tests
molecule test
```

## Structure des tests
```
molecule/
  default/
    molecule.yml    # Configuration
    converge.yml    # Playbook de test
    verify.yml      # Vérifications
```

---

# Exercice 4 : Partage du rôle

## Publication sur Ansible Galaxy
```bash
# Création du fichier meta/main.yml
---
galaxy_info:
  author: votre_nom
  description: Description du rôle
  license: MIT
  min_ansible_version: 2.9
  platforms:
    - name: Ubuntu
      versions:
        - bionic
        - focal

# Publication
ansible-galaxy role import
```

---

# Exercice 4 : Prochaines étapes

## Ce que nous avons accompli
- Création d'un rôle complet
- Organisation du code
- Tests automatisés
- Documentation

## Ce qui nous attend
- Déploiement d'applications
- Gestion des secrets
- Intégration continue
- Monitoring

---

# Templates et variables (1/2)

## Templates Jinja2
- Syntaxe de template
- Variables dans les templates
- Conditions et boucles
- Filtres

## Exemple de template

```yaml
# templates/apache.conf.j2
ServerName {{ server_name }}
DocumentRoot {{ doc_root }}
<Directory {{ doc_root }}>
    Options {{ apache_options }}
</Directory>
```

---

# Templates et variables (2/2)

## Utilisation dans un playbook
```yaml
- name: Configuration d'Apache
  template:
    src: apache.conf.j2
    dest: /etc/apache2/sites-available/000-default.conf
  notify: restart apache
```

---

# Ansible Vault (1/2)

## Sécurisation des données sensibles
- Chiffrement des variables
- Gestion des secrets
- Intégration avec les playbooks

## Création d'un fichier chiffré
```bash
ansible-vault create secrets.yml
```

---

# Ansible Vault (2/2)

## Utilisation dans un playbook
```yaml
- name: Chargement des secrets
  include_vars: secrets.yml
  no_log: true

- name: Configuration avec secrets
  template:
    src: config.j2
    dest: /etc/app/config.conf
    vars:
      db_password: "{{ vault_db_password }}"
```

---

# Tests avec Molecule (1/2)

## Installation
```bash
pip install molecule molecule-docker
```

## Création d'un scénario de test
```bash
molecule init scenario -r mon_role
```

---

# Tests avec Molecule (2/2)

## Structure des tests
```
molecule/
  default/
    molecule.yml
    converge.yml
    verify.yml
    prepare.yml
```

## Exécution des tests
```bash
molecule test
```

---

# Intégration continue (1/2)

## GitHub Actions
```yaml
name: Ansible CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Molecule
        run: |
          pip install molecule molecule-docker
          molecule test
```

---

# Intégration continue (2/2)

## GitLab CI
```yaml
test:
  image: python:3.8
  script:
    - pip install molecule molecule-docker
    - molecule test
```

---

# Bonnes pratiques (1/2)

## Organisation du code
- Structure claire
- Documentation
- Versioning
- Tests

## Sécurité
- Utilisation d'Ansible Vault
- Gestion des permissions
- Audit des playbooks

---

# Bonnes pratiques (2/2)

## Performance
- Optimisation des playbooks
- Gestion des erreurs
- Logging
- Monitoring

## Maintenance
- Mises à jour régulières
- Documentation à jour
- Revue de code
- Backup des configurations

---

# Checklist de sécurité

1. **Inventaire**
   - [ ] Pas de mots de passe en clair
   - [ ] Accès SSH sécurisé
   - [ ] Inventaire à jour

2. **Playbooks**
   - [ ] Vault pour les secrets
   - [ ] Permissions minimales
   - [ ] Validation des entrées

3. **Rôles**
   - [ ] Documentation complète
   - [ ] Tests automatisés
   - [ ] Mises à jour régulières

---

# Ressources

## Documentation
- [Documentation officielle](https://docs.ansible.com)
- [Ansible Galaxy](https://galaxy.ansible.com)
- [GitHub Ansible](https://github.com/ansible/ansible)

## Communauté
- Forums Ansible
- Meetups
- Conférences

## Formation
- Cours en ligne
- Certifications
- Workshops 

---

# Exercice 5 : Déploiement d'une application Node.js

**"Un cas d'usage réel : application Node.js avec MongoDB"**

## Contexte
Dans cet exercice, nous allons déployer une application Node.js complète avec une base de données MongoDB. C'est un cas d'usage courant en production qui nous permettra de mettre en pratique tout ce que nous avons appris jusqu'à présent.

## Pourquoi Node.js et MongoDB ?
- Stack moderne et performante
- Très utilisée en production
- Bonne intégration avec Ansible
- Facile à déployer et maintenir

---

# Exercice 5 : Structure du projet (1/2)

## Architecture globale
```
project/
├── roles/
│   ├── nodejs/          # Configuration Node.js
│   │   ├── tasks/
│   │   ├── templates/
│   │   └── vars/
│   ├── mongodb/         # Configuration MongoDB
│   │   ├── tasks/
│   │   └── templates/
│   └── nginx/           # Configuration Nginx
│       ├── tasks/
│       └── templates/
├── group_vars/
│   └── all.yml          # Variables globales
└── site.yml             # Playbook principal
```

---

# Exercice 5 : Structure du projet (2/2)

## Application Node.js
```
app/
├── package.json         # Dépendances
├── server.js           # Point d'entrée
├── routes/             # Routes API
├── models/             # Modèles MongoDB
└── config/             # Configuration
    └── database.js     # Configuration DB
```

## Variables d'environnement
```bash
# .env
NODE_ENV=production
PORT=3000
MONGODB_URI=mongodb://localhost:27017/app
```

---

# Exercice 5 : Rôle Node.js (1/2)

## Installation de Node.js
```yaml
# roles/nodejs/tasks/main.yml
---
- name: Add NodeSource repository
  apt_repository:
    repo: deb https://deb.nodesource.com/node_18.x {{ ansible_distribution_release }} main
    state: present
    filename: nodesource

- name: Install Node.js
  apt:
    name: nodejs
    state: present
    update_cache: yes

- name: Install PM2 globally
  npm:
    name: pm2
    global: yes
```

---

# Exercice 5 : Rôle Node.js (2/2)

## Déploiement de l'application
```yaml
# roles/nodejs/tasks/main.yml (suite)
---
- name: Create app directory
  file:
    path: "{{ app_dir }}"
    state: directory
    owner: "{{ app_user }}"
    group: "{{ app_group }}"

- name: Copy application files
  copy:
    src: "{{ app_src }}"
    dest: "{{ app_dir }}"
    owner: "{{ app_user }}"
    group: "{{ app_group }}"

- name: Install dependencies
  npm:
    path: "{{ app_dir }}"
    state: present

- name: Configure PM2
  template:
    src: ecosystem.config.js.j2
    dest: "{{ app_dir }}/ecosystem.config.js"
  notify: restart app
```

---

# Exercice 5 : Rôle MongoDB (1/2)

## Installation de MongoDB
```yaml
# roles/mongodb/tasks/main.yml
---
- name: Add MongoDB repository
  apt_repository:
    repo: deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu {{ ansible_distribution_release }}/mongodb-org/4.4 multiverse
    state: present
    filename: mongodb-org-4.4

- name: Install MongoDB
  apt:
    name: mongodb-org
    state: present
    update_cache: yes

- name: Configure MongoDB
  template:
    src: mongod.conf.j2
    dest: /etc/mongod.conf
  notify: restart mongodb
```

---

# Exercice 5 : Rôle MongoDB (2/2)

## Configuration de la base de données
```yaml
# roles/mongodb/tasks/main.yml (suite)
---
- name: Start and enable MongoDB
  systemd:
    name: mongod
    state: started
    enabled: yes

- name: Create MongoDB user
  mongodb_user:
    login_host: localhost
    login_port: 27017
    login_user: admin
    login_password: "{{ mongodb_admin_password }}"
    database: "{{ app_database }}"
    name: "{{ app_db_user }}"
    password: "{{ app_db_password }}"
    state: present
    roles: "{{ app_db_roles }}"
```

---

# Exercice 5 : Rôle Nginx (1/2)

## Configuration du reverse proxy
```yaml
# roles/nginx/tasks/main.yml
---
- name: Install Nginx
  apt:
    name: nginx
    state: present

- name: Configure Nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/sites-available/{{ app_name }}
  notify: restart nginx

- name: Enable site
  file:
    src: /etc/nginx/sites-available/{{ app_name }}
    dest: /etc/nginx/sites-enabled/{{ app_name }}
    state: link
  notify: restart nginx
```

---

# Exercice 5 : Rôle Nginx (2/2)

## Configuration du site
```nginx
# roles/nginx/templates/nginx.conf.j2
server {
    listen 80;
    server_name {{ app_domain }};

    location / {
        proxy_pass http://localhost:{{ app_port }};
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

# Exercice 5 : Variables globales

## Configuration commune
```yaml
# group_vars/all.yml
---
# Application settings
app_name: myapp
app_user: nodejs
app_group: nodejs
app_dir: /opt/{{ app_name }}
app_port: 3000
app_domain: app.example.com

# Database settings
app_database: myappdb
app_db_user: myappuser
app_db_password: "{{ vault_app_db_password }}"
app_db_roles: "[{role: 'readWrite', db: '{{ app_database }}'}]"

# MongoDB settings
mongodb_admin_password: "{{ vault_mongodb_admin_password }}"
```

---

# Exercice 5 : Playbook principal

## Configuration du déploiement
```yaml
# site.yml
---
- name: Deploy Node.js application
  hosts: appservers
  become: yes
  vars_files:
    - group_vars/all.yml
  roles:
    - nodejs
    - mongodb
    - nginx
  handlers:
    - name: restart app
      systemd:
        name: "{{ app_name }}"
        state: restarted
    - name: restart mongodb
      systemd:
        name: mongod
        state: restarted
    - name: restart nginx
      systemd:
        name: nginx
        state: restarted
```

---

# Exercice 5 : Déploiement

## Processus de déploiement
```bash
# Création du fichier vault pour les secrets
ansible-vault create group_vars/vault.yml

# Déploiement de l'application
ansible-playbook -i inventory site.yml --ask-vault-pass

# Vérification du déploiement
curl http://app.example.com
```

## Vérifications
- Service Node.js actif
- MongoDB en cours d'exécution
- Nginx configuré
- Application accessible

---

# Exercice 5 : Bonnes pratiques

## Sécurité
- Utilisation d'Ansible Vault
- Permissions minimales
- Configuration sécurisée
- Audit des logs

## Monitoring
- Logs centralisés
- Alertes de performance
- Backup automatique
- Tests réguliers

---

# Exercice 5 : Prochaines étapes

## Ce que nous avons accompli
- Déploiement complet d'une application
- Configuration de la base de données
- Mise en place du reverse proxy
- Gestion des secrets

## Ce qui nous attend
- Pipeline CI/CD
- Tests automatisés
- Monitoring avancé
- Scaling horizontal

---

# Exercice 6 : Mise en place d'un pipeline CI/CD

**"Automatisation complète : du code au déploiement"**

## Contexte
Après avoir déployé notre application Node.js, nous allons mettre en place un pipeline CI/CD (Intégration Continue / Déploiement Continu) pour automatiser les tests, la validation et le déploiement de notre application.

## Pourquoi un pipeline CI/CD ?
- Automatisation des tests
- Déploiements cohérents
- Détection précoce des erreurs
- Livraison continue
- Qualité du code

---

# Exercice 6 : Configuration GitHub Actions (1/3)

## Structure du workflow
```yaml
# .github/workflows/ansible.yml
name: Ansible CI/CD
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.8'
```

## Pourquoi GitHub Actions ?
- Intégration native avec GitHub
- Configuration en YAML
- Large écosystème d'actions
- Exécution dans des conteneurs

---

# Exercice 6 : Configuration GitHub Actions (2/3)

## Installation et tests
```yaml
# .github/workflows/ansible.yml (suite)
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install ansible molecule docker

      - name: Run Molecule tests
        run: |
          cd roles/nodejs
          molecule test
          cd ../mongodb
          molecule test
          cd ../nginx
          molecule test
```

## Pourquoi ces tests ?
- Validation des rôles
- Tests d'intégration
- Vérification des configurations
- Détection des régressions

---

# Exercice 6 : Configuration GitHub Actions (3/3)

## Déploiement automatique
```yaml
# .github/workflows/ansible.yml (suite)
      - name: Deploy to staging
        if: github.ref == 'refs/heads/main'
        env:
          ANSIBLE_VAULT_PASSWORD: ${{ secrets.VAULT_PASSWORD }}
        run: |
          echo "$ANSIBLE_VAULT_PASSWORD" > .vault_pass
          ansible-playbook -i staging site.yml --vault-password-file .vault_pass
```

## Pourquoi ce déploiement ?
- Automatisation du processus
- Gestion des secrets
- Environnement de staging
- Validation en production

---

# Exercice 6 : Tests avec Molecule (1/2)

## Configuration des tests
```yaml
# molecule/default/molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: instance
    image: ubuntu:20.04
    privileged: true
provisioner:
  name: ansible
verifier:
  name: ansible
```

## Pourquoi Molecule ?
- Tests d'intégration
- Environnements isolés
- Validation des rôles
- Tests reproductibles

---

# Exercice 6 : Tests avec Molecule (2/2)

## Playbook de test
```yaml
# molecule/default/converge.yml
---
- name: Converge
  hosts: all
  tasks:
    - name: Include role
      include_role:
        name: "{{ lookup('env', 'MOLECULE_ROLE_NAME') }}"
```

## Vérifications
```yaml
# molecule/default/verify.yml
---
- name: Verify
  hosts: all
  tasks:
    - name: Check service status
      service:
        name: "{{ item }}"
        state: started
      with_items:
        - nginx
        - mongod
```

---

# Exercice 6 : Sécurité du pipeline

## Gestion des secrets
```bash
# Création des secrets dans GitHub
# Settings > Secrets > Actions
VAULT_PASSWORD: "motdepassecomplexe"
SSH_PRIVATE_KEY: |
  -----BEGIN RSA PRIVATE KEY-----
  ...
  -----END RSA PRIVATE KEY-----
```

## Pourquoi cette gestion ?
- Protection des données sensibles
- Rotation des secrets
- Audit des accès
- Conformité sécurité

---

# Exercice 6 : Monitoring du pipeline

## Configuration des notifications
```yaml
# .github/workflows/ansible.yml (suite)
notifications:
  email:
    recipients:
      - team@example.com
    on_success: change
    on_failure: always
```

## Pourquoi le monitoring ?
- Détection des échecs
- Temps de réponse
- Métriques de performance
- Historique des déploiements

---

# Exercice 6 : Bonnes pratiques

## Organisation
- Branches feature
- Pull requests
- Reviews de code
- Documentation

## Sécurité
- Secrets managés
- Permissions minimales
- Audit des actions
- Backup des configurations

---

# Exercice 6 : Prochaines étapes

## Ce que nous avons accompli
- Pipeline CI/CD complet
- Tests automatisés
- Déploiement automatique
- Monitoring

## Ce qui nous attend
- Scaling horizontal
- High availability
- Disaster recovery
- Performance monitoring

---

# Conclusion

**"Ansible : l'automatisation à portée de main"**

## Ce que nous avons appris
- Configuration d'environnements
- Création de playbooks
- Organisation en rôles
- Déploiement d'applications
- Pipeline CI/CD

## Points clés
- Infrastructure as Code
- Automatisation reproductible
- Sécurité et maintenabilité
- Tests automatisés
- Monitoring continu

---

# Ressources pour aller plus loin

## Documentation officielle
- [Documentation Ansible](https://docs.ansible.com)
  - Guides détaillés
  - Exemples de code
  - Bonnes pratiques
  - Mises à jour

- [Galaxy Ansible](https://galaxy.ansible.com)
  - Rôles partagés
  - Collections
  - Exemples concrets
  - Communauté active

---

# Formation continue

## Cours en ligne
- [Red Hat Training](https://www.redhat.com/fr/services/training)
  - Cours certifiés
  - Labs pratiques
  - Examens de certification
  - Support expert

- [Ansible pour les DevOps](https://www.ansiblefordevops.com)
  - Livres électroniques
  - Vidéos tutoriels
  - Exercices pratiques
  - Cas d'usage

---

# Communauté et support

## Forums et discussions
- [Forum Ansible](https://forum.ansible.com)
  - Questions techniques
  - Partage d'expérience
  - Annonces importantes
  - Contribuer au projet

- [GitHub Ansible](https://github.com/ansible/ansible)
  - Code source
  - Issues
  - Pull requests
  - Documentation

---

# Prochaines étapes

## Améliorations possibles
- High Availability
- Disaster Recovery
- Performance Monitoring
- Security Hardening

## Projets à explorer
- Kubernetes avec Ansible
- Cloud Automation
- Network Automation
- Security Automation

---

# Remerciements

## À retenir
- Pratique régulière
- Contribution à la communauté
- Formation continue
- Partage des connaissances

## Contact
- Forums Ansible
- Meetups locaux
- Conférences
- Réseaux sociaux 