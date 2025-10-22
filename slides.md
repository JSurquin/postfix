---
theme: ./
colorSchema: "auto"
layout: intro
highlighter: shiki
# https://sli.dev/custom/highlighters.html
title: Formation Postfix 2025
# download: true
#transition: slide-left
# remoteAssets: false
# export:
#   zoom: 1
#   format: pdf
#   timeout: 300000000
#   pdfOptions:
#     format: A4
download: "https://postfix.andromed.fr/slides.pdf"
themeConfig:
  logoHeader: "/avatar.png"
  eventLogo: "https://img2.storyblok.com/352x414/f/84560/2388x414/23d8eb4b8d/vue-amsterdam-with-name.png"
  eventUrl: "https://vuejs.amsterdam/"
#addons:
  #- "@slidev/addon-sharp-long-code"
---

# Postfix 2025

📧 Une formation présentée par Ascent et Andromed.

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
media: 'https://www.postfix.org/mysza.gif'
caption: 'POSTFIX 2025'
---

# DISCLAIMER 📧

### Dans cette formation nous allons voir les concepts fondamentaux et avancés de Postfix en 2025.

### Postfix est un serveur de messagerie (MTA - Mail Transfer Agent) robuste, sécurisé et performant, créé par Wietse Venema.

---
layout: two-cols
routeAlias: 'sommaire-initiation'
---

<a name="SOMMAIRE-INITIATION" id="sommaire-initiation"></a>

# SOMMAIRE POSTFIX - INITIATION 📜

<br>

<div class="flex flex-col gap-2">
<Link to="introduction-postfix">📧 Introduction à Postfix</Link>
<Link to="installation-configuration-base">⚙️ Installation et configuration de base</Link>
<Link to="architecture-fonctionnement">🏗️ Architecture et fonctionnement</Link>
<Link to="configuration-main-cf">📝 Configuration du main.cf</Link>
<Link to="gestion-files-attente">📬 Gestion des files d'attente</Link>
<Link to="alias-tables-virtuelles">📋 Alias et tables virtuelles</Link>
<Link to="protection-anti-spam">🛡️ Protection anti-spam de base</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="dkim-spf-dmarc">🔐 DKIM, SPF et DMARC</Link>
<Link to="tls-securite">🔒 TLS et sécurité des communications</Link>
<Link to="logs-surveillance">📊 Logs et surveillance</Link>
<Link to="sauvegarde-restauration">💾 Sauvegarde et restauration</Link>
<Link to="exercices-debutant">🎯 Exercices pratiques débutant</Link>
<Link to="qcm-initiation">✅ QCM de validation initiation</Link>
</div>

---
layout: two-cols
routeAlias: 'sommaire-perfectionnement'
---

# SOMMAIRE POSTFIX - PERFECTIONNEMENT 📜

<br>

<div class="flex flex-col gap-2">
<Link to="relais-smtp-avances">🔄 Relais SMTP avancés</Link>
<Link to="restrictions-politiques">⚖️ Restrictions et politiques</Link>
<Link to="bases-donnees-externes">🗄️ Bases de données externes (MySQL/PostgreSQL)</Link>
<Link to="haute-disponibilite">🔧 Haute disponibilité et réplication</Link>
<Link to="performance-optimisation">🚀 Performance et optimisation</Link>
<Link to="content-filters-milters">🔍 Content filters et milters</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="integration-dovecot">📨 Intégration avec Dovecot</Link>
<Link to="troubleshooting-avance">🔧 Troubleshooting avancé</Link>
<Link to="exercices-intermediaire">🎯 Exercices pratiques intermédiaire</Link>
<Link to="exercices-avance">🏆 Exercices pratiques avancé</Link>
<Link to="qcm-perfectionnement">✅ QCM perfectionnement</Link>
<Link to="projet-final">🎉 Projet final</Link>
</div>

---
layout: two-cols
routeAlias: 'programme-initiation'
---

## PROGRAMME INITIATION POSTFIX 📅

**Jour 1 - Introduction et installation**

- Introduction à Postfix et son écosystème
- Histoire et philosophie de conception
- Installation sur différentes distributions Linux
- Configuration de base et tests de fonctionnement
- Premiers envois d'emails locaux

::right::

**Jour 2 - Architecture et configuration**

- Comprendre l'architecture modulaire de Postfix
- Le fichier main.cf en détail
- Les files d'attente (queue management)
- Alias et redirections d'emails
- Tables virtuelles et domaines virtuels

**Jour 3 - Sécurité de base**

- Protection contre le spam (RBL, restrictions)
- Configuration DKIM pour l'authentification
- Mise en place SPF et DMARC
- Chiffrement TLS des communications
- Certificats SSL/Let's Encrypt

---

**Jour 4 - Administration et maintenance**

- Analyse des logs Postfix
- Outils de surveillance (postqueue, mailq, postsuper)
- Gestion des files d'attente
- Sauvegarde et restauration de configuration
- Résolution de problèmes courants

**Jour 5 - Pratique et validation**

- Exercices pratiques guidés
- Configuration complète d'un serveur mail
- Tests d'envoi et de réception
- QCM de validation des connaissances
- Débriefing et perspectives

---
layout: two-cols
routeAlias: 'programme-perfectionnement'
---

## PROGRAMME PERFECTIONNEMENT POSTFIX 📅

### Formation avancée pour administrateurs expérimentés

**Jour 1 - Relais et bases de données**

- Configuration de relais SMTP complexes
- Authentification SASL
- Intégration MySQL/PostgreSQL
- Domaines virtuels avancés
- Politiques de restriction avancées

::right::

**Jour 2 - Haute disponibilité et performance**

- Architecture haute disponibilité
- Load balancing et failover
- Optimisation des performances
- Tuning pour gros volumes
- Clustering et réplication

**Jour 3 - Filtrage et intégrations**

- Content filters (SpamAssassin, Amavis)
- Milters (filtres de contenu)
- Antivirus (ClamAV)
- Intégration avec Dovecot (LMTP, auth)
- Politiques de sécurité avancées

**Jour 4 - Projet et troubleshooting**

- Troubleshooting avancé
- Analyse de performances
- Projet final complet
- QCM de validation
- Débriefing et certification

---
src: './pages/01-introduction-postfix.md'
---

---
src: './pages/02-installation-configuration-base.md'
---

---
src: './pages/03-architecture-fonctionnement.md'
---

---
src: './pages/04-configuration-main-cf.md'
---

---
src: './pages/05-gestion-files-attente.md'
---

---
src: './pages/06-alias-tables-virtuelles.md'
---

---
src: './pages/07-protection-anti-spam.md'
---

---
src: './pages/08-dkim-spf-dmarc.md'
---

---
src: './pages/09-tls-securite.md'
---

---
src: './pages/10-logs-surveillance.md'
---

---
src: './pages/11-sauvegarde-restauration.md'
---

---
src: './pages/12-exercices-debutant.md'
---

---
src: './pages/13-qcm-initiation.md'
---

---
src: './pages/14-relais-smtp-avances.md'
---

---
src: './pages/15-restrictions-politiques.md'
---

---
src: './pages/16-bases-donnees-externes.md'
---

---
src: './pages/17-haute-disponibilite.md'
---

---
src: './pages/18-performance-optimisation.md'
---

---
src: './pages/19-content-filters-milters.md'
---

---
src: './pages/20-integration-dovecot.md'
---

---
src: './pages/21-troubleshooting-avance.md'
---

---
src: './pages/22-exercices-intermediaire.md'
---

---
src: './pages/23-exercices-avance.md'
---

---
src: './pages/24-qcm-perfectionnement.md'
---

---
src: './pages/25-projet-final.md'
---
