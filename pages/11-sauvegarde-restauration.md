---
layout: new-section
routeAlias: 'sauvegarde-restauration'
---

<a name="sauvegarde-restauration" id="sauvegarde-restauration"></a>

# Sauvegarde et Restauration

💾 Protéger vos données et préparer le disaster recovery

---

# Introduction

Un serveur mail contient des données **critiques** :
- Emails des utilisateurs
- Configuration Postfix
- Clés DKIM
- Certificats SSL
- Bases de données (si domaines virtuels)

**Sans sauvegarde** = Catastrophe assurée !

---

## Analogie

Imaginez perdre tous vos emails professionnels des 5 dernières années en une seconde...

**Les sauvegardes ne sont pas optionnelles !**

---

# Quoi sauvegarder ?

## 📂 Fichiers de configuration

```
/etc/postfix/
├── main.cf
├── master.cf
├── virtual
├── vmailbox
├── aliases
├── sender_access
├── recipient_access
├── transport
└── ...
```

---

## 🔑 Clés DKIM

```
/etc/opendkim/
├── opendkim.conf
├── KeyTable
├── SigningTable
├── TrustedHosts
└── keys/
    └── example.com/
        ├── mail.private
        └── mail.txt
```

---

## 🔒 Certificats SSL

```
/etc/letsencrypt/
├── live/
│   └── mail.example.com/
│       ├── fullchain.pem
│       └── privkey.pem
└── renewal/
    └── mail.example.com.conf
```

---

## 📧 Mailboxes (emails des utilisateurs)

```
/var/mail/vhosts/
├── example.com/
│   ├── user1/
│   │   ├── cur/
│   │   ├── new/
│   │   └── tmp/
│   └── user2/
└── example.org/
```

---

## 📊 Queue Postfix

```
/var/spool/postfix/
├── active/
├── deferred/
├── hold/
└── ...
```

---

⚠️ **Note** : La queue change constamment. Sauvegarder la queue complète n'est généralement pas nécessaire.

---

## 🗄️ Bases de données

Si vous utilisez MySQL/PostgreSQL pour les domaines virtuels :

```bash
# MySQL
mysqldump -u root -p mailserver > /backup/maildb.sql

# PostgreSQL
pg_dump mailserver > /backup/maildb.sql
```

---

# Stratégie de sauvegarde

## Règle 3-2-1

**3** copies de vos données

**2** supports différents

**1** copie hors site

---

**Exemple** :

1. Données originales sur le serveur
2. Sauvegarde locale sur disque externe
3. Sauvegarde distante sur un autre serveur/cloud

---

## Fréquence de sauvegarde

**Configuration** : Quotidienne (voire après chaque modification)

**Mailboxes** : 2-4 fois par jour (selon criticité)

**Bases de données** : Quotidienne ou après chaque changement

---

## Rétention

**Court terme** : 7 jours (sauvegardes complètes)

**Moyen terme** : 4 semaines (sauvegardes hebdomadaires)

**Long terme** : 1 an (sauvegardes mensuelles)

---

# Méthodes de sauvegarde

## Sauvegarde manuelle avec tar

### 📦 Créer une archive

```bash
#!/bin/bash
# backup-postfix.sh

DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/backup/postfix"
mkdir -p $BACKUP_DIR
```

---

```bash
# Configuration Postfix
sudo tar czf $BACKUP_DIR/postfix-config-$DATE.tar.gz /etc/postfix/

# DKIM
sudo tar czf $BACKUP_DIR/dkim-$DATE.tar.gz /etc/opendkim/

# Certificats SSL
sudo tar czf $BACKUP_DIR/ssl-$DATE.tar.gz /etc/letsencrypt/
```

---

```bash
# Mailboxes (peut être très gros !)
sudo tar czf $BACKUP_DIR/mailboxes-$DATE.tar.gz /var/mail/vhosts/

# Queue (optionnel)
# sudo tar czf $BACKUP_DIR/queue-$DATE.tar.gz /var/spool/postfix/

echo "Backup completed: $DATE"
```

---

### 🔄 Restaurer une archive

```bash
# Restaurer configuration
sudo tar xzf postfix-config-20250113-120000.tar.gz -C /

# Restaurer DKIM
sudo tar xzf dkim-20250113-120000.tar.gz -C /

# Recharger Postfix
sudo systemctl reload postfix
```

---

## Sauvegarde avec rsync

rsync est idéal pour les **sauvegardes incrémentales**.

---

### 🔄 Sauvegarde locale

```bash
#!/bin/bash
# backup-rsync.sh

BACKUP_DIR="/backup/postfix"
DATE=$(date +%Y%m%d)

# Configuration
rsync -avz --delete /etc/postfix/ $BACKUP_DIR/$DATE/postfix/

# DKIM
rsync -avz --delete /etc/opendkim/ $BACKUP_DIR/$DATE/opendkim/
```

---

```bash
# SSL
rsync -avz --delete /etc/letsencrypt/ $BACKUP_DIR/$DATE/letsencrypt/

# Mailboxes (peut être long)
rsync -avz --delete /var/mail/vhosts/ $BACKUP_DIR/$DATE/mailboxes/
```

---

**Avantage** : Seuls les fichiers modifiés sont copiés (rapide !).

---

### 🌐 Sauvegarde distante

```bash
#!/bin/bash
# backup-rsync-remote.sh

REMOTE_USER="backup"
REMOTE_HOST="backup-server.com"
REMOTE_DIR="/backups/mail-server"
```

---

```bash
# Sync vers serveur distant
rsync -avz --delete -e ssh \
  /etc/postfix/ \
  $REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR/postfix/

rsync -avz --delete -e ssh \
  /var/mail/vhosts/ \
  $REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR/mailboxes/
```

---

**Prérequis** : Clé SSH configurée pour connexion sans mot de passe.

```bash
ssh-keygen -t ed25519
ssh-copy-id backup@backup-server.com
```

---

## Sauvegarde avec Borg

Borg est un outil de sauvegarde dédupliquée et chiffrée.

---

### 📦 Installation

```bash
# Ubuntu/Debian
sudo apt install borgbackup

# Rocky Linux
sudo dnf install borgbackup
```

---

### 🔧 Initialiser un dépôt

```bash
# Local
sudo borg init --encryption=repokey /backup/borg-repo

# Distant
sudo borg init --encryption=repokey backup@server.com:/backups/mail-server
```

---

### 💾 Créer une sauvegarde

```bash
#!/bin/bash
# backup-borg.sh

REPO="/backup/borg-repo"
DATE=$(date +%Y-%m-%d-%H:%M:%S)

sudo borg create \
  --stats \
  --progress \
  --compression lz4 \
  $REPO::postfix-$DATE \
  /etc/postfix \
  /etc/opendkim \
  /etc/letsencrypt \
  /var/mail/vhosts
```

---

**Avantages de Borg** :
- Déduplication (économise de l'espace)
- Chiffrement intégré
- Compression
- Snapshots avec historique

---

### 📜 Lister les sauvegardes

```bash
sudo borg list /backup/borg-repo
```

---

### 🔄 Restaurer avec Borg

```bash
# Lister le contenu d'une sauvegarde
sudo borg list /backup/borg-repo::postfix-2025-01-13-12:00:00

# Restaurer
sudo borg extract /backup/borg-repo::postfix-2025-01-13-12:00:00
```

---

### 🧹 Nettoyer les vieilles sauvegardes

```bash
# Garder 7 sauvegardes quotidiennes, 4 hebdo, 6 mensuelles
sudo borg prune \
  --keep-daily=7 \
  --keep-weekly=4 \
  --keep-monthly=6 \
  /backup/borg-repo
```

---

## Sauvegarde des bases de données

### 🗄️ MySQL

```bash
#!/bin/bash
# backup-mysql.sh

DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/backup/mysql"
mkdir -p $BACKUP_DIR

mysqldump -u root -p'password' \
  --single-transaction \
  --quick \
  mailserver > $BACKUP_DIR/maildb-$DATE.sql
```

---

```bash
# Compresser
gzip $BACKUP_DIR/maildb-$DATE.sql

echo "MySQL backup completed: $DATE"
```

---

### 🐘 PostgreSQL

```bash
#!/bin/bash
# backup-postgresql.sh

DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/backup/postgresql"
mkdir -p $BACKUP_DIR

sudo -u postgres pg_dump mailserver | gzip > $BACKUP_DIR/maildb-$DATE.sql.gz

echo "PostgreSQL backup completed: $DATE"
```

---

## Automatisation avec cron

### ⏰ Planifier les sauvegardes

```bash
sudo crontab -e
```

---

```bash
# Sauvegarde quotidienne à 2h du matin
0 2 * * * /usr/local/bin/backup-postfix.sh

# Sauvegarde toutes les 6 heures des mailboxes
0 */6 * * * /usr/local/bin/backup-mailboxes.sh

# Sauvegarde hebdomadaire complète le dimanche à 3h
0 3 * * 0 /usr/local/bin/backup-complete.sh
```

---

# Restauration

## Restauration complète

### 🔄 Scénario : Serveur crashé

1. Réinstaller le système d'exploitation
2. Réinstaller Postfix et dépendances
3. Restaurer les configurations
4. Restaurer les mailboxes
5. Restaurer les bases de données
6. Redémarrer les services
7. Tester

---

### 📋 Script de restauration

```bash
#!/bin/bash
# restore-postfix.sh

BACKUP_DIR="/backup/postfix"
BACKUP_DATE="20250113-120000"

echo "🔄 Restoring Postfix from $BACKUP_DATE"
```

---

```bash
# Arrêter les services
sudo systemctl stop postfix
sudo systemctl stop opendkim

# Restaurer configuration
sudo tar xzf $BACKUP_DIR/postfix-config-$BACKUP_DATE.tar.gz -C /

# Restaurer DKIM
sudo tar xzf $BACKUP_DIR/dkim-$BACKUP_DATE.tar.gz -C /
```

---

```bash
# Restaurer SSL
sudo tar xzf $BACKUP_DIR/ssl-$BACKUP_DATE.tar.gz -C /

# Restaurer mailboxes
sudo tar xzf $BACKUP_DIR/mailboxes-$BACKUP_DATE.tar.gz -C /

# Permissions
sudo chown -R postfix:postfix /etc/postfix
sudo chown -R opendkim:opendkim /etc/opendkim
sudo chown -R vmail:vmail /var/mail/vhosts
```

---

```bash
# Recompiler les tables
sudo postmap /etc/postfix/virtual
sudo postmap /etc/postfix/transport
sudo newaliases

# Vérifier la config
sudo postfix check
```

---

```bash
# Redémarrer les services
sudo systemctl start opendkim
sudo systemctl start postfix

echo "✅ Restore completed!"
```

---

## Restauration partielle

### 🔧 Restaurer seulement la configuration

```bash
sudo tar xzf postfix-config-20250113-120000.tar.gz -C /
sudo postfix check
sudo systemctl reload postfix
```

---

### 📧 Restaurer un seul utilisateur

```bash
# Extraire les emails d'un utilisateur spécifique
sudo tar xzf mailboxes-20250113-120000.tar.gz \
  -C / \
  var/mail/vhosts/example.com/user1/
```

---

### 🔑 Restaurer seulement DKIM

```bash
sudo tar xzf dkim-20250113-120000.tar.gz -C /
sudo chown -R opendkim:opendkim /etc/opendkim
sudo systemctl restart opendkim
```

---

# Tests de restauration

## Pourquoi tester ?

**Une sauvegarde non testée est une sauvegarde qui ne fonctionne pas !**

---

### ✅ Plan de test

1. Créer une VM de test
2. Installer Postfix de base
3. Restaurer depuis la sauvegarde
4. Vérifier que tout fonctionne
5. Envoyer/recevoir des emails de test
6. Noter les problèmes rencontrés
7. Documenter la procédure

---

### 📅 Fréquence des tests

**Minimum** : 1 fois tous les 3 mois

**Recommandé** : 1 fois par mois

**Idéal** : Automatiser les tests

---

# Sauvegarde vers le cloud

## Options cloud

**AWS S3** : Stockage objet économique

**Google Cloud Storage** : Alternative à S3

**Backblaze B2** : Moins cher que S3

**OVH Object Storage** : Alternative européenne

---

## Sauvegarde vers S3 avec rclone

### 📦 Installation

```bash
# Ubuntu/Debian
sudo apt install rclone

# Rocky Linux
sudo dnf install rclone
```

---

### ⚙️ Configuration

```bash
rclone config
```

---

Suivez les instructions pour configurer S3 :

```
name: s3-backup
type: s3
provider: AWS
access_key_id: YOUR_ACCESS_KEY
secret_access_key: YOUR_SECRET_KEY
region: eu-west-3
```

---

### 💾 Sauvegarder vers S3

```bash
#!/bin/bash
# backup-to-s3.sh

DATE=$(date +%Y%m%d)
LOCAL_BACKUP="/backup/postfix"
S3_BUCKET="s3-backup:my-mail-backups"

# Créer l'archive locale
tar czf /tmp/postfix-$DATE.tar.gz /etc/postfix /etc/opendkim /var/mail/vhosts
```

---

```bash
# Upload vers S3
rclone copy /tmp/postfix-$DATE.tar.gz $S3_BUCKET/daily/

# Nettoyer
rm /tmp/postfix-$DATE.tar.gz

echo "✅ Backup uploaded to S3"
```

---

### 🔄 Restaurer depuis S3

```bash
# Lister les backups
rclone ls s3-backup:my-mail-backups/daily/

# Télécharger
rclone copy s3-backup:my-mail-backups/daily/postfix-20250113.tar.gz /tmp/

# Extraire
sudo tar xzf /tmp/postfix-20250113.tar.gz -C /
```

---

# Monitoring des sauvegardes

## Vérifications automatiques

```bash
#!/bin/bash
# check-backups.sh

BACKUP_DIR="/backup/postfix"
MAX_AGE=86400  # 24 heures

LATEST=$(find $BACKUP_DIR -name "postfix-config-*.tar.gz" -mtime -1 | wc -l)

if [ $LATEST -eq 0 ]; then
    echo "⚠️ No recent backup found!" | mail -s "ALERT: Backup failed" admin@example.com
else
    echo "✅ Backup OK"
fi
```

---

### ⏰ Exécuter quotidiennement

```bash
0 9 * * * /usr/local/bin/check-backups.sh
```

---

## Alertes en cas d'échec

```bash
#!/bin/bash
# backup-with-alert.sh

if /usr/local/bin/backup-postfix.sh; then
    echo "✅ Backup successful" | mail -s "Backup OK" admin@example.com
else
    echo "❌ Backup FAILED!" | mail -s "ALERT: Backup FAILED" admin@example.com
fi
```

---

# Checklist de sauvegarde

✅ Configuration Postfix (`/etc/postfix/`)

✅ Clés DKIM (`/etc/opendkim/`)

✅ Certificats SSL (`/etc/letsencrypt/`)

✅ Mailboxes (`/var/mail/vhosts/`)

✅ Bases de données (si utilisées)

---

✅ Sauvegardes automatisées (cron)

✅ Sauvegardes hors site (cloud/serveur distant)

✅ Tests de restauration réguliers

✅ Monitoring et alertes

✅ Documentation de la procédure

---

# Exercices pratiques

## 🎯 Exercice 1 : Sauvegarde manuelle

1. Créez un script de sauvegarde avec tar
2. Sauvegardez `/etc/postfix/` et `/etc/opendkim/`
3. Vérifiez que les archives sont créées

---

## 🎯 Exercice 2 : Restauration

1. Modifiez un fichier de config
2. Restaurez depuis la sauvegarde
3. Vérifiez que le fichier est bien restauré

---

## 🎯 Exercice 3 : Automatisation

1. Configurez un cron pour sauvegarder quotidiennement
2. Testez manuellement le script
3. Vérifiez les logs cron

---

## 🎯 Exercice 4 : Sauvegarde distante

1. Configurez rsync vers un autre serveur (ou localhost)
2. Testez la synchronisation
3. Vérifiez que les fichiers sont bien copiés

---

## 🎯 Exercice 5 : Test de restauration

1. Créez une VM de test
2. Installez Postfix
3. Restaurez votre configuration depuis la sauvegarde
4. Vérifiez que Postfix démarre correctement

---

# Points clés à retenir

## 💡 Quoi sauvegarder

**Configuration** : `/etc/postfix/`, `/etc/opendkim/`

**Certificats** : `/etc/letsencrypt/`

**Mailboxes** : `/var/mail/vhosts/`

**Bases de données** : Dumps MySQL/PostgreSQL

---

## 💡 Outils

**tar** : Simple et universel

**rsync** : Incrémental et rapide

**Borg** : Dédupliqué et chiffré

**rclone** : Vers le cloud

---

## 💡 Règle 3-2-1

**3** copies

**2** supports différents

**1** copie hors site

---

## 💡 Automatisation

**Cron** : Planification automatique

**Alertes** : Notifier si échec

**Monitoring** : Vérifier régulièrement

**Tests** : Restaurer pour de vrai !

---

## Prochaine étape

Vos données sont maintenant protégées ! Passons aux **exercices pratiques** pour consolider toutes ces connaissances ! 🎯

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Exercices pratiques débutant <carbon:arrow-right class="inline"/>
  </span>
</div>

