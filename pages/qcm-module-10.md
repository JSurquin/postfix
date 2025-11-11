---


# QCM - Module 10 : Logs et surveillance

## Question 1

Où se trouvent les logs Postfix sur Ubuntu/Debian ?

A) `/var/log/postfix.log`  

B) `/var/log/mail.log`  

C) `/var/log/messages`  

D) `/var/log/syslog`

---

### ✅ Réponse : B

Sur Ubuntu/Debian : `/var/log/mail.log`. Sur Rocky/RHEL : `/var/log/maillog`.

---

## Question 2

Que signifie le code `dsn=2.0.0` dans les logs ?

A) Erreur permanente  

B) Erreur temporaire  

C) Succès  

D) Message en attente

---

### ✅ Réponse : C

`dsn=2.x.x` = **Succès** ! `dsn=4.x.x` = Erreur temporaire, `dsn=5.x.x` = Erreur permanente.

---

## Question 3

Quelle commande affiche en temps réel les rejets de spam ?

A) `mailq`  

B) `tail -f /var/log/mail.log | grep reject`  

C) `postqueue -p`  

D) `postsuper -v`

---

### ✅ Réponse : B

`tail -f /var/log/mail.log | grep reject` affiche en **temps réel** tous les messages rejetés.

---

## Question 4

Quelle commande basée sur `journalctl` affiche en direct les logs Postfix sur une distribution systemd ?

A) `journalctl postfix -f`  

B) `journalctl -u postfix -f`  

C) `journalctl --follow mail.log`  

D) `journalctl -t postfix/smtpd`

---

### ✅ Réponse : B

`journalctl -u postfix -f` suit en temps réel les journaux du service systemd `postfix`, pratique quand `/var/log/mail.log` est géré par `rsyslog` ou `systemd-journald`.

---

## Question 5

Quel est le nom de l'identifiant unique qui permet de suivre un email dans les logs et les files d'attente ?

A) PID  

B) Queue ID  

C) UID  

D) RID

---

### ✅ Réponse : B

Le **Queue ID** (ex : `3F2A641234`) est attribué par Postfix dès la réception et reste visible dans les logs, `mailq`, `postcat`, etc.

---

## Exercice pratique - Module 10

### 🎯 Objectif
Analyser les logs Postfix

### 📋 Tâches (20 minutes)

1. **Suivre les logs en temps réel** :
```bash
sudo tail -f /var/log/mail.log
# Dans un autre terminal, envoyez un email de test
```

2. **Rechercher un message spécifique** :
```bash
# Récupérer le Queue ID depuis mailq
mailq
# Rechercher dans les logs
sudo grep "VOTRE_QUEUE_ID" /var/log/mail.log
```

3. **Analyser les erreurs** :
```bash
sudo grep "status=deferred" /var/log/mail.log | tail -20
sudo grep "status=bounced" /var/log/mail.log | tail -20
```

---

4. **Statistiques des rejets** :
```bash
sudo grep "reject:" /var/log/mail.log | wc -l
sudo grep "reject_rbl_client" /var/log/mail.log | wc -l
```

5. **Utiliser pflogsumm** (si installé) :
```bash
sudo apt install pflogsumm  # Ubuntu/Debian
sudo pflogsumm /var/log/mail.log | less
```

**Bonus** : Créez un script pour générer un rapport quotidien des logs

