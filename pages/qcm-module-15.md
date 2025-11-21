---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 1 : Que signifie un enregistrement MX avec une priorité de 10 vs 20 ?**

- A) Le 10 est plus lent que le 20
- B) Le 10 est prioritaire, le 20 est secondaire ✅
- C) Le 20 est prioritaire, le 10 est secondaire
- D) Les deux ont la même priorité

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 2 : Gmail utilise combien de serveurs MX typiquement ?**

- A) 1 serveur
- B) 2 serveurs
- C) 5 serveurs ou plus ✅
- D) 10 serveurs

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 3 : Que se passe-t-il si le serveur MX prioritaire (10) est indisponible ?**

- A) L'email est perdu
- B) L'email est automatiquement envoyé au MX suivant (20) ✅
- C) L'expéditeur reçoit une erreur immédiate
- D) L'email reste en queue indéfiniment

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 4 : Quelle directive Postfix définit les domaines acceptés en local ?**

- A) `relay_domains`
- B) `mydestination` ✅
- C) `virtual_mailbox_domains`
- D) `mynetworks`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 5 : Pour partager les boîtes mail entre deux MX, quelle solution peut-on utiliser ?**

- A) NFS (Network File System) ✅
- B) FTP
- C) HTTP
- D) Telnet

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 6 : Dans une architecture avec relais, quel paramètre doit être vide sur les MX qui ne stockent pas ?**

- A) `myhostname`
- B) `mydomain`
- C) `mydestination` ✅
- D) `inet_interfaces`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 7 : Que signifient les crochets [] dans `smtp:[192.168.1.50]` ?**

- A) C'est une erreur de syntaxe
- B) Cela évite les lookups MX ✅
- C) Cela force IPv6
- D) Cela active le chiffrement

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 8 : Comment vérifier les enregistrements MX d'un domaine ?**

- A) `ping example.com`
- B) `dig MX example.com` ✅
- C) `telnet example.com`
- D) `nslookup A example.com`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 9 : Quelle est la commande pour voir les emails en queue sur Postfix ?**

- A) `mailq` ✅
- B) `postmap`
- C) `postconf`
- D) `systemctl status postfix`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 10 : Avec Keepalived, quel est l'avantage d'une VIP (IP Virtuelle) ?**

- A) C'est plus sécurisé
- B) Le basculement est ultra-rapide (quelques secondes) ✅
- C) Cela consomme moins de bande passante
- D) Cela permet d'avoir plus d'emails

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 11 : Si deux MX ont la même priorité, que se passe-t-il ?**

- A) Erreur de configuration
- B) Le premier dans l'ordre alphabétique est utilisé
- C) Load balancing possible (round-robin) ✅
- D) Les deux rejettent les emails

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 12 : Quelle directive limite les connexions aux réseaux autorisés ?**

- A) `mydestination`
- B) `mynetworks` ✅
- C) `relay_domains`
- D) `virtual_mailbox_domains`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 13 : Pour utiliser MySQL avec Postfix, quel package est nécessaire ?**

- A) `postfix-sql`
- B) `postfix-mysql` ✅
- C) `mysql-postfix`
- D) `postfix-db`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 14 : Quel fichier contient la configuration principale de Postfix ?**

- A) `/etc/postfix/postfix.conf`
- B) `/etc/postfix/main.cf` ✅
- C) `/etc/postfix/master.cf`
- D) `/etc/postfix/config.cf`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 15 : Comment recharger la configuration Postfix sans couper les connexions actives ?**

- A) `systemctl restart postfix`
- B) `systemctl reload postfix` ✅
- C) `postfix restart`
- D) `kill -9 postfix`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 16 : Quel est le TTL minimum recommandé pour un enregistrement MX ?**

- A) 300s (5 minutes)
- B) 3600s (1 heure) ✅
- C) 86400s (24 heures)
- D) 60s (1 minute)

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 17 : Pour vérifier qu'un serveur SMTP répond, quelle commande utiliser ?**

- A) `ping mail.example.com`
- B) `telnet mail.example.com 25` ✅
- C) `ssh mail.example.com`
- D) `curl mail.example.com`

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 18 : Quel est l'avantage d'avoir des MX dans différents datacenters ?**

- A) C'est moins cher
- B) Résilience géographique en cas de panne datacenter ✅
- C) Meilleure performance locale
- D) Configuration plus simple

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 19 : Que fait la commande `postmap /etc/postfix/transport` ?**

- A) Affiche la table de transport
- B) Supprime la table de transport
- C) Compile la table en format DB ✅
- D) Teste la configuration

---
layout: question
---

# QCM - Redondance et Haute Disponibilité SMTP

**Question 20 : Dans une config SPF, comment inclure tous les serveurs MX ?**

- A) `v=spf1 a -all`
- B) `v=spf1 mx -all` ✅
- C) `v=spf1 ip4:all -all`
- D) `v=spf1 include:mx -all`

---
layout: center
---

# 🎉 QCM Terminé !

**Correction :**

- **16-20 bonnes réponses** : Excellent ! Vous maîtrisez la redondance SMTP 🏆
- **12-15 bonnes réponses** : Bien ! Quelques révisions nécessaires 👍
- **8-11 bonnes réponses** : Correct, mais relisez le module 📚
- **< 8 bonnes réponses** : Revoyez le module sur la redondance 🔄


