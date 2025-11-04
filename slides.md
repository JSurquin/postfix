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
routeAlias: 'sommaire-formation-intensive'
---

<a name="SOMMAIRE-FORMATION-INTENSIVE" id="sommaire-initiation"></a>

# FORMATION POSTFIX INTENSIVE - 2 JOURS (14H) 🚀

## ⚡ PROGRAMME ULTRA-CONCENTRÉ

<div class="flex flex-col gap-2">
<Link to="introduction-postfix">📧 Introduction à Postfix (30min)</Link>
<Link to="installation-configuration-base">⚙️ Installation et config de base (1h30)</Link>
<Link to="architecture-fonctionnement">🏗️ Architecture essentielle (45min)</Link>
<Link to="configuration-main-cf">📝 Configuration main.cf (2h)</Link>
<Link to="alias-tables-virtuelles">📋 Alias et domaines virtuels (1h)</Link>
<Link to="dkim-spf-dmarc">🔐 DKIM, SPF, DMARC (1h30)</Link>
<Link to="tls-securite">🔒 TLS et sécurité (1h)</Link>
</div>
::right::

<div class="flex flex-col gap-2">
<Link to="protection-anti-spam">🛡️ Anti-spam essentiel (1h)</Link>
<Link to="logs-surveillance">📊 Logs et monitoring (45min)</Link>
<Link to="gestion-files-attente">📬 Files d'attente (30min)</Link>
<Link to="sauvegarde-restauration">💾 Sauvegarde (30min)</Link>
<Link to="exercices-debutant">🎯 TP pratiques (2h)</Link>
<Link to="troubleshooting-essentiel">🔧 Troubleshooting (1h)</Link>
<Link to="qcm-validation">✅ QCM final (30min)</Link>
</div>

---
layout: two-cols
routeAlias: 'programme-intensif'
---

## PROGRAMME FORMATION INTENSIVE 📅

**JOUR 1 (7h) - FONDAMENTAUX**

- **9h00-9h30** : Introduction Postfix (30min)
- **9h30-11h00** : Installation & config de base (1h30)
- **11h15-12h00** : Architecture essentielle (45min)
- **13h00-15h00** : Configuration main.cf (2h)
- **15h15-16h15** : Alias et domaines virtuels (1h)
- **16h30-18h00** : DKIM, SPF, DMARC (1h30)

::right::

**JOUR 2 (7h) - SÉCURITÉ & PRATIQUE**

- **9h00-10h00** : TLS et sécurité (1h)
- **10h15-11h15** : Anti-spam essentiel (1h)
- **11h30-12h15** : Logs et monitoring (45min)
- **13h00-13h30** : Files d'attente (30min)
- **13h30-14h00** : Sauvegarde (30min)
- **14h15-16h15** : TP pratiques (2h)
- **16h30-17h30** : Troubleshooting (1h)
- **17h30-18h00** : QCM final (30min)

---

## 🎯 OBJECTIFS DE LA FORMATION INTENSIVE

**À la fin de ces 2 jours, vous saurez :**

✅ **Installer et configurer** Postfix sur Linux

✅ **Comprendre l'architecture** modulaire de Postfix

✅ **Configurer le main.cf** pour vos besoins

✅ **Gérer les domaines virtuels** et alias

✅ **Sécuriser** avec DKIM, SPF, DMARC et TLS

✅ **Protéger contre le spam** efficacement

✅ **Surveiller et diagnostiquer** les problèmes

✅ **Sauvegarder et restaurer** votre configuration

---

## 📚 RESSOURCES COMPLÉMENTAIRES

**Documentation officielle :**
- [Postfix Documentation](http://www.postfix.org/documentation.html)
- [Postfix Configuration Parameters](http://www.postfix.org/postconf.5.html)

**Outils utiles :**
- [MXToolbox](https://mxtoolbox.com/) - Tests DNS et blacklists
- [Mail-tester](https://www.mail-tester.com/) - Test de qualité des emails
- [DKIM Validator](https://dkimvalidator.com/) - Validation DKIM

---
src: './pages/01-introduction-postfix.md'
---

---
src: './pages/qcm-module-01.md'
---

---
src: './pages/02-installation-configuration-base.md'
---

---
src: './pages/qcm-module-02.md'
---

---
src: './pages/03-architecture-fonctionnement.md'
---

---
src: './pages/qcm-module-03.md'
---

---
src: './pages/04-configuration-main-cf.md'
---

---
src: './pages/qcm-module-04.md'
---

---
src: './pages/05-gestion-files-attente.md'
---

---
src: './pages/qcm-module-05.md'
---

---
src: './pages/06-alias-tables-virtuelles.md'
---

---
src: './pages/qcm-module-06.md'
---

---
src: './pages/07-protection-anti-spam.md'
---

---
src: './pages/qcm-module-07.md'
---

---
src: './pages/08-dkim-spf-dmarc.md'
---

---
src: './pages/qcm-module-08.md'
---

---
src: './pages/09-tls-securite.md'
---

---
src: './pages/qcm-module-09.md'
---

---
src: './pages/10-logs-surveillance.md'
---

---
src: './pages/qcm-module-10.md'
---

---
src: './pages/11-sauvegarde-restauration.md'
---

---
src: './pages/qcm-module-11.md'
---

---
src: './pages/12-exercices-debutant.md'
---

---
src: './pages/13-qcm-initiation.md'
---
