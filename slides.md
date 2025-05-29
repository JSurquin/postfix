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

### Voici le sommaire de cette formation sur Docker:

<br>

<div class="flex flex-col gap-2">
<Link to="comprendre-cicd">🔧 Comprendre le CI/CD</Link>
<Link to="utiliser-des-pipelines-cicd">🔄 Utiliser des pipelines CI/CD</Link>
<Link to="comprendre-les-microservices">🔍 Comprendre les micro-services</Link>
<Link to="pourquoi-utiliser-les-microservices">❓ Pourquoi utiliser les micro-services?</Link>
<Link to="des-definitions-avant-tout">🔧 Des définitions avant tout</Link>
<Link to="virtualisation-vs-conteneurisation">💻 Virtualisation vs conteneurisation</Link>
<Link to="intro-Docker">🚀 Introduction à Docker</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="le-cli-docker">🔄 Le CLI Docker</Link>
<Link to="images-Docker">🔍 Les images Docker</Link>
<Link to="creer-son-premier-conteneur">🚀 Créer son premier conteneur</Link>
<Link to="le-cli-docker">🔄 Autres Commandes Docker</Link>
<Link to="commandes-docker-avancees">🔍 Commandes Docker Avancées</Link>
<Link to="reseaux-docker">🔍 Les réseaux Docker</Link>
<Link to="volumes-persistants">🔍 Les volumes persistants</Link>
<Link to="kubernetes">🎉 Bonus : Introduction à Kubernetes</Link>
</div>

---
layout: two-cols
routeAlias: 'sommaire-ansible'
---

# SOMMAIRE ANSIBLE 📜

### Voici le sommaire de cette formation sur Ansible:

<br>

<div class="flex flex-col gap-2">

<Link to="introduction-ansible">🔧 Introduction à Ansible</Link>
<Link to="historique-ansible">📚 Historique d'Ansible</Link>
<Link to="pourquoi-ansible">❓ Pourquoi utiliser Ansible ?</Link>
<Link to="installation-ansible">⚙️ Installation et configuration</Link>
<Link to="fondamentaux-ansible">📝 Les fondamentaux d'Ansible</Link>
<Link to="modules-ansible">🔧 Modules et fonctionnalités</Link>

</div>
::right::

<div class="flex flex-col gap-2">
<Link to="concepts-avances-ansible">🚀 Concepts avancés</Link>
<Link to="tests-bonnes-pratiques">✅ Tests et bonnes pratiques</Link>
<Link to="ressources-ansible">📚 Ressources pour aller plus loin</Link>
<Link to="formation-continue-ansible">🎓 Formation continue</Link>
<Link to="communaute-support">👥 Communauté et support</Link>
<Link to="ansible-docker">🎉 Ansible et Docker</Link>
<Link to="prochaines-etapes-ansible">🔜 Prochaines étapes</Link>
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
src: './pages/08-exercice-dockerfile.md'
---

---
src: './pages/09-reseaux.md'
---

---
src: './pages/10-volumes.md'
---

---
src: './pages/ansible-intro.md'
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

---
src: './pages/docker-2024-2025.md'
---
