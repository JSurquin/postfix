---
theme: ./
colorSchema: "auto"
layout: intro
highlighter: shiki
# https://sli.dev/custom/highlighters.html
title: Formation PostgreSQL 2025
# download: true
#transition: slide-left
# remoteAssets: false
# export:
#   zoom: 1
#   format: pdf
#   timeout: 300000000
#   pdfOptions:
#     format: A4
download: "https://psql.andromed.fr/slides.pdf"
themeConfig:
  logoHeader: "/avatar.png"
  eventLogo: "https://img2.storyblok.com/352x414/f/84560/2388x414/23d8eb4b8d/vue-amsterdam-with-name.png"
  eventUrl: "https://vuejs.amsterdam/"
#addons:
  #- "@slidev/addon-sharp-long-code"
---

# PostgreSQL 2025

🐘 Une formation présentée par Ascent et Andromed.

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
media: 'https://www.postgresql.org/media/img/about/press/elephant.png'
caption: 'POSTGRESQL 2025'
---

# DISCLAIMER 🐘

### Dans cette formation nous allons voir les concepts fondamentaux et avancés de PostgreSQL en 2025.

---
layout: two-cols
routeAlias: 'sommaire-initiation'
---

<a name="SOMMAIRE-INITIATION" id="sommaire-initiation"></a>

# SOMMAIRE POSTGRESQL - INITIATION 📜

<br>

<div class="flex flex-col gap-2">
<Link to="introduction-postgresql">🐘 Introduction à PostgreSQL</Link>
<Link to="installation-configuration">⚙️ Installation et configuration</Link>
<Link to="premiers-pas-psql">🚀 Premiers pas avec PostgreSQL</Link>
<Link to="types-donnees">📊 Types de données</Link>
<Link to="requetes-select">🔍 Requêtes SELECT de base</Link>
<Link to="jointures-relations">🔗 Jointures et relations</Link>
<Link to="fonctions-agregation">📈 Fonctions d'agrégation</Link>
<Link to="sous-requetes-cte">🔄 Sous-requêtes et CTE</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="manipulation-donnees">✏️ Manipulation de données (INSERT, UPDATE, DELETE)</Link>
<Link to="creation-gestion-tables">🏗️ Création et gestion de tables</Link>
<Link to="contraintes-index">🔐 Contraintes et index</Link>
<Link to="vues">👁️ Vues et vues matérialisées</Link>
<Link to="exercices-debutant">🎯 Exercices pratiques débutant</Link>
<Link to="qcm-initiation">✅ QCM de validation initiation</Link>
</div>

---
layout: two-cols
routeAlias: 'sommaire-perfectionnement'
---

# SOMMAIRE POSTGRESQL - PERFECTIONNEMENT 📜

<br>

<div class="flex flex-col gap-2">
<Link to="transactions-concurrence">💼 Transactions et gestion de concurrence</Link>
<Link to="fonctions-procedures">🔧 Fonctions et procédures stockées</Link>
<Link to="triggers">⚡ Triggers</Link>
<Link to="optimisation-performance">🚀 Optimisation et performance</Link>
<Link to="securite-utilisateurs">🔒 Sécurité et gestion des utilisateurs</Link>
<Link to="sauvegarde-restauration">💾 Sauvegarde et restauration</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="json-types-avances">📦 JSON et types avancés</Link>
<Link to="extensions-postgresql">🧩 Extensions PostgreSQL</Link>
<Link to="replication-ha">🔄 Réplication et haute disponibilité</Link>
<Link to="exercices-intermediaire">🎯 Exercices pratiques intermédiaire</Link>
<Link to="exercices-avance">🏆 Exercices pratiques avancé</Link>
<Link to="qcm-perfectionnement">✅ QCM perfectionnement</Link>
<Link to="projet-final">🎉 Projet final</Link>
</div>

---
layout: two-cols
routeAlias: 'programme-initiation'
---

## PROGRAMME INITIATION POSTGRESQL 📅

**Jour 1 - Introduction et installation**

- Introduction à PostgreSQL et son écosystème
- Installation et configuration de l'environnement
- Premiers pas avec psql et pgAdmin
- Types de données de base
- Exercices pratiques d'introduction

::right::

**Jour 2 - Requêtes de base**

- Requêtes SELECT simples
- Filtrage avec WHERE
- Tri et limitation des résultats
- Jointures (INNER, LEFT, RIGHT, FULL)
- Exercices sur les requêtes de base

**Jour 3 - Fonctions et agrégations**

- Fonctions d'agrégation (COUNT, SUM, AVG, MIN, MAX)
- GROUP BY et HAVING
- Sous-requêtes et CTE (Common Table Expressions)
- Exercices sur les agrégations

---

**Jour 4 - Manipulation de données**

- INSERT, UPDATE, DELETE
- Création et modification de tables
- Contraintes (PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK)
- Index et performance
- Exercices sur la manipulation de données

**Jour 5 - Vues et projet**

- Vues simples et matérialisées
- Bonnes pratiques de modélisation
- Projet final intégrateur
- QCM de validation
- Débriefing et perspectives

---
layout: two-cols
routeAlias: 'programme-perfectionnement'
---

## PROGRAMME PERFECTIONNEMENT POSTGRESQL 📅

### Formation avancée pour développeurs expérimentés

**Jour 1 - Transactions et fonctions**

- Transactions et niveaux d'isolation
- Gestion de la concurrence
- Fonctions et procédures stockées (PL/pgSQL)
- Triggers et automatisation
- Exercices sur les fonctions avancées

::right::

**Jour 2 - Optimisation et performance**

- Analyse des requêtes (EXPLAIN)
- Optimisation des index
- Partitionnement de tables
- Tuning de PostgreSQL
- Exercices d'optimisation

**Jour 3 - Sécurité et administration**

- Gestion des utilisateurs et rôles
- Permissions et privilèges
- Sauvegarde et restauration (pg_dump, pg_restore)
- Point-in-time recovery
- Exercices d'administration

**Jour 4 - Types avancés et projet**

- Types JSON et JSONB
- Extensions (PostGIS, pg_trgm, etc.)
- Réplication et haute disponibilité
- Projet final avancé
- QCM et débriefing

---
src: './pages/01-introduction-postgresql.md'
---

---
src: './pages/02-installation-configuration.md'
---

---
src: './pages/03-premiers-pas-psql.md'
---

---
src: './pages/04-types-donnees.md'
---

---
src: './pages/05-requetes-select.md'
---

---
src: './pages/06-jointures-relations.md'
---

---
src: './pages/07-fonctions-agregation.md'
---

---
src: './pages/08-sous-requetes-cte.md'
---

---
src: './pages/09-manipulation-donnees.md'
---

---
src: './pages/10-creation-gestion-tables.md'
---

---
src: './pages/11-contraintes-index.md'
---

---
src: './pages/12-vues.md'
---

---
src: './pages/13-transactions-concurrence.md'
---

---
src: './pages/14-fonctions-procedures.md'
---

---
src: './pages/15-triggers.md'
---

---
src: './pages/16-optimisation-performance.md'
---

---
src: './pages/17-securite-utilisateurs.md'
---

---
src: './pages/18-sauvegarde-restauration.md'
---

---
src: './pages/19-json-types-avances.md'
---

---
src: './pages/20-extensions-postgresql.md'
---

---
src: './pages/21-replication-ha.md'
---

---
src: './pages/22-exercices-debutant.md'
---

---
src: './pages/23-exercices-intermediaire.md'
---

---
src: './pages/24-exercices-avance.md'
---

---
src: './pages/25-qcm-initiation.md'
---

---
src: './pages/26-qcm-perfectionnement.md'
---

---
src: './pages/27-projet-final.md'
---
