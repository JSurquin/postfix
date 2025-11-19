---
layout: new-section
routeAlias: 'configuration-main-cf'
---

<a name="configuration-main-cf" id="configuration-main-cf"></a>

# Configuration du main.cf

<div class="mt-2">
  📝 Maîtriser le fichier de configuration principal de Postfix
</div>

---

# Introduction au main.cf

Le fichier `/etc/postfix/main.cf` est le **cerveau** de Postfix. C'est là que tout se configure !

---

## Structure du fichier

### 📋 Format

Très simple :

```bash
# Commentaire
paramètre = valeur

# Valeurs multiples
paramètre = valeur1,
    valeur2,
    valeur3
```

---

### 🔍 Règles de syntaxe

**Commentaires** : `# texte`

- **Continuation** : indentation ou backslash

- **Variables** : `$mydomain`

- **Listes** : virgules ou espaces

```bash
# Commentaire
myhostname = mail.example.com  # Commentaire fin de ligne

# Continuation avec indentation
smtpd_recipient_restrictions =
    permit_mynetworks,
    reject_unauth_destination

# Variables
mydomain = example.com
myorigin = $mydomain

# Listes
mydestination = $myhostname, localhost, $mydomain
```

---

### ⚙️ Modifier le fichier

```bash
# Éditer
sudo nano /etc/postfix/main.cf

# Vérifier la syntaxe
sudo postfix check

# Appliquer les changements
sudo systemctl reload postfix
```

---

## Paramètres d'identité

### 🏷️ myhostname

Le nom complet de votre serveur (FQDN - Fully Qualified Domain Name)

```bash
myhostname = mail.example.com
```

**Important** : Doit correspondre au PTR (reverse DNS) - Utilisé dans les en-têtes des emails - Première chose que voient les autres serveurs

---

### 🌐 mydomain

Le nom de votre domaine

```bash
mydomain = example.com
```

Par défaut, Postfix déduit `mydomain` depuis `myhostname` :

```
myhostname = mail.example.com
→ mydomain = example.com (automatique)
```

---

### 📤 myorigin

Le domaine qui apparaît dans le champ `From:` des emails locaux

```bash
myorigin = $mydomain
```

**Exemple** : Sans `myorigin`, un email de `root` apparaîtrait comme `root@mail.example.com`. Avec `myorigin = $mydomain`, il apparaît comme `root@example.com` (plus propre !)

---

## Paramètres réseau

### 🔌 inet_interfaces

Interfaces réseau sur lesquelles Postfix écoute

```bash
# Écouter partout (défaut pour serveur mail)
inet_interfaces = all

# Seulement localhost (pour tests)
inet_interfaces = localhost

# Interfaces spécifiques
inet_interfaces = 192.168.1.10, 127.0.0.1
```

<small>

**Pourquoi choisir 192 par exemple ?**

192.168.1.10 est l'adresse IP de votre serveur.

Donc on dit que le serveur écoute sur l'interface 192.168.1.10 et sur l'interface 127.0.0.1.

Cela limite l'exposition du service et améliore la sécurité si le serveur possède plusieurs interfaces ou adresses IP.

**Attention** : Si vous changez vers `all`, assurez-vous que votre firewall est configuré !

</small>

---

### 🌍 inet_protocols

Protocoles IP supportés

```bash
# IPv4 et IPv6 (défaut)
inet_protocols = all

# IPv4 seulement
inet_protocols = ipv4

# IPv6 seulement
inet_protocols = ipv6
```

En 2025, `all` est recommandé, mais si vous n'avez pas d'IPv6 configuré, mettez `ipv4` pour éviter des warnings.

---

### 🚪 proxy_interfaces

Adresses IP externes (si derrière un NAT/proxy)

```bash
# Si votre serveur est en 192.168.1.10 mais exposé en 203.0.113.10
proxy_interfaces = 203.0.113.10
```

Postfix considère ces adresses comme "locales" même si elles ne sont pas directement sur ses interfaces.

> Nous n'allons pas utiliser cette option dans ce cours.

---

## Paramètres de destination

### 📬 mydestination

Domaines pour lesquels Postfix accepte les emails comme destination finale

```bash
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
```

**Exemples** :

```bash
# Serveur mail classique
mydestination = mail.example.com, example.com, localhost

# Null client (n'accepte rien de l'extérieur)
mydestination =

# Plusieurs domaines
mydestination = example.com, example.org, localhost
```

**Attention** : Ne confondez pas `mydestination` et `relay_domains` !
- `mydestination` : Postfix **livre localement**
- `relay_domains` : Postfix **relaie ailleurs**

---

### 🔄 relay_domains

Domaines pour lesquels Postfix accepte de relayer les emails

```bash
# Pas de relais (défaut)
relay_domains =

# Relais pour certains domaines
relay_domains = subsidiary.example.com, partner.com
```

**Important** : Attention aux open relays !

```bash
# ❌ NE JAMAIS FAIRE ÇA
relay_domains = *
```

<small>

Votre serveur deviendrait un relais ouvert, utilisable par tous les spammeurs du monde !

Donc dans notre cas nous allons simplement laisser la valeur par défaut.

</small>

```bash
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
relay_domains = $mydestination
```

---

### 🏠 mynetworks

Réseaux autorisés à envoyer des emails sans authentification

```bash
# Seulement localhost (recommandé)
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128

# Réseau local en plus
mynetworks = 127.0.0.0/8, 192.168.1.0/24

# Détection automatique (déconseillé)
mynetworks_style = subnet
```

**Méthode de détection** :

```bash
# host : Seulement la machine locale
mynetworks_style = host

# subnet : Tout le sous-réseau (dangereux !)
mynetworks_style = subnet

# class : Toute la classe réseau (très dangereux !)
mynetworks_style = class
```

💡 **Bonne pratique** : Spécifiez toujours `mynetworks` manuellement, ne laissez pas Postfix deviner.

---

### 📮 relayhost

Serveur SMTP à utiliser pour envoyer tous les emails sortants

```bash
# Pas de relais (envoi direct)
relayhost =

# Via un serveur SMTP
relayhost = [smtp.example.com]

# Via un serveur avec port spécifique
relayhost = [smtp.example.com]:587
```

**Les crochets [ ]** : Désactivent le lookup MX

Sans crochets : `relayhost = smtp.example.com` → Postfix cherche l'enregistrement MX de smtp.example.com

Avec crochets : `relayhost = [smtp.example.com]` → Postfix se connecte directement à smtp.example.com

**Cas d'usage** : Serveur derrière un FAI qui bloque le port 25 - Application qui envoie via le serveur mail de l'entreprise - Serveur avec IP blacklistée qui passe par un relais propre

---

## Paramètres de stockage

### 📁 home_mailbox

Format de stockage des emails locaux

```bash
# Format Maildir (un fichier par email)
home_mailbox = Maildir/

# Format mbox (tous les emails dans un fichier)
home_mailbox = mail/
```

**Maildir** est recommandé car : Plus sûr (pas de corruption d'un fichier énorme) - Plus rapide (accès concurrent possible) - Compatible avec IMAP - Standard moderne

---

### 💾 message_size_limit

Taille maximum d'un message (headers + body)

```bash
# 50 MB (défaut : 10 MB)
message_size_limit = 52428800

# Illimité (déconseillé)
message_size_limit = 0
```

**Calcul** : 10 MB = 10 * 1024 * 1024 = 10485760 bytes - 50 MB = 50 * 1024 * 1024 = 52428800 bytes

### 📫 mailbox_size_limit

Taille maximum d'une mailbox (format mbox uniquement)

```bash
# Illimité (pour Maildir)
mailbox_size_limit = 0

# 1 GB pour mbox
mailbox_size_limit = 1073741824
```

Avec Maildir, ce paramètre n'a pas de sens (chaque email est un fichier séparé).

---

## Paramètres de bannière et identification

### 🎭 smtpd_banner

Message affiché lors de la connexion SMTP

```bash
# Par défaut (révèle la version)
smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)

# Version sécurisée (masque les détails)
smtpd_banner = $myhostname ESMTP

# Personnalisée
smtpd_banner = $myhostname ESMTP - No spam please
```

**Sécurité** : Ne révélez pas votre version de Postfix !

```
❌ 220 mail.example.com ESMTP Postfix (Ubuntu 3.6.4)
✅ 220 mail.example.com ESMTP
```

---

### 🕐 delay_warning_time

Délai avant d'envoyer un avertissement à l'expéditeur si l'email n'est pas encore délivré

```bash
# Avertissement après 4 heures (défaut)
delay_warning_time = 4h

# Désactiver les avertissements
delay_warning_time = 0h
```

### ⏱️ maximal_queue_lifetime

Durée maximum qu'un message peut rester en file d'attente

```bash
# 5 jours (défaut)
maximal_queue_lifetime = 5d

# 1 jour
maximal_queue_lifetime = 1d
```

---

Après ce délai, un email de bounce est envoyé à l'expéditeur et le message est supprimé.

### ⏰ bounce_queue_lifetime

Durée maximum pour les messages de bounce

```bash
# 5 jours (défaut)
bounce_queue_lifetime = 5d

# 1 jour (bounce abandonnés plus vite)
bounce_queue_lifetime = 1d
```

---

## Paramètres de restrictions

### 🚫 smtpd_recipient_restrictions

Restrictions sur les destinataires (qui peut recevoir des emails)

```bash
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_invalid_hostname,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain
```

---

**Ordre important !** Les règles sont évaluées de haut en bas.

Décryptons :

1. `permit_mynetworks` : Autorise les IPs de `mynetworks`
2. `permit_sasl_authenticated` : Autorise les utilisateurs authentifiés
3. `reject_unauth_destination` : Rejette si destination pas dans `mydestination` ou `relay_domains`
4. `reject_invalid_hostname` : Rejette les hostnames invalides
5. `reject_non_fqdn_recipient` : Rejette si destinataire pas en FQDN
6. `reject_unknown_recipient_domain` : Rejette si le domaine destinataire n'existe pas

---

⚠️ **CRUCIAL** : `reject_unauth_destination` doit **toujours** être présent !

Sans cette règle, votre serveur devient un open relay.


### 🚷 smtpd_sender_restrictions

Restrictions sur les expéditeurs

```bash
smtpd_sender_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain
```

---

### 👋 smtpd_helo_restrictions

Restrictions sur la commande HELO/EHLO

```bash
smtpd_helo_restrictions = 
    permit_mynetworks,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    reject_unknown_helo_hostname
```

---

**Pourquoi c'est important ?**

Beaucoup de spammeurs envoient des HELO invalides :

```
HELO localhost
HELO 192.168.1.1
HELO [203.0.113.10]
```

Ces règles les bloquent !

---

### 🔌 smtpd_client_restrictions

Restrictions sur les clients qui se connectent

```bash
smtpd_client_restrictions = 
    permit_mynetworks,
    reject_unknown_client_hostname,
    check_client_access hash:/etc/postfix/client_access
```

---

## Paramètres d'authentification

### 🔐 smtpd_sasl_auth_enable

Activer l'authentification SASL pour l'envoi d'emails

```bash
smtpd_sasl_auth_enable = yes
```

**SASL** = Simple Authentication and Security Layer

Permet aux clients d'envoyer des emails après authentification (username + password).

---

### 🔑 smtpd_sasl_type

Type de mécanisme SASL

```bash
# SASL basique (pour cette formation)
# smtpd_sasl_type = cyrus
# smtpd_sasl_path = smtpd

# Cyrus SASL
smtpd_sasl_type = cyrus
```

---

### 🛡️ smtpd_sasl_security_options

Options de sécurité SASL

```bash
# Désactiver les méthodes anonymes
smtpd_sasl_security_options = noanonymous

# En TLS, autoriser les méthodes plain
smtpd_sasl_tls_security_options = noanonymous
```

---

### 📝 smtpd_sasl_local_domain

Domaine SASL local

```bash
smtpd_sasl_local_domain = $mydomain
```

## Paramètres TLS

### 🔒 smtpd_tls_cert_file

Chemin vers le certificat SSL

```bash
smtpd_tls_cert_file = /etc/letsencrypt/live/mail.example.com/fullchain.pem
```

---

### 🔑 smtpd_tls_key_file

Chemin vers la clé privée SSL

```bash
smtpd_tls_key_file = /etc/letsencrypt/live/mail.example.com/privkey.pem
```

### 🔐 smtpd_tls_security_level

Niveau de sécurité TLS pour les connexions entrantes

```bash
# Pas de TLS (déconseillé)
smtpd_tls_security_level = none

# TLS si possible (opportuniste)
smtpd_tls_security_level = may

# TLS obligatoire pour tous
smtpd_tls_security_level = encrypt

# TLS obligatoire + vérification certificat
smtpd_tls_security_level = verify
```

---

En 2025, utilisez au minimum `may`, idéalement `encrypt` pour le port 587.

### 📤 smtp_tls_security_level

Niveau de sécurité TLS pour les connexions sortantes

```bash
# TLS si le serveur distant le supporte
smtp_tls_security_level = may

# TLS obligatoire
smtp_tls_security_level = encrypt
```

---

### 🔧 smtpd_tls_protocols

Versions de TLS acceptées

```bash
# TLS 1.2 et 1.3 uniquement (recommandé en 2025)
smtpd_tls_protocols = >=TLSv1.2

# TLS 1.3 uniquement (très strict)
smtpd_tls_protocols = >=TLSv1.3
```

---

⚠️ **Important** : Désactivez TLS 1.0 et 1.1, ils sont obsolètes et vulnérables !

### 📜 smtpd_tls_loglevel

Niveau de logging TLS

```bash
# Minimal (défaut)
smtpd_tls_loglevel = 0

# Debug (pour troubleshooting)
smtpd_tls_loglevel = 1

# Très verbeux
smtpd_tls_loglevel = 2
```

---

## Paramètres de file d'attente

### 📊 queue_run_delay

Fréquence de traitement de la file d'attente

```bash
# Toutes les 5 minutes (défaut)
queue_run_delay = 300s

# Plus rapide (toutes les minutes)
queue_run_delay = 60s
```

---

### 🔢 minimal_backoff_time

Délai minimum avant de retenter l'envoi

```bash
# 5 minutes (défaut)
minimal_backoff_time = 300s

# 1 minute
minimal_backoff_time = 60s
```

---

### 🔢 maximal_backoff_time

Délai maximum entre deux tentatives

```bash
# 4000 secondes (défaut)
maximal_backoff_time = 4000s

# 1 heure
maximal_backoff_time = 3600s
```

---

### 📈 Algorithm des retries

Postfix utilise un algorithme **exponentiel** :

```
Tentative 1 : immédiat
Tentative 2 : après minimal_backoff_time (5 min)
Tentative 3 : 10 min
Tentative 4 : 20 min
Tentative 5 : 40 min
...
Jusqu'à maximal_backoff_time
```

---

## Paramètres de performance

### ⚡ default_process_limit

Nombre maximum de processus Postfix simultanés

```bash
# 100 (défaut)
default_process_limit = 100

# Pour serveur haute capacité
default_process_limit = 500
```

---

### 📬 qmgr_message_active_limit

Nombre maximum de messages actifs dans la queue

```bash
# 20000 (défaut)
qmgr_message_active_limit = 20000

# Plus élevé pour gros volumes
qmgr_message_active_limit = 50000
```

---

### 🔄 smtp_destination_concurrency_limit

Nombre de connexions simultanées vers une même destination

```bash
# 20 (défaut)
smtp_destination_concurrency_limit = 20

# Plus agressif
smtp_destination_concurrency_limit = 50
```

---

### 📨 smtp_destination_rate_delay

Délai entre deux emails vers la même destination

```bash
# Pas de délai (défaut)
smtp_destination_rate_delay = 0s

# Limiter à 1 email par seconde
smtp_destination_rate_delay = 1s
```

**Cas d'usage** : Certains serveurs (Gmail, Yahoo) limitent le taux d'emails reçus. Si vous envoyez trop vite, ils vous throttle ou blacklist.

---

## Paramètres de logging

### 📝 maillog_file

Fichier de log personnalisé (Postfix 3.4+)

```bash
# Utiliser syslog (défaut)
maillog_file = 

# Fichier dédié
maillog_file = /var/log/postfix.log
```

---

### 🐛 debug_peer_list

Liste d'hôtes pour lesquels activer le debug

```bash
# Activer debug pour example.com
debug_peer_list = example.com, 203.0.113.10
```

### 🔍 debug_peer_level

Niveau de debug

```bash
debug_peer_level = 2
```

---

## Variables prédéfinies utiles

Postfix fournit des variables que vous pouvez utiliser :

```bash
$myhostname       # Nom du serveur
$mydomain         # Domaine
$myorigin         # Domaine d'origine
$mail_name        # Postfix
$mail_version     # 3.8.4
```

---

**Exemple d'utilisation** :

```bash
smtpd_banner = $myhostname ESMTP $mail_name
# Devient : mail.example.com ESMTP Postfix
```

---

## Configuration complète type

Voici un exemple de configuration complète et sécurisée pour 2025 :

```bash
# === IDENTITÉ ===
myhostname = mail.example.com
mydomain = example.com
myorigin = $mydomain
# === RÉSEAU ===
inet_interfaces = all
inet_protocols = ipv4
# === DESTINATIONS ===
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
relayhost =
# === STOCKAGE ===
home_mailbox = Maildir/
message_size_limit = 52428800
mailbox_size_limit = 0
```

---

```bash
# === SÉCURITÉ ===
smtpd_banner = $myhostname ESMTP
disable_vrfy_command = yes
smtpd_helo_required = yes

# === RESTRICTIONS ===
smtpd_recipient_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_invalid_hostname,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain
smtpd_helo_restrictions =
    permit_mynetworks,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname

smtpd_sender_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain
```

---

```bash
# === TLS ===
smtpd_tls_cert_file = /etc/letsencrypt/live/mail.example.com/fullchain.pem
smtpd_tls_key_file = /etc/letsencrypt/live/mail.example.com/privkey.pem
smtpd_tls_security_level = encrypt
smtpd_tls_protocols = >=TLSv1.2
smtp_tls_security_level = encrypt
smtp_tls_protocols = >=TLSv1.2
```

**(ne vous inquietez pas nous le voyons dans le module TLS)**

---

```bash
# === SASL ===
smtpd_sasl_auth_enable = yes
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_security_options = noanonymous
```

---

```bash
# === PERFORMANCE ===
# je limite le nombre de processus Postfix simultanés à 100
default_process_limit = 100
# je limite le nombre de messages actifs dans la queue à 20000
qmgr_message_active_limit = 20000
# je limite la fréquence de traitement de la file d'attente à 300 secondes
queue_run_delay = 300s
# je limite le délai minimum avant de retenter l'envoi à 300 secondes
minimal_backoff_time = 300s
# je limite le délai maximum entre deux tentatives à 4000 secondes
maximal_backoff_time = 4000s
# je limite la durée de vie de la file d'attente à 5 jours
maximal_queue_lifetime = 5d
# je limite la durée de vie de la file d'attente des messages rejetés à 5 jours
bounce_queue_lifetime = 5d
```

---

## Commandes utiles pour main.cf

### 🔍 Voir toute la configuration

```bash
postconf
```

(Affiche TOUS les paramètres, même ceux par défaut)

### 📋 Voir uniquement les paramètres modifiés

```bash
postconf -n
```

(Plus lisible !)

---

### 🔎 Voir un paramètre spécifique

```bash
postconf myhostname
postconf message_size_limit
```

### ✏️ Modifier un paramètre

```bash
# Via commande
sudo postconf -e "myhostname = mail.example.com"

# Ou manuellement dans le fichier
sudo nano /etc/postfix/main.cf
```

---

### 🔄 Recharger la configuration

```bash
# Vérifier d'abord
sudo postfix check

# Recharger
sudo systemctl reload postfix
```

### 📚 Voir l'aide d'un paramètre

```bash
man 5 postconf

# Ou en ligne
# http://www.postfix.org/postconf.5.html
```

---

## Bonnes pratiques

### 💡 Commentez votre configuration

```bash
# === CONFIGURATION RÉSEAU ===
# Écoute sur toutes les interfaces pour accepter les emails de l'extérieur
inet_interfaces = all

# IPv4 uniquement car notre hébergeur ne fournit pas d'IPv6
inet_protocols = ipv4
```

### 📦 Gardez une sauvegarde

```bash
# Avant toute modification
sudo cp /etc/postfix/main.cf /etc/postfix/main.cf.backup

# Avec date
sudo cp /etc/postfix/main.cf /etc/postfix/main.cf.$(date +%Y%m%d)
```

---

### ✅ Testez toujours

```bash
# 1. Vérifier la syntaxe
sudo postfix check

# 2. Voir ce qui a changé
sudo postconf -n > /tmp/new.conf
diff /etc/postfix/main.cf.backup /tmp/new.conf

# 3. Recharger
sudo systemctl reload postfix

# 4. Tester l'envoi
echo "Test" | mail -s "Test" root
```

---

### 🔐 Sécurité avant performance

Préférez toujours une configuration sécurisée à une configuration ultra-performante mais risquée.

```bash
# ❌ Rapide mais dangereux
mynetworks = 0.0.0.0/0

# ✅ Plus lent mais sécurisé
mynetworks = 127.0.0.0/8
smtpd_sasl_auth_enable = yes
```

### 📊 Loggez suffisamment

```bash
# Trop peu de logs = impossible de débugger
smtpd_tls_loglevel = 0

# Bon compromis
smtpd_tls_loglevel = 1

# Pour debug uniquement
smtpd_tls_loglevel = 2
```

---

## Exercices pratiques

### 🎯 Exercice 1 : Configuration de base

1. Modifiez `myhostname` pour correspondre à votre serveur
2. Configurez `mydomain` avec votre domaine
3. Vérifiez avec `postconf -n`
4. Testez l'envoi d'un email local

### 🎯 Exercice 2 : Restrictions

1. Ajoutez les restrictions recommandées
2. Testez d'envoyer un email sans authentification
3. Consultez les logs pour voir le rejet

---

### 🎯 Exercice 3 : Taille des messages

1. Limitez la taille des messages à 10 MB
2. Tentez d'envoyer un fichier de 15 MB
3. Observez l'erreur dans les logs

### 🎯 Exercice 4 : Bannière

1. Changez la bannière pour masquer la version de Postfix
2. Testez avec telnet :

```bash
telnet mail.andromed.cloud 25
```

3. Vérifiez que la bannière ne révèle plus d'informations

---

## Points clés à retenir

### 💡 Configuration

<small>

**Fichier** : `/etc/postfix/main.cf`

- **Format** : `paramètre = valeur`

- **Vérification** : `postfix check`

- **Rechargement** : `systemctl reload postfix`

**Paramètres essentiels** : `myhostname`, `mydomain`, `myorigin` (Identité)

- `inet_interfaces`, `mynetworks` (Réseau)

- `mydestination`, `relay_domains` (Destinations) - Restrictions (Sécurité)

**Commandes utiles** : `postconf` (Voir la config)

- `postconf -n` (Voir les modifs uniquement)
- `postconf -e` (Modifier un paramètre)

<div class="text-xs">

**Sécurité** : Toujours inclure `reject_unauth_destination` - Ne jamais faire un open relay - Limiter `mynetworks` au strict nécessaire - Masquer les informations dans la bannière

</div>

</small>

---

## Prochaine étape

Maintenant que vous maîtrisez le `main.cf`, nous allons apprendre les Alias et Domaines virtuels.

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Alias et Domaines virtuels <carbon:arrow-right class="inline"/>
  </span>
</div>

