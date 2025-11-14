---
layout: new-section
routeAlias: 'sauvegarde-restauration'
---

<a name="sauvegarde-restauration" id="sauvegarde-restauration"></a>

# Sauvegarde et Restauration

<div class="mt-2">
  💾 Protéger vos données et préparer le disaster recovery
</div>

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

Imaginez perdre tous vos emails professionnels des 5 dernières années en une seconde... **Les sauvegardes ne sont pas optionnelles !**

---

# Quoi sauvegarder ?

## Les 4 éléments critiques

**1. Configuration Postfix** : `/etc/postfix/` (main.cf, master.cf, tables...)

**2. Clés DKIM** : `/etc/opendkim/`

**3. Certificats SSL** : `/etc/letsencrypt/`

**4. Mailboxes** : `/var/mail/vhosts/`

---

## Optionnel

**Queue Postfix** : `/var/spool/postfix/` (change constamment, rarement sauvegardé)

**Bases de données** : Si vous utilisez MySQL/PostgreSQL pour domaines virtuels

---

# Stratégie de sauvegarde

## Règle 3-2-1

**3** copies - **2** supports différents - **1** copie hors site

---

## Fréquence

- Configuration : **Quotidienne**
- Mailboxes : **2-4 fois/jour** (selon criticité)
- Bases de données : **Quotidienne**

---

## Rétention simple

- **7 jours** : Sauvegardes quotidiennes
- **4 semaines** : Sauvegardes hebdomadaires
- **1 an** : Sauvegardes mensuelles

---

# Méthodes de sauvegarde

## Méthode 1 : tar (simple)

### 📦 Créer une sauvegarde

```bash
#!/bin/bash
DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/backup/postfix"
mkdir -p $BACKUP_DIR

# Tout en un
sudo tar czf $BACKUP_DIR/postfix-full-$DATE.tar.gz \
  /etc/postfix \
  /etc/opendkim \
  /etc/letsencrypt \
  /var/mail/vhosts

echo "✅ Backup completed: $DATE"
```

---

### 🔄 Restaurer

```bash
sudo tar xzf postfix-full-XXXXXXXX.tar.gz -C /
sudo systemctl reload postfix
```

---

## Méthode 2 : rsync (incrémental)

**Avantage** : Seuls les fichiers modifiés sont copiés (rapide !)

```bash
#!/bin/bash
BACKUP_DIR="/backup/postfix"
DATE=$(date +%Y%m%d)

# Sauvegarde locale
rsync -avz --delete /etc/postfix/ $BACKUP_DIR/$DATE/postfix/
rsync -avz --delete /etc/opendkim/ $BACKUP_DIR/$DATE/opendkim/
rsync -avz --delete /var/mail/vhosts/ $BACKUP_DIR/$DATE/mailboxes/
```

---

### Sauvegarde distante

```bash
# Vers un autre serveur (nécessite clé SSH)
rsync -avz -e ssh /etc/postfix/ backup@server.com:/backups/postfix/
```

---


## Sauvegarde des bases de données (optionnel)

Si vous utilisez MySQL/PostgreSQL pour les domaines virtuels :

```bash
# MySQL
mysqldump -u root -p mailserver | gzip > /backup/maildb-$(date +%Y%m%d).sql.gz

# PostgreSQL
sudo -u postgres pg_dump mailserver | gzip > /backup/maildb-$(date +%Y%m%d).sql.gz
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

## Procédure simple

```bash
#!/bin/bash
# restore-postfix.sh

# 1. Arrêter les services
sudo systemctl stop postfix opendkim

# 2. Restaurer depuis l'archive
sudo tar xzf /backup/postfix/postfix-full-*.tar.gz -C /

# 3. Corriger les permissions
sudo chown -R postfix:postfix /etc/postfix
sudo chown -R opendkim:opendkim /etc/opendkim
sudo chown -R vmail:vmail /var/mail/vhosts

# 4. Recompiler les tables
sudo postmap /etc/postfix/virtual
sudo newaliases

# 5. Vérifier et redémarrer
sudo postfix check
sudo systemctl start opendkim postfix

echo "✅ Restauration terminée !"
```

---

## Tester les sauvegardes !

⚠️ **Une sauvegarde non testée = Une sauvegarde qui ne marche pas !**

**Plan** : Testez la restauration tous les 3 mois sur une VM

---

---

## 💡 Pour aller plus loin

Les outils avancés suivants seront vus dans la formation **Perfectionnement** :

- **Borg Backup** : Sauvegarde dédupliquée et chiffrée
- **Rclone vers Cloud** : S3, Google Cloud, Backblaze
- **Bacula** : Solution d'entreprise complète
- **Restic** : Alternative moderne à Borg

---

# Monitoring simple

## Vérifier les sauvegardes

```bash
# Lister les sauvegardes récentes
ls -lh /backup/postfix/ | tail -10
```

---

```bash
# Vérifier la taille
du -sh /backup/postfix/
```

---

## Alerte simple avec cron

```bash
# Dans le script de sauvegarde, ajouter :
if [ $? -eq 0 ]; then
    echo "✅ Backup OK" | mail -s "Backup Success" admin@example.com
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

## 🎯 Exercice 1 : Sauvegarde et restauration

**Objectif** : Créer une sauvegarde et la restaurer

---

**Tâches** :

1. Créez un script de sauvegarde avec tar pour `/etc/postfix/`
2. Exécutez le script et vérifiez l'archive
3. Modifiez un fichier de config (ex: `main.cf`)
4. Restaurez depuis la sauvegarde
5. Vérifiez que la modification a été annulée

---

**Commandes utiles** :

```bash
# Créer la sauvegarde
sudo tar czf /backup/postfix-$(date +%Y%m%d).tar.gz /etc/postfix/

# Vérifier l'archive
tar tzf /backup/postfix-*.tar.gz | head

# Restaurer
sudo tar xzf /backup/postfix-*.tar.gz -C /
```

---

## 🎯 Exercice 2 : Automatisation

**Objectif** : Automatiser les sauvegardes quotidiennes

---

**Tâches** :

1. Créez le script `/usr/local/bin/backup-postfix.sh`
2. Rendez-le exécutable
3. Configurez un cron pour l'exécuter à 2h du matin
4. Testez manuellement le script
5. Vérifiez les logs cron le lendemain

---

**Configuration cron** :

```bash
sudo crontab -e

# Ajouter :
0 2 * * * /usr/local/bin/backup-postfix.sh

# Vérifier les logs
sudo grep CRON /var/log/syslog | grep backup
```

---

# Points clés à retenir

## 💡 Essentiel

**Quoi sauvegarder** :
- Configuration : `/etc/postfix/`, `/etc/opendkim/`
- Certificats : `/etc/letsencrypt/`
- Mailboxes : `/var/mail/vhosts/`
- Bases de données (si utilisées)

---

**Outils de base** :
- `tar` : Simple et universel
- `rsync` : Incrémental et rapide

---

**Règle 3-2-1** :
- 3 copies, 2 supports différents, 1 copie hors site

---

**Automatisation** :
- Cron pour planifier
- Alertes en cas d'échec
- **Tester les restaurations !**

---

## Prochaine étape

Vos données sont maintenant protégées ! Passons aux **exercices pratiques** pour consolider toutes ces connaissances ! 🎯

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Exercices pratiques débutant <carbon:arrow-right class="inline"/>
  </span>
</div>

