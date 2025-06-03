---
layout: new-section
routeAlias: 'fondamentaux-ansible'
---

<a name="fondamentaux-ansible" id="fondamentaux-ansible"></a>

# Ansible - Automatisation Infrastructure 🎯

---

# Ansible - Automatisation Infrastructure 🎯

### Infrastructure as Code moderne et simple

Ansible est l'outil d'automatisation de référence : **sans agent, idempotent, déclaratif**.

---

# Ansible - Perfect pour Docker

### Pourquoi Ansible + Docker ?

Parfait pour orchestrer Docker et automatiser l'infrastructure.

**Ansible 2025** : Support natif containers, modules cloud avancés, collections étendues

---

# Pourquoi Ansible ? 💡

### Les avantages clés

🎯 **Simple** : Configuration YAML lisible
🔄 **Idempotent** : Même résultat à chaque exécution
🚀 **Sans agent** : SSH/WinRM/API uniquement

---

# Pourquoi Ansible ? (suite) 💡

### Plus d'avantages

📈 **Scalable** : De 1 à 10,000+ serveurs
🔒 **Sécurisé** : Utilise vos connexions existantes

---

# Installation et setup 2025 ⚙️

### Installation rapide

```bash
# Installation via pip (recommandé)
python3 -m pip install --user ansible
# Collections essentielles
ansible-galaxy collection install community.general ansible.posix
# Vérification
ansible --version
```

```bash
# Si Windows et pas de python :
# Installer python via chocolatey
choco install python
# Installer ansible via pip
python3 -m pip install --user ansible
# Vérification
ansible --version
```

---

Si vous avez un problème d'installation, voici une solution :

> Sur python 3 vous devez mettre en place un venv :

```bash
python3 -m venv .venv
source .venv/bin/activate
pip3 install ansible
```

> Cela créer un environnement virtuel avec ansible installé dedans. (mode sandbox par défaut de python 3 pour ne pas polluer votre environnement global)

---

# Inventaire : Définir vos serveurs 📋

### Inventaire YAML moderne

```yaml
# inventory/hosts.yml
all:
  vars:
    ansible_user: ubuntu
    ansible_python_interpreter: /usr/bin/python3
    
  children:
    docker_hosts:
      hosts:
        docker-01: { ansible_host: 10.0.1.10 }
        docker-02: { ansible_host: 10.0.1.11 }
```

---

# Inventaire : Structure complète

```yaml
    databases:
      hosts:
        db-01: { ansible_host: 10.0.1.20 }
        
    webservers:
      hosts:
        web-[01:03]: { ansible_host: "10.0.1.{{ 30 + item }}" }
```

---

# Premier playbook 🎭

### Structure et exécution

```yaml
# deploy.yml
---
- name: Configuration serveurs Docker 🐳
  hosts: docker_hosts
  become: true
  vars:
    docker_compose_version: "2.24.0"

  tasks:
    - name: Installation Docker
      apt:
        name: [docker.io, docker-compose-plugin]
        state: present
        update_cache: true
```

---

# Premier playbook (suite)

```yaml
    - name: Ajout utilisateur au groupe docker
      user:
        name: "{{ ansible_user }}"
        groups: docker
        append: true

    - name: Démarrage et activation Docker
      systemd:
        name: docker
        state: started
        enabled: true
```

---

# Exécution du playbook

```bash
# Exécution
ansible-playbook -i inventory/hosts.yml deploy.yml
```

---

# Modules essentiels 📦

### Les modules indispensables pour Docker/Infrastructure

```yaml
# Gestion des packages
- name: Installation packages
  apt:
    name: [nginx, git, docker.io, python3-pip]
    state: present
```

---

# Modules : Fichiers et templates

```yaml
# Gestion des fichiers/templates
- name: Configuration nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    backup: true
  notify: restart nginx
```

---

# Modules : Commandes et scripts

```yaml
# Commandes et scripts
- name: Build application
  shell: |
    cd /opt/app
    docker build -t myapp:{{ app_version }} .
  changed_when: true
```

---

# Modules : Containers Docker

```yaml
# Gestion des containers Docker
- name: Lancer container webapp
  community.docker.docker_container:
    name: webapp
    image: "myapp:{{ app_version }}"
    ports:
      - "80:8080"
    state: started
    restart_policy: always
```

---

# Variables et templates 🔧

### Configuration dynamique

```yaml
# group_vars/all.yml
app_version: "v1.2.0"
db_password: "{{ vault_db_password }}"
nginx_worker_processes: "{{ ansible_processor_vcpus }}"
```

---

# Variables par environnement

```yaml
# Variables par environnement
environments:
  dev:
    domain: "dev.myapp.com"
    replicas: 1
  prod:
    domain: "myapp.com"
    replicas: 3
```

---

# Template nginx

```bash
{# templates/nginx.conf.j2 #}
worker_processes {{ nginx_worker_processes }};

upstream app {
{% for i in range(environments[env].replicas) %}
    server app-{{ i+1 }}:8080;
{% endfor %}
}
```

---

# Template nginx (suite)

```bash
server {
    server_name {{ environments[env].domain }};
    
    location / {
        proxy_pass http://app;
    }
}
```

---

# Handlers et conditions 🎯

### Réactivité et logique

```yaml
tasks:
  - name: Configuration Docker daemon
    template:
      src: daemon.json.j2
      dest: /etc/docker/daemon.json
    notify: restart docker
    when: configure_docker_daemon | default(false)
```

---

# Handlers : Déploiement multi-réplicas

```yaml
  - name: Déploiement app selon environnement
    community.docker.docker_container:
      name: "webapp-{{ item }}"
      image: "myapp:{{ app_version }}"
      ports:
        - "{{ 8080 + item }}:8080"
      env:
        ENV: "{{ env }}"
        REPLICA: "{{ item }}"
    loop: "{{ range(1, environments[env].replicas + 1) | list }}"
```

---

# Handlers : Définition

```yaml
handlers:
  - name: restart docker
    systemd:
      name: docker
      state: restarted

  - name: reload nginx
    systemd:
      name: nginx
      state: reloaded
```

---

# Rôles : Réutilisabilité 📦

### Structure modulaire

```yaml
# roles/docker/tasks/main.yml
---
- name: Installation Docker
  apt:
    name: [docker.io, docker-compose-plugin]
    state: present

- name: Configuration Docker daemon
  template:
    src: daemon.json.j2
    dest: /etc/docker/daemon.json
  notify: restart docker
```

---

# Utilisation des rôles

```yaml
# Utilisation dans un playbook
---
- name: Setup infrastructure
  hosts: all
  become: true
  
  roles:
    - docker
    - nginx
    - { role: app, app_version: "v2.0.0" }
```

---

# Ansible + Docker : Stack complète 🐳

### Déploiement d'application containerisée

```yaml
---
- name: Déploiement stack web complète
  hosts: docker_hosts
  become: true
  vars:
    app_name: "webapp"
    app_version: "{{ lookup('env', 'CI_COMMIT_SHA') | default('latest') }}"
```

---

# Stack Docker : Préparation

```yaml
  tasks:
    - name: Création des répertoires
      file:
        path: "/opt/{{ app_name }}/{{ item }}"
        state: directory
      loop: [data, logs, config]

    - name: Configuration docker-compose
      template:
        src: docker-compose.yml.j2
        dest: "/opt/{{ app_name }}/docker-compose.yml"
```

---

# Stack Docker : Déploiement

```yaml
    - name: Déploiement de l'application
      community.docker.docker_compose:
        project_src: "/opt/{{ app_name }}"
        pull: true
        state: present

    - name: Vérification santé des services
      uri:
        url: "http://{{ inventory_hostname }}/health"
        method: GET
      retries: 5
      delay: 10
```

---

# Collections et écosystème 🌐

### Extensions essentielles

```bash
# Collections Docker
ansible-galaxy collection install community.docker

# Collections Cloud
ansible-galaxy collection install amazon.aws
ansible-galaxy collection install azure.azcollection

# Collections Kubernetes
ansible-galaxy collection install kubernetes.core
```

---

# Utilisation avec collections

```yaml
# Utilisation avec collections
- name: Gestion infrastructure cloud + containers
  hosts: localhost
  tasks:
    - name: Création instance AWS
      amazon.aws.ec2_instance:
        name: "docker-host"
        image_id: ami-0abcdef1234567890
        instance_type: t3.medium
```

---

# Collections : Attente et déploiement

```yaml        
    - name: Attente démarrage
      wait_for:
        host: "{{ item.public_ip_address }}"
        port: 22
      
    - name: Déploiement containers
      community.docker.docker_container:
        name: myapp
        image: nginx:alpine
        delegate_to: "{{ item.public_ip_address }}"
```

---

# Ansible Vault : Secrets 🔐

### Gestion sécurisée des secrets

```bash
# Créer un fichier chiffré
ansible-vault create secrets.yml
ansible-vault edit secrets.yml

# Utilisation
ansible-playbook -i inventory deploy.yml --ask-vault-pass
```

---

# Vault : Utilisation des secrets

```yaml
# secrets.yml (chiffré)
vault_db_password: "super_secret_password"
vault_api_key: "1234567890abcdef"

# Utilisation dans les playbooks
database:
  password: "{{ vault_db_password }}"
  
api:
  key: "{{ vault_api_key }}"
```

---

# Optimisation et bonnes pratiques 🚀

### Configuration production

```ini
# ansible.cfg
[defaults]
host_key_checking = False
callback_whitelist = timer, profile_tasks
stdout_callback = yaml
forks = 20
timeout = 60
```

---

# Configuration SSH optimisée

```ini
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
pipelining = True
```

---

# Structure projet

```
ansible-project/
├── ansible.cfg
├── inventory/
│   ├── production.yml
│   └── staging.yml
├── group_vars/all.yml
├── playbooks/
│   ├── site.yml
│   └── deploy.yml
├── roles/
└── secrets.yml (vault)
```

---

# Tags et exécution sélective

```yaml
- name: Installation Docker
  apt: name=docker.io
  tags: [install, docker]

- name: Configuration
  template: src=config.j2 dest=/etc/app.conf
  tags: [config]
```

---

# Exécution avec tags

```bash
# Exécution sélective
ansible-playbook site.yml --tags "docker,config"
ansible-playbook site.yml --skip-tags "install"
``` 