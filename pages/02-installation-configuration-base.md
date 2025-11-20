---
layout: new-section
routeAlias: 'installation-configuration-base'
---

<a name="installation-configuration-base" id="installation-configuration-base"></a>

# Installation et Configuration de Base

<div class="mt-2">
  ⚙️ Mettre en place votre premier serveur Postfix
</div>

---

# Préparation de l'environnement

Avant d'installer Postfix, il faut préparer le terrain !

## Vérifications préalables

### 🖥️ Système d'exploitation

Postfix fonctionne sur tous les Unix/Linux :
- Ubuntu / Debian
- Red Hat / CentOS / Rocky Linux / AlmaLinux
- FreeBSD / OpenBSD
- macOS (pour le développement)

Pour cette formation, nous utiliserons principalement **Ubuntu 24.04 LTS** (Debian-based) et **Rocky Linux 9** (Red Hat-based).

---

### 🔍 Vérifier les prérequis système

Connexion SSH au serveur et vérification des ressources :

```bash
# Vérifier la version du système
cat /etc/os-release

# Vérifier les ressources disponibles
free -h
df -h
```

### 🌐 Configuration DNS préalable

**Crucial !** Avant d'installer Postfix, votre DNS doit être correctement configuré.

---

#### Enregistrements DNS nécessaires

> Ou enregistrer ces enregistrements DNS ?
> - Vous pouvez enregistrer ces enregistrements DNS chez votre hébergeur/FAI.

 Pour tester localement seulement

  - Vous pouvez modifier le fichier `/etc/hosts` sur votre machine pour "simuler" l'enregistrement A :

  ```bash
  # Modifier le fichier /etc/hosts
  sudo nano /etc/hosts
  ```

  <br/>

  ```
  127.0.0.1 mail.example.com
  ```

  <br/>

  ```bash
  # Redémarrer le service DNS
  sudo systemctl restart systemd-resolved
  ```

L’enregistrement MX n’est pas nécessaire pour les tests internes.

- Le PTR n’a pas d’importance pour les tests locaux (il sert uniquement pour la réputation d’envoi vers Internet).

- Vous pourrez envoyer des mails localement à root@localhost ou à user@tondomaine.local sans souci.

---

Nous allons utiliser un vrai domaine pour les tests, mais vous pouvez utiliser un domaine fictif comme `example.com`.

Dans notre cas nous allons utiliser le domaine `andromed.cloud`.

Et il nous faut bien sur un VPS ou un serveur dédié pour le serveur mail.

---

Le domaine est enregistré chez OVH.

Donc nous allons configurer le DNS chez OVH directement , si vous avez un autre FAI, vous devrez configurer le DNS chez eux , si vous utilisez un système plus classique, vous devrez configurer le tout dans B.I.N.D. (Bind DNS)

[https://www.it-connect.fr/dns-avec-bind-9/](Bind9 - Configuration DNS avec Bind9)

---

**Enregistrement A** : Pointe vers l'IP de votre serveur

```
mail.andromed.cloud.  IN  A  51.68.224.131
```

**Enregistrement MX** : Indique le serveur mail du domaine

```
andromed.cloud.  IN  MX  10  mail.andromed.cloud.
```

Le chiffre (10) est la priorité : plus c'est petit, plus c'est prioritaire.

**Enregistrement PTR (Reverse DNS)** : TRÈS IMPORTANT !

Le PTR fait le lien inverse : IP → nom de domaine. Sans PTR correct, vos emails seront considérés comme spam !

```
10.113.0.203.in-addr.arpa.  IN  PTR  mail.andromed.cloud.
```

⚠️ **Note importante** : Le PTR doit être configuré chez votre hébergeur/FAI (vous ne pouvez pas le faire vous-même).

---
layout: text-image
---

<img src="/andromed-cloud1.png" alt="Configuration DNS chez le FAI"
class="mx-auto lg:w-[600px]"
height='auto'
/>

---
layout: text-image
---

<img src="/andromed-spf.png" alt="Configuration DNS chez OVH"
class="mx-auto lg:w-[600px]"
height='auto'/>

---

### 🔧 Vérifier la configuration DNS

```bash
# Vérifier l'enregistrement MX
dig andromed.cloud MX +short

# Vérifier l'enregistrement A
dig mail.andromed.cloud A +short

# Vérifier le PTR (reverse DNS)
dig -x 203.0.113.10 +short
```

---

### 🔥 Configuration du firewall

Avant d'installer Postfix, comprenons les ports que nous allons utiliser !

---

## 🌐 Comprendre les ports mail

Avant d'ouvrir les ports, il est crucial de comprendre leur rôle.

Dans cette formation, nous allons configurer un serveur mail complet :
- **Postfix** pour envoyer/recevoir des emails
- **Dovecot** pour permettre aux clients (Outlook, Thunderbird, Apple Mail) de consulter leurs emails
- **TLS/SSL** pour sécuriser toutes les communications

Pour cela, nous devons ouvrir plusieurs ports selon leur usage :
- Ports **SMTP** : pour l'envoi de mails
- Ports **IMAP** : pour la lecture de mails
- Ports **POP3** : pour le téléchargement de mails (optionnel)

---

## 📧 Ports SMTP - Envoi de mails

| Port | Protocole / Mode | Usage | Notes |
|------|------------------|-------|-------|
| **25** | SMTP (plain) | Envoi serveur → serveur | Port standard pour le trafic entre serveurs mail. Les FAI bloquent souvent ce port pour les clients. |
| **587** | SMTP Submission + STARTTLS | Envoi client → serveur | **Port recommandé** pour tous les clients modernes (Outlook, Thunderbird, Apple Mail). TLS négocié après connexion. |
| **465** | SMTPS (SSL direct) | Envoi client → serveur | SSL/TLS dès la connexion. Supporté par la plupart des clients mail. |

---

### 📧 Détails des ports SMTP

<small>

**Port 25 - SMTP (Communication serveur à serveur)**
- C'est le port historique du protocole SMTP
- Utilisé pour le **relay entre serveurs** mail (exemple : gmail.com → votre-domaine.com)
- Les clients finaux (Outlook, Apple Mail) ne doivent **PAS** utiliser ce port
- Beaucoup de FAI bloquent ce port pour éviter le spam

**Port 587 - Submission (Client à serveur)**
- **Port recommandé** pour l'envoi depuis un client mail
- Obligatoire pour Thunderbird, Apple Mail, et la plupart des clients modernes
- Utilise **STARTTLS** : connexion en clair puis passage en TLS
- Nécessite généralement une authentification (SMTP AUTH)

**Port 465 - SMTPS (Client à serveur avec SSL)**
- SSL/TLS est actif **dès le début** de la connexion
- Port historique mais toujours très utilisé
- Supporté par Outlook, Apple Mail, Gmail
- Alternative au port 587

</small>

---

## 📥 Ports IMAP - Lecture de mails

| Port | Protocole / Mode | Usage | Notes |
|------|------------------|-------|-------|
| **143** | IMAP + STARTTLS | Lecture mail depuis client | TLS négocié après connexion via STARTTLS. Port standard IMAP. |
| **993** | IMAPS (SSL direct) | Lecture mail sécurisée | SSL/TLS dès la connexion. **Port recommandé** pour tous les clients modernes. |

---

### 📥 Détails des ports IMAP

**Port 143 - IMAP (avec STARTTLS)**
- Port standard du protocole IMAP (Internet Message Access Protocol)
- Permet de consulter ses emails depuis n'importe où
- **STARTTLS** : connexion en clair puis passage en TLS
- Les emails restent sur le serveur (contrairement à POP3)

**Port 993 - IMAPS (SSL direct)**
- **Port recommandé** pour tous les clients modernes
- SSL/TLS actif **dès le début** de la connexion
- Standard utilisé par défaut par Outlook, Apple Mail, Thunderbird
- Plus sécurisé que le port 143

💡 **IMAP vs POP3** : IMAP synchronise les emails (ils restent sur le serveur), POP3 les télécharge (et les supprime généralement du serveur).

---

## 📥 Ports POP3 - Téléchargement de mails

| Port | Protocole / Mode | Usage | Notes |
|------|------------------|-------|-------|
| **110** | POP3 + STARTTLS | Téléchargement depuis serveur | TLS peut être négocié via STARTTLS. |
| **995** | POP3S (SSL direct) | Téléchargement sécurisé | SSL/TLS dès la connexion. Port recommandé pour POP3. |

---

### 📥 Détails des ports POP3

**Port 110 - POP3 (avec STARTTLS)**
- Port standard du protocole POP3 (Post Office Protocol v3)
- Télécharge les emails du serveur vers le client
- Par défaut, **supprime les emails du serveur** après téléchargement
- **STARTTLS** : connexion en clair puis passage en TLS

**Port 995 - POP3S (SSL direct)**
- SSL/TLS actif **dès le début** de la connexion
- Port recommandé si vous utilisez POP3
- Certains clients anciens préfèrent POP3 à IMAP

⚠️ **Note** : Dans cette formation, nous privilégierons **IMAP** (plus moderne et pratique), mais nous configurerons quand même POP3 pour la compatibilité.

---

## ⚙️ Résumé rapide des ports

**Communication serveur à serveur :**
- Port **25** → SMTP (relay entre serveurs mail)

**Envoi depuis un client mail (Outlook, Apple Mail, Thunderbird) :**
- Port **587** → SMTP Submission avec STARTTLS ✅ **Recommandé**
- Port **465** → SMTPS avec SSL direct

**Lecture de mails (IMAP) :**
- Port **143** → IMAP avec STARTTLS
- Port **993** → IMAPS avec SSL direct ✅ **Recommandé**

**Téléchargement de mails (POP3) :**
- Port **110** → POP3 avec STARTTLS
- Port **995** → POP3S avec SSL direct ✅ **Recommandé**

---

🔵 1. SMTP : pourquoi 587 + STARTTLS ?

**587 = le « port submission »**

Le port 587 est aujourd’hui le standard pour soumettre un email au serveur d’envoi, c’est-à-dire quand un client (Mail, Outlook…) veut envoyer un mail.

- On commence par une connexion en clair.
- Puis on passe en mode chiffré via STARTTLS.
- Ce port est officiellement dédié aux clients (pas aux serveurs).
- Il exige une authentification obligatoire, ce que 465 n’a pas toujours imposé.

👉 Pourquoi STARTTLS et pas SSL direct ?

Historiquement, il n’existait pas de port SMTP officiel en SSL/TLS natif.
Mais il existait le port 465 qui était un port SSL/TLS.
Le port 465 avait été proposé pour ça… puis abandonné… puis réaccepté des années plus tard 😅.
Du coup, les bonnes pratiques modernes ont laissé :
- 587 avec STARTTLS = recommandé et standardisé (RFC 6409)
- 465 SMTPS = aussi possible aujourd’hui, mais longtemps considéré « legacy »

Donc Apple Mail et d’autres recommandent 587 car c’est le port normalisé et universel, compatible partout même si SSL direct échoue.

---

**2. IMAP/POP : pourquoi SSL direct (993 / 995) ?**

Contrairement à SMTP, IMAP et POP ont toujours eu des ports SSL/TLS dédiés :
- IMAP SSL → 993
- POP SSL → 995

Ici, pas de STARTTLS obligatoire :
- On se connecte directement en TLS, immédiatement et intégralement.

Pourquoi ne pas utiliser STARTTLS sur IMAP/POP ?

Ça existe (IMAP sur 143 + STARTTLS), mais :
- Les ports SSL directs (993/995) sont mieux supportés
- Plus simples à configurer pour les clients
- Pas de confusion historique comme avec SMTP
- Les providers recommandent presque tous les ports SSL natifs

Donc les clients de messagerie utilisent 993/995 par défaut.

---

**3. Résumé clair**

Protocole	Port recommandé	Type de chiffrement	Pourquoi
- SMTP (envoi)	587	STARTTLS	Port standardisé pour la soumission, authentification obligatoire, compatible partout
- SMTP (alternative)	465	SSL/TLS direct	Revenu tardivement, pas toujours supporté
- IMAP (lecture)	993	SSL/TLS direct	Port TLS dédié, simple, universel
- POP (lecture)	995	SSL/TLS direct	Idem : port TLS natif

---

**4. En une phrase**

👉 SMTP utilise 587 + STARTTLS pour des raisons historiques de normalisation, tandis que IMAP/POP ont toujours eu des ports SSL/TLS directs (993/995), plus simples et plus largement supportés.

---

## 💡 Astuces mnémotechniques

### Retenir les ports facilement

**Ports pairs = SSL/TLS direct**
- **465** (SMTPS) - SSL dès la connexion
- **993** (IMAPS) - SSL dès la connexion
- **995** (POP3S) - SSL dès la connexion

**Ports impairs = STARTTLS possible**
- **25** (SMTP) - Connexion en clair, STARTTLS optionnel
- **587** (Submission) - Connexion en clair, puis STARTTLS
- **143** (IMAP) - Connexion en clair, puis STARTTLS
- **110** (POP3) - Connexion en clair, puis STARTTLS

💡 **Règle simple** : Si le port est pair, c'est du SSL direct. Si le port est impair, c'est STARTTLS.

---

## 🔧 Configuration importante : master.cf

⚠️ **ATTENTION** : Pour que le port **587** (Submission) fonctionne, il faut activer le service dans `/etc/postfix/master.cf`.

**Par défaut, le service `submission` est commenté !**

Nous allons le configurer dans ce module pour permettre l'envoi d'emails depuis les clients (Outlook, Thunderbird, Apple Mail) :
- Nous **décommenterons** les lignes `submission` dans `master.cf`
- Nous activerons **SASL** pour l'authentification
- Nous configurerons **TLS optionnel** (sera rendu obligatoire au module 09)

Sans cette configuration, **Outlook et les autres clients ne pourront pas envoyer d'emails** via votre serveur !

---

## 🔥 Ouverture des ports firewall

Maintenant que nous comprenons les ports, ouvrons-les !

```bash
# Pour Ubuntu (UFW)
sudo ufw allow 25/tcp     # SMTP (serveur à serveur)
sudo ufw allow 587/tcp    # Submission (client à serveur avec STARTTLS)
sudo ufw allow 465/tcp    # SMTPS (client à serveur avec SSL)
sudo ufw allow 143/tcp    # IMAP (lecture mail avec STARTTLS)
sudo ufw allow 993/tcp    # IMAPS (lecture mail avec SSL)
sudo ufw allow 110/tcp    # POP3 (téléchargement avec STARTTLS)
sudo ufw allow 995/tcp    # POP3S (téléchargement avec SSL)
```

---

```bash
# Pour Rocky Linux (firewalld)
sudo firewall-cmd --permanent --add-service=smtp          # Port 25
sudo firewall-cmd --permanent --add-service=smtp-submission  # Port 587
sudo firewall-cmd --permanent --add-service=smtps         # Port 465
sudo firewall-cmd --permanent --add-service=imap          # Port 143
sudo firewall-cmd --permanent --add-service=imaps         # Port 993
sudo firewall-cmd --permanent --add-service=pop3          # Port 110
sudo firewall-cmd --permanent --add-service=pop3s         # Port 995
sudo firewall-cmd --reload
```

💡 **Note** : Même si nous n'utiliserons pas beaucoup POP3, nous ouvrons les ports pour assurer la compatibilité avec tous les clients mail.

---

## Installation de Postfix

### 📦 Installation sur Ubuntu/Debian

```bash
# Mise à jour des paquets
sudo apt update && sudo apt upgrade -y

# Installation de Postfix
sudo apt install postfix -y
```

Pendant l'installation, un assistant graphique apparaît :
1. **Type de configuration** : Choisissez "Internet Site"

> Il se peut que vous ne voyez pas cette étape, car Postfix est déjà installé sur votre système ou en fonction de la version il ne le propose pas, pas d'inquiétude.

2. **Nom du système de messagerie** : Entrez votre domaine (andromed.cloud)

---

### 📦 Installation sur Rocky Linux

```bash
# Mise à jour du système
sudo dnf update -y

# Installation de Postfix
sudo dnf install postfix -y

# Activer et démarrer Postfix
sudo systemctl enable postfix
sudo systemctl start postfix
```

---

### ✅ Vérifier l'installation

```bash
# Vérifier le statut de Postfix
sudo systemctl status postfix

# Vérifier la version installée
postconf mail_version

# Vérifier que Postfix écoute sur les bons ports
sudo ss -tlnp | grep master
```

Vous devriez voir le processus `master` écouter sur le port 25.

> Si vous ne voyez pas le processus `master` écouter sur le port 25, vérifiez que le firewall est correctement configuré.

---

## Comprendre l'arborescence de Postfix

### 📁 Les fichiers importants

**Configuration** : `/etc/postfix/main.cf` (config principale)

- `/etc/postfix/master.cf` (processus)

**Tables** : `/etc/postfix/aliases` (alias locaux)

- `/etc/postfix/virtual` (domaines virtuels)
- `/etc/postfix/transport` (routage)

**Files d'attente** : `/var/spool/postfix/` (incoming, active, deferred, hold, corrupt)

**Logs** : `/var/log/mail.log` (Ubuntu/Debian)

- `/var/log/maillog` (Rocky/Red Hat)

---

## Configuration de base du main.cf

Le fichier `/etc/postfix/main.cf` contient tous les paramètres de configuration.

```bash
sudo nano /etc/postfix/main.cf
```

### 🔧 Paramètres essentiels

```bash
# Identité du serveur
myhostname = mail.example.com
mydomain = example.com
myorigin = $mydomain

# Interfaces réseau
inet_interfaces = all  # ou localhost pour tests

# Domaines acceptés
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

# Réseaux autorisés (JAMAIS 0.0.0.0/0 = open relay!)
#mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
```

---

### 🔍 Qu'est ce qu'un relayhost ?

**Un relayhost est un serveur SMTP qui permet de relayer les emails vers un autre serveur SMTP.**

En gros, c'est un serveur SMTP qui permet de relayer les emails vers un autre serveur SMTP, par exemple si vous avez un serveur mail chez votre FAI et que vous voulez envoyer des emails vers un autre serveur mail, vous pouvez configurer le relayhost pour que Postfix relaye les emails vers le serveur mail de votre FAI.

On ne va pas trop s'attarder dessus dans cette formation, mais il faut savoir que c'est une bonne option dans certains cas. 

**relayhost** : Serveur SMTP relais (optionnel)

```bash
# Pas de relais (envoi direct)
relayhost =

# Ou via un relais (exemple : serveur de votre entreprise)
relayhost = [smtp.example.com]:587
```

---

### 🔐 Paramètres de sécurité de base

**home_mailbox** : Format de stockage des emails

```bash
# Format Maildir (recommandé)
home_mailbox = Maildir/

# Format mbox (ancien)
home_mailbox = mail/
```

Maildir vs mbox ?
- **Maildir** : Un fichier par email, plus sûr, plus rapide
- **mbox** : Tous les emails dans un seul fichier, risque de corruption

> Concrètement, chaque utilisateur disposera d'un répertoire Maildir dédié, par exemple : `/home/john/Maildir` pour l'utilisateur 'john'.

**smtpd_banner** : Bannière SMTP (ne pas révéler trop d'infos)

```bash
# Par défaut (affiche la version)
smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)

# Version sécurisée (masque les détails)
smtpd_banner = $myhostname ESMTP
```

---

### 📋 Exemple de configuration minimale

```bash
# Nom du serveur
myhostname = mail.andromed.cloud
mydomain = andromed.cloud
myorigin = $mydomain

# Interfaces réseau
inet_interfaces = all
inet_protocols = ipv4

# Domaines acceptés
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

# Réseaux autorisés
#mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128

# Pas de relais
relayhost =

# Stockage des emails
home_mailbox = Maildir/
# Attention nous allons plus tard utiliser le transport virtual pour les emails, donc nous ne pouvons pas utiliser home_mailbox.

# Bannière sécurisée
smtpd_banner = $myhostname ESMTP

# Limite de taille des messages (50 MB)
message_size_limit = 52428800
```

---

### 💾 Appliquer les modifications

Après avoir modifié `main.cf`, il faut recharger la configuration :

```bash
# Vérifier la syntaxe
sudo postfix check

# Si pas d'erreur, recharger
sudo systemctl reload postfix
```

⚠️ **Important** : `postfix check` est votre meilleur ami ! Utilisez-le systématiquement.

---

## Configuration du port 587 (Submission)

Maintenant que la configuration de base est en place, activons le port 587 pour permettre aux clients mail (Outlook, Thunderbird, Apple Mail) d'envoyer des emails via notre serveur.

⚠️ **Important** : Par défaut, le service `submission` est **commenté** dans `master.cf` !

---

### 📝 Modifier le fichier master.cf

Ouvrez le fichier de configuration des services :

```bash
sudo nano /etc/postfix/master.cf
```

Recherchez les lignes suivantes (elles sont commentées avec `#`) :

```bash
#submission inet n       -       y       -       -       smtpd
#  -o syslog_name=postfix/submission
#  -o smtpd_tls_security_level=encrypt
```

---

### ✏️ Décommenter et configurer submission

Décommentez et modifiez ces lignes pour activer le service :

```bash
submission inet n       -       y       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=may
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_client_restrictions=permit_sasl_authenticated,reject
  -o smtpd_relay_restrictions=permit_sasl_authenticated,reject
```

---

### 🔍 Explications de la configuration

**`submission inet n - y - - smtpd`**
- Active le service submission sur le port 587

**`-o syslog_name=postfix/submission`**
- Nom dans les logs pour distinguer du port 25

**`-o smtpd_tls_security_level=may`**
- TLS optionnel (pour l'instant, on le rendra obligatoire au module 09)

**`-o smtpd_sasl_auth_enable=yes`**
- Active l'authentification SASL (obligatoire pour envoyer des mails)

**`-o smtpd_client_restrictions=permit_sasl_authenticated,reject`**
- Autorise uniquement les clients authentifiés

**`-o smtpd_relay_restrictions=permit_sasl_authenticated,reject`**
- Autorise le relay uniquement pour les clients authentifiés

---

## Installation de SASL pour l'authentification

Pour que les clients puissent s'authentifier, il faut installer SASL (Simple Authentication and Security Layer).

### 📦 Installation sur Ubuntu/Debian

```bash
sudo apt install libsasl2-2 sasl2-bin libsasl2-modules -y
```

---

### 📦 Installation sur Rocky Linux

```bash
sudo dnf install cyrus-sasl cyrus-sasl-plain -y
```

---

### 🔧 Configuration de SASL dans main.cf

Ajoutez ces lignes dans `/etc/postfix/main.cf` :

```bash
# Configuration SASL
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
smtpd_sasl_local_domain = $myhostname
broken_sasl_auth_clients = yes
```

---

### 🔍 Explications SASL

**`smtpd_sasl_type = dovecot`**
- Utilise Dovecot pour l'authentification (nous le configurerons plus tard)
- En attendant Dovecot, SASL utilisera les comptes Unix du système

**`smtpd_sasl_path = private/auth`**
- Socket de communication avec Dovecot

**`smtpd_sasl_auth_enable = yes`**
- Active l'authentification SASL

**`smtpd_sasl_security_options = noanonymous`**
- Interdit les connexions anonymes

**`broken_sasl_auth_clients = yes`**
- Compatibilité avec les vieux clients (Outlook 2003, etc.)

---

### ⚠️ Configuration temporaire sans Dovecot

Pour tester immédiatement (avant d'installer Dovecot), modifiez temporairement la configuration SASL :

```bash
# Configuration SASL temporaire (sans Dovecot)
smtpd_sasl_type = cyrus
smtpd_sasl_path = smtpd
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
smtpd_sasl_local_domain = $myhostname
broken_sasl_auth_clients = yes
```

⚠️ **Cette configuration utilise les comptes Unix du système pour l'authentification.**

---

### 🔧 Configuration de Cyrus SASL (temporaire)

Créez le fichier de configuration SASL :

```bash
sudo nano /etc/postfix/sasl/smtpd.conf
```

Ajoutez :

```bash
pwcheck_method: saslauthd
mech_list: PLAIN LOGIN
```

---

### 🚀 Démarrer le service saslauthd

```bash
# Ubuntu/Debian
sudo systemctl enable saslauthd
sudo systemctl start saslauthd

# Rocky Linux
sudo systemctl enable saslauthd
sudo systemctl start saslauthd
```

---

### 💾 Appliquer la configuration

```bash
# Vérifier la syntaxe
sudo postfix check

# Recharger Postfix
sudo systemctl reload postfix

# Vérifier que le port 587 écoute
sudo ss -tlnp | grep master
```

Vous devriez maintenant voir les ports **25** et **587** !

---

### ✅ Vérifier les ports actifs

```bash
sudo ss -tlnp | grep master
```

Résultat attendu :

```
LISTEN  0  100  0.0.0.0:25   0.0.0.0:*  users:(("master",pid=1234,fd=13))
LISTEN  0  100  0.0.0.0:587  0.0.0.0:*  users:(("master",pid=1234,fd=17))
```

✅ Le port 587 est maintenant actif !

---

### 🧪 Tester l'authentification SASL

Vérifions que SASL fonctionne :

```bash
testsaslauthd -u votre_utilisateur -p votre_mot_de_passe
```

Résultat attendu : `0: OK "Success."`

⚠️ **Note** : Utilisez un compte Unix existant sur votre système pour tester.

---

### 📧 Tester l'envoi via le port 587

Test manuel avec telnet/openssl :

```bash
telnet localhost 587
```

Une fois connecté, tapez :

```
EHLO mail.example.com
```

Vous devriez voir dans la réponse :

```
250-AUTH PLAIN LOGIN
250-AUTH=PLAIN LOGIN
```

✅ L'authentification est disponible !

---

## 🎯 Récapitulatif de la configuration

À ce stade, nous avons :

✅ Postfix installé et configuré  
✅ Port 25 actif (serveur à serveur)  
✅ Port 587 actif (clients à serveur)  
✅ Authentification SASL fonctionnelle  
✅ Configuration basique (TLS optionnel)

🔜 **Au module 09 (TLS et Sécurité)**, nous sécuriserons tout ça en rendant TLS **obligatoire** sur le port 587 !

🔜 **Au module 14 (Dovecot)**, nous configurerons Dovecot pour une authentification plus robuste et la lecture des emails (IMAP/POP3).

---

## Premier test d'envoi

### 📧 Envoyer un email de test

Utilisons la commande `mail` (fournie par Postfix) :

```bash
echo "Test depuis Postfix" | mail -s "Test" root@localhost
```

### 🔍 Vérifier que l'email est arrivé

```bash
# Lister les emails de root
sudo ls -la /root/Maildir/new/

# Lire l'email
sudo cat /root/Maildir/new/*
```

### 📬 Test avec un vrai email

```bash
echo "Ceci est un test" | mail -s "Test Postfix" votre@email.com
```

⚠️ **Note** : Si la commande `mail` n'existe pas, installez `mailutils` (Ubuntu) ou `mailx` (Rocky).

---

## Commandes utiles de base

### 🛠️ Gestion du service Postfix

```bash
# Démarrer Postfix
sudo systemctl start postfix

# Arrêter Postfix
sudo systemctl stop postfix

# Redémarrer Postfix (arrêt puis démarrage)
sudo systemctl restart postfix

# Recharger la configuration (sans interruption)
sudo systemctl reload postfix

# Vérifier le statut
sudo systemctl status postfix
```

---

### 📊 Voir la file d'attente

```bash
# Afficher tous les messages en attente
mailq
# ou
postqueue -p
```

### 🗑️ Supprimer un message de la file

```bash
# Supprimer un message spécifique
sudo postsuper -d ID_DU_MESSAGE

# Supprimer tous les messages
sudo postsuper -d ALL
```

### 📝 Voir la configuration active

```bash
# Afficher toute la configuration
postconf

# Afficher un paramètre spécifique
postconf myhostname

# Afficher les paramètres non-défauts
postconf -n
```

---

## Configuration des alias

Les alias permettent de rediriger les emails d'un compte vers un autre.

### 📋 Le fichier /etc/aliases

```bash
sudo nano /etc/aliases
```

Contenu typique :

```bash
# Redirection des comptes systèmes
postmaster: root
webmaster: root
abuse: root

# Redirection de root vers un vrai email
root: admin@example.com
```

### 🔄 Appliquer les alias

Après modification, il faut recompiler la base de données :

```bash
sudo newaliases
# ou
sudo postalias /etc/aliases
```

---

### ✅ Tester un alias

```bash
# Envoyer un email à postmaster
echo "Test alias" | mail -s "Test" postmaster

# Vérifier qu'il arrive bien sur le compte redirigé
```

---

## Logs et debugging

### 📜 Suivre les logs en temps réel

```bash
# Ubuntu/Debian
sudo tail -f /var/log/mail.log

# Rocky Linux
sudo tail -f /var/log/maillog
```

### 🔍 Rechercher dans les logs

```bash
# Rechercher tous les logs d'un email spécifique
sudo grep "test@andromed.cloud" /var/log/mail.log

# Voir les erreurs uniquement
sudo grep "error\|warning" /var/log/mail.log
```

### 🐛 Activer le mode verbose

Pour avoir plus de détails dans les logs :

```bash
sudo postconf -e "smtpd_tls_loglevel = 1"
sudo postconf -e "smtp_tls_loglevel = 1"
sudo systemctl reload postfix
```

---

⚠️ **Attention** : Le mode verbose génère beaucoup de logs. À utiliser seulement pour le debug !

---

## Sécurisation minimale

Même pour une configuration de base, quelques mesures de sécurité s'imposent.

### 🚫 Désactiver les commandes dangereuses

```bash
# Dans main.cf
disable_vrfy_command = yes
```

La commande VRFY permet de vérifier si une adresse email existe. Les spammeurs l'adorent !

### 📏 Limiter la taille des messages

```bash
# Limite à 50 MB
message_size_limit = 52428800

# Limite de la boîte mail (0 = illimité)
mailbox_size_limit = 0
```

### ⏱️ Limites de temps

```bash
# Timeout de connexion SMTP
smtpd_timeout = 300s

# Timeout client SMTP
smtp_helo_timeout = 60s
```

---

### 🔒 Restrictions de base

```bash
# Rejeter les connexions trop précoces
smtpd_client_restrictions =
    permit_mynetworks,
    reject_unknown_client_hostname

# Vérifier le HELO
smtpd_helo_restrictions =
    permit_mynetworks,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname
```

---

## Troubleshooting courant

### ❌ Problème : Postfix ne démarre pas

**Solution 1** : Vérifier les logs

```bash
sudo journalctl -u postfix -n 50
```

**Solution 2** : Vérifier la syntaxe

```bash
sudo postfix check
```

**Solution 3** : Vérifier les permissions

```bash
sudo postfix set-permissions
```

---

### ❌ Problème : Emails ne partent pas

**Vérifier la file d'attente** :

```bash
mailq
```

**Voir les erreurs** :

```bash
sudo tail -n 100 /var/log/mail.log | grep error
```

**Forcer l'envoi** :

```bash
sudo postqueue -f
```

---

### ❌ Problème : Port 25 déjà utilisé

**Identifier le processus** :

```bash
sudo lsof -i :25
```

**Arrêter le service conflictuel** :

```bash
# Si c'est sendmail
sudo systemctl stop sendmail
sudo systemctl disable sendmail
```

---

### ❌ Problème : Reverse DNS manquant

**Vérifier le PTR** :

```bash
dig -x 51.68.224.131 +short
```

Si le PTR est incorrect ou manquant, contactez votre hébergeur. En attendant, vous pouvez utiliser un relais SMTP avec PTR correct.

---

## Checklist de validation

Avant de passer au module suivant, vérifiez que :

✅ Postfix est installé et démarré  
✅ Le DNS est correctement configuré (A, MX, PTR)  
✅ Le firewall autorise les ports nécessaires  
✅ Vous pouvez envoyer un email local  
✅ La commande `postconf -n` affiche votre configuration  
✅ Les logs sont accessibles et lisibles  
✅ La file d'attente fonctionne (`mailq`)

---

## Exercice pratique

<small>

#### 🎯 Exercice 1 : Installation complète

1. Installez Postfix sur votre système
2. Configurez le `main.cf` avec vos paramètres
3. Envoyez un email à root
4. Vérifiez qu'il est bien arrivé

#### 🎯 Exercice 2 : Configuration des alias

1. Créez un alias pour rediriger `contact@` vers votre email
2. Testez l'envoi à `contact@localhost`
3. Vérifiez la réception

#### 🎯 Exercice 3 : Analyse de logs

1. Envoyez plusieurs emails
2. Suivez les logs en temps réel
3. Identifiez les étapes de traitement du message

<div class="text-red-600">

#### 🎯 Exercice 4 : Docker : Si vous avez vu Docker

</div>

1. Créez un conteneur Postfix avec Docker
2. Configurez-le pour accepter les emails sur le port 2525
3. Testez l'envoi depuis l'extérieur du conteneur

</small>

---

## Points clés à retenir

### 💡 Ce qu'il faut retenir

<small>

**Configuration minimale** : `myhostname`, `mydomain`, `myorigin` sont essentiels 

- `inet_interfaces` définit les interfaces d'écoute - `mynetworks` contrôle qui peut envoyer des emails

**Sécurité de base** : Ne jamais faire un open relay (`mynetworks = 0.0.0.0/0`) 

- Toujours vérifier le DNS (surtout le PTR) pour les enregistrements A, MX et PTR

- Limiter les tailles de messages

**Outils essentiels** : `postconf` (voir et modifier la configuration) - `mailq` / `postqueue -p` (voir la file d'attente) 

- `postsuper` (gérer la file d'attente) 

- `postfix check` (vérifier la syntaxe)

**Logs** : `/var/log/mail.log` ou `/var/log/maillog` - Toujours consulter les logs en cas de problème - `tail -f` est votre ami

</small>

---

## Prochaine étape

Vous avez maintenant un Postfix fonctionnel ! 🎉

Dans le prochain module, nous allons plonger dans **l'architecture interne de Postfix** pour comprendre comment tout fonctionne sous le capot.

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Architecture et fonctionnement <carbon:arrow-right class="inline"/>
  </span>
</div>

