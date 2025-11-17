---
layout: question
routeAlias: 'qcm-dovecot'
---

<a name="qcm-dovecot" id="qcm-dovecot"></a>

# 📝 QCM Dovecot

## Validation des connaissances

---

## Question 1 : Rôle de Dovecot

**Quel est le rôle principal de Dovecot dans un serveur de messagerie ?**

A) Envoyer des emails vers d'autres serveurs

B) Recevoir des emails depuis Internet

C) Permettre aux utilisateurs de récupérer leurs emails (IMAP/POP3)

D) Filtrer les spams

---

## Question 2 : Ports standards

**Quels sont les ports standards pour IMAPS et SMTPS ?**

A) 143 et 25

B) 993 et 465

C) 995 et 587

D) 110 et 25

---

## Question 3 : Format de stockage

**Quel format de stockage des emails est recommandé avec Dovecot en 2025 ?**

A) mbox

B) Maildir

C) dbox

D) mdbox

---

## Question 4 : Authentification SMTP

**Comment Postfix authentifie-t-il les utilisateurs pour l'envoi d'emails via Dovecot ?**

A) Via PAM uniquement

B) Via le socket auth de Dovecot (SASL)

C) Via une base de données MySQL directe

D) Via des fichiers passwd

---

## Question 5 : Protocole LMTP

**Pourquoi utiliser LMTP au lieu de LDA pour la livraison locale ?**

A) LMTP est plus rapide

B) LMTP supporte la livraison simultanée à plusieurs destinataires

C) LMTP ne nécessite pas de privilèges élevés

D) Toutes les réponses ci-dessus

---

## Question 6 : SSL/TLS

**Quelle directive Dovecot force l'utilisation de SSL/TLS ?**

A) `ssl = yes`

B) `ssl = required`

C) `ssl_force = yes`

D) `disable_plaintext_auth = yes`

---

## Question 7 : Utilisateur vmail

**Pourquoi créer un utilisateur système 'vmail' pour Dovecot ?**

A) C'est obligatoire pour compiler Dovecot

B) Pour séparer les privilèges et sécuriser les boîtes emails

C) Pour la compatibilité avec Postfix uniquement

D) Ce n'est pas nécessaire

---

## Question 8 : Configuration client

**Quel paramètre SMTP doit-on utiliser pour l'envoi d'emails en 2025 ?**

A) Port 25 sans authentification

B) Port 465 avec SSL/TLS

C) Port 587 avec STARTTLS

D) B et C sont corrects

---

## Question 9 : IMAP vs POP3

**Quelle est la principale différence entre IMAP et POP3 ?**

A) IMAP est plus sécurisé que POP3

B) IMAP garde les emails sur le serveur, POP3 les télécharge

C) POP3 est plus rapide qu'IMAP

D) IMAP ne supporte pas SSL/TLS

---

## Question 10 : Sieve

**Qu'est-ce que Sieve dans Dovecot ?**

A) Un protocole de synchronisation

B) Un langage de filtrage des emails côté serveur

C) Un outil de compression des boîtes emails

D) Un système de sauvegarde

---

## Question 11 : Logs Dovecot

**Où se trouvent les logs de Dovecot par défaut ?**

A) `/var/log/mail.log`

B) `/var/log/dovecot/dovecot.log`

C) `/var/log/syslog`

D) `/var/log/messages`

---

## Question 12 : Commande doveadm

**Que fait la commande `doveadm auth test user@example.com` ?**

A) Crée un nouvel utilisateur

B) Teste l'authentification d'un utilisateur

C) Supprime un utilisateur

D) Change le mot de passe

---

## Question 13 : Certificats SSL

**Quelle commande permet de tester une connexion IMAPS ?**

A) `telnet mail.example.com 993`

B) `openssl s_client -connect mail.example.com:993`

C) `curl https://mail.example.com:993`

D) `nc mail.example.com 993`

---

## Question 14 : Quotas

**Comment définir un quota de 1GB par utilisateur dans Dovecot ?**

A) `quota = 1GB`

B) `quota_rule = *:storage=1GB`

C) `user_quota = 1024M`

D) `mailbox_size_limit = 1GB`

---

## Question 15 : Paramètres DH

**Pourquoi générer des paramètres DH pour Dovecot ?**

A) Pour améliorer la sécurité SSL/TLS (Perfect Forward Secrecy)

B) Pour accélérer les connexions

C) Pour la compatibilité avec Outlook

D) Ce n'est plus nécessaire en 2025

---

## Question 16 : Firewall

**Quelles commandes UFW sont nécessaires pour Dovecot ?**

A) `ufw allow 25,587`

B) `ufw allow 143,993,110,995`

C) `ufw allow 80,443`

D) `ufw allow 3306`

---

## Question 17 : Intégration Postfix

**Quelle directive Postfix définit l'utilisation de Dovecot LMTP ?**

A) `mailbox_command = dovecot-lmtp`

B) `virtual_transport = lmtp:unix:private/dovecot-lmtp`

C) `mailbox_transport = lmtp:unix:private/dovecot-lmtp`

D) `local_transport = dovecot`

---

## Question 18 : Fail2ban

**Pourquoi configurer Fail2ban avec Dovecot ?**

A) Pour améliorer les performances

B) Pour protéger contre les attaques par force brute

C) Pour compresser les logs

D) Pour créer des sauvegardes automatiques

---

## Question 19 : Format mail_location

**Que signifie `mail_location = maildir:/var/mail/vhosts/%d/%n` ?**

A) %d = nom d'utilisateur, %n = domaine

B) %d = domaine, %n = nom d'utilisateur (avant @)

C) %d = date, %n = numéro

D) %d = répertoire, %n = nom complet

---

## Question 20 : Debug mode

**Comment activer temporairement les logs de debug dans Dovecot ?**

A) `debug = yes` dans dovecot.conf

B) `mail_debug = yes` et `auth_debug = yes`

C) `loglevel = debug`

D) `dovecot --debug`

---

## Réponses - QCM Dovecot (1/2)

<small>

**Question 1 : Réponse C** - Dovecot est un serveur IMAP/POP3 qui permet aux clients email de récupérer leurs messages.

**Question 2 : Réponse B** - 993 : IMAPS / 465 : SMTPS / 143 : IMAP / 587 : SMTP STARTTLS / 110 : POP3 / 995 : POP3S

**Question 3 : Réponse B** - **Maildir** : un fichier par email, concurrent-safe, performant, compatible.

**Question 4 : Réponse B** - Via socket Unix : `smtpd_sasl_type = dovecot` et `smtpd_sasl_path = private/auth`

**Question 5 : Réponse D** - LMTP = plus rapide, livraison groupée, pas de privilèges root, meilleure gestion erreurs.

**Question 6 : Réponse B** - `ssl = required` force SSL/TLS sur toutes les connexions.

**Question 7 : Réponse B** - Séparation des privilèges, sécurité, UID/GID fixes, isolation des boîtes.

**Question 8 : Réponse D** - Port 587 + STARTTLS (recommandé) et Port 465 + SSL/TLS sont valides en 2025.

**Question 9 : Réponse B** - IMAP : sync, emails restent sur serveur. POP3 : téléchargement, emails supprimés.

**Question 10 : Réponse B** - **Sieve** : langage de filtrage côté serveur (tri, spam, réponses auto).

</small>

---

## Réponses - QCM Dovecot (2/2)

<small>

**Question 11 : Réponse B** - `/var/log/dovecot/dovecot.log`, `/var/log/dovecot/info.log`, `/var/log/dovecot/debug.log`

**Question 12 : Réponse B** - `doveadm auth test` teste l'authentification sans client email.

**Question 13 : Réponse B** - `openssl s_client -connect mail.example.com:993` teste SSL/TLS + certificat.

**Question 14 : Réponse B** - Dans `90-quota.conf` : `quota_rule = *:storage=1GB`

**Question 15 : Réponse A** - Paramètres DH = Perfect Forward Secrecy (PFS), 4096 bits recommandé.

**Question 16 : Réponse B** - Ports IMAP/IMAPS/POP3/POP3S : 143, 993, 110, 995.

**Question 17 : Réponse C** - `mailbox_transport = lmtp:unix:private/dovecot-lmtp` dans `main.cf`

**Question 18 : Réponse B** - Protection contre force brute et tentatives de connexion échouées.

**Question 19 : Réponse B** - %d = domaine, %n = nom local. Ex : `user@ex.com` → `/var/mail/vhosts/ex.com/user/`

**Question 20 : Réponse B** - `mail_debug = yes`, `auth_debug = yes`, `auth_debug_passwords = yes` (⚠️ désactiver après)

</small>

---

## 📊 Scoring

<small>

**Notation :**

- **18-20 bonnes réponses** : Excellent ! 🏆 Vous maîtrisez parfaitement Dovecot

- **15-17 bonnes réponses** : Très bien ! 🎯 Solides connaissances, quelques révisions conseillées

- **12-14 bonnes réponses** : Bien 👍 Bonnes bases, revoir certains points avancés

- **Moins de 12** : À revoir 📚 Relire le module et refaire les exercices

</small>

---

## 🎯 Points clés à retenir

<small>

1. **Dovecot = IMAP/POP3** (récupération des emails)
2. **Postfix = SMTP** (envoi/réception)
3. **LMTP** : Protocole de livraison local recommandé
4. **Maildir** : Format de stockage moderne
5. **SSL/TLS obligatoire** en production
6. **Ports** : 993 (IMAPS), 587 (Submission)
7. **vmail** : Utilisateur dédié pour la sécurité
8. **SASL** : Authentification Postfix via Dovecot
9. **Sieve** : Filtres côté serveur
10. **Fail2ban** : Protection contre les attaques

</small>

---

## 📚 Pour aller plus loin

<small>

**Sujets avancés :**
- Haute disponibilité (réplication Dovecot)
- Clustering et load balancing
- Authentification LDAP/Active Directory
- Antispam côté serveur (Rspamd + Sieve)
- Monitoring avancé (Prometheus, Grafana)

</small>

---

## 🎓 Félicitations !

Vous avez terminé le module Dovecot !

<small>

**Vous savez maintenant :**
- ✅ Installer et configurer Dovecot
- ✅ Intégrer avec Postfix via LMTP et SASL
- ✅ Sécuriser avec SSL/TLS modernes
- ✅ Configurer les clients email
- ✅ Diagnostiquer et résoudre les problèmes
- ✅ Optimiser les performances

</small>

---

## 🚀 Prochaines étapes

<small>

Pour un serveur de messagerie complet :

1. ✅ Postfix configuré et sécurisé
2. ✅ Dovecot installé et intégré
3. 🔄 SPF, DKIM, DMARC configurés
4. 🔄 Antispam (Rspamd/SpamAssassin)
5. 🔄 Monitoring (logs, alertes)
6. 🔄 Sauvegardes automatiques
7. 🔄 Tests de délivrabilité

</small>

---
layout: intro
---

# 🎉 Module Dovecot terminé !

Bravo ! Vous êtes prêt à déployer un serveur de messagerie complet.

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Retour au sommaire <carbon:arrow-right class="inline"/>
  </span>
</div>
