---


# QCM - Module 7 : Protection anti-spam

## Question 1

Que signifie RBL ?

A) Real-time Block List  

B) Realtime Blackhole List  

C) Reverse Blacklist  

D) Reject Bad Links

---

## Question 2

Quel est le principe du greylisting ?

A) Bloquer tous les emails gris  

B) Rejeter temporairement les nouveaux expéditeurs  

C) Mettre en liste grise les spammeurs  

D) Filtrer les emails sans couleur

---

## Question 3

Quelle restriction doit TOUJOURS être présente pour éviter un open relay ?

A) `reject_invalid_hostname`  

B) `reject_non_fqdn_sender`  

C) `reject_unauth_destination`  

D) `reject_unknown_sender_domain`

---

## Question 4

Quel service Postfix filtre les connexions SMTP entrantes avant de passer la main à `smtpd` pour bloquer les clients suspects ?

A) `anvil`  

B) `postscreen`  

C) `cleanup`  

D) `tlsproxy`

---

## Question 5

Quel mot-clé place immédiatement votre réseau local en liste blanche dans `smtpd_recipient_restrictions` ?

A) `permit_sasl_authenticated`  

B) `permit_mynetworks`  

C) `check_policy_service`  

D) `reject_unknown_client_hostname`

---

## Réponses - Module 7

<small>

**Question 1 : Réponse B** - **RBL** = Realtime Blackhole List. Ce sont des listes noires DNS d'IPs connues pour envoyer du spam.

**Question 2 : Réponse B** - Le greylisting **rejette temporairement** (code 450) les emails d'expéditeurs inconnus. Les serveurs légitimes réessaient, les spammeurs abandonnent.

**Question 3 : Réponse C** - `reject_unauth_destination` est **ESSENTIEL** ! Sans elle, votre serveur accepte d'envoyer des emails vers n'importe quel domaine = **OPEN RELAY** !

**Question 4 : Réponse B** - `postscreen` réalise des tests de réputation et de protocole sur les nouvelles connexions TCP avant d'autoriser l'accès complet à `smtpd`.

**Question 5 : Réponse B** - `permit_mynetworks` autorise sans délai les clients définis dans `mynetworks`. À placer en premier pour vos administrateurs ou relais internes.

</small>

---

## Exercice pratique - Module 7

<small>

### 🎯 Objectif
Mettre en place une protection anti-spam basique

### 📋 Tâches (25 minutes)

1. **Configurer les RBL** :

```bash
sudo postconf -e "smtpd_recipient_restrictions = \
    permit_mynetworks, \
    permit_sasl_authenticated, \
    reject_unauth_destination, \
    reject_rbl_client zen.spamhaus.org, \
    reject_rbl_client bl.spamcop.net"

sudo systemctl reload postfix
```

</small>

---

2. **Ajouter rate limiting** :

```bash
sudo postconf -e "smtpd_client_connection_count_limit = 10"
sudo postconf -e "smtpd_client_connection_rate_limit = 30"
sudo postconf -e "anvil_rate_time_unit = 60s"
sudo systemctl reload postfix
```

---

3. **Tester une IP** :

```bash
dig 4.3.2.1.zen.spamhaus.org
# Si réponse = IP blacklistée
```

---

4. **Vérifier les logs** :

```bash
sudo tail -f /var/log/mail.log | grep reject
```

---

**Bonus** : Installez et configurez Postgrey pour le greylisting
