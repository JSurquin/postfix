---
layout: new-section
routeAlias: 'alias-tables-virtuelles'
---

<a name="alias-tables-virtuelles" id="alias-tables-virtuelles"></a>

# Alias et Tables Virtuelles

📋 Rediriger et gérer les adresses email avec flexibilité

---

# Introduction

Les alias et les domaines virtuels permettent de :
- Rediriger des emails d'une adresse vers une autre
- Gérer plusieurs domaines sur un seul serveur
- Créer des adresses "catch-all"
- Automatiser le traitement des emails

---

## Différence entre Alias et Virtual

**Alias** : Pour les comptes **locaux** (utilisateurs système)

**Virtual** : Pour les domaines **virtuels** (pas de compte système)

---

**Analogie** :

**Alias** = Boîte aux lettres partagée dans votre immeuble
- root → admin@example.com

**Virtual** = Redirection postale vers une autre adresse
- contact@domain1.com → support@domain2.com

---

# Les Alias locaux

## Le fichier /etc/aliases

Le fichier `/etc/aliases` gère les redirections des comptes système.

---

### 📝 Format du fichier

```sql
# Commentaire
alias: destination

# Exemples
postmaster: root
webmaster: root
root: admin@example.com
```

---

### 🔍 Structure

```sql
# Alias simple (vers un utilisateur local)
admin: john

# Alias vers une adresse externe
root: admin@example.com

# Alias vers plusieurs destinations
support: john, jane, admin@example.com
```

---

```sql
# Alias vers un fichier
logs: /var/log/mail-archive.txt

# Alias vers un programme
spam: "|/usr/local/bin/spam-filter.sh"

# Alias vers :include: (fichier avec liste d'adresses)
all-staff: :include:/etc/postfix/staff-list.txt
```

---

### 🛠️ Créer et modifier des alias

```bash
# Éditer le fichier
sudo nano /etc/aliases

# Ajouter un alias
echo "contact: john@example.com" | sudo tee -a /etc/aliases
```

---

### 🔄 Compiler les alias

**Important** : Après modification, il faut recompiler !

```bash
sudo newaliases
# ou
sudo postalias /etc/aliases
```

---

Cela crée une base de données binaire `/etc/aliases.db` que Postfix utilise.

---

### ✅ Tester un alias

```bash
# Envoyer un email à l'alias
echo "Test alias" | mail -s "Test" postmaster

# Vérifier qu'il arrive à la bonne destination
```

---

## Exemples d'alias courants

### 📧 Rediriger les comptes système

```sql
# Tous les emails système vers l'admin
postmaster: admin@example.com
webmaster: admin@example.com
hostmaster: admin@example.com
abuse: admin@example.com
security: admin@example.com
root: admin@example.com
```

---

### 👥 Liste de diffusion simple

```sql
# Créer un fichier /etc/postfix/team-dev.txt
# Contenu :
#   john@example.com
#   jane@example.com
#   bob@example.com

# Dans /etc/aliases
dev-team: :include:/etc/postfix/team-dev.txt
```

---

### 📬 Alias avec plusieurs destinations

```sql
# Envoyer à plusieurs personnes
support: john@example.com, jane@example.com, support-archive

# support-archive peut être un autre alias
support-archive: /var/mail/support-archive.txt
```

---

### 🔧 Alias vers un programme

```sql
# Traiter automatiquement les emails
tickets: "|/usr/local/bin/ticket-system.sh"
```

---

**Exemple de script** `/usr/local/bin/ticket-system.sh` :

```bash
#!/bin/bash
# Lire l'email depuis stdin
cat > /tmp/email-$$.txt

# Traiter l'email (créer un ticket, etc.)
# ...

# Nettoyer
rm /tmp/email-$$.txt
```

---

**Important** : Le script doit être exécutable

```bash
sudo chmod +x /usr/local/bin/ticket-system.sh
```

---

## Configuration dans main.cf

```sql
# Chemin vers le fichier aliases
alias_maps = hash:/etc/aliases

# Base de données d'alias
alias_database = hash:/etc/aliases
```

---

# Les domaines virtuels

Les domaines virtuels permettent de gérer plusieurs domaines sans créer de comptes système.

---

## Types de domaines virtuels

### 1️⃣ Virtual Alias Domains

Redirection simple vers d'autres adresses

**Cas d'usage** : Vous gérez domain1.com et domain2.com, tous les emails vont vers example.com

---

### 2️⃣ Virtual Mailbox Domains

Boîtes mail virtuelles stockées sur le serveur

**Cas d'usage** : Vous hébergez plusieurs domaines avec de vraies boîtes mail

---

## Virtual Alias Domains

### 📝 Fichier /etc/postfix/virtual

```sql
# Format :
# adresse@virtuelle  destination

contact@domain1.com        john@example.com
admin@domain1.com          admin@example.com
info@domain2.com           support@example.com
```

---

### 🌐 Catch-all

```sql
# Tout email vers domain1.com va vers john@example.com
@domain1.com               john@example.com

# Combinaison : règles spécifiques + catch-all
contact@domain1.com        support@example.com
admin@domain1.com          admin@example.com
@domain1.com               catchall@example.com
```

---

⚠️ **Attention** : Les règles spécifiques doivent être **avant** le catch-all !

---

### 🔄 Compiler le fichier virtual

```bash
# Après modification
sudo postmap /etc/postfix/virtual
```

Cela crée `/etc/postfix/virtual.db`

---

### ⚙️ Configuration dans main.cf

```sql
# Déclarer les domaines virtuels
virtual_alias_domains = domain1.com, domain2.com

# Fichier de mapping
virtual_alias_maps = hash:/etc/postfix/virtual
```

---

**Ou lire les domaines depuis le fichier virtual** :

```sql
# Postfix déduit automatiquement les domaines
virtual_alias_maps = hash:/etc/postfix/virtual
```

---

### ✅ Recharger Postfix

```bash
sudo systemctl reload postfix
```

---

## Virtual Mailbox Domains

Pour stocker réellement les emails des domaines virtuels.

---

### 📝 Fichier /etc/postfix/vmailbox

```sql
# Format :
# adresse@virtuelle  chemin/vers/mailbox

user1@domain1.com    domain1.com/user1/
user2@domain1.com    domain1.com/user2/
admin@domain2.com    domain2.com/admin/
```

---

### 📂 Créer les répertoires

```bash
# Créer le répertoire de base
sudo mkdir -p /var/mail/vhosts

# Créer les sous-répertoires pour chaque domaine
sudo mkdir -p /var/mail/vhosts/domain1.com/user1
sudo mkdir -p /var/mail/vhosts/domain1.com/user2
```

---

### 👤 Utilisateur virtuel

Il faut un utilisateur système dédié :

```bash
# Créer l'utilisateur vmail
sudo groupadd -g 5000 vmail
sudo useradd -g vmail -u 5000 vmail -d /var/mail/vhosts -s /sbin/nologin

# Permissions
sudo chown -R vmail:vmail /var/mail/vhosts
```

---

### ⚙️ Configuration dans main.cf

```sql
# Domaines virtuels avec mailbox
virtual_mailbox_domains = domain1.com, domain2.com

# Mapping adresse → mailbox
virtual_mailbox_maps = hash:/etc/postfix/vmailbox

# Répertoire de base
virtual_mailbox_base = /var/mail/vhosts
```

---

```sql
# Utilisateur et groupe
virtual_uid_maps = static:5000
virtual_gid_maps = static:5000

# Taille minimum libre du disque
virtual_mailbox_limit = 52428800
virtual_minimum_uid = 1000
```

---

### 🔄 Compiler vmailbox

```bash
sudo postmap /etc/postfix/vmailbox
sudo systemctl reload postfix
```

---

### ✅ Tester

```bash
echo "Test virtual mailbox" | mail -s "Test" user1@domain1.com

# Vérifier
sudo ls -la /var/mail/vhosts/domain1.com/user1/
```

---

## Domaines virtuels avec base de données

Pour gérer beaucoup d'utilisateurs, utilisez MySQL/PostgreSQL !

Nous verrons ça en détail dans le module "Bases de données externes".

---

## Canonical Mapping

Réécrire les adresses avant traitement.

---

### 📝 Fichier /etc/postfix/canonical

```sql
# Format :
# pattern  résultat

# Réécrire l'expéditeur
@oldmachine.domain.com    @newmachine.domain.com

# Réécrire une adresse spécifique
john@localhost            john@example.com
```

---

### 🔧 Types de canonical

**sender_canonical_maps** : Réécrire l'expéditeur

```sql
sender_canonical_maps = hash:/etc/postfix/sender_canonical
```

---

**recipient_canonical_maps** : Réécrire le destinataire

```sql
recipient_canonical_maps = hash:/etc/postfix/recipient_canonical
```

---

**canonical_maps** : Réécrire les deux

```sql
canonical_maps = hash:/etc/postfix/canonical
```

---

### 🔄 Compiler

```bash
sudo postmap /etc/postfix/canonical
sudo systemctl reload postfix
```

---

## Relocated Mapping

Informer que l'adresse a changé.

---

### 📝 Fichier /etc/postfix/relocated

```sql
# Format :
# ancienne@adresse  nouvelle@adresse

john@oldcompany.com     john@newcompany.com
sales@oldcompany.com    contact@newcompany.com
```

---

### ⚙️ Configuration

```sql
relocated_maps = hash:/etc/postfix/relocated
```

---

```bash
sudo postmap /etc/postfix/relocated
sudo systemctl reload postfix
```

---

**Comportement** : Postfix rejette l'email avec un message :

```
550 5.1.1 <john@oldcompany.com>: Recipient address rejected:
User has moved to john@newcompany.com
```

---

## Transport Maps

Définir comment et où livrer les emails.

---

### 📝 Fichier /etc/postfix/transport

```sql
# Format :
# domaine  transport:nexthop

# Envoyer via SMTP vers un serveur spécifique
domain1.com       smtp:[mail.domain1.com]

# Livraison locale
domain2.com       local:

# Via relais
domain3.com       relay:[relay.domain3.com]:587
```

---

### ⚙️ Configuration

```sql
transport_maps = hash:/etc/postfix/transport
```

---

```bash
sudo postmap /etc/postfix/transport
sudo systemctl reload postfix
```

---

## Combinaison Alias + Virtual

**Question** : Quelle priorité ?

**Réponse** : Alias → Virtual → Local

---

**Exemple** :

```sql
# /etc/aliases
postmaster: root

# /etc/postfix/virtual
root@example.com: admin@external.com
```

---

Email à `postmaster@example.com` :
1. Alias : `postmaster` → `root`
2. Virtual : `root@example.com` → `admin@external.com`
3. Résultat : `admin@external.com`

---

## Cas d'usage pratiques

### 🏢 Entreprise multi-domaines

```sql
# /etc/postfix/virtual

# Domaine principal : example.com
contact@example.com       support-team
sales@example.com         sales-team
info@example.com          reception

# Domaine secondaire : example.fr (redirige vers .com)
@example.fr               $1@example.com
```

---

### 📧 Adresses temporaires

```sql
# Créer des adresses jetables
promo2025@example.com     marketing@example.com
event-dec@example.com     events@example.com

# Après l'événement, supprimer la ligne et recompiler
```

---

### 🎭 Alias personnels

```sql
# Noms sympathiques
jimmy@example.com         j.surquin@example.com
bob@example.com           robert.martin@example.com
```

---

### 📮 Départements

```sql
# Un alias vers toute une équipe
dev@example.com           :include:/etc/postfix/lists/dev-team.txt
marketing@example.com     :include:/etc/postfix/lists/marketing-team.txt
```

---

**Contenu de** `/etc/postfix/lists/dev-team.txt` :

```
john@example.com
jane@example.com
bob@example.com
alice@example.com
```

---

### 🔄 Migration de domaine

```sql
# Ancien domaine redirige vers nouveau
@oldcompany.com           $1@newcompany.com

# Sauf exceptions
ceo@oldcompany.com        ceo@newcompany.com
```

---

## Scripts de gestion

### 🔧 Ajouter un alias automatiquement

```bash
#!/bin/bash
# add-alias.sh

ALIAS=$1
DEST=$2

# Ajouter au fichier virtual
echo "$ALIAS    $DEST" | sudo tee -a /etc/postfix/virtual

# Recompiler
sudo postmap /etc/postfix/virtual
sudo systemctl reload postfix

echo "✅ Alias $ALIAS → $DEST créé !"
```

---

**Utilisation** :

```bash
chmod +x add-alias.sh
./add-alias.sh contact@domain1.com support@example.com
```

---

### 🗑️ Supprimer un alias

```bash
#!/bin/bash
# remove-alias.sh

ALIAS=$1

# Supprimer du fichier
sudo sed -i "/^$ALIAS/d" /etc/postfix/virtual

# Recompiler
sudo postmap /etc/postfix/virtual
sudo systemctl reload postfix

echo "✅ Alias $ALIAS supprimé !"
```

---

### 📋 Lister les alias

```bash
#!/bin/bash
# list-aliases.sh

echo "=== Alias locaux (/etc/aliases) ==="
sudo grep -v '^#' /etc/aliases | grep -v '^$'
echo ""

echo "=== Alias virtuels (/etc/postfix/virtual) ==="
sudo grep -v '^#' /etc/postfix/virtual | grep -v '^$'
```

---

## Validation et tests

### ✅ Vérifier un alias local

```bash
postalias -q postmaster /etc/aliases
# Output: root
```

---

### ✅ Vérifier un alias virtuel

```bash
postmap -q contact@domain1.com /etc/postfix/virtual
# Output: john@example.com
```

---

### ✅ Tester la résolution complète

```bash
# Installer postfix-policyd-spf-python si pas déjà fait
sudo apt install postfix-policyd-spf-python

# Tester
postmap -q contact@domain1.com hash:/etc/postfix/virtual
```

---

### 🧪 Test d'envoi

```bash
# Envoyer un email de test
echo "Test alias" | mail -s "Test" contact@domain1.com

# Suivre les logs
sudo tail -f /var/log/mail.log | grep contact@domain1.com
```

---

## Troubleshooting

### ❌ Problème : Alias ne fonctionne pas

**Cause 1** : Fichier pas recompilé

```bash
sudo newaliases
# ou
sudo postmap /etc/postfix/virtual
```

---

**Cause 2** : Fichier mal configuré dans main.cf

```bash
postconf virtual_alias_maps
# Doit afficher : hash:/etc/postfix/virtual
```

---

**Cause 3** : Permissions incorrectes

```bash
sudo chmod 644 /etc/postfix/virtual
sudo chown root:root /etc/postfix/virtual
```

---

### ❌ Problème : Boucle infinie

```sql
# ❌ NE PAS FAIRE
john@example.com    jane@example.com
jane@example.com    john@example.com
```

Postfix détecte et rejette !

---

**Dans les logs** :

```
mail for john@example.com loops back to myself
```

---

### ❌ Problème : Catch-all trop permissif

```sql
# ❌ Attrape TOUS les emails !
@   catchall@example.com
```

Solution : Soyez spécifique

```sql
@domain1.com    catchall@example.com
```

---

## Bonnes pratiques

### 💡 Organisation

**Séparez les fichiers par fonction** :

```bash
/etc/postfix/virtual-aliases
/etc/postfix/virtual-mailboxes
/etc/postfix/virtual-domains
```

---

**Dans main.cf** :

```sql
virtual_alias_maps = 
    hash:/etc/postfix/virtual-aliases,
    hash:/etc/postfix/virtual-users
```

---

### 💡 Documentation

**Commentez vos fichiers** :

```sql
# === Domaine principal ===
contact@example.com       support@example.com

# === Domaine client ABC Corp ===
# Créé le 2025-01-15, expire le 2026-01-15
info@abccorp.com          abccorp-support@example.com
```

---

### 💡 Sécurité

**Limitez les catch-all** :

Les catch-all attirent le spam !

```sql
# ✅ Bon : spécifique
contact@domain.com    support@example.com

# ⚠️ Mauvais : attrape tout
@domain.com           spam-magnet@example.com
```

---

### 💡 Sauvegarde

```bash
# Sauvegarde régulière
sudo cp /etc/aliases /etc/aliases.backup
sudo cp /etc/postfix/virtual /etc/postfix/virtual.backup

# Avec date
sudo cp /etc/postfix/virtual /etc/postfix/virtual.$(date +%Y%m%d)
```

---

## Exercices pratiques

### 🎯 Exercice 1 : Alias locaux

1. Créez un alias `contact` qui pointe vers votre email
2. Créez un alias `support` vers plusieurs destinations
3. Testez l'envoi
4. Vérifiez la réception

---

### 🎯 Exercice 2 : Domaine virtuel

1. Ajoutez un domaine virtuel `test.local`
2. Créez des alias pour `admin@test.local` et `info@test.local`
3. Créez un catch-all pour ce domaine
4. Testez

---

### 🎯 Exercice 3 : Liste de diffusion

1. Créez un fichier `/etc/postfix/lists/team.txt`
2. Ajoutez 3-4 adresses
3. Créez un alias `team@` qui utilise :include:
4. Envoyez un email à `team@` et vérifiez que tout le monde le reçoit

---

### 🎯 Exercice 4 : Script d'administration

1. Créez un script pour ajouter automatiquement un alias virtuel
2. Le script doit prendre 2 arguments : alias et destination
3. Il doit recompiler et recharger Postfix
4. Testez-le

---

## Points clés à retenir

### 💡 Alias vs Virtual

**Alias** (`/etc/aliases`) :
- Pour comptes locaux système
- `newaliases` pour compiler

**Virtual** (`/etc/postfix/virtual`) :
- Pour domaines virtuels
- `postmap` pour compiler

---

### 💡 Commandes essentielles

```bash
# Compiler
sudo newaliases               # Pour aliases
sudo postmap /etc/postfix/virtual   # Pour virtual

# Tester
postalias -q ALIAS /etc/aliases
postmap -q ADRESSE /etc/postfix/virtual

# Recharger
sudo systemctl reload postfix
```

---

### 💡 Configuration main.cf

```sql
# Alias locaux
alias_maps = hash:/etc/aliases

# Domaines virtuels (alias)
virtual_alias_domains = domain1.com, domain2.com
virtual_alias_maps = hash:/etc/postfix/virtual

# Domaines virtuels (mailbox)
virtual_mailbox_domains = domain3.com
virtual_mailbox_maps = hash:/etc/postfix/vmailbox
virtual_mailbox_base = /var/mail/vhosts
```

---

## Prochaine étape

Maintenant que vous savez gérer les alias et les domaines virtuels, passons à la **protection anti-spam** ! 🛡️

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Protection anti-spam <carbon:arrow-right class="inline"/>
  </span>
</div>

