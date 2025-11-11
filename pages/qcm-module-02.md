---

# QCM - Module 2 : Installation et configuration

## Question 1

Quel est le paramètre obligatoire pour définir le nom complet du serveur ?

A) `mydomain`  

B) `myhostname`  

C) `myorigin`  

D) `inet_interfaces`

---

### ✅ Réponse : B

`myhostname = mail.example.com` définit le FQDN du serveur. C'est le paramètre le plus important !

---

## Question 2

Quelle commande permet de recharger la configuration sans couper les connexions ?

A) `systemctl restart postfix`  

B) `systemctl reload postfix`  

C) `postfix restart`  

D) `killall postfix`

---

### ✅ Réponse : B

`systemctl reload postfix` recharge la config **sans couper** les connexions en cours. `restart` couperait tout !

---

## Question 3

Quel enregistrement DNS est OBLIGATOIRE pour un serveur mail ?

A) A  

B) CNAME  

C) MX  

D) TXT

---

### ✅ Réponse : C

L'enregistrement **MX** indique le serveur mail responsable du domaine. Sans lui, impossible de recevoir des emails !

---

## Question 4

Quel paramètre Postfix contrôle les interfaces réseau sur lesquelles le service SMTP écoute ?

A) `relayhost`  

B) `inet_interfaces`  

C) `smtpd_banner`  

D) `alias_database`

---

### ✅ Réponse : B

`inet_interfaces` définit si Postfix écoute sur `all`, `loopback-only` ou une IP précise. Pratique pour limiter l'écoute à `localhost` sur un serveur relai.

---

## Question 5

Quelle commande vérifie la configuration Postfix pour détecter les erreurs avant un rechargement ?

A) `postfix check`  

B) `postconf -n`  

C) `systemctl status postfix`  

D) `postqueue -p`

---

### ✅ Réponse : A

`postfix check` réalise une série de vérifications (permissions, syntaxe, ownership) et affiche les problèmes potentiels avant redémarrage.

---

## Exercice pratique - Module 2

### 🎯 Objectif
Installer et configurer Postfix pour la première fois

### 📋 Tâches (15 minutes)

1. **Installation** : Installez Postfix sur votre système
2. **Configuration** : Configurez ces paramètres dans `/etc/postfix/main.cf` :
   - `myhostname = mail.votredomaine.local`
   - `mydomain = votredomaine.local`
   - `myorigin = $mydomain`
   - `inet_interfaces = all`
   - `mydestination = $myhostname, localhost, $mydomain`

---

3. **Test** : Envoyez un email de test local :
```bash
echo "Test Postfix" | mail -s "Test" $USER
ls ~/Maildir/new/
```

4. **Vérification** : Consultez les logs : `tail -f /var/log/mail.log`

**Aide** : En cas de problème, utilisez `postfix check`

---

### ✅ Réponse :

```bash
# Installation
sudo apt install postfix

# Configuration
sudo nano /etc/postfix/main.cf
```

```bash
# Rechargement de la configuration
sudo systemctl reload postfix

# Test d'envoi
echo "Test Postfix" | mail -s "Test" $USER
ls ~/Maildir/new/
```

```bash
# Vérification des logs
tail -f /var/log/mail.log
```