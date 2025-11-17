---
layout: new-section
routeAlias: 'dovecot'
---

<a name="dovecot" id="dovecot"></a>

# 📬 Dovecot - Serveur IMAP/POP3

## Intégration avec Postfix

<div class="mt-2">
  📬 Configuration complète d'un serveur de messagerie
</div>

---

## 📋 Qu'est-ce que Dovecot ?

**Dovecot est un serveur IMAP et POP3 open-source**

### 🎯 Rôle dans l'écosystème email

- **Postfix** : MTA (Mail Transfer Agent) - Envoie et reçoit les emails
- **Dovecot** : MDA (Mail Delivery Agent) - Stocke et permet de récupérer les emails

---

## 🔄 Complémentarité Postfix + Dovecot

### Workflow complet

```
┌──────────┐    SMTP     ┌──────────┐    LMTP     ┌──────────┐
│ Internet │ ─────────> │ Postfix  │ ─────────> │ Dovecot  │
└──────────┘            └──────────┘            └──────────┘
                             │                        │
                             │                        │
                        Envoi emails          Lecture emails
                                                   (IMAP/POP3)
                                                       │
                                                       ▼
                                              ┌──────────────┐
                                              │   Clients    │
                                              │ (Mail apps)  │
                                              └──────────────┘
```

---

## ✨ Pourquoi Dovecot ?

### Avantages

- ⚡ **Performances** : Très rapide, même avec des milliers de boîtes
- 🔒 **Sécurité** : Support complet de SSL/TLS, authentification robuste
- 📊 **Scalabilité** : Gère de petites à très grandes installations
- 🔧 **Flexibilité** : Nombreuses options de stockage et d'authentification
- 📱 **Moderne** : Support IMAP IDLE, push notifications

---

## 📦 Installation sur Debian/Ubuntu

### Installation des paquets

```bash
# Mise à jour du système
apt update && apt upgrade -y
```

```bash
# Installation de Dovecot avec IMAP et POP3
apt install -y dovecot-core dovecot-imapd dovecot-pop3d
```

```bash
# Installation des modules complémentaires
apt install -y dovecot-lmtpd dovecot-mysql dovecot-sieve
```

---

## 🧩 Petite aparté : LMTPD et Dovecot-Sieve

### 📮 LMTPD
**Définition simple :** Reçoit les emails de Postfix et les dépose dans les boîtes aux lettres.  

**Analogie :**  
Postfix = facteur qui apporte les sacs de courrier  
LMTPD = personne dans le centre de tri qui ouvre les sacs et range chaque lettre dans la bonne case  

---

### 🧹 Dovecot-Sieve
**Définition simple :** Langage de règles qui trie automatiquement les emails (spam, newsletter, etc.)  

**Analogie :**  
Sieve = assistant personnel qui lit chaque enveloppe et place chaque email dans le bon dossier  
Sans Sieve, tout tombe en vrac dans la même boîte

---

## 📦 Installation sur CentOS/RHEL

### Installation avec dnf/yum

```bash
# Installation sur CentOS/RHEL 8+
dnf install -y dovecot dovecot-mysql dovecot-pigeonhole
```

---

```bash
# Démarrage et activation
systemctl start dovecot
systemctl enable dovecot
```

```bash
# Vérification de l'installation
dovecot --version
```

---

## 🏗️ Architecture de Dovecot

### Composants principaux

**Services**
- `dovecot` : Processus maître
- `imap` : Serveur IMAP
- `pop3` : Serveur POP3
- `lmtp` : Local Mail Transfer Protocol
- `auth` : Service d'authentification
- `lda` : Local Delivery Agent

---

## 📂 Structure des fichiers

### Fichiers de configuration

```
/etc/dovecot/
├── dovecot.conf              # Configuration principale
├── conf.d/                   # Configurations modulaires
│   ├── 10-auth.conf         # Authentification
│   ├── 10-mail.conf         # Stockage des emails
│   ├── 10-master.conf       # Services et sockets
│   ├── 10-ssl.conf          # Configuration SSL/TLS
│   ├── 15-mailboxes.conf    # Dossiers par défaut
│   ├── 20-imap.conf         # Configuration IMAP
│   └── 20-pop3.conf         # Configuration POP3
└── dovecot-sql.conf.ext     # Authentification SQL
```

---

## 📁 Répertoires importants

```
/var/mail/                    # Boîtes aux lettres (mbox)
/var/spool/mail/             # Alternative mbox
/home/vmail/                 # Maildir (recommandé)
/var/log/dovecot/            # Logs Dovecot
```

---

## 🔧 Configuration de base

### Fichier principal : `/etc/dovecot/dovecot.conf`

> lmtp = Local Mail Transfer Protocol (pour l'intégration avec Postfix)
> imap = Internet Message Access Protocol (pour les clients IMAP)
> pop3 = Post Office Protocol 3 (pour les clients POP3)

```bash
# Protocoles activés
protocols = imap pop3 lmtp

# Écouter sur toutes les interfaces
listen = *, ::
```

```bash
# Fichier de log principal
log_path = /var/log/dovecot/dovecot.log
info_log_path = /var/log/dovecot/info.log
debug_log_path = /var/log/dovecot/debug.log
```

```bash
# Niveau de log
mail_debug = no
auth_debug = no
auth_debug_passwords = no
```

---

## 📮 Configuration du stockage des emails

### Fichier : `/etc/dovecot/conf.d/10-mail.conf`

**Format Maildir (recommandé)**

```bash
# Location des boîtes emails
mail_location = maildir:/var/mail/vhosts/%d/%n
```

Si vous n'utilisez pas de vmail mais un utilisateur classique alors :

```bash
# Location des boîtes emails
mail_location = maildir:/home/%u/Maildir/
```

---

```bash
# Utilisateur et groupe pour les fichiers
mail_uid = vmail
mail_gid = vmail
```

```bash
# Privilèges
first_valid_uid = 5000
last_valid_uid = 5000
first_valid_gid = 5000
last_valid_gid = 5000
```

---

## 👤 Création de l'utilisateur vmail

```bash
# Créer le groupe vmail
groupadd -g 5000 vmail
```

```bash
# Créer l'utilisateur vmail
useradd -g vmail -u 5000 vmail -d /var/mail/vhosts
```

```bash
# Créer le répertoire et définir les permissions
mkdir -p /var/mail/vhosts
chown -R vmail:vmail /var/mail/vhosts
chmod -R 700 /var/mail/vhosts
```

---

## 🔐 Configuration de l'authentification

### Fichier : `/etc/dovecot/conf.d/10-auth.conf`

```bash
# Désactiver l'authentification en clair sauf sur SSL
disable_plaintext_auth = yes
# Mécanismes d'authentification
auth_mechanisms = plain login
# Inclure les fichiers d'authentification
!include auth-system.conf.ext
# !include auth-sql.conf.ext
# !include auth-ldap.conf.ext
```

---

## 🔑 Authentification système

### Fichier : `/etc/dovecot/conf.d/auth-system.conf.ext`

```bash
# Authentification PAM (utilisateurs système)
passdb {
  driver = pam
}
```

```bash
# Base de données utilisateurs système
userdb {
  driver = passwd
}
```

---

**Note** : L'authentification système convient pour de vrais utilisateurs Unix, mais pas pour nos **utilisateurs virtuels** !

---

## 🔑 Authentification utilisateurs virtuels

### Pour les domaines virtuels (recommandé)

Si vous avez configuré des domaines virtuels avec Postfix (comme dans le module 06), vous avez besoin d'une authentification par fichier.

---

### Créer le fichier d'utilisateurs

```bash
sudo nano /etc/dovecot/users
```

---

Contenu :

```bash
# Format : utilisateur@domaine:{PLAIN}motdepasse
johndoe@andromed.cloud:{PLAIN}MotDePasse123!
janedoe@andromed.cloud:{PLAIN}MotDePasse456!
contact@andromed.cloud:{PLAIN}MotDePasse789!
admin@andromed.cloud:{PLAIN}AdminPass123!
```

---

⚠️ **Important** : En production, utilisez des hash (SHA512-CRYPT) !

```bash
# Générer un hash sécurisé
doveadm pw -s SHA512-CRYPT
```

---

### Créer le fichier de configuration

```bash
sudo nano /etc/dovecot/conf.d/auth-passwdfile.conf.ext
```

---

Contenu :

```bash
passdb {
  driver = passwd-file
  args = scheme=PLAIN username_format=%u /etc/dovecot/users
}

userdb {
  driver = static
  args = uid=vmail gid=vmail home=/var/mail/vhosts/%d/%n
}
```

---

### Activer cette authentification

Dans `/etc/dovecot/conf.d/10-auth.conf` :

```bash
# Désactiver l'auth système
#!include auth-system.conf.ext

# Activer l'auth par fichier pour utilisateurs virtuels
!include auth-passwdfile.conf.ext
```

---

### Permissions du fichier

```bash
sudo chmod 640 /etc/dovecot/users
sudo chown root:dovecot /etc/dovecot/users
```

---

### Redémarrer Dovecot

```bash
sudo systemctl restart dovecot
```

---

### 🧪 Tester l'authentification

```bash
doveadm auth test johndoe@andromed.cloud MotDePasse123!
```

---

Résultat attendu :

```bash
passdb: johndoe@andromed.cloud auth succeeded
userdb: johndoe@andromed.cloud
  home      : /var/mail/vhosts/andromed.cloud/johndoe
  uid       : 5000
  gid       : 5000
```

---

## 🔌 Configuration des ports et services

### Fichier : `/etc/dovecot/conf.d/10-master.conf`

**Ports standards**
- IMAP : **143** (non chiffré) / **993** (SSL/TLS)
- POP3 : **110** (non chiffré) / **995** (SSL/TLS)
- LMTP : **24** (local uniquement)

---

## 📡 Configuration du service IMAP

Ce qu'on va faire ici c'est activer les protocoles IMAP et IMAPS.

```bash
service imap-login {
  inet_listener imap {
    port = 143
    # Désactiver si vous n'utilisez que SSL
    #port = 0
  }
  
  inet_listener imaps {
    port = 993
    ssl = yes
  }
}
```

---

## 📮 Configuration du service POP3

Ce qu'on va faire ici c'est activer le protocole POP3 et POP3S.

```bash
service pop3-login {
  inet_listener pop3 {
    port = 110
    # Désactiver si vous n'utilisez que SSL
    #port = 0
  }
  # Activer si vous utilisez SSL
  inet_listener pop3s {
    port = 995
    ssl = yes
  }
}
```

---

## 🚀 Configuration du service LMTP

### Pour l'intégration avec Postfix

Ce qu'on va faire ici c'est activer le protocole LMTP.

Pourquoi l'activer ? Parce que Postfix va utiliser LMTP pour envoyer les emails à Dovecot.

```bash
service lmtp {
  unix_listener /var/spool/postfix/private/dovecot-lmtp {
    mode = 0600
    user = postfix
    group = postfix
  }
}
```

---

## 🔐 Configuration de l'authentification SMTP

### Pour l'envoi via Postfix

Ce qu'on va faire ici c'est activer l'authentification SMTP via Dovecot.
Comme ça on va pouvoir se connecter à Dovecot avec Postfix.

```bash
service auth {
  unix_listener /var/spool/postfix/private/auth {
    mode = 0660
    user = postfix
    group = postfix
  }
  
  unix_listener auth-userdb {
    mode = 0600
    user = vmail
  }
}
```

---

## 🔒 Configuration SSL/TLS

### Fichier : `/etc/dovecot/conf.d/10-ssl.conf`

```bash
# Activer SSL (important)
ssl = required
```

```bash
# Certificats SSL (important, si vous utilisez Let's Encrypt => ceux du domaine)
ssl_cert = </etc/letsencrypt/live/mail.example.com/fullchain.pem
ssl_key = </etc/letsencrypt/live/mail.example.com/privkey.pem
```

```bash
# Chaîne de certificats
ssl_ca = </etc/letsencrypt/live/mail.example.com/chain.pem
```

---

```bash
# Protocoles et chiffrements modernes (2025)
ssl_min_protocol = TLSv1.2
ssl_cipher_list = ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
ssl_prefer_server_ciphers = yes
```

```bash
# Paramètres DH (Diffie-Hellman)
ssl_dh = </etc/dovecot/dh.pem
```

---

## 🔑 Génération des paramètres DH

```bash
# Générer des paramètres DH 4096 bits (peut prendre du temps)
openssl dhparam -out /etc/dovecot/dh.pem 4096
```

---

```bash
# Permissions
chmod 600 /etc/dovecot/dh.pem
chown root:root /etc/dovecot/dh.pem
```

---

## 🔥 Configuration du Firewall

### UFW (Ubuntu/Debian)

```bash
# IMAP
ufw allow 143/tcp comment 'Dovecot IMAP'
ufw allow 993/tcp comment 'Dovecot IMAPS'
```

```bash
# POP3
ufw allow 110/tcp comment 'Dovecot POP3'
ufw allow 995/tcp comment 'Dovecot POP3S'
```

```bash
# Recharger le firewall
ufw reload
```

---

## 🔥 Configuration Firewalld (CentOS/RHEL)

```bash
# IMAP
firewall-cmd --permanent --add-service=imap
firewall-cmd --permanent --add-service=imaps
```

```bash
# POP3
firewall-cmd --permanent --add-service=pop3
firewall-cmd --permanent --add-service=pop3s
```

```bash
# Recharger
firewall-cmd --reload
```

---

## 🔗 Intégration avec Postfix

### Configuration de Postfix pour utiliser Dovecot

**Fichier `/etc/postfix/main.cf`**

```bash
# Utiliser Dovecot LMTP pour la livraison locale (dans le main.cf de postfix)
mailbox_transport = lmtp:unix:private/dovecot-lmtp
```

---

```bash
# Authentification SMTP via Dovecot (dans le main.cf de postfix)
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
```

---

**SASL = Simple Authentication and Security Layer**

Protocole qui permet l'authentification sécurisée des utilisateurs pour l'envoi d'emails via SMTP. Il évite que n'importe qui puisse utiliser votre serveur comme relais.

```bash
# Options SASL (dans le main.cf de postfix)
smtpd_sasl_security_options = noanonymous
smtpd_sasl_local_domain = $myhostname
broken_sasl_auth_clients = yes
```

---

```bash
# Restreindre le relais aux utilisateurs authentifiés
# N'oubliez pas d'activer le paramètre smtpd_sasl_auth_enable = yes dans le fichier main.cf de postfix
smtpd_relay_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    # + les autres restrictions que nous avons vu avant
```

---

## 🔄 Redémarrage des services

```bash
# Tester la configuration Dovecot
doveconf -n
```

---

```bash
# Redémarrer Dovecot
systemctl restart dovecot
```

---

```bash
# Redémarrer Postfix
systemctl restart postfix
```

---

```bash
# Vérifier les status
systemctl status dovecot
systemctl status postfix
```

---

## 📧 Configuration Apple Mail

### Étape 1 : Ajouter un compte

1. Ouvrir **Mail**
2. **Mail** → **Comptes** → **Ajouter un compte...**
3. Sélectionner **Autre compte Mail...**
4. Cliquer sur **Compte Mail**

---

### Étape 2 : Informations du compte

**Informations à saisir :**

- **Nom** : Votre nom complet
- **Adresse e-mail** : `votre.email@example.com`
- **Mot de passe** : Votre mot de passe

---

### Étape 3 : Configuration manuelle

Si la configuration automatique échoue :

**Serveur de réception (IMAP)**
- **Type** : IMAP
- **Serveur** : `mail.example.com`
- **Port** : `993`
- **SSL** : Activé
- **Authentification** : Mot de passe

---

**Serveur d'envoi (SMTP)**
- **Serveur** : `mail.example.com`
- **Port** : `587` (ou `465` pour SMTPS)
- **SSL** : Activé
- **Authentification** : Mot de passe
- **Nom d'utilisateur** : `votre.email@example.com`

---

### Étape 4 : Paramètres avancés

1. Aller dans **Préférences** → **Comptes**
2. Sélectionner votre compte
3. Onglet **Avancé**

---

**Recommandations :**
- ✅ Activer **Utiliser IDLE si le serveur le prend en charge**
- ✅ Définir **Supprimer les copies sur le serveur** : Jamais (pour IMAP)
- ✅ Activer **Stocker les brouillons sur le serveur**
- ✅ Activer **Stocker les messages envoyés sur le serveur**

---

## 📧 Configuration Outlook (Windows)

### Étape 1 : Ajouter un compte

1. Ouvrir **Outlook**
2. **Fichier** → **Ajouter un compte**
3. Entrer votre adresse email
4. Cliquer sur **Options avancées**
5. Cocher **Configurer manuellement mon compte**

---

### Étape 2 : Choisir le type de compte

- Sélectionner **IMAP**

---

### Étape 3 : Configuration IMAP

**Courrier entrant**
- **Serveur** : `mail.example.com`
- **Port** : `993`
- **Méthode de chiffrement** : SSL/TLS
- **Méthode d'authentification** : Authentification par mot de passe normale

---

**Courrier sortant**
- **Serveur** : `mail.example.com`
- **Port** : `587`
- **Méthode de chiffrement** : STARTTLS
- **Méthode d'authentification** : Authentification par mot de passe normale

---

**Informations de connexion**
- **Nom d'utilisateur** : `votre.email@example.com`
- **Mot de passe** : Votre mot de passe

---

### Étape 4 : Paramètres avancés

1. Clic droit sur le compte → **Paramètres du compte**
2. **Plus de paramètres**
3. Onglet **Serveur sortant**

---

**Configuration SMTP :**
- ✅ Cocher **Mon serveur sortant (SMTP) requiert une authentification**
- ✅ Sélectionner **Utiliser les mêmes paramètres que mon serveur de courrier entrant**

---

### Étape 5 : Dossiers

Onglet **Avancé** :
- Définir le comportement des dossiers supprimés
- Configurer le stockage des éléments envoyés

---

## 📧 Configuration Outlook (Mac)

### Configuration similaire à Windows

**Différences principales :**

1. **Outlook** → **Préférences** → **Comptes**
2. Cliquer sur **+** → **Nouveau compte**
3. Saisir l'adresse email
4. Cliquer sur **Configurer manuellement**

---

Les paramètres serveurs sont identiques :
- **IMAP** : `mail.example.com:993` (SSL/TLS)
- **SMTP** : `mail.example.com:587` (STARTTLS)

---

## 📱 Configuration clients mobiles

### iOS (iPhone/iPad)

1. **Réglages** → **Mail** → **Comptes**
2. **Ajouter un compte** → **Autre**
3. **Ajouter un compte Mail**

---

**Informations à saisir :**
- **Nom** : Votre nom
- **Adresse** : `votre.email@example.com`
- **Mot de passe** : Votre mot de passe
- **Description** : Nom du compte

---

**Serveur de réception :**
- **Nom d'hôte** : `mail.example.com`
- **Nom d'utilisateur** : `votre.email@example.com`
- **Mot de passe** : Votre mot de passe

---

**Serveur d'envoi :**
- **Nom d'hôte** : `mail.example.com`
- **Nom d'utilisateur** : `votre.email@example.com`
- **Mot de passe** : Votre mot de passe

---

### Android (Gmail App)

1. Ouvrir **Gmail**
2. Menu → **Paramètres**
3. **Ajouter un compte** → **Autre**

---

**Configuration automatique ou manuelle :**
- Type : **IMAP personnel**
- Serveur : `mail.example.com`
- Port : **993**
- Sécurité : **SSL/TLS**

---

## 🔍 Tests et vérification

### Tester l'authentification

```bash
# Tester l'auth avec doveadm
doveadm auth test utilisateur@example.com
```

---

### Vérifier les ports ouverts

```bash
# Vérifier les ports en écoute
netstat -tlnp | grep dovecot
```

---

```bash
# Alternative avec ss
ss -tlnp | grep dovecot
```

---

### Résultat attendu

```
tcp  0  0  0.0.0.0:143    0.0.0.0:*  LISTEN  1234/dovecot
tcp  0  0  0.0.0.0:993    0.0.0.0:*  LISTEN  1234/dovecot
tcp  0  0  0.0.0.0:110    0.0.0.0:*  LISTEN  1234/dovecot
tcp  0  0  0.0.0.0:995    0.0.0.0:*  LISTEN  1234/dovecot
```

---

## 🧪 Tester IMAP manuellement

```bash
# Connexion IMAP non chiffrée (test uniquement)
telnet mail.example.com 143
```

---

Commandes à tester :

```
a001 LOGIN utilisateur@example.com password
a002 LIST "" "*"
a003 SELECT INBOX
a004 LOGOUT
```

---

## 🧪 Tester IMAPS avec OpenSSL

```bash
# Connexion IMAPS chiffrée
openssl s_client -connect mail.example.com:993
```

---

Puis tester l'authentification :

```
a001 LOGIN utilisateur@example.com password
a002 LIST "" "*"
a003 LOGOUT
```

---

## 🧪 Tester POP3 manuellement

```bash
# Connexion POP3 non chiffrée
telnet mail.example.com 110
```

---

Commandes à tester :

```
USER utilisateur@example.com
PASS password
STAT
LIST
QUIT
```

---

## 🧪 Tester POP3S avec OpenSSL

```bash
# Connexion POP3S chiffrée
openssl s_client -connect mail.example.com:995
```

---

## 📊 Surveillance et logs

### Fichiers de logs

```bash
# Log principal
tail -f /var/log/dovecot/dovecot.log
```

```bash
# Log d'information
tail -f /var/log/dovecot/info.log
```

```bash
# Log de debug (si activé)
tail -f /var/log/dovecot/debug.log
```

---

### Activer les logs de debug

**Fichier `/etc/dovecot/dovecot.conf`**

```bash
# Activer temporairement pour déboguer
mail_debug = yes
auth_debug = yes
auth_debug_passwords = yes
auth_verbose = yes
```

---

⚠️ **Attention** : Désactiver après débogage (les mots de passe apparaissent en clair)

## 🔧 Commandes utiles doveadm

### Gestion des utilisateurs

```bash
# Lister les boîtes aux lettres
doveadm mailbox list -u utilisateur@example.com
```

```bash
# Vérifier le quota d'un utilisateur
doveadm quota get -u utilisateur@example.com
```

```bash
# Forcer une réindexation
doveadm index -u utilisateur@example.com INBOX
```

---

```bash
# Purger les emails marqués comme supprimés
doveadm expunge -u utilisateur@example.com mailbox INBOX all
```

## 🔄 Recharger la configuration

```bash
# Recharger sans couper les connexions
doveadm reload
```

```bash
# Lister les connexions actives
doveadm who
```

---

```bash
# Déconnecter un utilisateur
doveadm kick utilisateur@example.com
```

---

## 🚨 Troubleshooting courant

### Problème : Impossible de se connecter

**Vérifications :**

1. Vérifier que Dovecot est démarré

```bash
systemctl status dovecot
```

2. Vérifier les ports ouverts

```bash
ss -tlnp | grep dovecot
```

3. Vérifier le firewall

```bash
# UFW
ufw status

# Firewalld
firewall-cmd --list-all
```

4. Vérifier les certificats SSL

```bash
openssl s_client -connect mail.example.com:993 -showcerts
```

---

### Problème : Erreur d'authentification

**Vérifications :**

```bash
# Tester l'authentification
doveadm auth test utilisateur@example.com
```

```bash
# Vérifier les logs
tail -100 /var/log/dovecot/dovecot.log | grep auth
```

```bash
# Vérifier les permissions
ls -la /var/mail/vhosts/
```

---

### Problème : Certificat SSL invalide

**Vérifications :**

1. Vérifier la validité du certificat

```bash
openssl x509 -in /etc/letsencrypt/live/mail.example.com/cert.pem -text -noout
```

2. Renouveler avec Let's Encrypt

```bash
certbot renew
systemctl restart dovecot
```

3. Vérifier la configuration SSL dans Dovecot

```bash
doveconf -n | grep ssl
```

---

### Problème : Emails non livrés

**Vérifications :**

```bash
# Vérifier l'intégration avec Postfix
postconf | grep mailbox_transport
```

```bash
# Vérifier le socket LMTP
ls -la /var/spool/postfix/private/dovecot-lmtp
```

```bash
# Tester LMTP manuellement
doveadm exec lmtp
```

---

## 📈 Optimisations performances

### Configuration pour serveur haute charge

**Fichier `/etc/dovecot/dovecot.conf`**

```bash
# Augmenter les limites de connexions
default_process_limit = 1000
default_client_limit = 10000
```

---

```bash
# Nombre de processus de login
service imap-login {
  process_min_avail = 10
  service_count = 0
}
```

---

```bash
# Cache d'authentification
auth_cache_size = 100M
auth_cache_ttl = 1 hour
auth_cache_negative_ttl = 1 hour
```

---

## 💾 Configuration des quotas

**Fichier `/etc/dovecot/conf.d/90-quota.conf`**

```bash
plugin {
  quota = maildir:User quota
  quota_rule = *:storage=1GB
  quota_rule2 = Trash:storage=+100M
  quota_warning = storage=95%% quota-warning 95 %u
  quota_warning2 = storage=80%% quota-warning 80 %u
}
```

---

```bash
# Service de notification de quota
service quota-warning {
  executable = script /usr/local/bin/quota-warning.sh
  unix_listener quota-warning {
    user = vmail
  }
}
```

---

## 📧 Configuration Sieve (filtres)

### Activer Sieve

**Fichier `/etc/dovecot/conf.d/20-lmtp.conf`**

```bash
protocol lmtp {
  mail_plugins = $mail_plugins sieve
}
```

---

**Fichier `/etc/dovecot/conf.d/90-sieve.conf`**

```bash
plugin {
  sieve = file:~/sieve;active=~/.dovecot.sieve
  sieve_dir = ~/sieve
  sieve_global_dir = /var/lib/dovecot/sieve/global/
}
```

---

### Exemple de règle Sieve

```bash
require ["fileinto", "mailbox"];

# Déplacer les spams dans Junk
if header :contains "X-Spam-Flag" "YES" {
  fileinto :create "Junk";
  stop;
}
```

---

```bash
# Filtrer par expéditeur
if address :is "from" "newsletter@example.com" {
  fileinto :create "Newsletters";
  stop;
}
```

---

## 📊 Statistiques avec doveadm

```bash
# Statistiques générales
doveadm stats dump
```

---

```bash
# Statistiques par utilisateur
doveadm stats dump user
```

---

```bash
# Statistiques des sessions
doveadm stats dump session
```

---

## 🔐 Sécurité supplémentaire

### Limiter les tentatives de connexion

**Fichier `/etc/dovecot/dovecot.conf`**

```bash
# Protection contre les attaques par force brute
auth_policy_server_url = http://localhost:9090/
auth_policy_server_api_header = Authorization: Bearer secret
auth_policy_hash_mech = sha256
```

---

### Fail2ban pour Dovecot

**Fichier `/etc/fail2ban/jail.local`**

```ini
[dovecot]
enabled = true
port = imap,imaps,pop3,pop3s
filter = dovecot
logpath = /var/log/dovecot/dovecot.log
maxretry = 5
bantime = 3600
```

---

## 🎯 Checklist de configuration

### Avant la mise en production

- ✅ Dovecot installé et démarré
- ✅ Ports 143, 993, 110, 995 ouverts dans le firewall
- ✅ Certificats SSL valides et configurés
- ✅ SSL forcé (disable_plaintext_auth = yes)
- ✅ Intégration avec Postfix fonctionnelle
- ✅ Authentification testée

---

- ✅ Tests manuels IMAP/POP3 réussis
- ✅ Configuration client email validée (Apple Mail, Outlook)
- ✅ Logs activés et surveillés
- ✅ Sauvegardes configurées
- ✅ Quotas définis (si nécessaire)
- ✅ Fail2ban activé
- ✅ DNS MX, SPF, DKIM, DMARC configurés

---

## 📚 Ressources complémentaires

**Documentation officielle :**
- [Dovecot Wiki](https://doc.dovecot.org/)
- [Dovecot Configuration](https://doc.dovecot.org/configuration_manual/)
- [Dovecot + Postfix](https://doc.dovecot.org/configuration_manual/howto/postfix_dovecot_lmtp/)

---

**Outils de test :**
- [IMAP Test Tool](https://mxtoolbox.com/SuperTool.aspx)
- [SSL Labs](https://www.ssllabs.com/ssltest/) - Test SSL/TLS

---

## 🎓 Points clés à retenir

1. **Dovecot complète Postfix** : Postfix envoie/reçoit, Dovecot stocke/distribue
2. **IMAP vs POP3** : IMAP garde les emails sur le serveur (recommandé)
3. **Ports** : 993 (IMAPS) et 587 (SMTP+STARTTLS) en production
4. **SSL/TLS obligatoire** : Jamais de connexions non chiffrées en production
5. **Maildir > Mbox** : Format moderne et performant

---

6. **Authentification** : SASL via Dovecot pour Postfix
7. **LMTP** : Protocole de livraison locale recommandé
8. **Logs** : Toujours surveiller `/var/log/dovecot/`
9. **Sieve** : Filtres côté serveur puissants
10. **Sécurité** : Fail2ban + certificats + chiffrement

---
layout: new-section
---

# 🎯 Exercices pratiques

<div class="mt-2">
  Configuration Dovecot
</div>

---

## 📝 Exercice 1 : Installation de base

**Objectif** : Installer et configurer Dovecot

**Durée** : 20 minutes

---

**Tâches :**

1. Installer Dovecot avec IMAP et POP3
2. Créer l'utilisateur `vmail` (UID/GID 5000)
3. Configurer le répertoire `/var/mail/vhosts`
4. Définir `mail_location` en Maildir

---

5. Activer les protocoles IMAP et POP3
6. Configurer les logs dans `/var/log/dovecot/`
7. Démarrer et activer Dovecot au boot
8. Vérifier que les ports 143, 993, 110, 995 sont en écoute

---

## 📝 Exercice 2 : Configuration SSL/TLS

**Objectif** : Sécuriser Dovecot avec SSL/TLS

**Durée** : 30 minutes

---

**Tâches :**

1. Obtenir un certificat Let's Encrypt pour `mail.example.com`
2. Configurer les chemins des certificats dans `10-ssl.conf`
3. Forcer SSL avec `ssl = required`
4. Définir `ssl_min_protocol = TLSv1.2`
5. Générer les paramètres DH 4096 bits

---

6. Configurer des chiffrements modernes
7. Désactiver les ports non chiffrés (143, 110)
8. Tester la connexion IMAPS avec `openssl s_client`
9. Vérifier le certificat avec SSL Labs

---

## 📝 Exercice 3 : Intégration avec Postfix

**Objectif** : Connecter Dovecot et Postfix

**Durée** : 25 minutes

---

**Tâches :**

1. Configurer le socket LMTP dans `10-master.conf`
2. Configurer le socket d'authentification pour Postfix
3. Modifier `main.cf` de Postfix :
   - `mailbox_transport = lmtp:unix:private/dovecot-lmtp`
   - `smtpd_sasl_type = dovecot`

---

4. Activer l'authentification SMTP
5. Redémarrer Dovecot et Postfix
6. Envoyer un email de test
7. Vérifier la livraison dans le Maildir

---

## 📝 Exercice 4 : Configuration client

**Objectif** : Configurer un client email

**Durée** : 15 minutes

---

**Tâches :**

1. Configurer Apple Mail ou Outlook avec :
   - IMAP : `mail.example.com:993` (SSL/TLS)
   - SMTP : `mail.example.com:587` (STARTTLS)
2. Tester l'envoi d'un email
3. Tester la réception d'un email

---

4. Vérifier la création des dossiers IMAP
5. Configurer les dossiers spéciaux (Envoyés, Brouillons)
6. Tester la synchronisation IMAP

---

## 📝 Exercice 5 : Troubleshooting

**Objectif** : Diagnostiquer et résoudre des problèmes

**Durée** : 20 minutes

---

**Scénarios à résoudre :**

1. **Erreur** : "Authentication failed"
   - Vérifier les logs
   - Tester avec `doveadm auth test`
   - Vérifier les permissions

---

2. **Erreur** : "Connection refused" sur le port 993
   - Vérifier que Dovecot est démarré
   - Vérifier le firewall
   - Vérifier la configuration SSL

---

3. **Erreur** : Emails non livrés dans le Maildir
   - Vérifier le socket LMTP
   - Vérifier la configuration Postfix
   - Consulter les logs de Postfix et Dovecot

---

## 📝 Exercice 6 : Configuration avancée

**Objectif** : Configurer des fonctionnalités avancées

**Durée** : 30 minutes

---

**Tâches :**

1. Configurer les quotas (1GB par utilisateur)
2. Activer Sieve pour les filtres
3. Créer une règle Sieve pour filtrer le spam
4. Configurer Fail2ban pour Dovecot

---

5. Activer le cache d'authentification
6. Optimiser les paramètres de performance
7. Configurer les statistiques avec `doveadm stats`

---

## ✅ Solutions

### Exercice 1 : Installation

```bash
# Installation
apt update
apt install -y dovecot-core dovecot-imapd dovecot-pop3d dovecot-lmtpd
```

---

Le compte vmail est utilisé pour gérer les boîtes aux lettres des utilisateurs virtuels. Il permet à Dovecot de stocker tous les emails sous un seul utilisateur système dédié, simplifiant la gestion des permissions et améliorant la sécurité, sans que chaque adresse email ait un compte système correspondant.

```bash
# Utilisateur vmail
groupadd -g 5000 vmail
useradd -g vmail -u 5000 vmail -d /var/mail/vhosts
mkdir -p /var/mail/vhosts
chown -R vmail:vmail /var/mail/vhosts
chmod -R 700 /var/mail/vhosts
```

---

```bash
# Configuration dans /etc/dovecot/conf.d/10-mail.conf
mail_location = maildir:/var/mail/vhosts/%d/%n
mail_uid = vmail
mail_gid = vmail
first_valid_uid = 5000
```

---

```bash
# Démarrage
systemctl start dovecot
systemctl enable dovecot
systemctl status dovecot
```

---

```bash
# Vérification
ss -tlnp | grep dovecot
```

---

### Exercice 2 : SSL/TLS

```bash
# Certificat Let's Encrypt
certbot certonly --standalone -d mail.example.com
```

---

```bash
# Configuration /etc/dovecot/conf.d/10-ssl.conf
ssl = required
ssl_cert = </etc/letsencrypt/live/mail.example.com/fullchain.pem
ssl_key = </etc/letsencrypt/live/mail.example.com/privkey.pem
ssl_min_protocol = TLSv1.2
```

---

```bash
# Paramètres DH
openssl dhparam -out /etc/dovecot/dh.pem 4096
```

---

```bash
# Désactiver ports non chiffrés dans /etc/dovecot/conf.d/10-master.conf
service imap-login {
  inet_listener imap {
    port = 0
  }
}
service pop3-login {
  inet_listener pop3 {
    port = 0
  }
}
```

---

```bash
# Redémarrer
systemctl restart dovecot
```

---

```bash
# Test
openssl s_client -connect mail.example.com:993
```

---

### Exercice 3 : Intégration Postfix

```bash
# /etc/dovecot/conf.d/10-master.conf
service lmtp {
  unix_listener /var/spool/postfix/private/dovecot-lmtp {
    mode = 0600
    user = postfix
    group = postfix
  }
}
```

---

```bash
service auth {
  unix_listener /var/spool/postfix/private/auth {
    mode = 0660
    user = postfix
    group = postfix
  }
}
```

---

```bash
# /etc/postfix/main.cf
mailbox_transport = lmtp:unix:private/dovecot-lmtp
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
```

---

```bash
# Redémarrer les services
systemctl restart dovecot
systemctl restart postfix
```

---

### Exercice 4 : Configuration client

**Apple Mail :**
- Ajouter un compte → Autre compte Mail
- IMAP : `mail.example.com:993` (SSL)
- SMTP : `mail.example.com:587` (TLS)
- Identifiant complet : `user@example.com`

---

**Outlook :**
- Fichier → Ajouter un compte → Configuration manuelle
- IMAP : `mail.example.com:993` (SSL/TLS)
- SMTP : `mail.example.com:587` (STARTTLS)
- Identifiant complet : `user@example.com`

---

### Exercice 5 : Troubleshooting

**Authentication failed :**

```bash
# Vérifier l'authentification
doveadm auth test user@example.com
```

---

```bash
# Logs
tail -f /var/log/dovecot/dovecot.log
```

---

```bash
# Permissions
ls -la /var/mail/vhosts/
```

---

**Connection refused :**

```bash
# Dovecot actif ?
systemctl status dovecot
```

---

```bash
# Port ouvert ?
ss -tlnp | grep 993
```

---

```bash
# Firewall ?
ufw status
ufw allow 993/tcp
```

---

**Emails non livrés :**

```bash
# Socket LMTP existe ?
ls -la /var/spool/postfix/private/dovecot-lmtp
```

---

```bash
# Configuration Postfix
postconf mailbox_transport
```

---

```bash
# Logs
tail -f /var/log/mail.log
tail -f /var/log/dovecot/dovecot.log
```

---

### Exercice 6 : Configuration avancée

```bash
# /etc/dovecot/conf.d/90-quota.conf
plugin {
  quota = maildir:User quota
  quota_rule = *:storage=1GB
}
```

---

```bash
# Activer quota dans /etc/dovecot/conf.d/10-mail.conf
mail_plugins = $mail_plugins quota
```

---

```bash
# Sieve dans /etc/dovecot/conf.d/20-lmtp.conf
protocol lmtp {
  mail_plugins = $mail_plugins sieve
}
```

---

```bash
# Fail2ban
apt install fail2ban
```

---

Fichier `/etc/fail2ban/jail.local` :

```ini
[dovecot]
enabled = true
port = imap,imaps,pop3,pop3s
filter = dovecot
logpath = /var/log/dovecot/dovecot.log
maxretry = 5
```

---

```bash
# Redémarrer
systemctl restart fail2ban
systemctl restart dovecot
```

---

## 🎉 Fin du module Dovecot

Vous savez maintenant :

- ✅ Installer et configurer Dovecot
- ✅ Intégrer Dovecot avec Postfix
- ✅ Sécuriser avec SSL/TLS
- ✅ Configurer les clients email
- ✅ Diagnostiquer les problèmes
- ✅ Optimiser les performances

