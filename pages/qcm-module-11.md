---


# QCM - Module 11 : Sauvegarde et restauration

## Question 1

Quels sont les 3 éléments ESSENTIELS à sauvegarder pour Postfix ?

A) Logs, queue, processus  

B) Configuration, certificats SSL, DKIM keys  

C) Binaires, man pages, documentation  

D) Cache DNS, cookies, sessions

---

### ✅ Réponse : B

Les **3 essentiels** : Configuration (`/etc/postfix/`), certificats SSL (`/etc/letsencrypt/`), clés DKIM (`/etc/opendkim/keys/`).

---

## Question 2

Quelle est la règle 3-2-1 de sauvegarde ?

A) 3 serveurs, 2 datacenters, 1 cloud  

B) 3 copies, 2 supports différents, 1 copie hors site  

C) 3 jours, 2 semaines, 1 mois  

D) 3 fichiers, 2 formats, 1 compression

---

### ✅ Réponse : B

**Règle 3-2-1** : **3** copies de vos données, sur **2** supports différents, avec **1** copie hors site.

---

## Question 3

Faut-il sauvegarder la queue Postfix ?

A) Oui, toujours  

B) Non, elle change constamment  

C) Seulement la queue active  

D) Seulement en production

---

### ✅ Réponse : B

La queue change **constamment** et se vide naturellement. Sauvegarder la config est plus important que la queue.

---

## Question 4

Quelle commande permet de sauvegarder la configuration active de Postfix dans un fichier texte versionné ?

A) `postfix status > postfix.conf`  

B) `postconf -n > postfix.conf`  

C) `postqueue -p > postfix.conf`  

D) `systemctl show postfix > postfix.conf`

---

### ✅ Réponse : B

`postconf -n` liste uniquement les paramètres personnalisés. Redirigez la sortie vers un fichier pour tracer vos changements dans Git ou une sauvegarde.

---

## Question 5

Quel service du système planifie l'exécution de votre script de sauvegarde chaque nuit à 02h00 ?

A) `systemd-networkd`  

B) `cron`  

C) `logrotate`  

D) `cupsd`

---

### ✅ Réponse : B

`cron` (ou `cronie`) reste la méthode la plus simple pour lancer un script récurrent. Vous pouvez aussi créer un timer systemd si vous préférez.

---

## Exercice pratique - Module 11

### 🎯 Objectif
Sauvegarder et restaurer la configuration Postfix

### 📋 Tâches (15 minutes)

1. **Créer un script de sauvegarde** :
```bash
#!/bin/bash
# backup-postfix.sh
DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/backup/postfix"
mkdir -p $BACKUP_DIR

# Sauvegarder la configuration
tar -czf $BACKUP_DIR/postfix-config-$DATE.tar.gz /etc/postfix/

# Sauvegarder les certificats SSL
tar -czf $BACKUP_DIR/ssl-certs-$DATE.tar.gz /etc/letsencrypt/

# Sauvegarder les clés DKIM (si présentes)
if [ -d /etc/opendkim/keys ]; then
    tar -czf $BACKUP_DIR/dkim-keys-$DATE.tar.gz /etc/opendkim/keys/
fi

echo "Backup completed: $DATE"
ls -lh $BACKUP_DIR/
```

---

2. **Rendre exécutable et tester** :
```bash
chmod +x backup-postfix.sh
sudo ./backup-postfix.sh
```

3. **Simuler une restauration** :
```bash
# Copier la config actuelle
sudo cp -r /etc/postfix /etc/postfix.backup

# Restaurer depuis l'archive
cd /
sudo tar -xzf /backup/postfix/postfix-config-VOTRE_DATE.tar.gz

# Vérifier
postfix check
sudo systemctl reload postfix
```

4. **Automatiser avec cron** :
```bash
# Ajouter dans crontab
sudo crontab -e
# Ajouter : 0 2 * * * /chemin/vers/backup-postfix.sh
```

**Bonus** : Envoyez les sauvegardes vers un serveur distant avec rsync ou scp

