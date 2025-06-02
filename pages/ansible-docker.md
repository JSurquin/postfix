---
layout: new-section
---

# Ansible avec Docker 🐳

---
routeAlias: 'ansible-docker'
---

<a name="ANSIBLE_DOCKER" id="ANSIBLE_DOCKER"></a>

# Ansible avec Docker 🐳

### Intégration et orchestration de containers

Ansible et Docker sont des outils complémentaires parfaits. Ansible automatise l'installation, la configuration et l'orchestration de vos containers Docker de manière idempotente et scalable.

---

# Installation de Docker avec Ansible ⚙️

## Playbook d'installation
```yaml
# playbooks/install_docker.yml
---
- name: Installation de Docker
  hosts: all
  become: yes
  tasks:
    - name: Installation des prérequis
      apt:
        name: "{{ packages }}"
        state: present
        update_cache: yes
      vars:
        packages:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
```

---

# Configuration repository Docker 🔑

```yaml
    - name: Ajout de la clé GPG Docker
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Ajout du repository Docker
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable
        state: present
```

---

# Installation des packages Docker 📦

```yaml
    - name: Installation de Docker
      apt:
        name: "{{ docker_packages }}"
        state: present
        update_cache: yes
      vars:
        docker_packages:
          - docker-ce
          - docker-ce-cli
          - containerd.io
```

---

# Gestion des conteneurs 🐳

## Playbook de déploiement
```yaml
# playbooks/deploy_containers.yml
---
- name: Déploiement des conteneurs
  hosts: all
  become: yes
  tasks:
    - name: Pull des images Docker
      docker_image:
        name: "{{ item }}"
        source: pull
      with_items:
        - nginx:latest
        - mysql:8.0
        - redis:alpine
```

---

# Création des conteneurs 🚀

```yaml
    - name: Création des conteneurs
      docker_container:
        name: "{{ item.name }}"
        image: "{{ item.image }}"
        state: started
        ports: "{{ item.ports | default([]) }}"
        volumes: "{{ item.volumes | default([]) }}"
        env: "{{ item.env | default({}) }}"
      with_items:
        - name: web
          image: nginx:latest
          ports:
            - "80:80"
        - name: db
          image: mysql:8.0
          env:
            MYSQL_ROOT_PASSWORD: "{{ mysql_root_password }}"
```

---

# Gestion des réseaux Docker 🌐

## Configuration réseau
```yaml
# playbooks/docker_network.yml
---
- name: Configuration des réseaux Docker
  hosts: all
  become: yes
  tasks:
    - name: Création du réseau frontend
      docker_network:
        name: frontend
        driver: bridge
        state: present

    - name: Création du réseau backend
      docker_network:
        name: backend
        driver: bridge
        state: present
```

---

# Connexion des conteneurs aux réseaux 🔗

```yaml
    - name: Connexion des conteneurs au réseau
      docker_network:
        name: "{{ item.network }}"
        connected: "{{ item.container }}"
        state: present
      with_items:
        - { network: frontend, container: web }
        - { network: backend, container: db }
```

---

# Gestion des volumes 💾

## Configuration des volumes
```yaml
# playbooks/docker_volumes.yml
---
- name: Configuration des volumes Docker
  hosts: all
  become: yes
  tasks:
    - name: Création des volumes
      docker_volume:
        name: "{{ item }}"
        state: present
      with_items:
        - web_data
        - db_data
        - redis_data
```

---

# Montage des volumes 📁

```yaml
    - name: Montage des volumes
      docker_container:
        name: "{{ item.name }}"
        volumes: "{{ item.volumes }}"
        state: started
      with_items:
        - name: web
          volumes:
            - web_data:/usr/share/nginx/html
        - name: db
          volumes:
            - db_data:/var/lib/mysql
```

---

# Docker Compose avec Ansible 🐳

## Déploiement avec Compose
```yaml
# playbooks/docker_compose.yml
---
- name: Déploiement avec Docker Compose
  hosts: all
  become: yes
  tasks:
    - name: Installation de Docker Compose
      get_url:
        url: https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-linux-x86_64
        dest: /usr/local/bin/docker-compose
        mode: '0755'
```

---

# Déploiement de l'application 🚀

```yaml
    - name: Déploiement de l'application
      docker_compose:
        project_src: "{{ app_path }}"
        files:
          - docker-compose.yml
        state: present
```

---

# Exemple de docker-compose.yml 📝

## Configuration complète
```yaml
# docker-compose.yml
version: '3.8'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - web_data:/usr/share/nginx/html
    networks:
      - frontend
    depends_on:
      - api
```

---

# Services API et Base de données 🗄️

```yaml
  api:
    image: node:20-alpine
    volumes:
      - ./app:/app
    networks:
      - frontend
      - backend
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: "{{ mysql_root_password }}"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - backend
```

---

# Volumes et réseaux 🔗

```yaml
volumes:
  web_data:
  db_data:

networks:
  frontend:
  backend:
```

---

# Sécurité Docker 🔒

## Configuration sécurisée
```yaml
# playbooks/docker_security.yml
---
- name: Configuration de la sécurité Docker
  hosts: all
  become: yes
  tasks:
    - name: Configuration de Docker daemon
      copy:
        content: |
          {
            "userns-remap": "default",
            "log-driver": "json-file",
            "log-opts": {
              "max-size": "10m",
              "max-file": "3"
            },
            "selinux-enabled": true,
            "no-new-privileges": true
          }
        dest: /etc/docker/daemon.json
        mode: '0644'
      notify: restart docker
```

---

# Configuration sécurité conteneurs 🛡️

```yaml
    - name: Activation des règles de sécurité
      docker_container:
        name: "{{ item.name }}"
        security_opt:
          - no-new-privileges:true
        cap_drop:
          - ALL
        cap_add:
          - "{{ item.capabilities | default([]) }}"
      with_items:
        - name: web
          capabilities:
            - NET_BIND_SERVICE
        - name: api
          capabilities: []
```

---

# Monitoring Docker 📊

## Configuration du monitoring
```yaml
# playbooks/docker_monitoring.yml
---
- name: Configuration du monitoring Docker
  hosts: all
  become: yes
  tasks:
    - name: Installation de Prometheus
      docker_container:
        name: prometheus
        image: prom/prometheus
        ports:
          - "9090:9090"
        volumes:
          - ./prometheus.yml:/etc/prometheus/prometheus.yml
          - prometheus_data:/prometheus
```

---

# Installation Grafana 📈

```yaml
    - name: Installation de Grafana
      docker_container:
        name: grafana
        image: grafana/grafana
        ports:
          - "3000:3000"
        volumes:
          - grafana_data:/var/lib/grafana
        env:
          GF_SECURITY_ADMIN_PASSWORD: "{{ grafana_admin_password }}"
```

---

# Bonnes pratiques 📋

## Organisation et maintenance
- Utiliser des rôles Ansible pour Docker
- Séparer les configurations par environnement
- Gérer les secrets avec Ansible Vault
- Documenter les playbooks

---

# Sécurité et performance 🚀

## Sécurité et performance
- Limiter les privilèges des conteneurs
- Utiliser des images officielles
- Mettre en place des politiques de mise à jour
- Monitorer les ressources

---

# Exemples avancés 🔧

## Déploiement d'une application complète
```yaml
# playbooks/deploy_app.yml
---
- name: Déploiement de l'application
  hosts: all
  become: yes
  tasks:
    - name: Cloner le repository
      git:
        repo: "{{ git_repo }}"
        dest: "{{ app_path }}"
        version: "{{ app_version }}"

    - name: Configuration de l'environnement
      template:
        src: env.j2
        dest: "{{ app_path }}/.env"
        mode: '0644'
```

---

# Déploiement avec Docker Compose 🐳

```yaml
    - name: Déploiement avec Docker Compose
      docker_compose:
        project_src: "{{ app_path }}"
        files:
          - docker-compose.yml
        state: present
        build: yes
```

---

# Intégration continue 🔄

## Avec GitHub Actions
```yaml
# .github/workflows/docker-ansible.yml
name: Docker Ansible Deployment
on: [push]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Run Ansible Playbook
      uses: dawidd6/action-ansible-playbook@v2
      with:
        playbook: playbooks/deploy_app.yml
        requirements: requirements.yml
        inventory: inventory/production
        vault_password: ${{ secrets.VAULT_PASSWORD }}
```

---

# Playbook Docker 📝

## Exemple de playbook

```yaml
---
- name: Gestion des conteneurs Docker
  hosts: docker_hosts
  tasks:
    - name: Pull de l'image nginx
      docker_image:
        name: nginx:latest
        source: pull

    - name: Création du réseau
      docker_network:
        name: web_network
        state: present

    - name: Démarrage du conteneur
      docker_container:
        name: web_server
        image: nginx:latest
        state: started
        networks:
          - name: web_network
        ports:
          - "80:80"
```

---

# Ansible et Docker Compose 🐳

## Intégration

- **Module docker_compose**
  - Gestion des stacks
  - Déploiement de services
  - Configuration

---

# Exemple Docker Compose 📋

## Exemple

```yaml
---
- name: Déploiement avec Docker Compose
  hosts: docker_hosts
  tasks:
    - name: Déploiement de la stack
      docker_compose:
        project_src: ./mon_projet
        files:
          - docker-compose.yml
        state: present
```

---

# Bonnes pratiques finales 🎯

## Sécurité

- **Gestion des secrets**
  - Ansible Vault
  - Variables sensibles
  - Credentials

- **Permissions**
  - Droits minimaux
  - Séparation des rôles
  - Audit des actions

---

# Maintenance et monitoring 🔧

## Maintenance

- **Versioning**
  - Tags des images
  - Gestion des dépendances
  - Mises à jour

- **Monitoring**
  - Logs
  - Métriques
  - Alertes 