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

---

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

---

**Malware**
- Pièces jointes infectées
- Liens malveillants
- Ransomware

---

**Backscatter**
- Bounces de spam
- Votre domaine usurpé
- Retour d'emails que vous n'avez pas envoyés

---

## Stratégie de défense en couches

Pensez à un château fort :

1. **Douves** : Restrictions réseau (RBL, rate limiting)
2. **Remparts** : Restrictions SMTP (HELO, sender, recipient)
3. **Gardes** : Vérifications d'authenticité (SPF, DKIM, DMARC)
4. **Inspection** : Content filtering (SpamAssassin, Rspamd)

---

# Couche 1 : Restrictions réseau

## DNS Blacklists (RBL)

Les RBL sont des listes d'IPs connues pour envoyer du spam.

---

### 🔍 Comment ça marche ?

Quand un serveur se connecte à vous :
1. Postfix extrait son IP : `1.2.3.4`
2. Inverse l'IP : `4.3.2.1`
3. Query DNS : `4.3.2.1.zen.spamhaus.org`
4. Si réponse = IP est blacklistée → Rejet

---

### 📋 RBL populaires en 2025

**Spamhaus ZEN** (le plus utilisé)
- `zen.spamhaus.org`
- Combine plusieurs listes
- Très fiable, peu de faux positifs

---

**Barracuda**
- `b.barracudacentral.org`
- Spécialisé dans les botnets

---

**SpamCop**
- `bl.spamcop.net`
- Basé sur les signalements utilisateurs

---

**SORBS**
- `dnsbl.sorbs.net`
- Détecte les proxys ouverts et relais

---

### ⚙️ Configuration dans main.cf

```sql
# Vérification RBL
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_rbl_client zen.spamhaus.org,
    reject_rbl_client b.barracudacentral.org,
    reject_rbl_client bl.spamcop.net
```

---

### ⚠️ Attention aux faux positifs !

Les RBL peuvent blacklister des IPs légitimes.

**Solution** : Utiliser plusieurs RBL et scorer (on verra ça plus tard avec SpamAssassin)

---

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

---

### ⚙️ Configuration avec Anvil

```sql
# Limite de connexions simultanées par IP
smtpd_client_connection_count_limit = 10

# Limite de taux (connexions par période)
smtpd_client_connection_rate_limit = 30

# Durée de la période (secondes)
anvil_rate_time_unit = 60s
```

---

**Exemple** : Avec ces paramètres, une IP peut :
- Maximum 10 connexions simultanées
- Maximum 30 nouvelles connexions par minute

Au-delà → Rejet temporaire

---

### 📊 Messages par connexion

```sql
# Limite de messages par connexion
smtpd_client_message_rate_limit = 100
```

---

## Greylisting

Technique basée sur le principe : **les spammeurs ne réessaient pas**.

---

### 🔍 Fonctionnement

1. Premier email d'un expéditeur inconnu → Rejet temporaire (450)
2. Serveur légitime réessaie après quelques minutes → Accepté
3. Spammer abandonne immédiatement → Bloqué

---

### 📦 Installation de Postgrey

```bash
# Ubuntu/Debian
sudo apt install postgrey

# Rocky Linux
sudo dnf install postgrey
```

---

### ⚙️ Configuration Postfix

Dans `/etc/postfix/main.cf` :

```sql
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    check_policy_service inet:127.0.0.1:10023
```

---

### 🔧 Configuration Postgrey

Fichier `/etc/default/postgrey` :

```bash
POSTGREY_OPTS="--inet=127.0.0.1:10023 --delay=300"
```

`--delay=300` : Délai de 5 minutes avant d'accepter

---

### ♻️ Redémarrer

```bash
sudo systemctl restart postgrey
sudo systemctl reload postfix
```

---

### ⚠️ Inconvénient

Le greylisting retarde **tous** les premiers emails (même légitimes) de 5-10 minutes.

Peut être gênant pour certains services (notifications urgentes, codes OTP).

---

**Solution** : Whitelist pour certains domaines importants

```bash
# /etc/postgrey/whitelist_clients.local
gmail.com
microsoft.com
paypal.com
```

---

# Couche 2 : Restrictions SMTP

## Vérifications HELO/EHLO

Le HELO est la première chose que dit un serveur SMTP.

Beaucoup de spammeurs ont un HELO invalide ou suspect.

---

### 🚫 Rejeter HELO invalides

```sql
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

```sql
smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    check_helo_access hash:/etc/postfix/helo_access
```

---

Fichier `/etc/postfix/helo_access` :

```sql
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

---

## Vérifications Sender

Vérifier que l'expéditeur est valide.

---

### 🚫 Restrictions sender

```sql
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

```sql
# Blacklist
spammer@spam.com        REJECT
@spam-domain.com        REJECT

# Whitelist
trusted@partner.com     OK
@trusted-partner.com    OK
```

---

```sql
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

---

## Vérifications Recipient

S'assurer que le destinataire est légitime.

---

### 🚫 Restrictions recipient

```sql
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

---

## Vérifications Client

Restrictions basées sur l'IP/hostname du client.

---

### 🚫 Restrictions client

```sql
smtpd_client_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unknown_client_hostname,
    check_client_access hash:/etc/postfix/client_access
```

---

Fichier `/etc/postfix/client_access` :

```sql
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

# Couche 3 : Content Filtering

## SpamAssassin

Le filtre anti-spam le plus populaire.

---

### 📦 Installation

```bash
# Ubuntu/Debian
sudo apt install spamassassin spamc

# Rocky Linux
sudo dnf install spamassassin
```

---

### ⚙️ Configuration SpamAssassin

Fichier `/etc/spamassassin/local.cf` :

```perl
# Score requis pour marquer comme spam
required_score 5.0

# Réécrire le sujet
rewrite_header Subject [***SPAM***]

# Utiliser Bayesian learning
use_bayes 1
bayes_auto_learn 1
```

---

### 🔧 Activer le service

```bash
sudo systemctl enable spamassassin
sudo systemctl start spamassassin
```

---

### 🔗 Intégration avec Postfix

**Méthode 1** : Via master.cf (simple mais limité)

Dans `/etc/postfix/master.cf`, modifier la ligne `smtp` :

```sql
smtp      inet  n       -       y       -       -       smtpd
  -o content_filter=spamassassin
```

---

Ajouter à la fin de `master.cf` :

```sql
spamassassin unix -     n       n       -       -       pipe
  user=spamd argv=/usr/bin/spamc -f -e  
  /usr/sbin/sendmail -oi -f ${sender} ${recipient}
```

---

**Méthode 2** : Via Amavis (avancé, recommandé pour production)

Nous verrons ça dans le module "Content filters et milters".

---

### 🎯 Entraîner SpamAssassin

SpamAssassin utilise le **Bayesian learning** pour s'améliorer.

---

**Entraîner avec du spam** :

```bash
sa-learn --spam /path/to/spam/folder
```

---

**Entraîner avec du ham (emails légitimes)** :

```bash
sa-learn --ham /path/to/ham/folder
```

---

**Voir les statistiques** :

```bash
sa-learn --dump magic
```

---

### 📊 Tester SpamAssassin

```bash
# Tester un email
spamassassin -t < email.txt

# Voir le score uniquement
spamc < email.txt | grep X-Spam-Status
```

---

## Rspamd (alternative moderne)

Rspamd est plus rapide et plus moderne que SpamAssassin.

---

### 📦 Installation

```bash
# Ubuntu/Debian
sudo apt install rspamd

# Rocky Linux
sudo dnf install rspamd
```

---

### ⚙️ Configuration basique

Fichier `/etc/rspamd/local.d/worker-controller.inc` :

```
password = "votre_mot_de_passe_hash";
enable_password = "votre_mot_de_passe_hash";
```

---

Générer le hash :

```bash
rspamadm pw
```

---

### 🌐 Interface web

Rspamd fournit une interface web sur `http://serveur:11334`

---

### 🔗 Intégration avec Postfix

Via milter (nous verrons ça dans le module dédié).

---

# Combinaison des protections

## Configuration complète recommandée

```sql
# === RESTRICTIONS CLIENT ===
smtpd_client_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unknown_client_hostname,
    check_client_access hash:/etc/postfix/client_access
```

---

```sql
# === RESTRICTIONS HELO ===
smtpd_helo_required = yes
smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    reject_unknown_helo_hostname
```

---

```sql
# === RESTRICTIONS SENDER ===
smtpd_sender_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain,
    check_sender_access hash:/etc/postfix/sender_access
```

---

```sql
# === RESTRICTIONS RECIPIENT ===
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain,
    reject_rbl_client zen.spamhaus.org,
    reject_rbl_client b.barracudacentral.org,
    check_policy_service inet:127.0.0.1:10023
```

---

```sql
# === RATE LIMITING ===
smtpd_client_connection_count_limit = 10
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 100
anvil_rate_time_unit = 60s

# === AUTRES ===
disable_vrfy_command = yes
smtpd_delay_reject = yes
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

```
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

---

## Affiner les règles

### 🎯 Réduire les faux positifs

Si des emails légitimes sont rejetés :

1. **Identifier la règle responsable** dans les logs
2. **Whitelister** le domaine/IP si légitime
3. **Ajuster le score** si utilisation de SpamAssassin

---

**Exemple de whitelist** :

```sql
# /etc/postfix/sender_access
partenaire-important.com    OK

# /etc/postfix/client_access
203.0.113.50                OK
```

---

### 🔥 Augmenter la sévérité

Si trop de spam passe :

1. Ajouter plus de RBL
2. Réduire le seuil SpamAssassin (`required_score`)
3. Activer le greylisting
4. Ajouter des règles custom

---

## Scripts utiles

### 🔍 Analyser les spams reçus

```bash
#!/bin/bash
# spam-analysis.sh

echo "=== Analyse des spams reçus ==="
echo "Top 10 expéditeurs :"
sudo grep 'X-Spam-Status: Yes' /var/log/mail.log | \
  grep -oP 'from=<[^>]+>' | sort | uniq -c | sort -rn | head -10
```

---

### 📊 Rapport quotidien

```bash
#!/bin/bash
# anti-spam-report.sh

DATE=$(date +%Y-%m-%d)

echo "=== Rapport anti-spam $DATE ==="
echo ""
echo "Rejets par type :"
sudo grep 'reject:' /var/log/mail.log | \
  awk '{print $7}' | sort | uniq -c | sort -rn
echo ""
echo "Top 5 IPs rejetées :"
```

---

```bash
sudo grep 'reject:' /var/log/mail.log | \
  grep -oP '\[\d+\.\d+\.\d+\.\d+\]' | \
  sort | uniq -c | sort -rn | head -5
echo ""
echo "Spams détectés par SpamAssassin :"
sudo grep 'X-Spam-Status: Yes' /var/log/mail.log | wc -l
```

---

## Troubleshooting

### ❌ Problème : Trop d'emails légitimes rejetés

**Diagnostic** : Consulter les logs

```bash
sudo grep 'reject:' /var/log/mail.log | tail -50
```

---

**Solution** :

1. Identifier la règle trop stricte
2. Whitelister les domaines/IPs légitimes
3. Ajuster les paramètres

---

### ❌ Problème : RBL trop lente

**Symptôme** : Postfix timeout lors de connexions

---

**Solution** : Réduire le nombre de RBL

```sql
# Garder seulement les plus fiables
smtpd_recipient_restrictions = 
    ...
    reject_rbl_client zen.spamhaus.org
```

---

### ❌ Problème : Greylisting trop agressif

**Solution** : Whitelister les domaines importants

```bash
# /etc/postgrey/whitelist_clients.local
gmail.com
outlook.com
important-client.com
```

---

## Exercices pratiques

### 🎯 Exercice 1 : Configuration RBL

1. Ajoutez 2-3 RBL à votre configuration
2. Testez avec une IP blacklistée connue
3. Consultez les logs pour voir les rejets

---

### 🎯 Exercice 2 : Restrictions HELO

1. Configurez les restrictions HELO
2. Testez avec telnet en envoyant un HELO invalide
3. Vérifiez le rejet

---

### 🎯 Exercice 3 : Greylisting

1. Installez et configurez Postgrey
2. Envoyez un email depuis un nouveau serveur
3. Observez le délai avant acceptance

---

### 🎯 Exercice 4 : SpamAssassin

1. Installez SpamAssassin
2. Configurez l'intégration avec Postfix
3. Envoyez un email de test avec GTUBE (test de spam)
4. Vérifiez qu'il est marqué comme spam

---

**GTUBE** (Generic Test for Unsolicited Bulk Email) :

```
XJS*C4JDBQADN1.NSBN3*2IDNEN*GTUBE-STANDARD-ANTI-UBE-TEST-EMAIL*C.34X
```

---

## Points clés à retenir

### 💡 Défense en profondeur

**Couche 1** : Réseau (RBL, rate limiting)

**Couche 2** : SMTP (HELO, sender, recipient)

**Couche 3** : Contenu (SpamAssassin, Rspamd)

---

### 💡 Balance faux positifs / faux négatifs

Trop strict = emails légitimes rejetés

Trop laxiste = spam qui passe

**Solution** : Ajuster progressivement et monitorer

---

### 💡 Commandes essentielles

```bash
# Recharger config
sudo systemctl reload postfix

# Voir les rejets
sudo grep 'reject:' /var/log/mail.log

# Tester SpamAssassin
spamassassin -t < email.txt
```

---

## Prochaine étape

La protection anti-spam de base est en place ! Maintenant, renforçons l'**authenticité** de vos emails avec **DKIM, SPF et DMARC** ! 🔐

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : DKIM, SPF et DMARC <carbon:arrow-right class="inline"/>
  </span>
</div>

