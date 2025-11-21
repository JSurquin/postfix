---
layout: new-section
routeAlias: 'protection-anti-spam'
---

<a name="protection-anti-spam" id="protection-anti-spam"></a>

# Protection Anti-Spam

<div class="mt-2">
  🛡️ Défendre votre serveur contre les indésirables
</div>

---

# Introduction au spam

Le spam représente **plus de 50%** du trafic email mondial en 2025 !

Sans protection, votre serveur sera rapidement submergé.

---

## 🎯 Comprendre le spam : Une analogie simple

Imaginez votre serveur mail comme **une boîte aux lettres physique** :

🏠 **Sans protection** = N'importe qui peut y déposer n'importe quoi
- Publicités
- Courriers frauduleux
- Prospectus non sollicités

🔒 **Avec protection** = Un filtre intelligent vérifie chaque courrier
- Identité de l'expéditeur
- Origine du courrier
- Réputation du facteur

---

## Les types de spam (expliqués simplement)

### 📧 Spam publicitaire
**C'est quoi ?** Les prospectus dans votre boîte aux lettres physique

**Exemples** :
- "Gagnez 10000€ par jour !"
- Produits miracles (Viagra, médicaments)
- Casinos en ligne

---

### 🎣 Phishing (Hameçonnage)
**C'est quoi ?** Quelqu'un se fait passer pour votre banque

**Exemples** :
- "Votre compte est bloqué, cliquez ici"
- Fausses factures EDF/impôts
- "Confirmez vos identifiants bancaires"

💡 **Astuce** : Un vrai établissement ne demande JAMAIS vos mots de passe par email !

---

### 🦠 Malware (Logiciels malveillants)
**C'est quoi ?** Des virus cachés dans les pièces jointes

**Exemples** :
- Facture.pdf.exe (faux PDF)
- Ransomware (chiffre vos fichiers)
- Keyloggers (volent vos mots de passe)

---

### 🔄 Backscatter (Rebonds indésirables)
**C'est quoi ?** Un spammeur usurpe VOTRE adresse email

**Scénario** :
1. Spammeur envoie 1000 emails en utilisant `vous@votredomaine.fr`
2. 500 emails rebondissent car adresses invalides
3. VOUS recevez les 500 messages d'erreur

😤 **Résultat** : Vous êtes victime sans rien avoir fait !

---

## 📊 Statistiques alarmantes

En 2025, sur 100 emails reçus :

- 🚫 **50-60** sont du spam
- 🎣 **10-15** sont du phishing
- 🦠 **5-10** contiennent des malwares
- ✅ **25-30** sont légitimes

**Sans protection = serveur saturé en quelques heures !**

---

## 🛡️ La stratégie de défense : Le château fort

Pensez à un **château médiéval** :

```
┌─────────────────────────────────────┐
│         🏰 Votre Serveur Mail       │
│                                     │
│   ┌─────────────────────────────┐  │
│   │    📬 Boîtes mail saines    │  │
│   └─────────────────────────────┘  │
│              ⬆                      │
│         🛡️ Couche 2                │
│    Restrictions SMTP                │
│    (HELO, Sender, etc.)             │
│              ⬆                      │
│         🛡️ Couche 1                │
│    Restrictions réseau              │
│    (RBL, Rate limit)                │
│              ⬆                      │
│         🌐 Internet                 │
└─────────────────────────────────────┘
```

---

**Principe** : Bloquer le spam **le plus tôt possible** !

- Couche 1 : Bloquer les IPs suspectes avant même de parler
- Couche 2 : Vérifier l'identité et les adresses

---

## 🎯 Objectif de ce module

Mettre en place **les bases solides** de protection anti-spam :

✅ **Couche 1** : RBL et rate limiting
✅ **Couche 2** : Restrictions SMTP

💡 **Note** : Les protections avancées (Greylisting, SpamAssassin, Rspamd) seront vues dans la formation **Perfectionnement**.

---

# Couche 1 : Restrictions réseau

## 🎯 Objectif de la Couche 1

Bloquer le spam **avant même** qu'il ne parle à votre serveur !

C'est comme avoir un **videur de boîte de nuit** :
- 🚫 Liste noire en main (RBL)
- ⏱️ Limite le nombre d'entrées par personne (Rate limit)

---

## DNS Blacklists (RBL) - Expliqué simplement

### 🤔 C'est quoi une RBL ?

Imaginez un **fichier de police partagé** entre tous les serveurs mail :

- 🚨 Base de données mondiale des "mauvaises IPs"
- 📋 Maintenue par des organisations (Spamhaus, etc.)
- 🔄 Mise à jour en temps réel

**Si une IP envoie du spam** → elle est ajoutée à la liste
**Les autres serveurs** → la bloquent automatiquement

---

### 🔍 Comment ça marche ? (Version simple)

**Scénario** : Un serveur suspect (`1.2.3.4`) essaie de vous envoyer un email

```
1. 🌐 Serveur 1.2.3.4 se connecte
   ↓
2. 🔍 Postfix demande : "Cette IP est-elle blacklistée ?"
   ↓
3. 📞 Query DNS : 4.3.2.1.zen.spamhaus.org
   ↓
4. 📡 Réponse :
   - Si OUI → 🚫 Rejet immédiat
   - Si NON → ✅ Continuer les vérifications
```

**Résultat** : Spam bloqué en 0,001 seconde ! ⚡

---

### 🎯 Pourquoi inverser l'IP ?

**IP normale** : `1.2.3.4`
**IP inversée** : `4.3.2.1`

**Raison** : Format standard DNS pour les lookups inversés

💡 **Vous n'avez rien à faire**, Postfix le fait automatiquement !

---

### 📋 RBL recommandée : Spamhaus ZEN

**Pourquoi Spamhaus ZEN ?**

✅ **La plus fiable** du marché
✅ **Peu de faux positifs** (<0,01%)
✅ **Gratuite** pour usage normal
✅ **Mise à jour en temps réel**
✅ **Combine 5 listes** en une seule

---

**Les 5 listes combinées** :

1. **SBL** : Spammeurs connus
2. **XBL** : Machines infectées (botnet)
3. **PBL** : Connexions résidentielles (pas de serveur mail)
4. **DROP** : Réseaux détournés/hostiles
5. **EDROP** : Extensions DROP

**En gros** : La crème de la crème des RBL ! 🏆

---

### 💡 Exemple concret de blocage

**Sans RBL** :
```
[Spammer 1.2.3.4] → [Votre serveur] → Spam reçu 😢
```

**Avec RBL** :
```
[Spammer 1.2.3.4] → [RBL Check] → 🚫 BLOQUÉ
                                  ✅ Serveur protégé
```

Le spam n'arrive même pas à votre serveur !

---

### ⚙️ Configuration dans main.cf (Expliquée)

```bash
# Vérification RBL avec Spamhaus ZEN
smtpd_recipient_restrictions = 
    permit_mynetworks,              # 1️⃣ Vos serveurs passent
    permit_sasl_authenticated,      # 2️⃣ Utilisateurs connectés passent
    reject_unauth_destination,      # 3️⃣ Bloque les relais non autorisés
    reject_rbl_client zen.spamhaus.org  # 4️⃣ Check RBL en dernier
```

---

**Ordre important** :

1. **permit_mynetworks** : Ne pas bloquer vos propres serveurs !
2. **permit_sasl_authenticated** : Vos utilisateurs authentifiés OK
3. **reject_unauth_destination** : Empêche le relayage abusif
4. **reject_rbl_client** : RBL en dernier (économise des requêtes DNS)

---

### 🧪 Test pratique : Vérifier une IP

**Méthode 1** : Ligne de commande

```bash
# Tester si 1.2.3.4 est blacklistée
dig 4.3.2.1.zen.spamhaus.org

# Si réponse = 127.0.0.x → IP blacklistée
# Si NXDOMAIN → IP propre
```

---

**Exemple de résultat (IP blacklistée)** :

```bash
$ dig 4.3.2.1.zen.spamhaus.org

;; ANSWER SECTION:
4.3.2.1.zen.spamhaus.org. 300 IN A 127.0.0.2

# 127.0.0.2 = IP dans la liste SBL (spammeurs)
```

---

**Exemple de résultat (IP propre)** :

```bash
$ dig 100.50.25.10.zen.spamhaus.org

;; status: NXDOMAIN
# Pas de réponse = IP pas blacklistée ✅
```

---

**Méthode 2** : Outil en ligne (plus simple)

🌐 **MXToolbox** : https://mxtoolbox.com/blacklists.aspx

1. Entrez l'IP à tester
2. Cliquez sur "Blacklist Check"
3. Résultat instantané avec détails

---

### ⚠️ Comprendre les faux positifs

**Faux positif** = IP légitime blacklistée par erreur

**Causes possibles** :
- IP partagée avec un spammeur
- Hébergeur compromis temporairement
- Erreur de signalement

---

**Symptôme** : Email d'un partenaire rejeté

```
reject: RBL from mail.partner.com[203.0.113.50]:
  blocked using zen.spamhaus.org
```

**Solution** : Whitelister temporairement l'IP

---

### 🎯 Whitelister une IP légitime

Si un partenaire est injustement bloqué :

**Fichier `/etc/postfix/rbl_override`** :

```bash
# IPs à ne pas vérifier avec RBL
203.0.113.50    OK
mail.partner.com    OK
```

---

**Dans main.cf** :

```bash
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    check_client_access hash:/etc/postfix/rbl_override,  # ✅ Check override d'abord
    reject_unauth_destination,
    reject_rbl_client zen.spamhaus.org
```

---

**Activer** :

```bash
sudo postmap /etc/postfix/rbl_override
sudo systemctl reload postfix
```

---

### 📊 Surveiller l'efficacité des RBL

**Compter les blocages RBL du jour** :

```bash
sudo grep "$(date +%b\ %e)" /var/log/mail.log | \
  grep -c "reject_rbl_client"
```

---

**Voir les derniers blocages** :

```bash
sudo grep "reject_rbl_client" /var/log/mail.log | tail -10
```

---

**Exemple de log RBL** :

```
Nov 21 10:15:32 mail postfix/smtpd[1234]: NOQUEUE: 
  reject: RCPT from unknown[1.2.3.4]: 
  554 5.7.1 Service unavailable; 
  Client host [1.2.3.4] blocked using zen.spamhaus.org; 
  from=<spam@evil.com> to=<user@example.com>
```

**Traduction** : IP `1.2.3.4` bloquée car dans Spamhaus ZEN ✅

---

### 💡 Astuce Pro : Utiliser plusieurs RBL

Vous pouvez cumuler plusieurs RBL pour plus de protection :

```bash
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_rbl_client zen.spamhaus.org,
    reject_rbl_client bl.spamcop.net
```

⚠️ **Attention** : Plus de RBL = plus de faux positifs potentiels

---

### 🎓 RBL : Points clés à retenir

✅ **RBL = Liste noire mondiale** d'IPs spammeuses

✅ **Spamhaus ZEN = meilleur choix** pour débuter

✅ **Bloque le spam AVANT** qu'il n'entre

✅ **Très efficace** : ~40-60% du spam bloqué

✅ **Faux positifs rares** mais possibles → whitelist

✅ **Gratuit** pour usage normal

✅ **Économise des ressources** serveur

---

## Rate Limiting - Expliqué simplement

### 🤔 C'est quoi le Rate Limiting ?

Imaginez un **videur de boîte de nuit** :

- ✅ Laisse entrer les gens **un par un**
- 🚫 Si quelqu'un essaie d'entrer **100 fois en 1 minute** → suspect !
- ⏱️ Limite le **nombre d'entrées** par personne

**Rate Limiting = Limiter la vitesse de connexion**

---

### 🎯 Pourquoi c'est important ?

**Scénario sans Rate Limiting** :

Un botnet de 1000 machines zombies attaque votre serveur :
```
[Bot 1] ───→ 100 connexions/sec ───→ [Serveur]
[Bot 2] ───→ 100 connexions/sec ───→ [Saturé]
[Bot 3] ───→ 100 connexions/sec ───→ [💥 Crash]
...
```

**Résultat** : Serveur inaccessible en quelques secondes

---

**Scénario avec Rate Limiting** :

```
[Bot 1] ───→ 30 connexions/min max ───→ [Serveur]
           └→ 70 connexions REJETÉES    [✅ OK]
```

**Résultat** : Serveur protégé, fonctionne normalement

---

### 🛡️ Les 3 types de limites

**1. Connexions simultanées**
→ Combien de connexions **en même temps** ?

**2. Connexions par période**
→ Combien de **nouvelles** connexions par minute ?

**3. Messages par connexion**
→ Combien de mails envoyés dans **une connexion** ?

---

### ⚙️ Configuration avec Anvil (Le gardien)

**Anvil** = Service Postfix qui compte les connexions

```bash
# 1️⃣ Limite de connexions simultanées par IP
smtpd_client_connection_count_limit = 10

# 2️⃣ Limite de taux (nouvelles connexions par période)
smtpd_client_connection_rate_limit = 30

# 3️⃣ Durée de la période (secondes)
anvil_rate_time_unit = 60s
```

---

**Traduction en français** :

- 🔢 Maximum **10 connexions** en même temps par IP
- ⏱️ Maximum **30 nouvelles connexions** par minute
- 📊 Fenêtre de mesure : **60 secondes**

**Au-delà** → Message d'erreur `450 4.7.1 Error: too many connections`

---

### 💡 Exemple concret

**IP normale (Gmail par exemple)** :
```
10h00 : 5 connexions simultanées
10h01 : 3 connexions simultanées
10h02 : 2 connexions simultanées
```
✅ **OK** : Sous les limites

---

**IP suspecte (Bot spammer)** :
```
10h00 : 50 connexions simultanées ❌ > 10 limite
10h00 : 200 nouvelles connexions en 1 min ❌ > 30 limite
```
🚫 **BLOQUÉ** : Dépasse les limites

---

### 📊 Limite de messages par connexion

```bash
# Limite de messages par connexion
smtpd_client_message_rate_limit = 100
```

**Traduction** : Une IP peut envoyer max **100 messages** par connexion

---

**Pourquoi ?**

Les spammeurs essaient d'envoyer **des milliers de mails** dans une seule connexion :

```
[Spammer] MAIL FROM: spam1@evil.com
          RCPT TO: victim1@target.com
          DATA
          ... (répété 10000 fois)
```

**Avec limite** : Après 100 messages → Connexion coupée 🚫

---

### 🎯 Configuration recommandée par type de serveur

**Petit serveur (<100 users)** :
```bash
smtpd_client_connection_count_limit = 10
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 50
anvil_rate_time_unit = 60s
```

---

**Serveur moyen (100-500 users)** :
```bash
smtpd_client_connection_count_limit = 20
smtpd_client_connection_rate_limit = 50
smtpd_client_message_rate_limit = 100
anvil_rate_time_unit = 60s
```

---

**Gros serveur (>500 users)** :
```bash
smtpd_client_connection_count_limit = 50
smtpd_client_connection_rate_limit = 100
smtpd_client_message_rate_limit = 200
anvil_rate_time_unit = 60s
```

---

### 🧪 Test pratique : Voir les limites en action

**Simuler plusieurs connexions** :

```bash
# Terminal 1
telnet localhost 25

# Terminal 2
telnet localhost 25

# Terminal 3
telnet localhost 25

# ... jusqu'à dépasser la limite
```

---

**Résultat attendu (après 10 connexions)** :

```
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
450 4.7.1 Error: too many connections from 127.0.0.1
Connection closed by foreign host.
```

✅ **Ça marche !** Le rate limiting fonctionne

---

### 📊 Surveiller le Rate Limiting

**Voir les rejets par rate limiting** :

```bash
sudo grep "too many connections" /var/log/mail.log | tail -10
```

---

**Exemple de log** :

```
Nov 21 10:15:32 mail postfix/smtpd[1234]: 
  warning: Connection rate limit exceeded: 
  45 from unknown[1.2.3.4] for service smtp
```

**Traduction** : IP `1.2.3.4` a tenté 45 connexions/min (limite : 30) 🚫

---

### 💡 Whitelist : Exclure des IPs légitimes

Si un gros partenaire (Gmail, Microsoft) est bloqué par erreur :

**Créer `/etc/postfix/rate_limit_exemptions`** :

```bash
# IPs exemptées du rate limiting
64.233.160.0/19     OK   # Gmail
40.92.0.0/15        OK   # Microsoft
203.0.113.50        OK   # Partenaire important
```

---

**Dans master.cf**, modifier la ligne smtpd :

```bash
smtp      inet  n       -       y       -       -       smtpd
  -o smtpd_client_connection_count_limit=${stress?{1}:{10}}
  -o smtpd_client_restrictions=check_client_access hash:/etc/postfix/rate_limit_exemptions
```

⚠️ **Configuration avancée** - Pas obligatoire pour débuter

---

### 🎯 Anvil : Le processus qui compte

**Voir les statistiques Anvil** :

```bash
sudo postconf -d | grep anvil
```

---

**Vérifier si Anvil est actif** :

```bash
ps aux | grep anvil
```

**Résultat attendu** :

```
postfix  1234  0.0  0.1  12345  1234  ?  S  10:00  0:00  anvil
```

✅ Anvil tourne en permanence et surveille !

---

### ⚠️ Attention : Ne pas bloquer vos propres utilisateurs

**Mauvaise config** :

```bash
smtpd_client_connection_rate_limit = 5  # ❌ TROP STRICT !
```

**Problème** : Vos utilisateurs avec Outlook/Thunderbird seront bloqués !

**Solution** : Valeurs raisonnables (30-50 pour début)

---

### 🎓 Rate Limiting : Points clés

✅ **Protège contre le flood** et attaques par volume

✅ **3 types de limites** : simultanées, par période, par connexion

✅ **Anvil = gardien** qui compte les connexions

✅ **Valeurs recommandées** :
   - 10-20 connexions simultanées
   - 30-50 nouvelles connexions/min
   - 50-100 messages/connexion

✅ **Whitelist possible** pour gros expéditeurs légitimes

✅ **Log des rejets** dans `/var/log/mail.log`

---

### 💡 Combiner RBL + Rate Limiting = 🔥

**RBL** : Bloque les IPs connues (spam)
**Rate Limiting** : Bloque les comportements suspects (flood)

**Ensemble** : Protection efficace contre 70-80% du spam ! 🛡️

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

Vous pouvez aller plus loin avec les protections avancées suivantes :

- **Greylisting** (Postgrey) : Retarder temporairement les emails inconnus
- **SpamAssassin** : Filtrage de contenu avec score bayésien
- **Rspamd** : Solution moderne et performante
- **Amavis** : Intégration complète anti-virus + anti-spam

---

# Configuration complète anti-spam

## Fichier /etc/postfix/main.cf

Ajouter ces paramètres pour une protection de base efficace :

---

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

## Exercices pratiques progressifs

### 🎯 Exercice 1 : Configuration RBL (Débutant)

**Objectif** : Mettre en place la protection RBL avec Spamhaus

**Niveau** : ⭐ Facile | **Durée** : 10 minutes

---

**Étape 1** : Ouvrir le fichier de configuration

```bash
sudo nano /etc/postfix/main.cf
```

---

**Étape 2** : Trouver la section `smtpd_recipient_restrictions`

Si elle n'existe pas, ajoutez-la :

```bash
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_rbl_client zen.spamhaus.org
```

---

**Étape 3** : Sauvegarder et vérifier

```bash
# Sauvegarder : Ctrl+O puis Entrée, puis Ctrl+X

# Vérifier la syntaxe
sudo postfix check

# Si pas d'erreur, recharger
sudo systemctl reload postfix
```

---

**Étape 4** : Tester manuellement une IP blacklistée

```bash
# Tester l'IP 2.0.0.1 (souvent blacklistée)
dig 1.0.0.2.zen.spamhaus.org

# Si réponse 127.0.0.x = blacklistée ✅
```

---

**Étape 5** : Consulter les rejets

```bash
# Attendre quelques minutes, puis :
sudo grep 'reject_rbl_client' /var/log/mail.log | tail -5
```

---

✅ **Bravo !** Vous avez activé la protection RBL

**Ce que vous avez appris** :
- Configurer une RBL
- Vérifier la config Postfix
- Tester les blocages

---

### 🎯 Exercice 2 : Rate Limiting (Débutant)

**Objectif** : Limiter les connexions pour éviter le flood

**Niveau** : ⭐ Facile | **Durée** : 10 minutes

---

**Étape 1** : Ajouter les limites dans main.cf

```bash
sudo nano /etc/postfix/main.cf
```

Ajouter à la fin :

```bash
# Rate Limiting
smtpd_client_connection_count_limit = 10
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 50
anvil_rate_time_unit = 60s
```

---

**Étape 2** : Sauvegarder et recharger

```bash
sudo postfix check
sudo systemctl reload postfix
```

---

**Étape 3** : Vérifier qu'Anvil est actif

```bash
ps aux | grep anvil
```

**Résultat attendu** : Une ligne avec `anvil` qui tourne

---

**Étape 4** : Tester manuellement (optionnel)

Ouvrir 11 terminaux et faire :

```bash
telnet localhost 25
```

La 11ème connexion devrait être rejetée !

---

✅ **Bravo !** Votre serveur est protégé contre le flood

**Ce que vous avez appris** :
- Configurer le rate limiting
- Vérifier Anvil
- Comprendre les limites de connexion

---

### 🎯 Exercice 3 : Restrictions HELO (Intermédiaire)

**Objectif** : Bloquer les HELO invalides et usurpations

**Niveau** : ⭐⭐ Moyen | **Durée** : 15 minutes

---

**Étape 1** : Configurer les restrictions HELO

```bash
sudo nano /etc/postfix/main.cf
```

Ajouter :

```bash
# Restrictions HELO
smtpd_helo_required = yes
smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    reject_unknown_helo_hostname
```

---

**Étape 2** : Créer le fichier helo_access

```bash
sudo nano /etc/postfix/helo_access
```

Ajouter (remplacez par VOTRE domaine) :

```bash
# Rejeter les usurpations de notre domaine
mail.votredomaine.fr    REJECT  You are not me!
votredomaine.fr         REJECT  You are not me!
```

---

**Étape 3** : Compiler et intégrer

```bash
# Compiler le fichier
sudo postmap /etc/postfix/helo_access

# Ajouter dans main.cf (section helo_restrictions)
smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    check_helo_access hash:/etc/postfix/helo_access,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    reject_unknown_helo_hostname
```

---

**Étape 4** : Recharger et tester

```bash
sudo systemctl reload postfix

# Tester avec telnet
telnet localhost 25
```

```
HELO localhost
# Doit être rejeté : "need fully-qualified hostname"

EHLO votredomaine.fr
# Doit être rejeté : "You are not me!"

EHLO valid-server.com
# Doit passer ✅
```

---

**Étape 5** : Vérifier les logs

```bash
sudo grep 'reject:.*HELO' /var/log/mail.log | tail -5
```

---

✅ **Excellent !** Vous bloquez les HELO suspects

**Ce que vous avez appris** :
- Restrictions HELO
- Fichiers de tables Postfix
- Commande `postmap`
- Test avec telnet

---

### 🎯 Exercice 4 : Configuration complète (Avancé)

**Objectif** : Mettre en place TOUTES les protections anti-spam de base

**Niveau** : ⭐⭐⭐ Avancé | **Durée** : 30 minutes

---

**Mission** : Créer une configuration anti-spam complète avec :

1. ✅ RBL Spamhaus ZEN
2. ✅ Rate Limiting
3. ✅ Restrictions HELO
4. ✅ Restrictions Sender
5. ✅ Restrictions Recipient
6. ✅ Restrictions Client

---

**Fichier main.cf complet** :

```bash
# ============================================
# CONFIGURATION ANTI-SPAM COMPLÈTE
# ============================================

# === RESTRICTIONS CLIENT ===
smtpd_client_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unknown_client_hostname,
    check_client_access hash:/etc/postfix/client_access
```

---

```bash
# === RESTRICTIONS HELO ===
smtpd_helo_required = yes
smtpd_helo_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    check_helo_access hash:/etc/postfix/helo_access,
    reject_invalid_helo_hostname,
    reject_non_fqdn_helo_hostname,
    reject_unknown_helo_hostname
```

---

```bash
# === RESTRICTIONS SENDER ===
smtpd_sender_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    check_sender_access hash:/etc/postfix/sender_access,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain
```

---

```bash
# === RESTRICTIONS RECIPIENT ===
smtpd_recipient_restrictions = 
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain,
    reject_rbl_client zen.spamhaus.org
```

---

```bash
# === RATE LIMITING ===
smtpd_client_connection_count_limit = 10
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 50
anvil_rate_time_unit = 60s

# === SÉCURITÉ GÉNÉRALE ===
disable_vrfy_command = yes
smtpd_delay_reject = yes
```

---

**Créer les fichiers d'accès** :

```bash
# HELO access
sudo touch /etc/postfix/helo_access
sudo postmap /etc/postfix/helo_access

# Sender access
sudo touch /etc/postfix/sender_access
sudo postmap /etc/postfix/sender_access

# Client access
sudo touch /etc/postfix/client_access
sudo postmap /etc/postfix/client_access
```

---

**Vérifier et appliquer** :

```bash
sudo postfix check
sudo systemctl reload postfix
```

---

**Tester la configuration** :

```bash
# Test complet avec telnet
telnet localhost 25

EHLO test.com
MAIL FROM: test@test.com
RCPT TO: user@yourdomain.com
QUIT
```

Regarder les logs pour voir les vérifications :

```bash
sudo tail -f /var/log/mail.log
```

---

✅ **Félicitations !** Configuration anti-spam professionnelle !

**Ce que vous avez appris** :
- Configuration complète multi-couches
- Fichiers de tables multiples
- Tests complets
- Analyse des logs

---

### 🎯 Exercice 5 : Monitoring et statistiques (Intermédiaire)

**Objectif** : Créer un script de monitoring anti-spam

**Niveau** : ⭐⭐ Moyen | **Durée** : 20 minutes

---

**Créer le script** :

```bash
sudo nano /usr/local/bin/spam-stats.sh
```

---

**Contenu du script** :

```bash
#!/bin/bash
# Script de statistiques anti-spam

echo "📊 === STATISTIQUES ANTI-SPAM ==="
echo ""

# RBL
echo "🚫 Blocages RBL:"
sudo grep 'reject_rbl_client' /var/log/mail.log | wc -l

# Rate limiting
echo "⏱️  Blocages Rate Limit:"
sudo grep 'too many connections' /var/log/mail.log | wc -l

# HELO
echo "🎭 Blocages HELO:"
sudo grep 'reject.*HELO' /var/log/mail.log | wc -l

# Total rejets
echo ""
echo "📊 Total rejets aujourd'hui:"
sudo grep "$(date +%b\ %e)" /var/log/mail.log | grep -c 'reject:'

# Top IPs rejetées
echo ""
echo "🏆 Top 5 IPs rejetées:"
sudo grep 'reject:' /var/log/mail.log | \
  grep -oP '\[\d+\.\d+\.\d+\.\d+\]' | \
  sort | uniq -c | sort -rn | head -5
```

---

**Rendre exécutable** :

```bash
sudo chmod +x /usr/local/bin/spam-stats.sh
```

---

**Exécuter** :

```bash
sudo /usr/local/bin/spam-stats.sh
```

---

**Automatiser (optionnel)** :

```bash
# Ajouter dans crontab
sudo crontab -e

# Rapport quotidien à 18h
0 18 * * * /usr/local/bin/spam-stats.sh | mail -s "Stats Anti-Spam" admin@votredomaine.fr
```

---

✅ **Super !** Vous surveillez l'efficacité de votre protection

**Ce que vous avez appris** :
- Créer un script de monitoring
- Extraire des statistiques
- Automatiser avec cron
- Analyser l'efficacité

---

### 📝 Checklist finale Anti-Spam

Avant de passer au module suivant, vérifiez que vous avez :

□ ✅ Activé RBL Spamhaus ZEN
□ ✅ Configuré le Rate Limiting
□ ✅ Mis en place les restrictions HELO
□ ✅ Configuré les restrictions Sender/Recipient
□ ✅ Testé avec telnet
□ ✅ Vérifié les logs
□ ✅ Créé un script de monitoring

---

### 🆘 Aide-mémoire : Commandes essentielles

```bash
# Vérifier la config
sudo postfix check

# Recharger Postfix
sudo systemctl reload postfix

# Voir les rejets
sudo grep 'reject:' /var/log/mail.log | tail -20

# Tester une IP RBL
dig 4.3.2.1.zen.spamhaus.org

# Compiler un fichier de table
sudo postmap /etc/postfix/nom_fichier

# Statistiques du jour
sudo grep "$(date +%b\ %e)" /var/log/mail.log | grep -c 'reject:'
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