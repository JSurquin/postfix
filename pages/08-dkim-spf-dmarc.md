---
layout: new-section
routeAlias: 'dkim-spf-dmarc'
---

<a name="dkim-spf-dmarc" id="dkim-spf-dmarc"></a>

# DKIM, SPF et DMARC

🔐 Authentifier vos emails et améliorer votre délivrabilité

---

# Introduction

Sans SPF, DKIM et DMARC, vos emails risquent de :
- Être marqués comme spam
- Être rejetés par Gmail, Outlook, etc.
- Permettre l'usurpation de votre domaine (spoofing)

En 2025, ces trois standards sont **obligatoires** pour toute infrastructure mail professionnelle.

---

## Les 3 piliers de l'authentification

**SPF** : "Quels serveurs peuvent envoyer des emails pour mon domaine ?"

**DKIM** : "Cet email vient vraiment de moi (signature cryptographique)"

**DMARC** : "Comment traiter les emails qui échouent SPF/DKIM ?"

---

## Analogie

**SPF** = Liste des facteurs autorisés

**DKIM** = Cachet officiel de la poste

**DMARC** = Politique de traitement des lettres suspectes

---

# SPF (Sender Policy Framework)

## Qu'est-ce que SPF ?

SPF est un enregistrement DNS qui liste les serveurs autorisés à envoyer des emails pour votre domaine.

---

### 🔍 Comment ça marche ?

1. Serveur `spammer.com` envoie un email prétendant venir de `example.com`
2. Serveur destinataire vérifie l'enregistrement SPF de `example.com`
3. L'IP `spammer.com` n'est pas dans la liste → Rejet !

---

## Format d'un enregistrement SPF

```
v=spf1 <mécanismes> <qualifiers> <all>
```

---

### 📋 Exemple simple

```
v=spf1 mx a ip4:203.0.113.10 -all
```

---

**Décryptage** :

- `v=spf1` : Version SPF
- `mx` : Les serveurs MX du domaine peuvent envoyer
- `a` : Le serveur A du domaine peut envoyer
- `ip4:203.0.113.10` : Cette IP peut envoyer
- `-all` : Rejeter tout le reste

---

### 🎯 Mécanismes SPF

**a** : Autorise l'enregistrement A du domaine

```
v=spf1 a -all
```

---

**mx** : Autorise les serveurs MX du domaine

```
v=spf1 mx -all
```

---

**ip4:IP** : Autorise une IP ou plage IPv4

```
v=spf1 ip4:203.0.113.10 ip4:203.0.113.0/24 -all
```

---

**ip6:IP** : Autorise une IP IPv6

```
v=spf1 ip6:2001:db8::1 -all
```

---

**include:domaine** : Inclut le SPF d'un autre domaine

```
v=spf1 include:_spf.google.com -all
```

(Utile si vous utilisez Gmail pour envoyer)

---

**exists:domaine** : Vérifie l'existence d'un enregistrement A

(Rarement utilisé)

---

### 🏷️ Qualifiers

**+** : PASS (autorisé) - défaut

```
v=spf1 +mx -all
# Équivalent à
v=spf1 mx -all
```

---

**-** : FAIL (rejeté)

```
v=spf1 mx -all
```

---

**~** : SOFTFAIL (suspect mais pas rejeté)

```
v=spf1 mx ~all
```

---

**?** : NEUTRAL (neutre, pas de recommandation)

```
v=spf1 mx ?all
```

---

### 💡 Quelle fin choisir ?

**-all** : Strict (recommandé si vous contrôlez tous vos serveurs)

**~all** : Permissif (si vous avez peur de bloquer des emails légitimes)

**?all** : Très permissif (déconseillé)

---

En 2025, utilisez **-all** si possible !

---

## Créer votre enregistrement SPF

### 📝 Étape 1 : Lister vos serveurs d'envoi

- Votre serveur Postfix : `mail.example.com` (IP: 203.0.113.10)
- Gmail (G Suite) pour certains utilisateurs
- Service marketing (ex: Mailchimp)

---

### 📋 Étape 2 : Construire l'enregistrement

```
v=spf1 
  a:mail.example.com 
  ip4:203.0.113.10 
  include:_spf.google.com 
  include:servers.mcsv.net 
  -all
```

---

### 🌐 Étape 3 : Ajouter l'enregistrement DNS

**Type** : TXT

**Nom** : `example.com` (ou `@`)

**Valeur** :

```
v=spf1 a mx ip4:203.0.113.10 -all
```

---

### ⏱️ Étape 4 : Attendre la propagation DNS

Cela peut prendre de quelques minutes à 48 heures.

---

### ✅ Étape 5 : Tester

```bash
# Vérifier l'enregistrement SPF
dig example.com TXT +short | grep spf

# Ou avec nslookup
nslookup -type=TXT example.com
```

---

**Outils en ligne** :

- https://mxtoolbox.com/spf.aspx
- https://www.kitterman.com/spf/validate.html

---

## Limites de SPF

⚠️ **Maximum 10 lookups DNS** dans un enregistrement SPF !

Chaque `include:` compte comme un lookup.

---

**Exemple qui dépasse** :

```
v=spf1 
  include:spf1.example.com 
  include:spf2.example.com 
  include:spf3.example.com 
  ... (10 includes)
  -all
```

→ SPF invalide !

---

**Solution** : Remplacer les `include:` par des IPs directes quand possible.

---

# DKIM (DomainKeys Identified Mail)

## Qu'est-ce que DKIM ?

DKIM signe cryptographiquement vos emails pour prouver qu'ils viennent vraiment de vous.

---

### 🔍 Comment ça marche ?

1. Postfix signe l'email avec une clé privée
2. La signature est ajoutée dans les headers (`DKIM-Signature:`)
3. Destinataire récupère la clé publique via DNS
4. Vérifie la signature

---

**Analogie** : C'est comme un cachet de cire sur une lettre médiévale. Si le cachet est brisé, la lettre a été modifiée.

---

## Installation d'OpenDKIM

```bash
# Ubuntu/Debian
sudo apt install opendkim opendkim-tools

# Rocky Linux
sudo dnf install opendkim
```

---

## Configuration d'OpenDKIM

### 📝 Fichier /etc/opendkim.conf

```
# Mode
Mode                    sv

# Domaines à signer
Domain                  example.com

# Sélecteur
Selector                mail

# Clés
KeyFile                 /etc/opendkim/keys/example.com/mail.private

# Socket pour Postfix
Socket                  inet:8891@localhost
```

---

```
# Logging
Syslog                  yes
SyslogSuccess           yes
LogWhy                  yes

# Canonicalisation
Canonicalization        relaxed/simple

# Autres
AutoRestart             yes
AutoRestartRate         10/1h
Background              yes
DNSTimeout              5
```

---

### 🔑 Générer les clés DKIM

```bash
# Créer le répertoire
sudo mkdir -p /etc/opendkim/keys/example.com

# Générer la paire de clés
sudo opendkim-genkey -b 2048 -d example.com -D /etc/opendkim/keys/example.com -s mail -v
```

---

**Paramètres** :

- `-b 2048` : Taille de la clé (2048 bits recommandé en 2025)
- `-d example.com` : Domaine
- `-D /path` : Répertoire de sortie
- `-s mail` : Sélecteur
- `-v` : Verbose

---

Cela crée deux fichiers :

- `mail.private` : Clé privée (garde secret !)
- `mail.txt` : Clé publique (à publier en DNS)

---

### 🔒 Permissions

```bash
sudo chown opendkim:opendkim /etc/opendkim/keys/example.com/mail.private
sudo chmod 600 /etc/opendkim/keys/example.com/mail.private
```

---

### 📋 Fichier /etc/opendkim/KeyTable

```
mail._domainkey.example.com example.com:mail:/etc/opendkim/keys/example.com/mail.private
```

Format : `selector._domainkey.domain  domain:selector:keyfile`

---

### 📋 Fichier /etc/opendkim/SigningTable

```
*@example.com mail._domainkey.example.com
```

Format : `pattern  key`

---

### 📋 Fichier /etc/opendkim/TrustedHosts

```
127.0.0.1
localhost
203.0.113.10
*.example.com
```

---

### ♻️ Démarrer OpenDKIM

```bash
sudo systemctl enable opendkim
sudo systemctl start opendkim
sudo systemctl status opendkim
```

---

## Intégration avec Postfix

### ⚙️ Configuration dans main.cf

```sql
# DKIM signing via OpenDKIM
smtpd_milters = inet:localhost:8891
non_smtpd_milters = inet:localhost:8891
milter_default_action = accept
milter_protocol = 6
```

---

```bash
sudo systemctl reload postfix
```

---

## Publier la clé publique DKIM en DNS

### 📄 Récupérer la clé publique

```bash
sudo cat /etc/opendkim/keys/example.com/mail.txt
```

---

**Contenu** :

```
mail._domainkey IN TXT ( "v=DKIM1; h=sha256; k=rsa; "
"p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA..."
"..." ) ;  ----- DKIM key mail for example.com
```

---

### 🌐 Ajouter l'enregistrement DNS

**Type** : TXT

**Nom** : `mail._domainkey.example.com`

**Valeur** :

```
v=DKIM1; h=sha256; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA...
```

---

⚠️ **Important** : Supprimez les guillemets et concaténez les lignes !

---

### ✅ Vérifier l'enregistrement DNS

```bash
dig mail._domainkey.example.com TXT +short
```

---

**Outils en ligne** :

- https://mxtoolbox.com/dkim.aspx

---

## Tester DKIM

### 📧 Envoyer un email de test

```bash
echo "Test DKIM" | mail -s "Test DKIM" test@gmail.com
```

---

### 🔍 Vérifier la signature

Dans Gmail, ouvrez l'email → "Afficher l'original" → Cherchez :

```
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple;
    d=example.com; s=mail; t=1234567890;
    h=from:to:subject:date:message-id;
    bh=...;
    b=...
```

---

Et vérifiez :

```
dkim=pass header.d=example.com
```

✅ DKIM fonctionne !

---

## Rotation des clés DKIM

**Bonne pratique** : Changer les clés DKIM tous les 6-12 mois.

---

**Processus** :

1. Générer une nouvelle paire de clés avec un nouveau sélecteur
2. Publier la nouvelle clé publique en DNS
3. Configurer OpenDKIM pour utiliser la nouvelle clé
4. Attendre 48h (propagation DNS)
5. Supprimer l'ancienne clé du DNS

---

# DMARC (Domain-based Message Authentication, Reporting & Conformance)

## Qu'est-ce que DMARC ?

DMARC combine SPF et DKIM et définit une **politique** : que faire si SPF ou DKIM échoue ?

---

### 🔍 Comment ça marche ?

1. Email arrive chez le destinataire
2. Vérification SPF : ✅ ou ❌
3. Vérification DKIM : ✅ ou ❌
4. Consulte la politique DMARC
5. Applique l'action (accepter, quarantaine, rejeter)
6. Envoie un rapport au propriétaire du domaine

---

## Format d'un enregistrement DMARC

```
v=DMARC1; p=reject; rua=mailto:dmarc@example.com; ruf=mailto:dmarc@example.com; pct=100
```

---

### 📋 Tags DMARC

**v** : Version (toujours `DMARC1`)

**p** : Politique (none, quarantine, reject)

**rua** : Adresse pour les rapports agrégés

**ruf** : Adresse pour les rapports forensiques

**pct** : Pourcentage d'emails concernés (0-100)

**adkim** : Alignement DKIM (s=strict, r=relaxed)

**aspf** : Alignement SPF (s=strict, r=relaxed)

**sp** : Politique pour les sous-domaines

---

### 🎯 Politiques DMARC

**none** : Monitoring uniquement (aucune action)

```
v=DMARC1; p=none; rua=mailto:dmarc@example.com
```

Utilisez ça au début pour surveiller !

---

**quarantine** : Marquer comme spam

```
v=DMARC1; p=quarantine; rua=mailto:dmarc@example.com
```

---

**reject** : Rejeter complètement

```
v=DMARC1; p=reject; rua=mailto:dmarc@example.com
```

Le plus strict ! À utiliser quand vous êtes sûr de votre config.

---

## Créer votre enregistrement DMARC

### 📋 Étape 1 : Démarrer avec "none"

```
v=DMARC1; p=none; rua=mailto:dmarc-reports@example.com; pct=100
```

---

### 🌐 Étape 2 : Ajouter l'enregistrement DNS

**Type** : TXT

**Nom** : `_dmarc.example.com`

**Valeur** :

```
v=DMARC1; p=none; rua=mailto:dmarc-reports@example.com; pct=100
```

---

### 📊 Étape 3 : Surveiller les rapports

Les serveurs qui reçoivent vos emails envoient des rapports XML à `dmarc-reports@example.com`.

---

**Format des rapports** : XML (pas très lisible...)

**Solution** : Utilisez un service comme :
- https://dmarc.postmarkapp.com/ (gratuit)
- https://dmarcian.com/
- https://mxtoolbox.com/dmarc.aspx

---

### 🔒 Étape 4 : Passer à "quarantine" puis "reject"

Après 1-2 semaines de monitoring sans problème :

```
v=DMARC1; p=quarantine; rua=mailto:dmarc-reports@example.com; pct=100
```

---

Après 1 mois :

```
v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com; pct=100
```

---

### ✅ Tester DMARC

```bash
dig _dmarc.example.com TXT +short
```

---

**Outils en ligne** :

- https://mxtoolbox.com/dmarc.aspx
- https://dmarcian.com/dmarc-inspector/

---

## Configuration complète SPF + DKIM + DMARC

### 🌐 Enregistrements DNS

**Enregistrement SPF** :

```
Type: TXT
Nom: example.com (ou @)
Valeur: v=spf1 a mx ip4:203.0.113.10 -all
```

---

**Enregistrement DKIM** :

```
Type: TXT
Nom: mail._domainkey.example.com
Valeur: v=DKIM1; h=sha256; k=rsa; p=MIIBIjAN...
```

---

**Enregistrement DMARC** :

```
Type: TXT
Nom: _dmarc.example.com
Valeur: v=DMARC1; p=reject; rua=mailto:dmarc@example.com; pct=100
```

---

## Vérification complète

### ✅ Test via email

Envoyez un email à : `check-auth@verifier.port25.com`

Vous recevrez un rapport complet sur SPF, DKIM, DMARC !

---

### ✅ Test via outils

- https://www.mail-tester.com/ (note sur 10)
- https://mxtoolbox.com/emailhealth/

---

## Troubleshooting

### ❌ Problème : SPF échoue

**Cause 1** : Enregistrement SPF incorrect

```bash
dig example.com TXT +short | grep spf
```

Vérifiez la syntaxe !

---

**Cause 2** : Email envoyé depuis une IP non autorisée

**Solution** : Ajoutez l'IP au SPF ou utilisez un relais autorisé

---

### ❌ Problème : DKIM échoue

**Cause 1** : Clé publique pas en DNS ou incorrecte

```bash
dig mail._domainkey.example.com TXT +short
```

---

**Cause 2** : OpenDKIM ne signe pas

```bash
sudo systemctl status opendkim
sudo tail -f /var/log/mail.log | grep dkim
```

---

**Cause 3** : Permissions sur la clé privée

```bash
sudo chmod 600 /etc/opendkim/keys/example.com/mail.private
sudo chown opendkim:opendkim /etc/opendkim/keys/example.com/mail.private
```

---

### ❌ Problème : DMARC échoue

**Cause** : SPF **ET** DKIM échouent tous les deux

**Solution** : Fixez au moins un des deux !

---

## Cas d'usage avancés

### 🔄 Plusieurs domaines

**OpenDKIM** :

```
# KeyTable
mail._domainkey.example.com example.com:mail:/etc/opendkim/keys/example.com/mail.private
mail._domainkey.example.org example.org:mail:/etc/opendkim/keys/example.org/mail.private

# SigningTable
*@example.com mail._domainkey.example.com
*@example.org mail._domainkey.example.org
```

---

### 📧 Sous-domaines

**DMARC avec politique pour sous-domaines** :

```
v=DMARC1; p=reject; sp=quarantine; rua=mailto:dmarc@example.com
```

- `p=reject` : Politique pour `example.com`
- `sp=quarantine` : Politique pour `*.example.com`

---

## Points clés à retenir

### 💡 SPF

- Enregistrement TXT sur `example.com`
- Liste les serveurs autorisés
- Finir par `-all` (strict) ou `~all` (permissif)
- Maximum 10 lookups DNS

---

### 💡 DKIM

- Signature cryptographique des emails
- Clé privée sur le serveur (OpenDKIM)
- Clé publique en DNS (`selector._domainkey.domain`)
- Rotation des clés tous les 6-12 mois

---

### 💡 DMARC

- Combine SPF + DKIM
- Définit la politique en cas d'échec
- Enregistrement TXT sur `_dmarc.example.com`
- Commencer par `p=none`, finir par `p=reject`

---

### 💡 Progression recommandée

1. **Semaine 1** : Configurer SPF et DKIM
2. **Semaine 2** : Activer DMARC avec `p=none`
3. **Semaine 4** : Passer à `p=quarantine`
4. **Semaine 8** : Passer à `p=reject`

---

## Exercices pratiques

### 🎯 Exercice 1 : SPF

1. Créez un enregistrement SPF pour votre domaine
2. Publiez-le en DNS
3. Testez avec `dig` et un outil en ligne

---

### 🎯 Exercice 2 : DKIM

1. Installez OpenDKIM
2. Générez une paire de clés
3. Configurez Postfix pour signer
4. Publiez la clé publique en DNS
5. Envoyez un email de test et vérifiez la signature

---

### 🎯 Exercice 3 : DMARC

1. Créez un enregistrement DMARC avec `p=none`
2. Publiez-le en DNS
3. Envoyez des emails et consultez les rapports

---

### 🎯 Exercice 4 : Test complet

1. Envoyez un email à `check-auth@verifier.port25.com`
2. Analysez le rapport reçu
3. Testez sur https://www.mail-tester.com/
4. Visez un score de 10/10 !

---

## Prochaine étape

SPF, DKIM et DMARC sont configurés ! Passons maintenant au **chiffrement TLS** pour sécuriser les communications ! 🔒

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : TLS et sécurité <carbon:arrow-right class="inline"/>
  </span>
</div>

