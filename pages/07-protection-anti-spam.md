---
layout: new-section
routeAlias: 'protection-anti-spam'
---

<a name="protection-anti-spam" id="protection-anti-spam"></a>

# Protection Anti-Spam

🛡️ Défendre votre serveur contre les indésirables

---

# Introduction au spam

Le spam représente **plus de 50%** du trafic email mondial en 2025 !

Sans protection, votre serveur sera rapidement submergé.

## Les types de spam

**Spam publicitaire**
- Produits douteux
- Arnaques
- Viagra, casinos, etc.

---

**Phishing**
- Usurpation d'identité
- Vol de données bancaires
- Fausses factures

**Malware**
- Pièces jointes infectées
- Liens malveillants
- Ransomware


**Backscatter**
- Bounces de spam
- Votre domaine usurpé
- Retour d'emails que vous n'avez pas envoyés

---

## Stratégie de défense en couches

Pensez à un château fort :

1. **Couche 1** : Restrictions réseau (RBL, rate limiting)
2. **Couche 2** : Restrictions SMTP (HELO, sender, recipient, client)

💡 **Note** : Les protections avancées (Greylisting, SpamAssassin, Rspamd) seront vues dans la formation **Perfectionnement**.

---

# Couche 1 : Restrictions réseau

## DNS Blacklists (RBL)

Les RBL sont des listes d'IPs connues pour envoyer du spam.

### 🔍 Comment ça marche ?

Quand un serveur se connecte à vous :
1. Postfix extrait son IP : `1.2.3.4`
2. Inverse l'IP : `4.3.2.1`
3. Query DNS : `4.3.2.1.zen.spamhaus.org`
4. Si réponse = IP est blacklistée → Rejet

---

### 📋 RBL recommandé pour débuter

**Spamhaus ZEN** (le plus utilisé et fiable) :
- Domaine : `zen.spamhaus.org`
- Combine plusieurs listes Spamhaus
- Très fiable, peu de faux positifs
- **Le meilleur choix pour une configuration de base**

---

### ⚙️ Configuration dans main.cf

```bash
# Vérification RBL avec Spamhaus ZEN
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_rbl_client zen.spamhaus.org
```

---

### ⚠️ Attention aux faux positifs !

Les RBL peuvent parfois blacklister des IPs légitimes.

**Solution** : Whitelister les IPs/domaines de confiance si nécessaire

### ✅ Tester si une IP est blacklistée

```bash
# Tester manuellement
dig 4.3.2.1.zen.spamhaus.org

# Ou avec un outil en ligne
# https://mxtoolbox.com/blacklists.aspx
```

---

## Rate Limiting

Limiter le nombre de connexions par IP pour empêcher le flood.

### ⚙️ Configuration avec Anvil

```bash
# Limite de connexions simultanées par IP
smtpd_client_connection_count_limit = 10

# Limite de taux (connexions par période)
smtpd_client_connection_rate_limit = 30

# Durée de la période (secondes)
anvil_rate_time_unit = 60s
```

**Exemple** : Avec ces paramètres, une IP peut avoir maximum 10 connexions simultanées et 30 nouvelles connexions par minute. Au-delà → Rejet temporaire

### 📊 Messages par connexion

```bash
# Limite de messages par connexion
smtpd_client_message_rate_limit = 100
```

---

# Couche 2 : Restrictions SMTP

## Vérifications HELO/EHLO

Le HELO est la première chose que dit un serveur SMTP.

Beaucoup de spammeurs ont un HELO invalide ou suspect.

---

### 🚫 Rejeter HELO invalides

```bash
smtpd_helo_required = yes

smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    reject_unknown_helo_hostname
```

---

**Explications** :

- `reject_invalid_helo_hostname` : HELO syntaxiquement incorrect
- `reject_non_fqdn_helo_hostname` : HELO pas en FQDN (ex: "localhost")
- `reject_unknown_helo_hostname` : HELO dont le domaine n'existe pas en DNS

---

### 🎭 Rejeter HELO se faisant passer pour vous

```bash
smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    check_helo_access hash:/etc/postfix/helo_access
```

Fichier `/etc/postfix/helo_access` :

```bash
# Rejeter les serveurs qui prétendent être nous
mail.example.com    REJECT  You are not me!
example.com         REJECT  You are not me!
192.168.1.10        REJECT  You are not my IP!
```

---

```bash
sudo postmap /etc/postfix/helo_access
sudo systemctl reload postfix
```

## Vérifications Sender

Vérifier que l'expéditeur est valide.

### 🚫 Restrictions sender

```bash
smtpd_sender_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain
```

---

**Explications** :

- `reject_non_fqdn_sender` : Expéditeur pas en FQDN (ex: "user")
- `reject_unknown_sender_domain` : Domaine expéditeur n'existe pas en DNS

---

### 📝 Blacklist/Whitelist manuelle

Fichier `/etc/postfix/sender_access` :

```bash
# Blacklist
spammer@spam.com        REJECT
@spam-domain.com        REJECT

# Whitelist
trusted@partner.com     OK
@trusted-partner.com    OK
```

---

```bash
smtpd_sender_restrictions = 
    check_sender_access hash:/etc/postfix/sender_access,
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain
```

---

```bash
sudo postmap /etc/postfix/sender_access
sudo systemctl reload postfix
```

## Vérifications Recipient

S'assurer que le destinataire est légitime.

### 🚫 Restrictions recipient

```bash
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain,
    reject_rbl_client zen.spamhaus.org
```

---

**CRUCIAL** : `reject_unauth_destination` empêche votre serveur d'être un open relay !

## Vérifications Client

Restrictions basées sur l'IP/hostname du client.

### 🚫 Restrictions client

```bash
smtpd_client_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unknown_client_hostname,
    check_client_access hash:/etc/postfix/client_access
```

---

Fichier `/etc/postfix/client_access` :

```bash
# Blacklist IP
1.2.3.4                     REJECT  Spammer IP
10.0.0.0/8                  REJECT  Private network

# Whitelist
trusted.partner.com         OK
203.0.113.0/24              OK
```

---

```bash
sudo postmap /etc/postfix/client_access
sudo systemctl reload postfix
```

---

## 💡 Pour aller plus loin

Les protections avancées suivantes seront vues dans la formation **Perfectionnement Postfix** :

- **Greylisting** (Postgrey) : Retarder temporairement les emails inconnus
- **SpamAssassin** : Filtrage de contenu avec score bayésien
- **Rspamd** : Solution moderne et performante
- **Amavis** : Intégration complète anti-virus + anti-spam

---

# Configuration complète anti-spam

## Fichier /etc/postfix/main.cf

Ajouter ces paramètres pour une protection de base efficace :

```bash
# ============================================
# PROTECTION ANTI-SPAM - CONFIGURATION BASE
# ============================================

# === RESTRICTIONS CLIENT ===
smtpd_client_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unknown_client_hostname,
    check_client_access hash:/etc/postfix/client_access

# === RESTRICTIONS HELO ===
smtpd_helo_required = yes
smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    reject_unknown_helo_hostname

# === RESTRICTIONS SENDER ===
smtpd_sender_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain,
    check_sender_access hash:/etc/postfix/sender_access

# === RESTRICTIONS RECIPIENT ===
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain,
    reject_rbl_client zen.spamhaus.org

# === RATE LIMITING ===
smtpd_client_connection_count_limit = 10
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 100
anvil_rate_time_unit = 60s

# === SÉCURITÉ GÉNÉRALE ===
disable_vrfy_command = yes
smtpd_delay_reject = yes
```

---

## Appliquer la configuration

```bash
# Vérifier la configuration
postfix check
```

---

```bash
# Recharger Postfix
sudo systemctl reload postfix
```

---

## Ordre des restrictions

**Important** : L'ordre compte !

1. `permit_mynetworks` en premier (vos serveurs passent)
2. `permit_sasl_authenticated` ensuite (utilisateurs authentifiés passent)
3. `reject_unauth_destination` avant les RBL (économise des requêtes DNS)
4. RBL en dernier (coûteux en requêtes DNS)

---

## Monitoring de l'efficacité

### 📊 Statistiques des rejets

```bash
# Compter les rejets par type
sudo grep 'reject:' /var/log/mail.log | \
  awk '{print $7}' | sort | uniq -c | sort -rn
```

---

**Exemple de sortie** :

```bash
 1523 RCPT
  842 HELO
  312 Client
  156 Sender
```

---

### 📈 Top des IPs rejetées

```bash
sudo grep 'reject:' /var/log/mail.log | \
  grep -oP '\[\d+\.\d+\.\d+\.\d+\]' | \
  sort | uniq -c | sort -rn | head -20
```

---

### 🚫 Raisons de rejet

```bash
sudo grep 'reject:' /var/log/mail.log | tail -20
```


## Troubleshooting rapide

### ❌ Problème : Emails légitimes rejetés

**Diagnostic** :

```bash
sudo grep 'reject:' /var/log/mail.log | tail -20
```

---

**Solution** :

1. Identifier la règle responsable
2. Whitelister l'IP ou le domaine légitime
3. Recharger Postfix

---

**Exemple de whitelist** :

```bash
# /etc/postfix/sender_access
partenaire-important.com    OK

# /etc/postfix/client_access
203.0.113.50                OK
```

---

```bash
sudo postmap /etc/postfix/sender_access
sudo postmap /etc/postfix/client_access
sudo systemctl reload postfix
```

---

## Exercices pratiques

### 🎯 Exercice 1 : Configuration RBL

**Objectif** : Mettre en place la protection RBL avec Spamhaus

---

**Tâches** :

1. Ajouter Spamhaus ZEN dans `smtpd_recipient_restrictions`
2. Recharger Postfix
3. Tester avec une IP blacklistée : `dig 4.3.2.1.zen.spamhaus.org`
4. Consulter les logs pour voir les rejets

---

**Commandes utiles** :

```bash
sudo nano /etc/postfix/main.cf
sudo systemctl reload postfix
sudo grep 'reject_rbl_client' /var/log/mail.log
```

---

### 🎯 Exercice 2 : Restrictions HELO complètes

**Objectif** : Bloquer les HELO invalides et suspects

---

**Tâches** :

1. Configurer les restrictions HELO dans main.cf
2. Créer le fichier `/etc/postfix/helo_access`
3. Ajouter votre domaine pour rejeter les usurpations
4. Tester avec telnet

---

**Test avec telnet** :

```bash
telnet localhost 25
HELO localhost
# Doit être rejeté car non-FQDN

HELO votre-domaine.com
# Doit être rejeté car usurpation
```

---

**Vérifier les logs** :

```bash
sudo grep 'reject:.*HELO' /var/log/mail.log
```

---

## Points clés à retenir

### 💡 Protection anti-spam de base

**Couche 1 - Réseau** :
- RBL Spamhaus ZEN (le plus fiable)
- Rate limiting (10 connexions, 30/min)

**Couche 2 - SMTP** :
- Restrictions HELO (rejet HELO invalides)
- Restrictions Sender (domaines valides)
- Restrictions Recipient (éviter open relay)
- Restrictions Client (blacklist/whitelist IPs)

---

### 💡 Ordre des règles = important !

1. `permit_mynetworks` (vos serveurs)
2. `permit_sasl_authenticated` (utilisateurs authentifiés)
3. `reject_unauth_destination` (anti open relay)
4. Restrictions diverses
5. `reject_rbl_client` en dernier (coûteux)

---

### 💡 Commandes essentielles

```bash
# Vérifier la configuration
postfix check

# Recharger config
sudo systemctl reload postfix

# Voir les rejets
sudo grep 'reject:' /var/log/mail.log | tail -20

# Statistiques des rejets
sudo grep 'reject:' /var/log/mail.log | \
  awk '{print $7}' | sort | uniq -c | sort -rn
```

---

### 💡 Équilibre et ajustement

- Trop strict → emails légitimes rejetés
- Trop laxiste → spam qui passe

**Solution** : Monitorer les logs et ajuster progressivement

**Whitelist** : Toujours possible pour les partenaires de confiance

---

## Prochaine étape

La protection anti-spam de base est en place ! Maintenant, attaquons les logs !

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Logs et monitoring <carbon:arrow-right class="inline"/>
  </span>
</div>

