---
layout: new-section
routeAlias: 'installation-configuration-base'
---

<a name="installation-configuration-base" id="installation-configuration-base"></a>

# Installation et Configuration de Base

⚙️ Mettre en place votre premier serveur Postfix

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

Dans notre cas nous allons utiliser le domaine `jimmylan.fr`.

Et il nous faut bien sur un VPS ou un serveur dédié pour le serveur mail.

---

Le domaine est enregistré chez OVH.

Donc nous allons configurer le DNS chez OVH directement , si vous avez un autre FAI, vous devrez configurer le DNS chez eux , si vous utilisez un système plus classique, vous devrez configurer le tout dans B.I.N.D. (Bind DNS)

[https://www.it-connect.fr/dns-avec-bind-9/](Bind9 - Configuration DNS avec Bind9)

---

**Enregistrement A** : Pointe vers l'IP de votre serveur

```
mail.jimmylan.fr.  IN  A  51.68.224.131
```

**Enregistrement MX** : Indique le serveur mail du domaine

```
jimmylan.fr.  IN  MX  10  mail.jimmylan.fr.
```

Le chiffre (10) est la priorité : plus c'est petit, plus c'est prioritaire.

**Enregistrement PTR (Reverse DNS)** : TRÈS IMPORTANT !

Le PTR fait le lien inverse : IP → nom de domaine. Sans PTR correct, vos emails seront considérés comme spam !

```
10.113.0.203.in-addr.arpa.  IN  PTR  mail.example.com.
```

⚠️ **Note importante** : Le PTR doit être configuré chez votre hébergeur/FAI (vous ne pouvez pas le faire vous-même).

---

Capture d'écran de la configuration DNS chez le FAI :

<img src="/ovh2.png" alt="Configuration DNS chez le FAI"
width='300px'
height='auto'/>

Capture d'écran de la configuration DNS chez OVH :

<img src="/ovh1.png" alt="Configuration DNS chez OVH"
width='300px'
height='auto'/>

---

### 🔧 Vérifier la configuration DNS

```bash
# Vérifier l'enregistrement MX
dig example.com MX +short

# Vérifier l'enregistrement A
dig mail.example.com A +short

# Vérifier le PTR (reverse DNS)
dig -x 203.0.113.10 +short
```

---

### 🔥 Configuration du firewall

Avant d'installer Postfix, ouvrez les ports nécessaires :

```bash
# Pour Ubuntu (UFW)
sudo ufw allow 25/tcp    # SMTP = Postfix = Votre serveur mail
sudo ufw allow 587/tcp   # Submission = Postfix = Pour envoyer des emails depuis votre serveur mail
sudo ufw allow 465/tcp   # SMTPS = Postfix = Pour envoyer des emails via SSL/TLS

# Pour Rocky Linux (firewalld)
sudo firewall-cmd --permanent --add-service=smtp
sudo firewall-cmd --permanent --add-service=smtp-submission
sudo firewall-cmd --permanent --add-service=smtps
sudo firewall-cmd --reload
```

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

2. **Nom du système de messagerie** : Entrez votre domaine (jimmylan.fr)

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

```sql
# Identité du serveur
myhostname = mail.example.com
mydomain = example.com
myorigin = $mydomain

# Interfaces réseau
inet_interfaces = all  # ou localhost pour tests

# Domaines acceptés
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

# Réseaux autorisés (JAMAIS 0.0.0.0/0 = open relay!)
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
```

**relayhost** : Serveur SMTP relais (optionnel)

```sql
# Pas de relais (envoi direct)
relayhost =

# Ou via un relais (exemple : serveur de votre entreprise)
relayhost = [smtp.example.com]:587
```

---

### 🔐 Paramètres de sécurité de base

**home_mailbox** : Format de stockage des emails

```sql
# Format Maildir (recommandé)
home_mailbox = Maildir/

# Format mbox (ancien)
home_mailbox = mail/
```

Maildir vs mbox ?
- **Maildir** : Un fichier par email, plus sûr, plus rapide
- **mbox** : Tous les emails dans un seul fichier, risque de corruption

> En clair : vous allez voir un dossier MailDir par utilisateur. exemple : mon user john doe aura son dossier MailDir dans /home/john/Maildir.

**smtpd_banner** : Bannière SMTP (ne pas révéler trop d'infos)

```sql
# Par défaut (affiche la version)
smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)

# Version sécurisée (masque les détails)
smtpd_banner = $myhostname ESMTP
```

---

### 📋 Exemple de configuration minimale

```sql
# Nom du serveur
myhostname = mail.example.com
mydomain = example.com
myorigin = $mydomain

# Interfaces réseau
inet_interfaces = all
inet_protocols = ipv4

# Domaines acceptés
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

# Réseaux autorisés
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128

# Pas de relais
relayhost =

# Stockage des emails
home_mailbox = Maildir/

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

```sql
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
sudo grep "user@example.com" /var/log/mail.log

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

```sql
# Dans main.cf
disable_vrfy_command = yes
```

La commande VRFY permet de vérifier si une adresse email existe. Les spammeurs l'adorent !

### 📏 Limiter la taille des messages

```sql
# Limite à 50 MB
message_size_limit = 52428800

# Limite de la boîte mail (0 = illimité)
mailbox_size_limit = 0
```

### ⏱️ Limites de temps

```sql
# Timeout de connexion SMTP
smtpd_timeout = 300s

# Timeout client SMTP
smtp_helo_timeout = 60s
```

---

### 🔒 Restrictions de base

```sql
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

## Installation avec Docker (pour les tests)

Pour tester rapidement sans toucher à votre système, utilisez Docker !

### 🐳 Dockerfile simple

```dockerfile
FROM ubuntu:24.04

# Installation de Postfix en mode non-interactif
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    postfix \
    mailutils \
    && apt-get clean

# Configuration minimale
RUN postconf -e "myhostname=mail.example.com" && \
    postconf -e "mydomain=example.com" && \
    postconf -e "myorigin=\$mydomain" && \
    postconf -e "inet_interfaces=all" && \
    postconf -e "mydestination=\$myhostname, localhost.\$mydomain, localhost, \$mydomain"

EXPOSE 25 587

CMD ["postfix", "start-fg"]
```

---

### 🚀 Construire et lancer

```bash
# Construire l'image
docker build -t postfix-test .

# Lancer le conteneur
docker run -d --name postfix -p 2525:25 postfix-test

# Tester l'envoi d'un email
docker exec postfix sendmail root@localhost <<EOF
Subject: Test Docker
From: test@example.com

Ceci est un test depuis Docker
EOF
```

---

### 📦 docker-compose.yml complet

```yaml
version: '3.8'

services:
  postfix:
    image: ubuntu:24.04
    container_name: postfix
    hostname: mail.example.com
    ports:
      - "2525:25"
    volumes:
      - ./postfix-data:/var/spool/postfix
      - ./postfix-config:/etc/postfix
    environment:
      - POSTFIX_HOSTNAME=mail.example.com
      - POSTFIX_DOMAIN=example.com
    command: >
      bash -c "
      apt-get update &&
      DEBIAN_FRONTEND=noninteractive apt-get install -y postfix mailutils &&
      postconf -e 'myhostname=mail.example.com' &&
      postconf -e 'mydomain=example.com' &&
      postfix start-fg
      "
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
dig -x VOTRE_IP +short
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

#### 🎯 Exercice 4 : Docker

1. Créez un conteneur Postfix avec Docker
2. Configurez-le pour accepter les emails sur le port 2525
3. Testez l'envoi depuis l'extérieur du conteneur

</small>

---

## Points clés à retenir

### 💡 Ce qu'il faut retenir

**Configuration minimale** : `myhostname`, `mydomain`, `myorigin` sont essentiels - `inet_interfaces` définit les interfaces d'écoute - `mynetworks` contrôle qui peut envoyer des emails

**Sécurité de base** : Ne jamais faire un open relay (`mynetworks = 0.0.0.0/0`) - Toujours vérifier le DNS (surtout le PTR) - Limiter les tailles de messages

**Outils essentiels** : `postconf` (voir et modifier la configuration) - `mailq` / `postqueue -p` (voir la file d'attente) - `postsuper` (gérer la file d'attente) - `postfix check` (vérifier la syntaxe)

**Logs** : `/var/log/mail.log` ou `/var/log/maillog` - Toujours consulter les logs en cas de problème - `tail -f` est votre ami

---

## Prochaine étape

Vous avez maintenant un Postfix fonctionnel ! 🎉

Dans le prochain module, nous allons plonger dans **l'architecture interne de Postfix** pour comprendre comment tout fonctionne sous le capot.

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Architecture et fonctionnement <carbon:arrow-right class="inline"/>
  </span>
</div>

