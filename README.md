# 📧 Formation Postfix 2025

![Postfix](https://www.postfix.org/mysza.gif)

Formation complète sur Postfix présentée par **Andromed** et **Ascent**.

---

## 📋 À propos

Cette formation couvre l'installation, la configuration, la sécurisation et la maintenance d'un serveur de messagerie professionnel avec **Postfix**, **Dovecot** et **OpenDKIM**.

**Formateur** : Jimmylan Surquin  
**Durée** : 2 jours (14h) pour l'initiation

---

## 📅 Calendrier

**Formation Postfix - Initiation (2 jours / 14h)**

- 📍 17 au 21 novembre - Site de Bourges
- 📍 26 au 30 janvier - Site de Bourges

**Formation Postfix - Perfectionnement (4 jours)**

- 📍 03 au 06 février - Site de Bourges

---

## 🎯 Objectifs

À la fin de cette formation, vous saurez :

- ✅ Installer et configurer Postfix sur Linux
- ✅ Comprendre l'architecture modulaire de Postfix
- ✅ Configurer les paramètres essentiels (main.cf, master.cf)
- ✅ Gérer les domaines virtuels et les alias
- ✅ Sécuriser avec DKIM, SPF, DMARC et TLS/SSL
- ✅ Configurer Dovecot pour IMAP/POP3
- ✅ Protéger contre le spam (RBL, filtres)
- ✅ Surveiller et diagnostiquer via les logs
- ✅ Sauvegarder et restaurer la configuration

---

## 📚 Programme

### Jour 1 (7h)

1. **Introduction à Postfix** (30min)
2. **Installation et configuration de base** (1h30)
3. **Architecture essentielle** (45min)
4. **Configuration main.cf** (2h)
5. **Alias et domaines virtuels** (1h)
6. **TLS et sécurité** (1h)
7. **DKIM, SPF, DMARC** (1h30)

### Jour 2 (7h)

8. **Dovecot** (1h30)
9. **Protection anti-spam** (1h)
10. **Logs et surveillance** (45min)
11. **Sauvegarde et restauration** (30min)
12. **Travaux pratiques** (2h)
13. **QCM de validation** (30min)

---

## 🔧 Configuration d'exemple : Serveur mail complet

Le dossier **`confs/`** contient une configuration complète et fonctionnelle d'un serveur de messagerie prêt pour les exercices pratiques.

### 📁 Contenu du dossier confs/

```
confs/
└── etc/
    ├── postfix/postfix/          # Configuration Postfix
    │   ├── main.cf               # Configuration principale ⭐
    │   ├── master.cf             # Services Postfix
    │   ├── virtual               # Alias virtuels
    │   ├── virtual_domains       # Domaines gérés
    │   ├── vmailbox              # Boîtes mail virtuelles
    │   ├── vmailbox.db           # Base de données compilée
    │   └── sasl/                 # Authentification SASL
    │
    ├── dovecot/dovecot/          # Configuration Dovecot
    │   ├── dovecot.conf          # Configuration principale ⭐
    │   ├── users                 # Utilisateurs de test
    │   └── conf.d/               # Configurations modulaires
    │       ├── 10-auth.conf      # Authentification
    │       ├── 10-mail.conf      # Stockage des mails ⭐
    │       ├── 10-master.conf    # Services (IMAP, POP3, LMTP)
    │       ├── 10-ssl.conf       # Certificats SSL/TLS
    │       └── ...
    │
    └── opendkim/                 # Configuration OpenDKIM
        ├── opendkim.conf         # Configuration principale ⭐
        └── opendkim/
            ├── KeyTable          # Table des clés DKIM
            ├── SigningTable      # Règles de signature
            ├── TrustedHosts      # Hôtes de confiance
            └── keys/             # Clés privées DKIM
                └── andromed.cloud/
                    ├── mail.private  # Clé privée
                    └── mail.txt      # Enregistrement DNS
```

---

## 🔑 Configuration Postfix (main.cf)

### Résumé de la configuration

| Paramètre | Valeur |
|-----------|--------|
| **Domaine** | `andromed.cloud` |
| **Hostname** | `mail.andromed.cloud` |
| **TLS/SSL** | Let's Encrypt (STARTTLS + certificats) |
| **Domaines virtuels** | Oui (via `virtual_mailbox_domains`) |
| **Stockage** | `/var/mail/vhosts` |
| **User/Group** | `vmail:vmail` (UID/GID: 5000) |
| **Authentification** | SASL via Dovecot |
| **DKIM** | OpenDKIM (milter sur port 8891) |
| **Anti-spam** | RBL Spamhaus ZEN |
| **Soumission** | Port 587 avec authentification |

### Extraits clés du main.cf

```ini
# Identité du serveur
myhostname = mail.andromed.cloud
mydomain = andromed.cloud
myorigin = $mydomain
inet_interfaces = all
inet_protocols = ipv4

# TLS/SSL avec Let's Encrypt
smtpd_tls_cert_file=/etc/letsencrypt/live/mail.andromed.cloud/fullchain.pem
smtpd_tls_key_file=/etc/letsencrypt/live/mail.andromed.cloud/privkey.pem
smtpd_tls_security_level = encrypt
smtp_tls_security_level = encrypt

# Domaines virtuels
virtual_mailbox_domains = andromed.cloud
virtual_mailbox_maps = hash:/etc/postfix/vmailbox
virtual_mailbox_base = /var/mail/vhosts
virtual_uid_maps = static:5000
virtual_gid_maps = static:5000
virtual_alias_maps = hash:/etc/postfix/virtual

# Authentification SASL via Dovecot
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous

# OpenDKIM (milter)
smtpd_milters = inet:localhost:8891
non_smtpd_milters = inet:localhost:8891
milter_protocol = 6
milter_default_action = accept

# Protection anti-spam
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_rbl_client zen.spamhaus.org
```

### Boîtes mail virtuelles (vmailbox)

```
johndoe@andromed.cloud       andromed.cloud/johndoe/
janedoe@andromed.cloud       andromed.cloud/janedoe/
contact@andromed.cloud       andromed.cloud/contact/
support@andromed.cloud       andromed.cloud/support/
admin@andromed.cloud         andromed.cloud/admin/
```

### Services Postfix (master.cf)

- **Port 25** (SMTP) : Réception des emails
- **Port 587** (submission) : Soumission avec authentification
  - TLS obligatoire
  - Authentification SASL requise

---

## 📬 Configuration Dovecot

### Résumé de la configuration

| Paramètre | Valeur |
|-----------|--------|
| **Protocoles** | IMAP |
| **Stockage** | Maildir dans `/var/mail/vhosts/%d/%n` |
| **User/Group** | `vmail:vmail` |
| **Authentification** | Fichier `users` (passdb file) |
| **Socket SASL** | `/var/run/dovecot/private/auth` (pour Postfix) |
| **SSL/TLS** | Certificats Let's Encrypt |

### Configuration du stockage (10-mail.conf)

```ini
# Emplacement des mailbox : /var/mail/vhosts/domaine/utilisateur
mail_location = maildir:/var/mail/vhosts/%d/%n

# Utilisateur et groupe virtuels (même que Postfix)
mail_uid = vmail
mail_gid = vmail

# Limites de sécurité
first_valid_uid = 1000
last_valid_uid = 5000
first_valid_gid = 1000
last_valid_gid = 5000
```

### Utilisateurs de test (users)

```
johndoe@andromed.cloud:{PLAIN}MotDePasse123!
```

**Format** : `email:{SCHEME}password`

Pour générer un mot de passe hashé :

```bash
doveadm pw -s SHA512-CRYPT -p VotreMotDePasse
```

### Socket SASL pour Postfix (10-master.conf)

```
service auth {
  unix_listener /var/spool/postfix/private/auth {
    mode = 0666
    user = postfix
    group = postfix
  }
}
```

---

## 🔐 Configuration OpenDKIM

### Résumé de la configuration

| Paramètre | Valeur |
|-----------|--------|
| **Domaine** | `andromed.cloud` |
| **Sélecteur** | `mail` |
| **Socket** | `inet:8891@localhost` |
| **Canonicalisation** | `relaxed/simple` |
| **Clé privée** | `/etc/opendkim/keys/andromed.cloud/mail.private` |

### Configuration principale (opendkim.conf)

```ini
# Socket pour Postfix
Socket = inet:8891@localhost

# Tables de configuration
KeyTable = /etc/opendkim/KeyTable
SigningTable = /etc/opendkim/SigningTable
InternalHosts = /etc/opendkim/TrustedHosts

# Canonicalisation
Canonicalization = relaxed/simple

# User
UserID = opendkim
UMask = 007
```

### KeyTable

Mapping entre le sélecteur et la clé privée :

```
mail._domainkey.andromed.cloud andromed.cloud:mail:/etc/opendkim/keys/andromed.cloud/mail.private
```

### SigningTable

Règles de signature par email/domaine :

```
johndoe@andromed.cloud mail._domainkey.andromed.cloud
*@andromed.cloud mail._domainkey.andromed.cloud
```

### TrustedHosts

Hôtes autorisés à signer :

```
127.0.0.1
localhost
mail.andromed.cloud
51.68.120.60
```

### Enregistrement DNS DKIM

Le fichier `keys/andromed.cloud/mail.txt` contient l'enregistrement DNS à publier :

```
mail._domainkey.andromed.cloud. IN TXT "v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC..."
```

---

## 🚀 Installer et utiliser ces configurations

### 1. Prérequis

```bash
# Installer les paquets nécessaires
sudo apt update
sudo apt install postfix dovecot-core dovecot-imapd opendkim opendkim-tools
```

### 2. Créer l'utilisateur vmail

```bash
sudo groupadd -g 5000 vmail
sudo useradd -g vmail -u 5000 vmail -d /var/mail/vhosts -m
sudo chown -R vmail:vmail /var/mail/vhosts
sudo chmod -R 770 /var/mail/vhosts
```

### 3. Copier les configurations

```bash
# Postfix
sudo cp confs/etc/postfix/postfix/main.cf /etc/postfix/
sudo cp confs/etc/postfix/postfix/master.cf /etc/postfix/
sudo cp confs/etc/postfix/postfix/vmailbox /etc/postfix/
sudo cp confs/etc/postfix/postfix/virtual /etc/postfix/
sudo cp confs/etc/postfix/postfix/virtual_domains /etc/postfix/

# Dovecot
sudo cp confs/etc/dovecot/dovecot/dovecot.conf /etc/dovecot/
sudo cp -r confs/etc/dovecot/dovecot/conf.d/* /etc/dovecot/conf.d/
sudo cp confs/etc/dovecot/dovecot/users /etc/dovecot/

# OpenDKIM
sudo cp confs/etc/opendkim.conf /etc/
sudo mkdir -p /etc/opendkim/keys/andromed.cloud
sudo cp confs/etc/opendkim/opendkim/KeyTable /etc/opendkim/
sudo cp confs/etc/opendkim/opendkim/SigningTable /etc/opendkim/
sudo cp confs/etc/opendkim/opendkim/TrustedHosts /etc/opendkim/
sudo cp confs/etc/opendkim/opendkim/keys/andromed.cloud/* /etc/opendkim/keys/andromed.cloud/
```

### 4. Adapter les configurations à votre domaine

**Remplacer partout dans les fichiers :**

- `andromed.cloud` → votre domaine
- `mail.andromed.cloud` → votre hostname
- `51.68.120.60` → votre IP
- Chemins des certificats SSL/TLS

**Fichiers à modifier :**

- `/etc/postfix/main.cf`
- `/etc/postfix/vmailbox`
- `/etc/postfix/virtual_domains`
- `/etc/dovecot/conf.d/10-mail.conf`
- `/etc/opendkim/KeyTable`
- `/etc/opendkim/SigningTable`
- `/etc/opendkim/TrustedHosts`

### 5. Générer vos clés DKIM

```bash
# Créer le répertoire pour vos clés
sudo mkdir -p /etc/opendkim/keys/votredomaine.com

# Générer la paire de clés
sudo opendkim-genkey -D /etc/opendkim/keys/votredomaine.com/ -d votredomaine.com -s mail

# Définir les permissions
sudo chown -R opendkim:opendkim /etc/opendkim/keys
sudo chmod 600 /etc/opendkim/keys/*/mail.private

# Afficher la clé publique (à mettre dans le DNS)
sudo cat /etc/opendkim/keys/votredomaine.com/mail.txt
```

### 6. Compiler les tables Postfix

```bash
sudo postmap /etc/postfix/vmailbox
sudo postmap /etc/postfix/virtual
sudo postmap /etc/postfix/virtual_domains
```

### 7. Créer la structure des mailbox

```bash
# Créer les répertoires pour chaque utilisateur
sudo mkdir -p /var/mail/vhosts/votredomaine.com/utilisateur
sudo chown -R vmail:vmail /var/mail/vhosts
```

### 8. Obtenir un certificat SSL/TLS

```bash
# Avec Certbot (Let's Encrypt)
sudo apt install certbot
sudo certbot certonly --standalone -d mail.votredomaine.com

# Les certificats seront dans :
# /etc/letsencrypt/live/mail.votredomaine.com/fullchain.pem
# /etc/letsencrypt/live/mail.votredomaine.com/privkey.pem
```

### 9. Configurer les enregistrements DNS

```dns
; MX Record
votredomaine.com.           IN  MX  10  mail.votredomaine.com.

; A Record
mail.votredomaine.com.      IN  A       VOTRE_IP

; SPF Record
votredomaine.com.           IN  TXT "v=spf1 mx ip4:VOTRE_IP ~all"

; DKIM Record (copier depuis mail.txt)
mail._domainkey.votredomaine.com.  IN  TXT "v=DKIM1; k=rsa; p=MIGfMA0..."

; DMARC Record
_dmarc.votredomaine.com.    IN  TXT "v=DMARC1; p=quarantine; rua=mailto:postmaster@votredomaine.com"

; Reverse DNS (rDNS) - à configurer chez votre hébergeur
VOTRE_IP                    PTR mail.votredomaine.com.
```

### 10. Redémarrer les services

```bash
sudo systemctl restart postfix
sudo systemctl restart dovecot
sudo systemctl restart opendkim

# Vérifier les statuts
sudo systemctl status postfix
sudo systemctl status dovecot
sudo systemctl status opendkim
```

### 11. Tester l'installation

```bash
# Test SMTP (port 25)
telnet localhost 25
> EHLO test
> QUIT

# Test soumission (port 587)
telnet localhost 587
> EHLO test
> QUIT

# Test IMAP (port 143)
telnet localhost 143
> a1 LOGIN johndoe@votredomaine.com MotDePasse123!
> a2 LOGOUT

# Test authentification Dovecot
doveadm auth test johndoe@votredomaine.com MotDePasse123!

# Voir les logs
sudo tail -f /var/log/mail.log
```

### 12. Tester l'envoi d'email

```bash
# Envoyer un email de test
echo "Test email" | mail -s "Test" -a "From: johndoe@votredomaine.com" destinataire@example.com

# Vérifier la queue Postfix
postqueue -p

# Voir les logs en temps réel
sudo tail -f /var/log/mail.log
```

---

## 🧪 Tester DKIM, SPF et DMARC

### Outils en ligne

- **Mail-tester** : [https://www.mail-tester.com/](https://www.mail-tester.com/)
  - Envoyez un email à l'adresse fournie
  - Obtenez un score sur 10 avec détails

- **Email-tester** : Envoyez un email à `check-auth@verifier.port25.com`
  - Vous recevrez un rapport détaillé par email

- **DKIM Validator** : [https://dkimvalidator.com/](https://dkimvalidator.com/)

- **MXToolbox** : [https://mxtoolbox.com/](https://mxtoolbox.com/)
  - Tests DNS (MX, SPF, DKIM, DMARC)
  - Vérification des blacklists

### Commandes de diagnostic

```bash
# Vérifier les enregistrements DNS
dig MX votredomaine.com
dig TXT votredomaine.com                              # SPF
dig TXT mail._domainkey.votredomaine.com              # DKIM
dig TXT _dmarc.votredomaine.com                       # DMARC
dig -x VOTRE_IP                                       # rDNS

# Tester OpenDKIM
sudo opendkim-testkey -d votredomaine.com -s mail -vvv

# Logs Postfix
sudo grep -i "reject" /var/log/mail.log
sudo grep -i "dkim" /var/log/mail.log

# État de la queue
postqueue -p

# Statistiques
pflogsumm /var/log/mail.log
```

---

## 📊 Commandes utiles Postfix

### Gestion de la queue

```bash
# Voir la queue
postqueue -p
mailq

# Vider toute la queue (ATTENTION !)
postsuper -d ALL

# Supprimer un email spécifique
postsuper -d QUEUE_ID

# Relancer la queue
postqueue -f
postfix flush

# Mettre la queue en attente
postsuper -h ALL

# Relancer les emails en attente
postsuper -H ALL
```

### Configuration

```bash
# Voir la configuration active
postconf -n

# Voir un paramètre spécifique
postconf myhostname

# Modifier un paramètre
postconf -e "myhostname = mail.example.com"

# Recharger la configuration
postfix reload

# Redémarrer Postfix
systemctl restart postfix
```

### Logs et diagnostic

```bash
# Logs en temps réel
tail -f /var/log/mail.log

# Rechercher des erreurs
grep -i error /var/log/mail.log
grep -i warning /var/log/mail.log
grep -i reject /var/log/mail.log

# Statistiques
pflogsumm /var/log/mail.log | less
```

---

## 📊 Commandes utiles Dovecot

```bash
# Tester l'authentification
doveadm auth test utilisateur@domaine.com motdepasse

# Lister les utilisateurs connectés
doveadm who

# Voir les mailbox d'un utilisateur
doveadm mailbox list -u utilisateur@domaine.com

# Forcer l'indexation
doveadm index -u utilisateur@domaine.com '*'

# Recharger la configuration
doveadm reload

# Logs
tail -f /var/log/mail.log | grep dovecot
```

---

## 🔍 Dépannage

### Problèmes courants

**1. Postfix ne démarre pas**

```bash
# Vérifier la syntaxe
postfix check

# Voir les erreurs
systemctl status postfix
journalctl -xeu postfix
```

**2. Erreur "User unknown"**

- Vérifier `virtual_mailbox_maps` dans main.cf
- Vérifier `/etc/postfix/vmailbox`
- Recompiler : `postmap /etc/postfix/vmailbox`

**3. Authentification échoue**

```bash
# Tester le socket SASL
ls -la /var/spool/postfix/private/auth

# Tester avec Dovecot
doveadm auth test user@domain.com password

# Logs Dovecot
grep "auth" /var/log/mail.log
```

**4. DKIM non détecté**

```bash
# Tester la clé
opendkim-testkey -d domaine.com -s mail -vvv

# Vérifier le DNS
dig TXT mail._domainkey.domaine.com

# Vérifier le socket
netstat -tulpn | grep 8891
```

**5. Emails en spam**

- Vérifier SPF, DKIM, DMARC
- Vérifier le rDNS (PTR)
- Tester sur mail-tester.com
- Vérifier les blacklists : [MXToolbox Blacklist Check](https://mxtoolbox.com/blacklists.aspx)

**6. Erreur de permissions**

```bash
# Vérifier les permissions
ls -la /var/mail/vhosts

# Corriger
sudo chown -R vmail:vmail /var/mail/vhosts
sudo chmod -R 770 /var/mail/vhosts

# OpenDKIM
sudo chown -R opendkim:opendkim /etc/opendkim/keys
sudo chmod 600 /etc/opendkim/keys/*/mail.private
```

---

## 📖 Ressources

### Documentation officielle

- [Postfix Documentation](http://www.postfix.org/documentation.html)
- [Postfix Configuration Parameters](http://www.postfix.org/postconf.5.html)
- [Postfix Basic Configuration](http://www.postfix.org/BASIC_CONFIGURATION_README.html)
- [Postfix Virtual Domain Hosting](http://www.postfix.org/VIRTUAL_README.html)
- [Dovecot Wiki](https://doc.dovecot.org/)
- [OpenDKIM Documentation](http://www.opendkim.org/)

### Outils de test

- [Mail-tester](https://www.mail-tester.com/) - Score de qualité email
- [Email-tester](https://email-tester.com/) - Test via check-auth@verifier.port25.com
- [MXToolbox](https://mxtoolbox.com/) - Tests DNS, MX, blacklists
- [DKIM Validator](https://dkimvalidator.com/) - Validation DKIM
- [SPF Record Testing](https://www.kitterman.com/spf/validate.html)
- [DMARC Inspector](https://dmarcian.com/dmarc-inspector/)

---

## ⚠️ Avertissement de sécurité

Les configurations du dossier `confs/` sont des **exemples à but pédagogique**.

**Pour un environnement de production :**

- ⚠️ **Changez TOUS les mots de passe** (fichier `users`, etc.)
- ⚠️ **Générez vos propres clés DKIM** (ne réutilisez JAMAIS les clés d'exemple)
- ⚠️ **Utilisez vos propres certificats SSL/TLS** valides
- ⚠️ **Configurez un pare-feu** (UFW, iptables) pour limiter les accès
- ⚠️ **Activez fail2ban** pour bloquer les tentatives de connexion
- ⚠️ **Surveillez les logs** régulièrement
- ⚠️ **Configurez des sauvegardes** automatiques
- ⚠️ **Testez dans un environnement de staging** avant production
- ⚠️ **Mettez à jour** régulièrement (apt update && apt upgrade)

---

## 🚀 Lancer les slides de formation

```bash
# Installer les dépendances
pnpm install

# Lancer en mode développement
pnpm run dev

# Construire pour la production
pnpm run build

# Exporter en PDF
pnpm run export
```

Les slides seront disponibles sur `http://localhost:3030`

---

## 📞 Contact

**Jimmylan Surquin**

- 🌐 Website : [jimmylan.fr](https://jimmylan.fr)
- 🐦 Twitter : [@jimmylansrq](https://twitter.com/jimmylansrq)
- 📺 YouTube : [jimmylansrq](https://www.youtube.com/channel/jimmylansrq)
- 🏢 Entreprise : [Andromed](https://www.andromed.fr/)
- 📧 Email : [jimmylan.surquin@andromed.fr](mailto:jimmylan.surquin@andromed.fr)

---

## 📜 Licence

MIT License

---

<div align="center">

**Made with ❤️ by Andromed - 2025**

[🌐 Website](https://www.andromed.fr/) • [📧 Contact](mailto:jimmylan.surquin@andromed.fr)

</div>
