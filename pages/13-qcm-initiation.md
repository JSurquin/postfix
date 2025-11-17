---
layout: new-section
routeAlias: 'qcm-initiation'
---

<a name="qcm-initiation" id="qcm-initiation"></a>

# QCM de Validation - Initiation

<div class="mt-2">
  ✅ Testez vos connaissances Postfix !
</div>

---

# Instructions

**Format** : QCM avec une seule bonne réponse par question 

- **Durée** : 30 minutes 

- **Score minimum** : 70% (21/30) 

- **Consignes** : Lisez attentivement chaque question avant de répondre

---

# Questions - Concepts de base

## Question 1

Postfix est un :

A) MUA (Mail User Agent) 

B) MTA (Mail Transfer Agent) 

C) MDA (Mail Delivery Agent) 

D) POP3 server

---

## Question 2

Le fichier de configuration principal de Postfix est :

A) `/etc/postfix/postfix.conf`

B) `/etc/postfix/main.cf`

C) `/etc/postfix/config.cf`

D) `/etc/mail/postfix.conf`

---

## Question 3

Quel processus Postfix gère les files d'attente ?

A) smtpd

B) smtp

C) qmgr

D) cleanup

---

## Question 4

La commande pour recharger la configuration Postfix sans interruption est :

A) `systemctl restart postfix`

B) `systemctl reload postfix`

C) `postfix restart`

D) `postfix refresh`

---

## Question 5

L'enregistrement DNS obligatoire pour un serveur mail est :

A) A

B) CNAME

C) MX

D) TXT

---

# Questions - Configuration

## Question 6

Pour accepter les emails pour `example.com`, il faut configurer :

A) `myorigin`

B) `mydestination`

C) `relay_domains`

D) `virtual_alias_domains`

---

## Question 7

Pour éviter que votre serveur devienne un open relay, il faut absolument avoir :

A) `smtpd_tls_security_level = encrypt`

B) `reject_unauth_destination`

C) `smtpd_helo_required = yes`

D) `disable_vrfy_command = yes`

---

## Question 8

Pour compiler le fichier `/etc/postfix/virtual`, on utilise :

A) `postconf /etc/postfix/virtual`

B) `postmap /etc/postfix/virtual`

C) `newaliases`

D) `postfix reload`

---

## Question 9

Le format de mailbox recommandé en 2025 est :

A) mbox

B) Maildir

C) mh

D) maildir++

---

## Question 10

Pour masquer la version de Postfix dans la bannière, on configure :

A) `smtpd_banner = $myhostname ESMTP`

B) `hide_version = yes`

C) `smtpd_show_version = no`

D) `banner_anonymize = yes`

---

# Questions - Files d'attente

## Question 11

La commande pour voir la file d'attente est :

A) `postqueue`

B) `mailq`

C) `queuelist`

D) `showqueue`

---

## Question 12

Pour supprimer TOUS les messages de la queue, on utilise :

A) `postsuper -d ALL`

B) `postqueue -d ALL`

C) `mailq --delete-all`

D) `postfix flush`

---

## Question 13

Un message avec le statut `deferred` signifie :

A) Le message est supprimé

B) Le message est en erreur temporaire

C) Le message est en erreur permanente

D) Le message est en cours d'envoi

---

## Question 14

Pour forcer l'envoi immédiat de tous les messages en queue :

A) `postsuper -f`

B) `postqueue -f`

C) `mailq -send`

D) `postfix send`

---

# Questions - Sécurité

## Question 15

SPF permet de :

A) Signer cryptographiquement les emails

B) Lister les serveurs autorisés à envoyer pour un domaine

C) Chiffrer les communications SMTP

D) Authentifier les utilisateurs

---

## Question 16

DKIM utilise :

A) Un enregistrement MX

B) Une signature cryptographique

C) Un certificat SSL

D) Un mot de passe

---

## Question 17

Dans un enregistrement SPF, `-all` signifie :

A) Tout est autorisé

B) Tout est rejeté sauf les serveurs listés

C) Pas de SPF

D) Mode test

---

## Question 18

La version TLS minimum recommandée en 2025 est :

A) TLS 1.0

B) TLS 1.1

C) TLS 1.2

D) TLS 1.3

---

## Question 19

Le port standard pour la submission (envoi client) est :

A) 25

B) 465

C) 587

D) 993

---

## Question 20

Pour activer TLS en mode opportuniste (si disponible) :

A) `smtpd_tls_security_level = none`

B) `smtpd_tls_security_level = may`

C) `smtpd_tls_security_level = encrypt`

D) `smtpd_tls_security_level = mandatory`

---

# Questions - Anti-spam

## Question 21

Un RBL (Real-time Blackhole List) permet de :

A) Bloquer les IPs connues pour envoyer du spam

B) Lister les emails légitimes

C) Chiffrer les emails

D) Authentifier les utilisateurs

---

## Question 22

Le RBL le plus utilisé est :

A) spamcop.net

B) zen.spamhaus.org

C) blacklist.mail.com

D) rbl.google.com

---

## Question 23

Le greylisting fonctionne sur le principe que :

A) Les spammeurs ne réessaient pas

B) Les emails sont marqués comme spam

C) Les IPs sont blacklistées

D) Les emails sont chiffrés

---

## Question 24

Pour rejeter les HELO invalides, on configure :

A) `smtpd_helo_required = yes` + `reject_invalid_helo_hostname`

B) `smtpd_reject_helo = yes`

C) `smtpd_check_helo = strict`

D) `helo_restrictions = reject_all`

---

# Questions - Administration

## Question 25

Les logs Postfix se trouvent par défaut dans :

A) `/var/log/postfix.log` (Ubuntu/Debian)

B) `/var/log/mail.log` (Ubuntu/Debian)

C) `/var/log/messages` (Ubuntu/Debian)

D) `/etc/postfix/logs/`

---

## Question 26

Pour suivre les logs en temps réel, on utilise :

A) `cat /var/log/mail.log`

B) `less /var/log/mail.log`

C) `tail -f /var/log/mail.log`

D) `head /var/log/mail.log`

---

## Question 27

Pour vérifier la syntaxe du fichier `main.cf`, on utilise :

A) `postfix test`

B) `postconf -t`

C) `postfix check`

D) `postconf --verify`

---

## Question 28

Un backup de Postfix doit au minimum inclure :

A) `/var/spool/postfix/` uniquement

B) `/etc/postfix/` uniquement

C) `/etc/postfix/` et `/etc/opendkim/`

D) `/var/log/mail.log`

---

## Question 29

Pour automatiser une sauvegarde quotidienne à 2h du matin, on ajoute dans cron :

A) `0 2 * * * /backup.sh`

B) `2 0 * * * /backup.sh`

C) `* 2 * * * /backup.sh`

D) `0 2 1 * * /backup.sh`

---

## Question 30

La règle 3-2-1 pour les sauvegardes signifie :

A) 3 serveurs, 2 backups, 1 admin

B) 3 copies, 2 supports, 1 hors site

C) 3 jours, 2 semaines, 1 mois

D) 3 fichiers, 2 disques, 1 cloud

---

## Réponses - QCM Initiation (1/3)

<small>

**Q1 : B** - MTA (Mail Transfer Agent) - Postfix achemine les emails entre serveurs.

**Q2 : B** - `/etc/postfix/main.cf` - Fichier de configuration centrale.

**Q3 : C** - qmgr - Le Queue Manager gère toutes les files d'attente.

**Q4 : B** - `systemctl reload postfix` - Reload sans couper les connexions.

**Q5 : C** - MX - Enregistrement MX indique le serveur mail du domaine.

**Q6 : B** - `mydestination` - Liste les domaines pour livraison locale.

**Q7 : B** - `reject_unauth_destination` - Empêche le relais non autorisé.

**Q8 : B** - `postmap /etc/postfix/virtual` - Compile les tables.

**Q9 : B** - Maildir - Plus sûr et performant que mbox.

**Q10 : A** - `smtpd_banner = $myhostname ESMTP` - Sans `$mail_name` ou `$mail_version`.

</small>

---

## Réponses - QCM Initiation (2/3)

<small>

**Q11 : B** - `mailq` (ou `postqueue -p`) - Affiche la queue.

**Q12 : A** - `postsuper -d ALL` - Gère les opérations sur la queue.

**Q13 : B** - Erreur temporaire - `deferred` sera réessayé.

**Q14 : B** - `postqueue -f` - Force le traitement immédiat.

**Q15 : B** - Lister les serveurs autorisés - SPF = enregistrement DNS des IPs autorisées.

**Q16 : B** - Signature cryptographique - DKIM signe avec clé privée.

**Q17 : B** - Tout rejeté sauf serveurs listés - `-all` strict (FAIL), `~all` permissif (SOFTFAIL).

**Q18 : C** - TLS 1.2 - TLS 1.2 minimum, idéalement TLS 1.3.

**Q19 : C** - 587 - Port submission avec STARTTLS obligatoire.

**Q20 : B** - `smtpd_tls_security_level = may` - TLS si client supporte.

</small>

---

## Réponses - QCM Initiation (3/3)

<small>

**Q21 : A** - Bloquer IPs spam - RBL = listes noires d'IPs.

**Q22 : B** - zen.spamhaus.org - Spamhaus ZEN le plus fiable.

**Q23 : A** - Spammeurs ne réessaient pas - Greylisting rejette temporairement.

**Q24 : A** - `smtpd_helo_required = yes` + `reject_invalid_helo_hostname` - Combinaison obligatoire.

**Q25 : B** - `/var/log/mail.log` (Ubuntu/Debian) - Sur Rocky : `/var/log/maillog`.

**Q26 : C** - `tail -f /var/log/mail.log` - Suit en temps réel.

**Q27 : C** - `postfix check` - Vérifie la configuration.

**Q28 : C** - `/etc/postfix/` et `/etc/opendkim/` - Config + clés DKIM essentiels.

**Q29 : A** - `0 2 * * * /backup.sh` - Format cron : minute heure jour mois jour-semaine.

**Q30 : B** - 3 copies, 2 supports, 1 hors site - Règle de base sauvegarde.

</small>

---

# Résultats

<small>

## Barème

**27-30/30** : Excellent ! 🏆

**24-26/30** : Très bien ! 🎖️

**21-23/30** : Bien (validation acquise) ✅

**18-20/30** : Passable (à retravailler) ⚠️

**< 18/30** : Insuffisant (révision nécessaire) ❌

## Correction

Comptez vos bonnes réponses et notez les questions ratées.

Pour chaque erreur, relisez le module correspondant.

## Prochaines étapes

Si vous avez validé (≥ 21/30), félicitations ! 🎉

</small>
