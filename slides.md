---
theme: ./
colorSchema: "auto"
layout: intro
highlighter: shiki
# https://sli.dev/custom/highlighters.html
title: Formation Python 2025
# download: true
#transition: slide-left
# remoteAssets: false
# export:
#   zoom: 1
#   format: pdf
#   timeout: 300000000
#   pdfOptions:
#     format: A4
download: "https://python.andromed.fr/slides.pdf"
themeConfig:
  logoHeader: "/avatar.png"
  eventLogo: "https://img2.storyblok.com/352x414/f/84560/2388x414/23d8eb4b8d/vue-amsterdam-with-name.png"
  eventUrl: "https://vuejs.amsterdam/"
#addons:
  #- "@slidev/addon-sharp-long-code"
---

# Python 2025

🐍 Une formation présentée par Ascent et Andromed.

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
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
caption: 'PYTHON 2025'
---

# DISCLAIMER 🐍

### Dans cette formation nous allons voir les concepts fondamentaux et avancés de Python en 2025.

---
layout: two-cols
routeAlias: 'sommaire-initiation'
---

<a name="SOMMAIRE-INITIATION" id="sommaire-initiation"></a>

# SOMMAIRE PYTHON - INITIATION 📜

### Formation pratique en 5 jours

<br>

<div class="flex flex-col gap-2">
<Link to="introduction-python">🐍 Introduction à Python</Link>
<Link to="installation-environnement">⚙️ Installation et environnement</Link>
<Link to="premiers-pas">🚀 Premiers pas avec Python</Link>
<Link to="variables-types">📊 Variables et types de données</Link>
<Link to="structures-controle">🔄 Structures de contrôle</Link>
<Link to="fonctions">🔧 Fonctions et modules</Link>
<Link to="collections">📦 Collections (listes, tuples, dictionnaires)</Link>
<Link to="gestion-erreurs">⚠️ Gestion des erreurs</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="fichiers-io">📁 Manipulation de fichiers</Link>
<Link to="programmation-objet">🏗️ Programmation orientée objet</Link>
<Link to="exercices-initiation">🎯 Exercices pratiques</Link>
<Link to="projet-final-initiation">🎉 Projet final</Link>
<Link to="qcm-initiation">✅ QCM de validation</Link>
</div>

---
layout: two-cols
routeAlias: 'sommaire-perfectionnement'
---

# SOMMAIRE PYTHON - PERFECTIONNEMENT 📜

### Formation avancée en 4 jours

<br>

<div class="flex flex-col gap-2">
<Link to="rappel-fondamentaux">🔄 Rappel des fondamentaux</Link>
<Link to="decorateurs-generateurs">🎭 Décorateurs et générateurs</Link>
<Link to="context-managers">🔧 Context managers</Link>
<Link to="metaclasses">🏗️ Métaclasses et introspection</Link>
<Link to="async-await">⚡ Programmation asynchrone</Link>
<Link to="testing">🧪 Tests et TDD</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="design-patterns">📐 Design patterns</Link>
<Link to="optimisation-performance">🚀 Optimisation et performance</Link>
<Link to="frameworks-web">🌐 Frameworks web (Flask/Django)</Link>
<Link to="data-science">📊 Data Science avec Python</Link>
<Link to="exercices-perfectionnement">🎯 Exercices avancés</Link>
<Link to="qcm-perfectionnement">✅ QCM perfectionnement</Link>
</div>

---
layout: two-cols
routeAlias: 'programme-initiation'
---

## PROGRAMME INITIATION - 5 JOURS 📅

### Structure pédagogique optimisée

**Jour 1 - Introduction et environnement**

- Introduction à Python et son écosystème
- Installation et configuration de l'environnement
- Premier programme "Hello World"
- Variables et types de données de base
- Exercices pratiques d'introduction

::right::

**Jour 2 - Structures de contrôle**

- Conditions et opérateurs logiques
- Boucles (for, while)
- Structures de données simples
- Exercices sur les structures de contrôle
- Mini-projet : calculateur simple

**Jour 3 - Fonctions et modules**

- Définition et utilisation des fonctions
- Paramètres et valeurs de retour
- Modules et packages
- Gestion des erreurs (try/except)
- Exercices sur les fonctions

**Jour 4 - Collections avancées**

- Listes et leurs méthodes
- Tuples et leurs spécificités
- Dictionnaires et sets
- Manipulation de fichiers
- Exercices sur les collections

**Jour 5 - POO et projet final**

- Concepts de la programmation orientée objet
- Classes, objets, héritage
- Projet final intégrateur
- QCM de validation
- Débriefing et perspectives

---
layout: two-cols
routeAlias: 'programme-perfectionnement'
---

## PROGRAMME PERFECTIONNEMENT - 4 JOURS 📅

### Formation avancée pour développeurs expérimentés

**Jour 1 - Fonctionnalités avancées**

- Rappel des fondamentaux
- Décorateurs et leurs applications
- Générateurs et itérateurs
- Context managers
- Exercices sur les fonctionnalités avancées

::right::

**Jour 2 - Métaprogrammation et performance**

- Métaclasses et introspection
- Programmation asynchrone (async/await)
- Optimisation et profiling
- Design patterns en Python
- Exercices de métaprogrammation

**Jour 3 - Frameworks et écosystème**

- Frameworks web (Flask vs Django)
- Tests unitaires et TDD
- Intégration continue
- Bonnes pratiques de développement
- Exercices sur les frameworks

**Jour 4 - Data Science et projet final**

- Introduction à la data science
- Pandas, NumPy, Matplotlib
- Machine Learning avec scikit-learn
- Projet final avancé
- QCM et débriefing

---
src: './pages/01-introduction-python.md'
---

---
src: './pages/02-installation-environnement.md'
---

---
src: './pages/03-premiers-pas.md'
---

---
src: './pages/04-variables-types.md'
---

---
src: './pages/05-structures-controle.md'
---

---
src: './pages/06-fonctions.md'
---

---
src: './pages/07-collections.md'
---

---
src: './pages/08-gestion-erreurs.md'
---

---
src: './pages/09-programmation-objet.md'
---

---
src: './pages/10-fichiers-io.md'
---

---
src: './pages/11-projet-final.md'
---

---
src: './pages/12-qcm-initiation.md'
---

---
src: './pages/13-rappel-fondamentaux.md'
---

---
src: './pages/14-decorateurs-generateurs.md'
---

---
src: './pages/15-context-managers.md'
---

---
src: './pages/16-metaclasses.md'
---

---
src: './pages/17-async-await.md'
---

---
src: './pages/18-testing.md'
---

---
src: './pages/19-design-patterns.md'
---

---
src: './pages/20-optimisation-performance.md'
---

---
src: './pages/21-frameworks-web.md'
---

