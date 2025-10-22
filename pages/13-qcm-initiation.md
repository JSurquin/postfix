---
layout: new-section
routeAlias: 'qcm-initiation'
---

<a name="qcm-initiation" id="qcm-initiation"></a>

# QCM de Validation - Initiation

✅ Testez vos connaissances Postfix !

---

# Instructions

**Format** : QCM avec une seule bonne réponse par question

**Durée** : 30 minutes

**Score minimum** : 70% (21/30)

**Consignes** : Lisez attentivement chaque question avant de répondre

---

# Questions - Concepts de base

## Question 1

Postfix est un :

A) MUA (Mail User Agent)

B) MTA (Mail Transfer Agent)

C) MDA (Mail Delivery Agent)

D) POP3 server

---

### ✅ Réponse correcte

**B) MTA (Mail Transfer Agent)**

Postfix est un MTA qui achemine les emails entre serveurs.

---

## Question 2

Le fichier de configuration principal de Postfix est :

A) `/etc/postfix/postfix.conf`

B) `/etc/postfix/main.cf`

C) `/etc/postfix/config.cf`

D) `/etc/mail/postfix.conf`

---

### ✅ Réponse correcte

**B) `/etc/postfix/main.cf`**

C'est le fichier de configuration centrale de Postfix.

---

## Question 3

Quel processus Postfix gère les files d'attente ?

A) smtpd

B) smtp

C) qmgr

D) cleanup

---

### ✅ Réponse correcte

**C) qmgr**

Le Queue Manager (qmgr) gère toutes les files d'attente.

---

## Question 4

La commande pour recharger la configuration Postfix sans interruption est :

A) `systemctl restart postfix`

B) `systemctl reload postfix`

C) `postfix restart`

D) `postfix refresh`

---

### ✅ Réponse correcte

**B) `systemctl reload postfix`**

Reload recharge la config sans couper les connexions.

---

## Question 5

L'enregistrement DNS obligatoire pour un serveur mail est :

A) A

B) CNAME

C) MX

D) TXT

---

### ✅ Réponse correcte

**C) MX**

L'enregistrement MX indique le serveur mail du domaine.

---

# Questions - Configuration

## Question 6

Pour accepter les emails pour `example.com`, il faut configurer :

A) `myorigin`

B) `mydestination`

C) `relay_domains`

D) `virtual_alias_domains`

---

### ✅ Réponse correcte

**B) `mydestination`**

`mydestination` liste les domaines pour lesquels Postfix livre localement.

---

## Question 7

Pour éviter que votre serveur devienne un open relay, il faut absolument avoir :

A) `smtpd_tls_security_level = encrypt`

B) `reject_unauth_destination`

C) `smtpd_helo_required = yes`

D) `disable_vrfy_command = yes`

---

### ✅ Réponse correcte

**B) `reject_unauth_destination`**

Cette règle empêche le relais non autorisé.

---

## Question 8

Pour compiler le fichier `/etc/postfix/virtual`, on utilise :

A) `postconf /etc/postfix/virtual`

B) `postmap /etc/postfix/virtual`

C) `newaliases`

D) `postfix reload`

---

### ✅ Réponse correcte

**B) `postmap /etc/postfix/virtual`**

`postmap` compile les tables de correspondance.

---

## Question 9

Le format de mailbox recommandé en 2025 est :

A) mbox

B) Maildir

C) mh

D) maildir++

---

### ✅ Réponse correcte

**B) Maildir**

Maildir est plus sûr et performant que mbox.

---

## Question 10

Pour masquer la version de Postfix dans la bannière, on configure :

A) `smtpd_banner = $myhostname ESMTP`

B) `hide_version = yes`

C) `smtpd_show_version = no`

D) `banner_anonymize = yes`

---

### ✅ Réponse correcte

**A) `smtpd_banner = $myhostname ESMTP`**

On personnalise la bannière sans inclure `$mail_name` ou `$mail_version`.

---

# Questions - Files d'attente

## Question 11

La commande pour voir la file d'attente est :

A) `postqueue`

B) `mailq`

C) `queuelist`

D) `showqueue`

---

### ✅ Réponse correcte

**B) `mailq`**

`mailq` (ou `postqueue -p`) affiche la queue.

---

## Question 12

Pour supprimer TOUS les messages de la queue, on utilise :

A) `postsuper -d ALL`

B) `postqueue -d ALL`

C) `mailq --delete-all`

D) `postfix flush`

---

### ✅ Réponse correcte

**A) `postsuper -d ALL`**

`postsuper` gère les opérations sur la queue.

---

## Question 13

Un message avec le statut `deferred` signifie :

A) Le message est supprimé

B) Le message est en erreur temporaire

C) Le message est en erreur permanente

D) Le message est en cours d'envoi

---

### ✅ Réponse correcte

**B) Le message est en erreur temporaire**

`deferred` = échec temporaire, sera réessayé.

---

## Question 14

Pour forcer l'envoi immédiat de tous les messages en queue :

A) `postsuper -f`

B) `postqueue -f`

C) `mailq -send`

D) `postfix send`

---

### ✅ Réponse correcte

**B) `postqueue -f`**

Force le traitement immédiat de la queue.

---

# Questions - Sécurité

## Question 15

SPF permet de :

A) Signer cryptographiquement les emails

B) Lister les serveurs autorisés à envoyer pour un domaine

C) Chiffrer les communications SMTP

D) Authentifier les utilisateurs

---

### ✅ Réponse correcte

**B) Lister les serveurs autorisés à envoyer pour un domaine**

SPF est un enregistrement DNS qui liste les IPs/serveurs autorisés.

---

## Question 16

DKIM utilise :

A) Un enregistrement MX

B) Une signature cryptographique

C) Un certificat SSL

D) Un mot de passe

---

### ✅ Réponse correcte

**B) Une signature cryptographique**

DKIM signe les emails avec une clé privée.

---

## Question 17

Dans un enregistrement SPF, `-all` signifie :

A) Tout est autorisé

B) Tout est rejeté sauf les serveurs listés

C) Pas de SPF

D) Mode test

---

### ✅ Réponse correcte

**B) Tout est rejeté sauf les serveurs listés**

`-all` est strict (FAIL), `~all` est permissif (SOFTFAIL).

---

## Question 18

La version TLS minimum recommandée en 2025 est :

A) TLS 1.0

B) TLS 1.1

C) TLS 1.2

D) TLS 1.3

---

### ✅ Réponse correcte

**C) TLS 1.2**

TLS 1.2 minimum, idéalement TLS 1.3.

---

## Question 19

Le port standard pour la submission (envoi client) est :

A) 25

B) 465

C) 587

D) 993

---

### ✅ Réponse correcte

**C) 587**

Port 587 = submission avec STARTTLS obligatoire.

---

## Question 20

Pour activer TLS en mode opportuniste (si disponible) :

A) `smtpd_tls_security_level = none`

B) `smtpd_tls_security_level = may`

C) `smtpd_tls_security_level = encrypt`

D) `smtpd_tls_security_level = mandatory`

---

### ✅ Réponse correcte

**B) `smtpd_tls_security_level = may`**

`may` = TLS si le client le supporte.

---

# Questions - Anti-spam

## Question 21

Un RBL (Real-time Blackhole List) permet de :

A) Bloquer les IPs connues pour envoyer du spam

B) Lister les emails légitimes

C) Chiffrer les emails

D) Authentifier les utilisateurs

---

### ✅ Réponse correcte

**A) Bloquer les IPs connues pour envoyer du spam**

Les RBL sont des listes noires d'IPs.

---

## Question 22

Le RBL le plus utilisé est :

A) spamcop.net

B) zen.spamhaus.org

C) blacklist.mail.com

D) rbl.google.com

---

### ✅ Réponse correcte

**B) zen.spamhaus.org**

Spamhaus ZEN est le plus fiable et utilisé.

---

## Question 23

Le greylisting fonctionne sur le principe que :

A) Les spammeurs ne réessaient pas

B) Les emails sont marqués comme spam

C) Les IPs sont blacklistées

D) Les emails sont chiffrés

---

### ✅ Réponse correcte

**A) Les spammeurs ne réessaient pas**

Le greylisting rejette temporairement et attend que le serveur réessaie.

---

## Question 24

Pour rejeter les HELO invalides, on configure :

A) `smtpd_helo_required = yes` + `reject_invalid_helo_hostname`

B) `smtpd_reject_helo = yes`

C) `smtpd_check_helo = strict`

D) `helo_restrictions = reject_all`

---

### ✅ Réponse correcte

**A) `smtpd_helo_required = yes` + `reject_invalid_helo_hostname`**

Combinaison obligatoire pour rejeter les HELO invalides.

---

# Questions - Administration

## Question 25

Les logs Postfix se trouvent par défaut dans :

A) `/var/log/postfix.log` (Ubuntu/Debian)

B) `/var/log/mail.log` (Ubuntu/Debian)

C) `/var/log/messages` (Ubuntu/Debian)

D) `/etc/postfix/logs/`

---

### ✅ Réponse correcte

**B) `/var/log/mail.log` (Ubuntu/Debian)**

Sur Rocky Linux c'est `/var/log/maillog`.

---

## Question 26

Pour suivre les logs en temps réel, on utilise :

A) `cat /var/log/mail.log`

B) `less /var/log/mail.log`

C) `tail -f /var/log/mail.log`

D) `head /var/log/mail.log`

---

### ✅ Réponse correcte

**C) `tail -f /var/log/mail.log`**

`tail -f` suit le fichier en temps réel.

---

## Question 27

Pour vérifier la syntaxe du fichier `main.cf`, on utilise :

A) `postfix test`

B) `postconf -t`

C) `postfix check`

D) `postconf --verify`

---

### ✅ Réponse correcte

**C) `postfix check`**

`postfix check` vérifie la configuration.

---

## Question 28

Un backup de Postfix doit au minimum inclure :

A) `/var/spool/postfix/` uniquement

B) `/etc/postfix/` uniquement

C) `/etc/postfix/` et `/etc/opendkim/`

D) `/var/log/mail.log`

---

### ✅ Réponse correcte

**C) `/etc/postfix/` et `/etc/opendkim/`**

Configuration + clés DKIM sont essentiels. Les certificats SSL aussi idéalement.

---

## Question 29

Pour automatiser une sauvegarde quotidienne à 2h du matin, on ajoute dans cron :

A) `0 2 * * * /backup.sh`

B) `2 0 * * * /backup.sh`

C) `* 2 * * * /backup.sh`

D) `0 2 1 * * /backup.sh`

---

### ✅ Réponse correcte

**A) `0 2 * * * /backup.sh`**

Format cron : minute heure jour mois jour-semaine.

---

## Question 30

La règle 3-2-1 pour les sauvegardes signifie :

A) 3 serveurs, 2 backups, 1 admin

B) 3 copies, 2 supports, 1 hors site

C) 3 jours, 2 semaines, 1 mois

D) 3 fichiers, 2 disques, 1 cloud

---

### ✅ Réponse correcte

**B) 3 copies, 2 supports, 1 hors site**

Règle de base pour une bonne stratégie de sauvegarde.

---

# Résultats

## Barème

**27-30/30** : Excellent ! 🏆

**24-26/30** : Très bien ! 🎖️

**21-23/30** : Bien (validation acquise) ✅

**18-20/30** : Passable (à retravailler) ⚠️

**< 18/30** : Insuffisant (révision nécessaire) ❌

---

## Correction

Comptez vos bonnes réponses et notez les questions ratées.

Pour chaque erreur, relisez le module correspondant.

---

## Prochaines étapes

Si vous avez validé (≥ 21/30), félicitations ! 🎉

Vous pouvez passer à la formation **Perfectionnement** !

---

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Formation Perfectionnement <carbon:arrow-right class="inline"/>
  </span>
</div>

