---
layout: new-section
routeAlias: 'exercices-debutant'
---

<a name="exercices-debutant" id="exercices-debutant"></a>

# Exercices Pratiques Débutant

🎯 Mettre en pratique les connaissances acquises

---

# Introduction

Ces exercices couvrent tous les modules de la formation **initiation**.

Chaque exercice est conçu pour renforcer un concept spécifique.

---

## Organisation

Les exercices sont classés par **difficulté** :

⭐ **Facile** : Configuration de base

⭐⭐ **Moyen** : Configuration avancée

⭐⭐⭐ **Difficile** : Troubleshooting et intégration

---

# Exercice 1 : Installation de base ⭐

## 🎯 Objectif

Installer et configurer un serveur Postfix fonctionnel.

---

## 📋 Prérequis

- Serveur Linux (Ubuntu 22.04+ ou Rocky Linux 9+)
- Accès root/sudo
- Nom de domaine configuré en DNS

---

## 🔧 Tâches

1. Installez Postfix
2. Configurez `myhostname`, `mydomain`, `myorigin`
3. Configurez `mydestination` pour accepter les emails locaux
4. Testez l'envoi d'un email local
5. Vérifiez que l'email est bien arrivé

---

## ✅ Critères de validation

- Postfix démarre sans erreur
- `postconf -n` affiche votre configuration
- Email local reçu dans `/home/user/Maildir/`

---

## 💡 Solution

```bash
# 1. Installation
sudo apt update && sudo apt install postfix -y

# 2. Configuration
sudo postconf -e "myhostname = mail.example.com"
sudo postconf -e "mydomain = example.com"
sudo postconf -e "myorigin = \$mydomain"
```

---

```bash
# 3. Destinations
sudo postconf -e "mydestination = \$myhostname, localhost.\$mydomain, localhost, \$mydomain"

# 4. Maildir
sudo postconf -e "home_mailbox = Maildir/"

# 5. Recharger
sudo systemctl reload postfix
```

---

```bash
# 6. Test
echo "Test Postfix" | mail -s "Test" $USER

# 7. Vérifier
ls ~/Maildir/new/
cat ~/Maildir/new/*
```

---

# Exercice 2 : Configuration DNS ⭐⭐

## 🎯 Objectif

Configurer correctement les enregistrements DNS pour votre serveur mail.

---

## 📋 Prérequis

- Accès à la configuration DNS de votre domaine
- Serveur Postfix installé

---

## 🔧 Tâches

1. Créez un enregistrement A pour `mail.example.com`
2. Créez un enregistrement MX pour `example.com`
3. Configurez le PTR (reverse DNS)
4. Vérifiez tous les enregistrements

---

## ✅ Critères de validation

- `dig mail.example.com A` retourne votre IP
- `dig example.com MX` retourne `mail.example.com`
- `dig -x VOTRE_IP` retourne `mail.example.com`

---

## 💡 Solution

```bash
# Enregistrements DNS à ajouter

# Enregistrement A
# Type: A
# Nom: mail.example.com
# Valeur: 203.0.113.10

# Enregistrement MX
# Type: MX
# Nom: example.com
# Priorité: 10
# Valeur: mail.example.com
```

---

```bash
# PTR (à configurer chez votre hébergeur)
# Type: PTR
# IP: 203.0.113.10
# Valeur: mail.example.com

# Vérification
dig mail.example.com A +short
dig example.com MX +short
dig -x 203.0.113.10 +short
```

---

# Exercice 3 : Alias et redirections ⭐

## 🎯 Objectif

Créer des alias pour rediriger les emails.

---

## 🔧 Tâches

1. Créez un alias `contact` qui redirige vers votre email
2. Créez un alias `support` qui redirige vers plusieurs adresses
3. Redirigez tous les emails système (root, postmaster) vers votre email
4. Testez chaque alias

---

## ✅ Critères de validation

- Email à `contact@localhost` arrive à votre adresse
- Email à `support@localhost` arrive à toutes les adresses
- Email à `root@localhost` arrive à votre adresse

---

## 💡 Solution

```bash
# Éditer /etc/aliases
sudo nano /etc/aliases
```

---

```sql
# Contenu de /etc/aliases

# Alias simple
contact: john@example.com

# Alias multiples
support: john@example.com, jane@example.com, admin@example.com

# Redirections système
postmaster: admin@example.com
webmaster: admin@example.com
root: admin@example.com
```

---

```bash
# Recompiler
sudo newaliases

# Tester
echo "Test contact" | mail -s "Test" contact
echo "Test support" | mail -s "Test" support
echo "Test root" | mail -s "Test" root
```

---

# Exercice 4 : Domaines virtuels ⭐⭐

## 🎯 Objectif

Configurer des domaines virtuels avec redirections.

---

## 🔧 Tâches

1. Créez un domaine virtuel `virtual.local`
2. Configurez `admin@virtual.local` → votre email
3. Configurez `info@virtual.local` → votre email
4. Créez un catch-all pour `virtual.local`
5. Testez les redirections

---

## ✅ Critères de validation

- Email à `admin@virtual.local` arrive
- Email à `info@virtual.local` arrive
- Email à `n'importe quoi@virtual.local` arrive (catch-all)

---

## 💡 Solution

```bash
# Créer /etc/postfix/virtual
sudo nano /etc/postfix/virtual
```

---

```sql
# Contenu de /etc/postfix/virtual

# Règles spécifiques
admin@virtual.local       john@example.com
info@virtual.local        john@example.com

# Catch-all (doit être après les règles spécifiques)
@virtual.local            catchall@example.com
```

---

```bash
# Configuration main.cf
sudo postconf -e "virtual_alias_domains = virtual.local"
sudo postconf -e "virtual_alias_maps = hash:/etc/postfix/virtual"

# Compiler
sudo postmap /etc/postfix/virtual

# Recharger
sudo systemctl reload postfix
```

---

```bash
# Tester
echo "Test admin" | mail -s "Test" admin@virtual.local
echo "Test info" | mail -s "Test" info@virtual.local
echo "Test catch-all" | mail -s "Test" random@virtual.local
```

---

# Exercice 5 : Protection anti-spam basique ⭐⭐

## 🎯 Objectif

Mettre en place des protections anti-spam de base.

---

## 🔧 Tâches

1. Configurez les restrictions HELO
2. Configurez les restrictions sender
3. Configurez les restrictions recipient
4. Ajoutez 2 RBL (Spamhaus et Barracuda)
5. Testez les rejets

---

## ✅ Critères de validation

- Email avec HELO invalide est rejeté
- Email depuis domaine inexistant est rejeté
- IP blacklistée est rejetée

---

## 💡 Solution

```sql
# Dans /etc/postfix/main.cf

# Restrictions HELO
smtpd_helo_required = yes
smtpd_helo_restrictions = 
    permit_mynetworks,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname
```

---

```sql
# Restrictions sender
smtpd_sender_restrictions = 
    permit_mynetworks,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain

# Restrictions recipient
smtpd_recipient_restrictions = 
    permit_mynetworks,
    reject_unauth_destination,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain,
    reject_rbl_client zen.spamhaus.org,
    reject_rbl_client b.barracudacentral.org
```

---

```bash
# Recharger
sudo postfix check
sudo systemctl reload postfix

# Tester le rejet (depuis un autre serveur)
telnet mail.example.com 25
EHLO invalid
MAIL FROM:<user@nonexistent-domain-12345.com>
```

---

# Exercice 6 : SPF ⭐

## 🎯 Objectif

Créer et publier un enregistrement SPF.

---

## 🔧 Tâches

1. Listez tous vos serveurs d'envoi d'emails
2. Créez un enregistrement SPF approprié
3. Publiez-le en DNS
4. Testez avec un outil en ligne

---

## ✅ Critères de validation

- `dig example.com TXT` retourne l'enregistrement SPF
- Test sur https://mxtoolbox.com/spf.aspx passe

---

## 💡 Solution

```bash
# Enregistrement DNS à ajouter
# Type: TXT
# Nom: example.com (ou @)
# Valeur: v=spf1 a mx ip4:203.0.113.10 -all

# Vérification
dig example.com TXT +short | grep spf

# Test en ligne
# https://www.kitterman.com/spf/validate.html
```

---

# Exercice 7 : DKIM ⭐⭐

## 🎯 Objectif

Configurer la signature DKIM pour vos emails.

---

## 🔧 Tâches

1. Installez OpenDKIM
2. Générez une paire de clés
3. Configurez Postfix pour signer
4. Publiez la clé publique en DNS
5. Testez la signature

---

## ✅ Critères de validation

- OpenDKIM démarre sans erreur
- Clé publique publiée en DNS
- Email de test contient `DKIM-Signature:`
- Vérification DKIM passe

---

## 💡 Solution

```bash
# 1. Installation
sudo apt install opendkim opendkim-tools -y

# 2. Générer clés
sudo mkdir -p /etc/opendkim/keys/example.com
sudo opendkim-genkey -b 2048 -d example.com -D /etc/opendkim/keys/example.com -s mail -v
```

---

```bash
# 3. Permissions
sudo chown opendkim:opendkim /etc/opendkim/keys/example.com/mail.private
sudo chmod 600 /etc/opendkim/keys/example.com/mail.private

# 4. Configuration OpenDKIM
sudo nano /etc/opendkim.conf
```

---

```
# /etc/opendkim.conf
Mode                    sv
Domain                  example.com
Selector                mail
KeyFile                 /etc/opendkim/keys/example.com/mail.private
Socket                  inet:8891@localhost
Canonicalization        relaxed/simple
```

---

```bash
# 5. Configuration Postfix
sudo postconf -e "smtpd_milters = inet:localhost:8891"
sudo postconf -e "non_smtpd_milters = inet:localhost:8891"
sudo postconf -e "milter_default_action = accept"

# 6. Démarrer
sudo systemctl enable opendkim
sudo systemctl start opendkim
sudo systemctl reload postfix
```

---

```bash
# 7. Publier clé DNS
sudo cat /etc/opendkim/keys/example.com/mail.txt

# Type: TXT
# Nom: mail._domainkey.example.com
# Valeur: v=DKIM1; h=sha256; k=rsa; p=MIIBIjAN...

# 8. Tester
echo "Test DKIM" | mail -s "Test" test@gmail.com
# Vérifier la signature dans les headers
```

---

# Exercice 8 : TLS ⭐⭐

## 🎯 Objectif

Activer TLS pour sécuriser les communications.

---

## 🔧 Tâches

1. Obtenez un certificat Let's Encrypt
2. Configurez TLS dans Postfix
3. Restreignez aux versions TLS 1.2+
4. Testez la connexion TLS

---

## ✅ Critères de validation

- Certificat Let's Encrypt valide
- TLS activé sur port 25
- TLS 1.2+ uniquement
- Test `openssl s_client` réussit

---

## 💡 Solution

```bash
# 1. Installation Certbot
sudo apt install certbot -y

# 2. Obtenir certificat
sudo certbot certonly --standalone -d mail.example.com

# 3. Vérifier
sudo ls -la /etc/letsencrypt/live/mail.example.com/
```

---

```bash
# 4. Configuration Postfix
sudo postconf -e "smtpd_tls_cert_file = /etc/letsencrypt/live/mail.example.com/fullchain.pem"
sudo postconf -e "smtpd_tls_key_file = /etc/letsencrypt/live/mail.example.com/privkey.pem"
sudo postconf -e "smtpd_tls_security_level = may"
sudo postconf -e "smtpd_tls_protocols = >=TLSv1.2"
sudo postconf -e "smtp_tls_security_level = may"
sudo postconf -e "smtp_tls_protocols = >=TLSv1.2"
```

---

```bash
# 5. Recharger
sudo systemctl reload postfix

# 6. Tester
openssl s_client -connect mail.example.com:25 -starttls smtp
# Vérifier : Protocol : TLSv1.2 ou TLSv1.3
```

---

# Exercice 9 : Analyse des logs ⭐

## 🎯 Objectif

Analyser les logs pour comprendre l'activité du serveur.

---

## 🔧 Tâches

1. Envoyez 5 emails de test
2. Trouvez les Queue IDs dans les logs
3. Tracez le parcours complet d'un message
4. Comptez les emails envoyés aujourd'hui
5. Identifiez les 3 destinataires les plus fréquents

---

## ✅ Critères de validation

- Vous pouvez tracer un message de A à Z
- Vous savez compter les emails
- Vous savez extraire des statistiques

---

## 💡 Solution

```bash
# 1. Envoyer des tests
for i in {1..5}; do
    echo "Test $i" | mail -s "Test $i" test@example.com
done

# 2. Trouver les Queue IDs
sudo grep "from=<$(whoami)@" /var/log/mail.log | tail -5
```

---

```bash
# 3. Tracer un message (remplacez QUEUEID)
sudo grep QUEUEID /var/log/mail.log

# 4. Compter emails aujourd'hui
sudo grep "$(date +%b\ %e)" /var/log/mail.log | grep "status=sent" | wc -l

# 5. Top destinataires
sudo grep "status=sent" /var/log/mail.log | \
  grep -oP 'to=<[^>]+>' | \
  sed 's/.*@//' | sed 's/>//' | \
  sort | uniq -c | sort -rn | head -3
```

---

# Exercice 10 : Sauvegarde ⭐⭐

## 🎯 Objectif

Créer un système de sauvegarde automatique.

---

## 🔧 Tâches

1. Créez un script de sauvegarde
2. Sauvegardez `/etc/postfix/` et `/etc/opendkim/`
3. Testez manuellement le script
4. Automatisez avec cron (quotidien à 2h)
5. Testez la restauration

---

## ✅ Critères de validation

- Script crée une archive avec date
- Cron exécute le script quotidiennement
- Restauration fonctionne

---

## 💡 Solution

```bash
#!/bin/bash
# /usr/local/bin/backup-postfix.sh

DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/backup/postfix"
mkdir -p $BACKUP_DIR

# Configuration Postfix
tar czf $BACKUP_DIR/postfix-$DATE.tar.gz /etc/postfix/

# DKIM
tar czf $BACKUP_DIR/dkim-$DATE.tar.gz /etc/opendkim/
```

---

```bash
# SSL
tar czf $BACKUP_DIR/ssl-$DATE.tar.gz /etc/letsencrypt/

echo "✅ Backup completed: $DATE"
```

---

```bash
# Rendre exécutable
sudo chmod +x /usr/local/bin/backup-postfix.sh

# Tester
sudo /usr/local/bin/backup-postfix.sh

# Vérifier
ls -lh /backup/postfix/
```

---

```bash
# Automatiser avec cron
sudo crontab -e

# Ajouter :
0 2 * * * /usr/local/bin/backup-postfix.sh

# Tester la restauration
sudo tar xzf /backup/postfix/postfix-XXXXXXXX.tar.gz -C /
sudo systemctl reload postfix
```

---

# Projet final : Serveur mail complet ⭐⭐⭐

## 🎯 Objectif

Créer un serveur mail complet et fonctionnel de A à Z.

---

## 📋 Cahier des charges

Votre serveur mail doit :

1. Envoyer et recevoir des emails
2. Avoir un DNS correct (A, MX, PTR)
3. Avoir SPF, DKIM et DMARC configurés
4. Être sécurisé avec TLS
5. Avoir des protections anti-spam
6. Avoir des alias et domaines virtuels
7. Être sauvegardé quotidiennement
8. Être surveillé (logs, queue)

---

## ✅ Critères de validation

### 📧 Fonctionnalité

- Email à Gmail passe (inbox, pas spam)
- Email depuis Gmail arrive
- Score sur https://www.mail-tester.com/ > 8/10

---

### 🔐 Sécurité

- TLS 1.2+ uniquement
- SPF, DKIM, DMARC configurés
- RBL actifs
- Authentification SASL (si port 587)

---

### 🛠️ Administration

- Sauvegardes quotidiennes automatiques
- Monitoring de la queue
- Logs accessibles et analysables

---

## 💡 Checklist

✅ Postfix installé et configuré

✅ DNS : A, MX, PTR

✅ SPF publié

✅ DKIM signé et publié

✅ DMARC configuré

✅ TLS activé (Let's Encrypt)

---

✅ Restrictions anti-spam

✅ RBL configurés

✅ Alias système configurés

✅ Domaine virtuel fonctionnel

✅ Sauvegarde automatique

✅ Test d'envoi/réception réussi

---

# Félicitations ! 🎉

Vous avez terminé les exercices de la formation **Initiation Postfix** !

Vous êtes maintenant capable de :
- Installer et configurer Postfix
- Sécuriser un serveur mail
- Gérer les domaines et alias
- Protéger contre le spam
- Surveiller et sauvegarder

---

## Prochaine étape

Direction le **QCM de validation** pour tester vos connaissances ! ✅

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : QCM initiation <carbon:arrow-right class="inline"/>
  </span>
</div>

