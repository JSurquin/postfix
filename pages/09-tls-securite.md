---
layout: new-section
routeAlias: 'tls-securite'
---

<a name="tls-securite" id="tls-securite"></a>

# TLS et Sécurité

🔒 Chiffrer les communications et protéger les données

---

# Introduction au TLS

Sans TLS, vos emails circulent **en clair** sur Internet !

Tout intermédiaire (FAI, gouvernement, hacker) peut les lire.

TLS (Transport Layer Security) chiffre les communications entre serveurs SMTP.

---

## Analogie

**Sans TLS** : Envoyer une carte postale
- Tout le monde peut la lire en chemin

**Avec TLS** : Envoyer une lettre scellée
- Seul le destinataire peut l'ouvrir

---

## TLS vs SSL

SSL (Secure Sockets Layer) est l'ancien nom.

TLS (Transport Layer Security) est la version moderne.

---

**Versions** :
- SSL 2.0 : Obsolète et vulnérable
- SSL 3.0 : Obsolète et vulnérable
- TLS 1.0 : Déprécié
- TLS 1.1 : Déprécié
- TLS 1.2 : OK mais ancien
- **TLS 1.3** : Recommandé en 2025 ✅

---

## Types de chiffrement SMTP

### 📧 SMTP classique (port 25)

**STARTTLS** : Commence en clair, puis upgrade vers TLS

```
220 mail.example.com ESMTP
EHLO client.example.com
250-STARTTLS
250 HELP
STARTTLS
220 Ready to start TLS
[Connexion chiffrée]
```

---

### 🔐 SMTPS (port 465)

**TLS immédiat** : Chiffré dès la connexion

Obsolète depuis 1998, mais... revenu en 2018 !

Supporté par Gmail, Outlook, etc.

---

### 📬 Submission (port 587)

**STARTTLS obligatoire** : Pour les clients qui envoient des emails

Standard recommandé en 2025.

---

# Obtenir un certificat SSL

## Let's Encrypt (Gratuit !)

Let's Encrypt fournit des certificats gratuits, valables 90 jours, renouvelables automatiquement.

---

### 📦 Installation de Certbot

```bash
# Ubuntu/Debian
sudo apt install certbot

# Rocky Linux
sudo dnf install certbot
```

---

### 🔑 Obtenir un certificat

```bash
sudo certbot certonly --standalone -d mail.example.com
```

---

**Paramètres** :
- `certonly` : Obtenir le certificat seulement (sans configurer de serveur web)
- `--standalone` : Serveur web temporaire (port 80 doit être libre)
- `-d mail.example.com` : Domaine pour lequel obtenir le certificat

---

**Alternative si port 80 utilisé** :

```bash
sudo certbot certonly --webroot -w /var/www/html -d mail.example.com
```

---

### 📂 Emplacement des certificats

```
/etc/letsencrypt/live/mail.example.com/
├── cert.pem          # Certificat du serveur
├── chain.pem         # Chaîne de certification
├── fullchain.pem     # Certificat + chaîne (recommandé)
├── privkey.pem       # Clé privée
└── README
```

---

### 🔄 Renouvellement automatique

```bash
# Tester le renouvellement
sudo certbot renew --dry-run

# Configurer le cron
sudo systemctl enable certbot-renew.timer
sudo systemctl start certbot-renew.timer
```

---

Ou manuellement dans crontab :

```bash
0 0 * * * certbot renew --quiet --post-hook "systemctl reload postfix"
```

---

## Certificat auto-signé (pour tests)

Si vous n'avez pas de domaine public, créez un certificat auto-signé.

⚠️ **Ne pas utiliser en production !**

---

### 🔧 Générer un certificat auto-signé

```bash
# Créer le répertoire
sudo mkdir -p /etc/postfix/ssl

# Générer clé et certificat
sudo openssl req -new -x509 -days 365 -nodes \
  -out /etc/postfix/ssl/cert.pem \
  -keyout /etc/postfix/ssl/key.pem
```

---

**Questions** :

```
Country Name: FR
State or Province Name: Nord
Locality Name: Lille
Organization Name: MonEntreprise
Organizational Unit Name: IT
Common Name: mail.example.com
Email Address: admin@example.com
```

---

**Common Name** doit correspondre au hostname de votre serveur !

---

### 🔒 Permissions

```bash
sudo chmod 600 /etc/postfix/ssl/key.pem
sudo chmod 644 /etc/postfix/ssl/cert.pem
sudo chown root:root /etc/postfix/ssl/*
```

---

# Configuration TLS dans Postfix

## TLS pour les connexions entrantes (SMTPD)

```sql
# Certificats
smtpd_tls_cert_file = /etc/letsencrypt/live/mail.example.com/fullchain.pem
smtpd_tls_key_file = /etc/letsencrypt/live/mail.example.com/privkey.pem

# Niveau de sécurité
smtpd_tls_security_level = may
```

---

**Niveaux de sécurité** :

- `none` : Pas de TLS
- `may` : TLS si le client le supporte (recommandé pour port 25)
- `encrypt` : TLS obligatoire
- `dane` : TLS avec validation DANE
- `dane-only` : TLS DANE uniquement
- `fingerprint` : Validation par empreinte
- `verify` : TLS avec vérification du certificat client
- `secure` : TLS avec vérification stricte

---

### 🔐 Configuration recommandée pour port 25

```sql
smtpd_tls_security_level = may
smtpd_tls_auth_only = yes
smtpd_tls_loglevel = 1
```

---

### 🔒 Configuration recommandée pour port 587

```sql
# Port 587 = submission = TLS obligatoire
smtpd_tls_security_level = encrypt
smtpd_tls_auth_only = yes
```

---

## TLS pour les connexions sortantes (SMTP)

```sql
# Niveau de sécurité
smtp_tls_security_level = may

# Vérifier les certificats
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

# Logging
smtp_tls_loglevel = 1
```

---

## Versions TLS acceptées

```sql
# TLS 1.2 et 1.3 uniquement (recommandé 2025)
smtpd_tls_protocols = >=TLSv1.2
smtp_tls_protocols = >=TLSv1.2

# TLS 1.3 uniquement (très strict)
smtpd_tls_protocols = >=TLSv1.3
smtp_tls_protocols = >=TLSv1.3
```

---

## Ciphers (Algorithmes de chiffrement)

```sql
# Ciphers forts uniquement
smtpd_tls_mandatory_ciphers = high

# Exclure les algorithmes faibles
smtpd_tls_exclude_ciphers = 
    aNULL, eNULL, EXPORT, DES, RC4, MD5,
    PSK, aECDH, EDH-DSS-DES-CBC3-SHA,
    EDH-RSA-DES-CBC3-SHA, KRB5-DES-CBC3-SHA
```

---

## Session cache

Pour améliorer les performances, Postfix peut mettre en cache les sessions TLS.

```sql
# Cache côté serveur
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtpd_tls_session_cache_timeout = 3600s

# Cache côté client
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
smtp_tls_session_cache_timeout = 3600s
```

---

## Configuration complète TLS

### 📝 Dans main.cf

```sql
# === TLS GÉNÉRAL ===
tls_random_source = dev:/dev/urandom

# === TLS ENTRANT (SMTPD) ===
smtpd_tls_cert_file = /etc/letsencrypt/live/mail.example.com/fullchain.pem
smtpd_tls_key_file = /etc/letsencrypt/live/mail.example.com/privkey.pem
smtpd_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```

---

```sql
smtpd_tls_security_level = may
smtpd_tls_auth_only = yes
smtpd_tls_protocols = >=TLSv1.2
smtpd_tls_mandatory_protocols = >=TLSv1.2
smtpd_tls_mandatory_ciphers = high
smtpd_tls_exclude_ciphers = aNULL, eNULL, EXPORT, DES, RC4, MD5, PSK, aECDH, EDH-DSS-DES-CBC3-SHA, EDH-RSA-DES-CBC3-SHA, KRB5-DES-CBC3-SHA
```

---

```sql
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtpd_tls_session_cache_timeout = 3600s
smtpd_tls_loglevel = 1
smtpd_tls_received_header = yes

# === TLS SORTANT (SMTP) ===
smtp_tls_security_level = may
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```

---

```sql
smtp_tls_protocols = >=TLSv1.2
smtp_tls_mandatory_protocols = >=TLSv1.2
smtp_tls_mandatory_ciphers = high
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
smtp_tls_session_cache_timeout = 3600s
smtp_tls_loglevel = 1
```

---

## Configuration du port 587 (Submission)

### 📝 Dans master.cf

```sql
submission inet n       -       y       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_tls_auth_only=yes
  -o smtpd_reject_unlisted_recipient=no
  -o smtpd_client_restrictions=permit_sasl_authenticated,reject
  -o smtpd_relay_restrictions=permit_sasl_authenticated,reject
  -o milter_macro_daemon_name=ORIGINATING
```

---

### ♻️ Recharger Postfix

```bash
sudo postfix check
sudo systemctl reload postfix
```

---

## Tester TLS

### 🔍 Test avec openssl

```bash
openssl s_client -connect mail.example.com:25 -starttls smtp
```

---

**Vérifiez** :

```
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    ...
Verify return code: 0 (ok)
```

---

### 📧 Test avec telnet

```bash
telnet mail.example.com 25
```

---

```
EHLO test
250-mail.example.com
250-SIZE 52428800
250-STARTTLS
250 HELP

STARTTLS
220 Ready to start TLS
```

✅ STARTTLS disponible !

---

### 🌐 Tests en ligne

**SSL Labs** (pour HTTPS) : https://www.ssllabs.com/ssltest/

**CheckTLS** (pour SMTP) : https://www.checktls.com/

**MXToolbox** : https://mxtoolbox.com/diagnostic.aspx

---

## Forcer TLS pour certains domaines

Si vous échangez des emails sensibles avec un partenaire, forcez TLS.

---

### 📋 Fichier /etc/postfix/tls_policy

```sql
# Format: domaine niveau

# Forcer TLS pour ce domaine
secure-partner.com     encrypt

# Forcer TLS + vérifier certificat
banking-partner.com    verify

# Pas de TLS (si serveur ancien)
old-system.com         none
```

---

### ⚙️ Configuration

```sql
smtp_tls_policy_maps = hash:/etc/postfix/tls_policy
```

---

```bash
sudo postmap /etc/postfix/tls_policy
sudo systemctl reload postfix
```

---

## MTA-STS et DANE

### 🔐 MTA-STS

MTA-STS (Mail Transfer Agent Strict Transport Security) force les serveurs à utiliser TLS.

---

**Créer** `/.well-known/mta-sts.txt` sur `mta-sts.example.com` :

```
version: STSv1
mode: enforce
mx: mail.example.com
max_age: 86400
```

---

**Ajouter un enregistrement DNS** :

```
Type: TXT
Nom: _mta-sts.example.com
Valeur: v=STSv1; id=20250101T000000
```

---

### 🔒 DANE

DANE (DNS-based Authentication of Named Entities) utilise DNSSEC pour valider les certificats.

Plus complexe, nécessite DNSSEC activé sur votre domaine.

---

# Sécurité avancée

## Authentification SASL

SASL (Simple Authentication and Security Layer) permet aux clients de s'authentifier pour envoyer des emails.

---

### 🔗 Avec Dovecot (recommandé)

```sql
# Dans main.cf
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
smtpd_sasl_local_domain = $mydomain
smtpd_sasl_tls_security_options = noanonymous
```

---

### 📂 Socket Dovecot

Dans `/etc/dovecot/conf.d/10-master.conf` :

```
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
sudo systemctl restart dovecot
sudo systemctl reload postfix
```

---

## Restrictions d'authentification

```sql
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination
```

---

Les utilisateurs authentifiés peuvent envoyer des emails même s'ils ne sont pas dans `mynetworks`.

---

## Chroot et Privilèges

Par défaut, Postfix tourne en chroot pour limiter les dégâts en cas de compromission.

---

**Dans** `/etc/postfix/master.cf` :

```sql
# service type  private unpriv  chroot  wakeup  maxproc command
smtp      inet  n       -       y       -       -       smtpd
```

Le `y` dans la colonne `chroot` active le chroot.

---

## Limitations de connexions

```sql
# Limite de connexions simultanées par IP
smtpd_client_connection_count_limit = 10

# Taux de connexions
smtpd_client_connection_rate_limit = 30
anvil_rate_time_unit = 60s

# Messages par connexion
smtpd_client_message_rate_limit = 100
```

---

## Désactiver les commandes dangereuses

```sql
# Désactiver VRFY (vérifier si adresse existe)
disable_vrfy_command = yes

# Désactiver ETRN (trigger queue run)
smtpd_etrn_restrictions = reject
```

---

## Masquer les informations système

```sql
# Ne pas révéler la version
smtpd_banner = $myhostname ESMTP

# Headers anonymisés
smtpd_discard_ehlo_keywords = silent-discard, dsn
```

---

## Firewall

### 🔥 UFW (Ubuntu)

```bash
sudo ufw allow 25/tcp
sudo ufw allow 587/tcp
sudo ufw allow 465/tcp
```

---

### 🔥 firewalld (Rocky Linux)

```bash
sudo firewall-cmd --permanent --add-service=smtp
sudo firewall-cmd --permanent --add-service=smtp-submission
sudo firewall-cmd --permanent --add-service=smtps
sudo firewall-cmd --reload
```

---

## Fail2ban

Bloquer les IPs qui tentent des attaques par force brute.

---

### 📦 Installation

```bash
# Ubuntu/Debian
sudo apt install fail2ban

# Rocky Linux
sudo dnf install fail2ban
```

---

### ⚙️ Configuration

Fichier `/etc/fail2ban/jail.local` :

```ini
[postfix-sasl]
enabled = true
port = smtp,submission,smtps
filter = postfix-sasl
logpath = /var/log/mail.log
maxretry = 3
bantime = 3600
```

---

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

---

### 📊 Surveiller les bans

```bash
sudo fail2ban-client status postfix-sasl
```

---

## Checklist de sécurité

✅ TLS activé avec certificat valide

✅ TLS 1.2 minimum, idéalement TLS 1.3

✅ Ciphers forts uniquement

✅ Authentification SASL configurée

✅ Port 587 avec TLS obligatoire

---

✅ Rate limiting activé

✅ Fail2ban configuré

✅ Firewall restreint aux ports nécessaires

✅ Commandes dangereuses désactivées (VRFY, ETRN)

✅ Bannière anonymisée

---

✅ Logs activés et surveillés

✅ Mises à jour régulières du système

✅ SPF, DKIM, DMARC configurés

---

## Exercices pratiques

### 🎯 Exercice 1 : Obtenir un certificat

1. Installez Certbot
2. Obtenez un certificat Let's Encrypt
3. Vérifiez qu'il est valide

---

### 🎯 Exercice 2 : Configurer TLS

1. Configurez TLS dans main.cf
2. Restreignez aux versions TLS 1.2+
3. Rechargez Postfix

---

### 🎯 Exercice 3 : Tester TLS

1. Testez avec `openssl s_client`
2. Vérifiez la version TLS utilisée
3. Testez sur https://www.checktls.com/

---

### 🎯 Exercice 4 : Port 587

1. Configurez le port 587 dans master.cf
2. Forcez TLS sur ce port
3. Testez avec un client mail

---

## Points clés à retenir

### 💡 TLS

**Port 25** : `smtpd_tls_security_level = may`

**Port 587** : `smtpd_tls_security_level = encrypt`

**Versions** : >= TLS 1.2 minimum

---

### 💡 Certificats

**Let's Encrypt** : Gratuit, renouvelable automatiquement

**Chemins** : `/etc/letsencrypt/live/domain/`

**Renouvellement** : Tous les 90 jours

---

### 💡 Sécurité

**SASL** : Authentification obligatoire pour envoyer

**Rate limiting** : Limiter les connexions abusives

**Fail2ban** : Bloquer les IPs attaquantes

---

## Prochaine étape

Votre serveur est maintenant bien sécurisé ! Passons à la **surveillance et l'analyse des logs** ! 📊

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Logs et surveillance <carbon:arrow-right class="inline"/>
  </span>
</div>

