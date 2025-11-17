---

# QCM - Module 3 : Architecture de Postfix

## Question 1

Quel processus est le "chef d'orchestre" de Postfix ?

A) smtpd  

B) qmgr  

C) master  

D) cleanup

---

## Question 2

Quel processus gère les files d'attente et décide quand envoyer les messages ?

A) smtpd  

B) qmgr (queue manager)  

C) pickup  

D) smtp

---

## Question 3

Dans quelle file d'attente se trouvent les messages en cours de livraison ?

A) maildrop  

B) incoming  

C) active  

D) deferred

---

## Question 4

Quel processus Postfix accepte les connexions SMTP entrantes depuis les clients ou d'autres MTAs ?

A) `pickup`  

B) `smtpd`  

C) `smtp`  

D) `cleanup`

---

## Question 5

Quel est le rôle du processus `pickup` dans l'architecture Postfix ?

A) Envoyer les messages sortants vers Internet  

B) Nettoyer les en-têtes et appliquer les règles MIME  

C) Reprendre les messages déposés dans `maildrop` par les agents locaux  

D) Vérifier les signatures DKIM

---

## Réponses - Module 3

<small>

**Question 1 : Réponse C** - Le processus **master** est le chef d'orchestre. Il lance tous les autres processus, surveille leur santé et les redémarre si nécessaire.

**Question 2 : Réponse B** - **qmgr** (queue manager) est le CŒUR de Postfix. Il gère toutes les files d'attente et décide quand envoyer les messages.

**Question 3 : Réponse C** - La file **active** contient les messages prêts à être envoyés et en cours de livraison (max 20 000 par défaut).

**Question 4 : Réponse B** - `smtpd` est le démon serveur SMTP. Il gère l'authentification, applique les restrictions et remet les messages au processus `cleanup`.

**Question 5 : Réponse C** - `pickup` surveille la file **maildrop** (messages injectés localement par `sendmail`/`postdrop`) et les transfère vers `cleanup` pour traitement.

</small>

---

## Exercice pratique - Module 3

### 🎯 Objectif
Observer l'architecture de Postfix en action

### 📋 Tâches (10 minutes)

1. **Observer les processus** :

```bash
ps aux | grep postfix
pstree -p $(pgrep -o master)
```

---

2. **Suivre un message** :

```bash
# Envoyer un email
echo "Test" | mail -s "Test architecture" $USER

# Suivre dans les logs
tail -f /var/log/mail.log | grep "postfix"
```

---

3. **Explorer les queues** :

```bash
sudo ls -la /var/spool/postfix/active/
sudo postqueue -p
```

---

**Bonus** : Utilisez `postcat -q QUEUE_ID` pour lire un message
