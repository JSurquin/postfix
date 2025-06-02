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
  eventLogo: "https://img2.storyblok.com/352x0/f/84560/2388x414/23d8eb4b8d/vue-amsterdam-with-name.png"
  eventUrl: "https://vuejs.amsterdam/"
  twitter: "@jimmylan"
  twitterUrl: "https://twitter.com/jimmylansrq"
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
<Link to="des-definitions-avant-tout">📚 Définitions essentielles</Link>
<Link to="intro-Docker">🚀 Introduction à Docker</Link>
<Link to="le-cli-docker">⌨️ Le CLI Docker</Link>
<Link to="dockerfile">📝 Dockerfile et images</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="reseaux-volumes-docker">🌐💾 Réseaux & Volumes</Link>
<Link to="exercice-unifie-docker">🎯 Exercice unifié</Link>
<Link to="fondamentaux-ansible">🤖 Fondamentaux Ansible</Link>
<Link to="ansible-docker">🔗 Ansible + Docker</Link>
<Link to="qcm-ansible">✅ QCM Ansible</Link>
</div>

---
layout: two-cols
routeAlias: 'sommaire-ansible'
---

# PROGRAMME 3 JOURS 📅

### Structure pédagogique optimisée

<br>

**Jour 1 - Fondamentaux Docker**
<div class="flex flex-col gap-1 text-sm">
- CI/CD et microservices
- Virtualisation vs conteneurisation
- Docker CLI et premiers containers
- Images et registries
</div>

**Jour 2 - Docker avancé**
<div class="flex flex-col gap-1 text-sm">
- Dockerfile et builds
- Réseaux et communication
- Volumes et persistance
- Exercices pratiques
</div>

::right::

**Jour 3 - Ansible et intégration**
<div class="flex flex-col gap-1 text-sm">
- Introduction à Ansible
- Playbooks et inventaires
- Modules essentiels
- Ansible + Docker
- Projet final
</div>

**Méthode pédagogique**
<div class="flex flex-col gap-1 text-sm">
- 80% pratique, 20% théorie
- Exercices progressifs
- QCM de validation
- Projet fil rouge
</div>

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
src: './pages/04-definitions.md'
---

---
src: './pages/06-docker-intro.md'
---

---
src: './pages/06-qcm-docker-intro.md'
---

---
src: './pages/07-docker-cli.md'
---

---
src: './pages/04-exercice-definitions.md'
---

---
src: './pages/08-dockerfile.md'
---

---
src: './pages/09-reseaux.md'
---

---
src: './pages/08-exercice-dockerfile.md'
---

---
src: './pages/ansible.md'
---

---
src: './pages/ansible-qcm.md'
---

---
src: './pages/ansible-docker.md'
---
