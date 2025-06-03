---
theme: ./
colorSchema: "auto"
layout: intro
highlighter: shiki
# https://sli.dev/custom/highlighters.html
title: Formation Docker & Ansible 2025
# download: true
#transition: slide-left
# remoteAssets: false
# export:
#   zoom: 1
#   format: pdf
#   timeout: 300000000
#   pdfOptions:
#     format: A4
download: "https://docker.andromed.fr/slides.pdf"
themeConfig:
  logoHeader: "/avatar.png"
  eventLogo: "https://img2.storyblok.com/352x414/f/84560/2388x414/23d8eb4b8d/vue-amsterdam-with-name.png"
  eventUrl: "https://vuejs.amsterdam/"
---

# Docker & Ansible 2025

🐳 Une formation présentée par Andromed.

<div class="pt-12">
  <span @click="next" class="px-2 p-1 rounded cursor-pointer hover:bg-white hover:bg-opacity-10">
    Appuyez sur espace pour la page suivante <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: presenter
eventLogo: 'https://img2.storyblok.com/352x0/f/84560/2388x414/23d8eb4b8d/vue-amsterdam-with-name.png'
eventUrl: 'https://vuejs.amsterdam/'
twitter: '@jimmylansrq'

twitterUrl: 'https://twitter.com/jimmylansrq'
presenterImage: 'https://legacy.andromed.fr/images/fondator.jpg'
---

# Jimmylan Surquin

Fondateur <a  href="https://www.andromed.fr/"><logos-storyblok-icon  mr-1/>Andromed</a>

- Lille, France 🇫🇷
- Création de contenu sur <a href="https://www.youtube.com/channel/jimmylansrq"> <logos-youtube-icon mr-1 /> jimmylansrq </a>
- Blog & Portfolio <a href="https://jimmylan.fr"> jimmylan.fr </a>

---
layout: text-image
media: 'https://i.pinimg.com/originals/f5/5e/80/f55e8059ea945abfd6804b887dd4a0af.gif'
caption: 'DOCKER & ANSIBLE'
---

# DISCLAIMER 🐧

### Dans cette formation nous allons voir les commandes principales de Docker et Ansible en 2025.

---
layout: two-cols
routeAlias: 'sommaire'
---

<a name="SOMMAIRE" id="sommaire"></a>

# SOMMAIRE DOCKER 📜

### Formation pratique en 3 jours

<br>

<div class="flex flex-col gap-2">
<Link to="comprendre-cicd">🔧 CI/CD & Microservices</Link>
<Link to="virtualisation-vs-conteneurisation">💻 Virtualisation vs conteneurisation</Link>
<Link to="intro-docker">🚀 Introduction & Définitions Docker</Link>
<Link to="premier-contact-docker">🎯 Premier contact Docker</Link>
<Link to="le-cli-docker">⌨️ Le CLI Docker</Link>
<Link to="reseaux-volumes-docker">🌐💾 Réseaux & Volumes</Link>
<Link to="exercices-cli-docker">🎯 Exercices CLI Docker</Link>
<Link to="dockerfile">📝 Dockerfile et images</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="exercices-dockerfile">🎯 Exercices Dockerfile</Link>
<Link to="docker-compose-orchestration">🎼 Docker Compose</Link>
<Link to="exercices-docker-compose">🎯 Exercices Compose</Link>
<Link to="fondamentaux-ansible">🤖 Fondamentaux Ansible</Link>
<Link to="exercices-ansible">🎯 Exercices Ansible</Link>
<Link to="qcm-ansible">✅ QCM Ansible</Link>
</div>

---
layout: two-cols
routeAlias: 'sommaire-ansible'
---

## PROGRAMME 3 JOURS 📅

### Structure pédagogique optimisée

**Jour 1 - Fondamentaux Docker**

- CI/CD et microservices
- Virtualisation vs conteneurisation
- Introduction & définitions Docker
- CLI Docker et commandes essentielles
- Exercices CLI pratiques (3 niveaux)
- Premier contact pratique

::right::

**Jour 2 - Docker avancé**

- Dockerfile et bonnes pratiques
- Exercices Dockerfile (3 niveaux)
- Réseaux et communication
- Volumes et persistance
- Docker Compose multi-containers
- Exercices Compose (3 niveaux)

**Jour 3 - Ansible et intégration**

- Introduction à Ansible
- Playbooks et inventaires
- Modules essentiels
- Ansible + Docker
- Projet final

---
src: './pages/02-cicd-microservices.md'
---

---
src: './pages/03-qcm-microservices-cicd.md'
---

---
src: './pages/05-virtualisation.md'
---

---
src: './pages/06-docker-intro.md'
---

---
src: './pages/04-exercice-definitions.md'
---

---
src: './pages/07-docker-cli.md'
---

---
src: './pages/09-reseaux.md'
---

---
src: './pages/06-qcm-docker-intro.md'
---

---
src: './pages/11-exercices-cli-docker.md'
---

---
src: './pages/08-dockerfile.md'
---

---
src: './pages/12-exercices-dockerfile.md'
---

---
src: './pages/10-docker-compose.md'
---

---
src: './pages/13-exercices-docker-compose.md'
---

---
src: './pages/ansible.md'
---

---
src: './pages/14-exercices-ansible.md'
---

---
src: './pages/ansible-qcm.md'
---
