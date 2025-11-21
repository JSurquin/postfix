---
layout: new-section
routeAlias: 'redondance-smtp'
---

<a name="redondance-smtp" id="redondance-smtp"></a>

# 🔄 Redondance et Haute Disponibilité SMTP

## Configuration de plusieurs serveurs MX comme Gmail

---

# Pourquoi la redondance SMTP ? 🤔

**La haute disponibilité des emails est critique**

- ✅ **Continuité de service** : Si un serveur tombe, les emails sont toujours livrés
- ✅ **Répartition de charge** : Distribution du trafic entre plusieurs serveurs
- ✅ **Résilience** : Protection contre les pannes matérielles/réseau
- ✅ **Maintenance** : Mise à jour d'un serveur sans interruption

**Exemple réel :** Gmail utilise plusieurs serveurs MX avec différentes priorités

---

# Comment fonctionne la redondance MX ? 📬

**Les enregistrements MX définissent les serveurs de messagerie avec des priorités**

```bash
# Exemple : Enregistrements MX de Gmail
gmail.com.        3600    IN      MX      5  gmail-smtp-in.l.google.com.
gmail.com.        3600    IN      MX      10 alt1.gmail-smtp-in.l.google.com.
gmail.com.        3600    IN      MX      20 alt2.gmail-smtp-in.l.google.com.
gmail.com.        3600    IN      MX      30 alt3.gmail-smtp-in.l.google.com.
gmail.com.        3600    IN      MX      40 alt4.gmail-smtp-in.l.google.com.
```

---

# Comment fonctionne la redondance MX ? 📬

**Logique de livraison**

1. **Priorité la plus basse** = serveur préféré (5 = prioritaire)
2. Si le serveur prioritaire ne répond pas → serveur suivant (10)
3. Tentatives successives jusqu'à livraison réussie
4. Si tous échouent → email en queue, réessais ultérieurs

---

# Architecture de redondance avec Postfix 🏗️

**Configuration typique avec 2 serveurs MX**

```
┌─────────────────────────────────────────┐
│         Expéditeur (SMTP Client)        │
└──────────────┬──────────────────────────┘
               │
               ▼
        ┌──────────────┐
        │  DNS Query   │
        │  MX Records  │
        └──────┬───────┘
               │
    ┌──────────┴──────────┐
    │                     │
    ▼                     ▼
┌─────────┐          ┌─────────┐
│  MX 10  │          │  MX 20  │
│ Primary │          │ Backup  │
│ Postfix │          │ Postfix │
└────┬────┘          └────┬────┘
     │                    │
     └────────┬───────────┘
              │
              ▼
     ┌────────────────┐
     │ Boîtes locales │
     │   ou relais    │
     └────────────────┘
```

---

# Configuration DNS pour la redondance 🌐

**Ajouter plusieurs enregistrements MX dans votre zone DNS**

```bash
; Zone DNS pour example.com
$ORIGIN example.com.
$TTL 3600

; Enregistrements A pour les serveurs mail
mx1              IN      A       203.0.113.10
mx2              IN      A       203.0.113.20

; Enregistrements MX avec priorités
@                IN      MX      10  mx1.example.com.
@                IN      MX      20  mx2.example.com.

; Reverse DNS (PTR) pour chaque IP
```

---

# Configuration DNS pour la redondance 🌐

**⚠️ Points critiques**

- **Priorité** : Plus le chiffre est bas, plus le serveur est prioritaire
- **rDNS** : Chaque IP doit avoir un PTR valide
- **TTL** : Minimum 3600s (1h) recommandé pour les MX
- **Propagation** : Attendre 24-48h après modification

---

# Configuration Postfix - Serveur Principal (MX 10) 🖥️

**Configuration du serveur primaire (/etc/postfix/main.cf)**

```bash
# Hostname et domaine
myhostname = mx1.example.com
mydomain = example.com
myorigin = $mydomain

# Domaines acceptés
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
relay_domains = 

# Interface réseau
inet_interfaces = all
inet_protocols = ipv4
```

---

# Configuration Postfix - Serveur Principal (MX 10) 🖥️

**Configuration du serveur primaire (suite)**

```bash
# Domaines virtuels (si applicable)
virtual_mailbox_domains = example.com, autredomain.com
virtual_mailbox_base = /var/mail/vhosts
virtual_mailbox_maps = hash:/etc/postfix/vmailbox
virtual_uid_maps = static:5000
virtual_gid_maps = static:5000

# Sécurité de base
smtpd_banner = $myhostname ESMTP
smtpd_helo_required = yes
strict_rfc821_envelopes = yes
```

---

# Configuration Postfix - Serveur Secondaire (MX 20) 🖥️

**Configuration identique au serveur principal, sauf**

```bash
# Hostname différent
myhostname = mx2.example.com
mydomain = example.com
myorigin = $mydomain

# Mêmes domaines acceptés
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

# Même configuration virtuelle
virtual_mailbox_domains = example.com, autredomain.com
virtual_mailbox_base = /var/mail/vhosts
virtual_mailbox_maps = hash:/etc/postfix/vmailbox
```

---

# Configuration Postfix - Serveur Secondaire (MX 20) 🖥️

**⚠️ Important : Synchronisation des données**

Les deux serveurs doivent partager :
- **Même configuration** : vmailbox, aliases, virtual_domains
- **Même stockage** : NFS, GlusterFS, ou réplication
- **Même base utilisateurs** : LDAP, MySQL, PostgreSQL

---

# Scénario 1 : Stockage partagé NFS 💾

**Architecture avec NFS pour partager les boîtes mail**

```bash
# Sur le serveur NFS (192.168.1.100)
apt-get install nfs-kernel-server

# /etc/exports
/var/mail/vhosts 192.168.1.10(rw,sync,no_subtree_check)
/var/mail/vhosts 192.168.1.20(rw,sync,no_subtree_check)

exportfs -a
systemctl restart nfs-kernel-server
```

---

# Scénario 1 : Stockage partagé NFS 💾

**Sur chaque serveur Postfix (MX1 et MX2)**

```bash
# Installation client NFS
apt-get install nfs-common

# Montage NFS
mkdir -p /var/mail/vhosts
mount -t nfs 192.168.1.100:/var/mail/vhosts /var/mail/vhosts

# Ajout dans /etc/fstab pour montage automatique
echo "192.168.1.100:/var/mail/vhosts /var/mail/vhosts nfs defaults 0 0" >> /etc/fstab

# Vérifier le montage
df -h | grep vhosts
```

---

# Scénario 2 : Réplication MySQL pour les utilisateurs 🔄

**Base de données partagée pour la gestion des utilisateurs**

```sql
-- Sur le serveur MySQL principal
CREATE DATABASE mail;
CREATE USER 'postfix'@'%' IDENTIFIED BY 'motdepasse_securise';
GRANT ALL PRIVILEGES ON mail.* TO 'postfix'@'%';

CREATE TABLE virtual_users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    quota INT DEFAULT 0
);

CREATE TABLE virtual_domains (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE
);
```

---

# Scénario 2 : Réplication MySQL pour les utilisateurs 🔄

**Insérer des données de test**

```sql
INSERT INTO virtual_domains (name) VALUES 
    ('example.com'),
    ('autredomain.com');

INSERT INTO virtual_users (email, password) VALUES
    ('user1@example.com', '$6$encrypted_password'),
    ('user2@example.com', '$6$encrypted_password');
```

---

# Configuration Postfix avec MySQL 🔗

**Sur chaque serveur Postfix**

```bash
# Installation du support MySQL
apt-get install postfix-mysql

# Créer /etc/postfix/mysql-virtual-mailbox-domains.cf
cat > /etc/postfix/mysql-virtual-mailbox-domains.cf << 'EOF'
user = postfix
password = motdepasse_securise
hosts = 192.168.1.100
dbname = mail
query = SELECT 1 FROM virtual_domains WHERE name='%s'
EOF
```

---

# Configuration Postfix avec MySQL 🔗

**Configuration des mailboxes virtuelles**

```bash
# /etc/postfix/mysql-virtual-mailbox-maps.cf
cat > /etc/postfix/mysql-virtual-mailbox-maps.cf << 'EOF'
user = postfix
password = motdepasse_securise
hosts = 192.168.1.100
dbname = mail
query = SELECT 1 FROM virtual_users WHERE email='%s'
EOF

chmod 640 /etc/postfix/mysql-*.cf
chgrp postfix /etc/postfix/mysql-*.cf
```

---

# Configuration Postfix avec MySQL 🔗

**Mise à jour du main.cf**

```bash
# Dans /etc/postfix/main.cf
virtual_mailbox_domains = mysql:/etc/postfix/mysql-virtual-mailbox-domains.cf
virtual_mailbox_maps = mysql:/etc/postfix/mysql-virtual-mailbox-maps.cf

# Test de la configuration
postmap -q example.com mysql:/etc/postfix/mysql-virtual-mailbox-domains.cf
postmap -q user1@example.com mysql:/etc/postfix/mysql-virtual-mailbox-maps.cf

# Recharger Postfix
systemctl reload postfix
```

---

# Scénario 3 : Relais vers serveur de stockage final 📨

**Architecture avec relais (MX reçoivent et transfèrent)**

```
┌──────────┐         ┌──────────┐
│  MX 10   │────┐    │  MX 20   │────┐
│ (Relais) │    │    │ (Relais) │    │
└──────────┘    │    └──────────┘    │
                │                    │
                ▼                    ▼
            ┌────────────────────────┐
            │  Serveur de stockage   │
            │  (Dovecot + Postfix)   │
            └────────────────────────┘
```

---

# Configuration Relais - Serveurs MX 🔀

**Configuration des serveurs MX en mode relais**

```bash
# /etc/postfix/main.cf sur MX1 et MX2
myhostname = mx1.example.com  # ou mx2.example.com
mydomain = example.com
myorigin = $mydomain

# Pas de livraison locale, tout en relais
mydestination = 
local_recipient_maps = 

# Relayer vers le serveur de stockage
relay_domains = example.com
transport_maps = hash:/etc/postfix/transport
default_transport = smtp
```

---

# Configuration Relais - Serveurs MX 🔀

**Table de transport (/etc/postfix/transport)**

```bash
# Envoyer tous les emails vers le serveur de stockage interne
example.com     smtp:[192.168.1.50]
autredomain.com smtp:[192.168.1.50]

# Les crochets [] évitent les lookups MX
```

**Appliquer la configuration**

```bash
postmap /etc/postfix/transport
systemctl reload postfix
```

---

# Configuration Serveur de Stockage 📦

**Le serveur final qui stocke réellement les emails**

```bash
# /etc/postfix/main.cf sur le serveur de stockage (192.168.1.50)
myhostname = mail-storage.example.com
mydomain = example.com

# Accepter les emails des domaines
mydestination = 
virtual_mailbox_domains = example.com, autredomain.com
virtual_mailbox_base = /var/mail/vhosts
virtual_mailbox_maps = hash:/etc/postfix/vmailbox
```

---

# Configuration Serveur de Stockage 📦

**Restriction pour n'accepter que depuis les MX**

```bash
# /etc/postfix/main.cf (suite)
smtpd_recipient_restrictions =
    permit_mynetworks,
    reject_unauth_destination

# Définir les réseaux autorisés (vos MX)
mynetworks = 127.0.0.0/8, 192.168.1.10/32, 192.168.1.20/32

# Interface d'écoute (réseau privé uniquement)
inet_interfaces = 192.168.1.50, 127.0.0.1
```

---

# Sécurité entre MX et serveur de stockage 🔐

**Option 1 : TLS entre relais et stockage**

```bash
# Sur les serveurs MX (relais)
smtp_tls_security_level = encrypt
smtp_tls_note_starttls_offer = yes
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

# Sur le serveur de stockage
smtpd_tls_security_level = encrypt
smtpd_tls_cert_file = /etc/ssl/certs/mail.crt
smtpd_tls_key_file = /etc/ssl/private/mail.key
smtpd_tls_auth_only = yes
```

---

# Sécurité entre MX et serveur de stockage 🔐

**Option 2 : Authentification SASL entre serveurs**

```bash
# Sur les serveurs MX - créer /etc/postfix/sasl_passwd
[192.168.1.50]:25 relay_user:motdepasse_securise

# Hasher le fichier
postmap /etc/postfix/sasl_passwd
chmod 600 /etc/postfix/sasl_passwd*

# Dans main.cf des MX
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
```

---

# Monitoring de la redondance 📊

**Vérifier que tous les MX fonctionnent**

```bash
# Test depuis l'extérieur
dig MX example.com

# Test de connexion SMTP sur chaque MX
telnet mx1.example.com 25
telnet mx2.example.com 25

# Vérifier les queues sur chaque serveur
ssh mx1.example.com 'postqueue -p'
ssh mx2.example.com 'postqueue -p'
```

---

# Monitoring de la redondance 📊

**Script de monitoring simple**

```bash
#!/bin/bash
# check-mx-health.sh

MX_SERVERS=("mx1.example.com" "mx2.example.com")

for mx in "${MX_SERVERS[@]}"; do
    echo "=== Checking $mx ==="
    
    # Test connexion SMTP
    timeout 5 bash -c "echo QUIT | nc -w 3 $mx 25" > /dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "✅ $mx SMTP OK"
    else
        echo "❌ $mx SMTP FAILED"
        # Alerter l'admin
    fi
    
    # Vérifier la queue via SSH
    QUEUE=$(ssh $mx 'postqueue -p | tail -1')
    echo "Queue: $QUEUE"
done
```

---

# Monitoring de la redondance 📊

**Configuration avec Nagios/Icinga**

```bash
# Commande de check dans Nagios
define command{
    command_name    check_smtp_mx
    command_line    $USER1$/check_smtp -H $HOSTADDRESS$ -p 25 -t 10
}

# Service pour MX1
define service{
    use                     generic-service
    host_name               mx1.example.com
    service_description     SMTP
    check_command           check_smtp_mx
}
```

---

# Load Balancing avec Round-Robin DNS 🔄

**Même priorité = load balancing automatique**

```bash
; Plusieurs MX avec la même priorité
example.com.     IN      MX      10  mx1.example.com.
example.com.     IN      MX      10  mx2.example.com.
example.com.     IN      MX      10  mx3.example.com.
```

**Comportement**
- Les serveurs SMTP expéditeurs **peuvent** choisir aléatoirement
- Distribution approximative du trafic
- ⚠️ Pas garanti : certains MTA préfèrent toujours le premier

---

# Basculement automatique (Failover) 🔄

**Le failover est automatique avec les MX**

1. **Serveur primaire (MX 10) disponible** → Tout le trafic vers lui
2. **Serveur primaire DOWN** → Trafic automatiquement vers MX 20
3. **Serveur primaire revient** → Retour progressif du trafic

**⏱️ Temps de basculement**
- Dépend du timeout SMTP de l'expéditeur (généralement 30-60s)
- Pas de latence DNS si TTL respecté

---

# Exemple réel : Configuration complète 2 MX 🎯

**Récapitulatif d'une architecture redondante**

```
┌─────────────────────────────────┐
│       DNS Records (MX)          │
│  10 mx1.example.com (Primary)   │
│  20 mx2.example.com (Secondary) │
└─────────────────────────────────┘
         │                │
         ▼                ▼
    ┌─────────┐      ┌─────────┐
    │  MX1    │      │  MX2    │
    │ Postfix │      │ Postfix │
    └────┬────┘      └────┬────┘
         │                │
         └────────┬───────┘
                  │
            ┌─────▼─────┐
            │    NFS    │
            │  Storage  │
            └───────────┘
```

---

# Test de basculement (Failover Test) 🧪

**Scénario de test complet**

```bash
# 1. Envoyer un email de test avec MX1 actif
echo "Test 1" | mail -s "Primary MX" test@example.com

# 2. Stopper le serveur primaire
ssh mx1.example.com 'systemctl stop postfix'

# 3. Vérifier que MX2 prend le relais
dig MX example.com
telnet mx2.example.com 25

# 4. Envoyer un email avec MX1 down
echo "Test 2 - Failover" | mail -s "Secondary MX" test@example.com
```

---

# Test de basculement (Failover Test) 🧪

**Vérifications post-test**

```bash
# 5. Vérifier la réception sur MX2
ssh mx2.example.com 'tail -f /var/log/maillog'

# 6. Redémarrer MX1
ssh mx1.example.com 'systemctl start postfix'

# 7. Vérifier que MX1 redevient prioritaire
echo "Test 3 - Back to primary" | mail -s "Primary restored" test@example.com

# 8. Analyser les logs des deux serveurs
ssh mx1.example.com 'grep "Test" /var/log/maillog'
ssh mx2.example.com 'grep "Test" /var/log/maillog'
```

---

# Problèmes courants et solutions 🔧

**Problème 1 : Les emails n'arrivent que sur MX1**

```bash
# Vérifier que MX2 accepte bien les emails
postconf -n | grep mydestination
postconf -n | grep relay_domains

# Tester manuellement
telnet mx2.example.com 25
> EHLO test.com
> MAIL FROM:<sender@test.com>
> RCPT TO:<user@example.com>
```

---

# Problèmes courants et solutions 🔧

**Problème 2 : "Relay access denied" sur MX2**

```bash
# Vérifier les domaines acceptés
postconf virtual_mailbox_domains
postconf relay_domains
postconf mydestination

# S'assurer que example.com est dans l'une de ces directives
```

---

# Problèmes courants et solutions 🔧

**Problème 3 : Emails dupliqués**

**Cause** : Les deux MX stockent localement au lieu de partager

**Solution** : Vérifier le stockage partagé

```bash
# Sur chaque MX, vérifier le montage NFS
df -h | grep vhosts
mount | grep vhosts

# Tester l'écriture
touch /var/mail/vhosts/test-$(hostname).txt

# Vérifier sur l'autre serveur
ls -la /var/mail/vhosts/
```

---

# Problèmes courants et solutions 🔧

**Problème 4 : Performance dégradée**

```bash
# Vérifier la latence réseau entre MX et stockage
ping -c 10 192.168.1.50

# Vérifier les performances NFS
time dd if=/dev/zero of=/var/mail/vhosts/test.dat bs=1M count=100

# Vérifier les connexions MySQL
mysql -h 192.168.1.100 -u postfix -p -e "SHOW PROCESSLIST;"
```

---

# Bonnes pratiques de redondance 📋

**✅ Configuration**
- Toujours avoir au moins 2 MX dans des datacenters différents
- Priorités MX espacées (10, 20, 30...) pour clarté
- Synchroniser régulièrement les configurations
- Documenter l'architecture

**✅ Sécurité**
- Même niveau de sécurité sur tous les MX
- SPF incluant tous les MX : `v=spf1 mx -all`
- DKIM configuré sur tous les serveurs sortants
- Certificats TLS valides partout

---

# Bonnes pratiques de redondance 📋

**✅ Monitoring**
- Superviser chaque MX individuellement
- Alertes en cas de panne d'un MX
- Surveiller les queues sur chaque serveur
- Tester régulièrement le failover

**✅ Maintenance**
- Planifier les mises à jour en alternance
- Toujours garder au moins 1 MX actif
- Sauvegarder les configurations de tous les MX
- Tester la restauration complète

---

# Alternative : Postfix avec Keepalived (VIP) 🔀

**Haute disponibilité avec IP virtuelle**

```
┌──────────────┐           ┌──────────────┐
│   MX1        │◄─────────►│   MX2        │
│  (Master)    │ Keepalived│  (Backup)    │
│ 192.168.1.10 │           │ 192.168.1.20 │
└──────┬───────┘           └──────┬───────┘
       │                          │
       └──────────┬───────────────┘
                  │
          ┌───────▼────────┐
          │  VIP Flottante │
          │  192.168.1.5   │
          └────────────────┘
```

---

# Configuration Keepalived 🔧

**Installation sur MX1 et MX2**

```bash
apt-get install keepalived

# /etc/keepalived/keepalived.conf sur MX1 (Master)
vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 1
    
    authentication {
        auth_type PASS
        auth_pass SecurePass123
    }
    
    virtual_ipaddress {
        192.168.1.5/24
    }
}
```

---

# Configuration Keepalived 🔧

**Configuration sur MX2 (Backup)**

```bash
# /etc/keepalived/keepalived.conf sur MX2 (Backup)
vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 90
    advert_int 1
    
    authentication {
        auth_type PASS
        auth_pass SecurePass123
    }
    
    virtual_ipaddress {
        192.168.1.5/24
    }
}
```

---

# Configuration Keepalived 🔧

**Démarrage et test**

```bash
# Sur les deux serveurs
systemctl enable keepalived
systemctl start keepalived

# Vérifier l'IP virtuelle (sur le master)
ip addr show eth0

# Tester le basculement
systemctl stop keepalived  # Sur le master
ip addr show eth0          # Sur le backup (devrait avoir la VIP)
```

---

# DNS avec Keepalived 🌐

**Configuration DNS simplifiée avec VIP**

```bash
; Un seul enregistrement MX pointant vers la VIP
example.com.     IN      MX      10  mail.example.com.
mail.example.com. IN     A       192.168.1.5
```

**Avantages**
- ✅ Basculement transparent (secondes)
- ✅ Pas de dépendance aux timeouts SMTP
- ✅ Un seul point d'entrée

**Inconvénients**
- ❌ Pas de répartition de charge automatique
- ❌ Nécessite un réseau Layer 2 commun

---

# Comparaison des architectures 🔍

**Multiple MX (Gmail style)**

✅ Répartition de charge possible
✅ Géographiquement distribué
✅ Standard SMTP natif
❌ Basculement plus lent (timeouts)

**VIP avec Keepalived**

✅ Basculement ultra-rapide
✅ Transparent pour les clients
❌ Limitation réseau (Layer 2)
❌ Pas de load balancing natif

---

# Exercice Pratique : Configurer 2 MX 🎯

**Objectif** : Mettre en place une infrastructure redondante

1. **Configurer 2 serveurs Postfix** (MX1 et MX2)
2. **Ajouter les enregistrements DNS MX**
3. **Configurer un stockage partagé** (NFS ou relais)
4. **Tester le failover** en stoppant MX1
5. **Vérifier les logs** et la livraison des emails
6. **Bonus** : Ajouter du monitoring

**Durée estimée** : 45-60 minutes

---

# Ressources et Documentation 📚

**Documentation Postfix**
- [Postfix Backup MX Configuration](http://www.postfix.org/STANDARD_CONFIGURATION_README.html#backup)
- [Transport Maps](http://www.postfix.org/transport.5.html)

**Outils de test**
- [MXToolbox - MX Lookup](https://mxtoolbox.com/MXLookup.aspx)
- [DNS Checker - MX Records](https://dnschecker.org/mx-lookup.php)

**Keepalived**
- [Keepalived Documentation](https://www.keepalived.org/documentation.html)

---

# Résumé 📝

**Ce que nous avons vu**

✅ **Concept de redondance** via enregistrements MX multiples
✅ **Architecture Gmail** avec plusieurs serveurs smtp-in
✅ **Configuration Postfix** pour MX primaire et secondaire
✅ **Stockage partagé** (NFS, relais, MySQL)
✅ **Failover automatique** et load balancing
✅ **Monitoring** de la haute disponibilité
✅ **Alternative Keepalived** avec VIP flottante
✅ **Tests et troubleshooting**

**Prochaine étape** : Exercices pratiques ! 🚀


