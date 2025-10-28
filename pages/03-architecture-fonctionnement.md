---
layout: new-section
routeAlias: 'architecture-fonctionnement'
---

<a name="architecture-fonctionnement" id="architecture-fonctionnement"></a>

# Architecture et Fonctionnement

🏗️ Comprendre les entrailles de Postfix

---

# Introduction à l'architecture

Postfix n'est pas un simple programme, c'est une **orchestration** de plusieurs processus indépendants travaillant ensemble.

Pensez à une usine où chaque ouvrier a une tâche précise : c'est exactement comme ça que Postfix fonctionne !

---

## La philosophie de conception

### 🎯 Principes fondamentaux

**Séparation des privilèges** : Chaque processus tourne avec le minimum de droits nécessaires - Si un processus est compromis, les dégâts sont limités

**Architecture modulaire** : Chaque tâche est gérée par un processus dédié - Facile de remplacer ou désactiver un composant - Isolation des pannes

**Communication par files d'attente** : Les processus ne se parlent pas directement - Ils communiquent via des fichiers dans des répertoires - Robustesse : si un processus crash, les messages ne sont pas perdus

---

## Vue d'ensemble de l'architecture

```
                    ┌──────────────┐
                    │    Master    │ (Process manager)
                    └──────┬───────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────┐       ┌────▼────┐       ┌────▼────┐
   │ smtpd   │       │ pickup  │       │  local  │
   └────┬────┘       └────┬────┘       └────┬────┘
        │                  │                  │
        └─────────┬────────┴──────────────────┘
                  │
            ┌─────▼─────┐
            │  cleanup  │
            └─────┬─────┘
                  │
            ┌─────▼─────┐
            │   qmgr    │ (Queue manager)
            └─────┬─────┘
                  │
        ┌─────────┼─────────┐
        │                   │
   ┌────▼────┐         ┌───▼────┐
   │  smtp   │         │bounce  │
   └─────────┘         └────────┘
```

---

## Le processus Master

Le **master** est le chef d'orchestre. C'est lui qui lance tous les autres processus, surveille leur santé, les redémarre en cas de crash et gère leur cycle de vie.

### 📋 Configuration du master

Le fichier `/etc/postfix/master.cf` définit tous les services :

```sql
# service type  private unpriv  chroot  wakeup  maxproc command
smtp      inet  n       -       y       -       -       smtpd
pickup    unix  n       -       y       60      1       pickup
cleanup   unix  n       -       y       -       0       cleanup
qmgr      unix  n       -       n       300     1       qmgr
```

---

Décryptons une ligne :

```sql
smtp      inet  n       -       y       -       -       smtpd
```

- **service** : `smtp` - nom du service
- **type** : `inet` - socket internet (vs `unix` pour socket Unix)
- **private** : `n` - accessible de l'extérieur
- **unpriv** : `-` - tourne avec les privilèges par défaut
- **chroot** : `y` - tourne dans un environnement chrooté (isolé)
- **wakeup** : `-` - pas de réveil automatique
- **maxproc** : `-` - nombre max de processus (défaut)
- **command** : `smtpd` - programme à exécuter

---

## Les processus principaux

### 📨 smtpd (SMTP daemon)

**Rôle** : Recevoir les emails depuis Internet ou les clients

**Responsabilités** : Écoute sur le port 25 (ou 587 pour submission) - Dialogue SMTP avec les clients - Applique les restrictions et politiques - Accepte ou rejette les messages - Passe les messages acceptés à `cleanup`

**Analogie** : C'est le réceptionniste de l'hôtel qui accueille les clients et vérifie leurs réservations.

---

### 📬 pickup

**Rôle** : Récupérer les emails déposés localement

**Responsabilités** : Surveille le répertoire `maildrop/` - Récupère les emails déposés par les programmes locaux (via `sendmail`) - Passe les messages à `cleanup`

**Analogie** : C'est l'employé qui ramasse le courrier déposé dans la boîte aux lettres interne.

---

### 🧹 cleanup

**Rôle** : Nettoyer et normaliser les messages

**Responsabilités** : Ajoute les en-têtes manquants (Date, Message-ID, etc.) - Complète les adresses (user → user@domain.com) - Extrait les destinataires des en-têtes - Écrit le message dans la file `incoming/` - Notifie le `qmgr`

**Analogie** : C'est le service qualité qui vérifie que le courrier est conforme avant expédition.

---

### 📊 qmgr (Queue manager)

**Rôle** : Gérer les files d'attente - C'est le **cœur** de Postfix !

**Responsabilités** : Surveille les files d'attente - Décide quand envoyer les messages - Choisit le bon processus de livraison - Gère les tentatives et les délais - Optimise l'envoi (regroupe par destination)

**Analogie** : C'est le chef de gare qui décide quels trains partent, quand, et vers où.

---

### 🚀 smtp (SMTP client)

**Rôle** : Envoyer les emails vers d'autres serveurs

**Responsabilités** : Se connecte aux serveurs destinataires - Négocie TLS si possible - Transmet le message - Gère les erreurs temporaires (retry) et permanentes - Notifie le `qmgr` du résultat

**Analogie** : C'est le facteur qui livre le courrier chez le destinataire.

---

### 📮 local

**Rôle** : Livrer les emails locaux

**Responsabilités** : Livre les emails dans les boîtes locales - Gère les fichiers `.forward` - Applique les alias - Peut invoquer des programmes externes (filtres)

**Analogie** : C'est le facteur qui distribue le courrier dans les boîtes aux lettres de l'immeuble.

---

### ↩️ bounce

**Rôle** : Gérer les rebonds (emails non délivrés)

**Responsabilités** : Génère les messages de non-délivrance (bounce) - Notifie l'expéditeur en cas d'échec définitif - Gère les messages d'avertissement (delay warning)

**Analogie** : C'est le service retour qui renvoie le courrier avec la mention "n'habite pas à l'adresse indiquée".

---

### 🔧 trivial-rewrite

**Rôle** : Réécriture d'adresses

**Responsabilités** : Résout les adresses (lookup DNS) - Applique les règles de réécriture - Détermine le transport approprié

---

## Les files d'attente

Postfix utilise plusieurs files d'attente dans `/var/spool/postfix/` :

### 📂 maildrop

**Contenu** : Messages déposés localement par les programmes  
**Processus responsable** : `pickup`  
**Durée de vie** : Très courte (quelques secondes)

### 📂 incoming

**Contenu** : Messages reçus, en cours de nettoyage  
**Processus responsable** : `cleanup`  
**Durée de vie** : Courte (secondes à minutes)

### 📂 active

**Contenu** : Messages en cours de livraison  
**Processus responsable** : `qmgr`  
**Taille limite** : Contrôlée (évite la saturation mémoire)

### 📂 deferred

**Contenu** : Messages en échec temporaire  
**Processus responsable** : `qmgr`  
**Durée de vie** : Jusqu'à 5 jours par défaut

Les messages en `deferred` sont retentés selon un algorithme exponentiel : 1ère tentative immédiate, 2ème après quelques minutes, 3ème après 15-30 minutes, 4ème après 1 heure, etc.

### 📂 hold

**Contenu** : Messages mis en attente manuellement  
**Processus responsable** : Admin (vous !)  
**Durée de vie** : Jusqu'à libération manuelle

### 📂 corrupt

**Contenu** : Messages corrompus  
**Processus responsable** : Aucun (pour investigation)  
**Durée de vie** : Jusqu'à suppression manuelle

---

## Le parcours d'un email

Suivons un email de bout en bout !

---

### 📧 Scénario 1 : Email entrant depuis Internet

```
1. Connexion au port 25
   → Process smtpd démarre

2. Dialogue SMTP
   EHLO client.example.com
   MAIL FROM:<sender@example.com>
   RCPT TO:<user@votredomaine.com>
   DATA
   [contenu email]
   .

3. smtpd applique les restrictions
   → Vérifie le sender
   → Vérifie le recipient
   → Applique les RBL, SPF, etc.
```

---

```
4. Message accepté
   → smtpd passe à cleanup

5. cleanup normalise le message
   → Ajoute Message-ID
   → Complète les headers
   → Écrit dans incoming/

6. cleanup notifie qmgr
```

---

```
7. qmgr récupère le message
   → Déplace vers active/
   → Détermine le transport (local)

8. qmgr invoque le process local
   → local lit le message
   → Applique les alias
   → Livre dans Maildir/

9. Message délivré !
```

---

### 📤 Scénario 2 : Email sortant (envoi local)

```
1. Programme local appelle sendmail
   echo "Test" | sendmail user@example.com

2. sendmail écrit dans maildrop/

3. pickup détecte le nouveau fichier
   → Récupère le message
   → Passe à cleanup
```

---

```
4. cleanup normalise
   → Complète l'adresse sender
   → Ajoute headers manquants
   → Écrit dans incoming/

5. qmgr prend le relais
   → Déplace vers active/
   → Détermine le transport (smtp)

6. qmgr invoque le process smtp
```

---

```
7. smtp se connecte au serveur destination
   → Résolution DNS (MX record)
   → Connexion au port 25
   → Négociation TLS
   → Transmission du message

8. Serveur distant accepte
   → smtp notifie qmgr
   → Message supprimé de la queue

9. Email délivré !
```

---

### ❌ Scénario 3 : Échec de livraison

```
1-6. Même processus que scénario 2

7. smtp tente la connexion
   → Erreur : Connection refused

8. smtp notifie qmgr de l'échec temporaire
   → qmgr déplace le message dans deferred/
   → Planifie un retry dans 5 minutes
```

---

```
9. Après 5 minutes, qmgr réessaie
   → Déplace dans active/
   → Relance smtp

10. Si échec persiste
    → Nouvelle tentative après 15 min
    → Puis 30 min, 1h, 2h, etc.

11. Après 5 jours d'échecs
    → bounce génère un message de non-délivrance
    → Envoie à l'expéditeur original
    → Message original supprimé
```

---

## Communication inter-processus

Les processus Postfix communiquent via :

### 🔌 Sockets Unix

Fichiers spéciaux dans `/var/spool/postfix/` :

```bash
ls -la /var/spool/postfix/public/
# cleanup
# pickup
# qmgr
# showq
```

### 📬 Files système

Messages = fichiers dans les répertoires de queue

Format optimisé pour : Rapidité d'accès - Intégrité (pas de corruption en cas de crash) - Atomicité des opérations

### 🔒 Locking

Postfix utilise des verrous (locks) pour éviter : Les accès concurrents au même fichier - Les race conditions - La corruption de données

---

## Gestion de la mémoire et des ressources

### 🎛️ Limites par défaut

```sql
# Nombre max de processus smtpd simultanés
default_process_limit = 100

# Taille max de la queue active
qmgr_message_active_limit = 20000

# Taille max de la queue recipient
qmgr_message_recipient_limit = 20000
```

---

### ⚡ Optimisation des performances

**Connection caching** : Réutilisation des connexions SMTP

```sql
smtp_connection_cache_on_demand = yes
smtp_connection_cache_destinations = example.com
```

---

**Lazy binding** : Connexions LDAP/DB à la demande

```sql
# Pas de connexion permanente
ldap_cache_size = 0
```

---

## Modes de fonctionnement

### 📬 Mode "null client"

Postfix configuré uniquement pour **envoyer**, pas recevoir :

```sql
inet_interfaces = loopback-only
mydestination =
relayhost = [smtp.example.com]
```

---

**Cas d'usage** :
- Serveur web qui envoie des notifications
- Application qui ne reçoit jamais d'emails
- Machine dans un LAN sans exposition Internet

---

### 🌐 Mode "Internet site"

Configuration classique, reçoit et envoie :

```sql
inet_interfaces = all
mydestination = $myhostname, $mydomain, localhost
mynetworks = 127.0.0.0/8
```

---

### 🔄 Mode "relay"

Relais entre réseaux :

```sql
inet_interfaces = all
relay_domains = $mydestination, domain1.com, domain2.com
relayhost = [mail.backend.com]
```

---

## Sécurité par conception

### 🛡️ Chroot

La plupart des processus tournent dans un chroot :

```bash
/var/spool/postfix/  # Racine du chroot
├── etc/            # Fichiers de config nécessaires
├── lib/            # Bibliothèques
├── usr/
│   └── lib/
└── [process directories]
```

---

Le processus ne peut pas accéder à `/etc/passwd`, `/root`, etc.

Même si compromis, les dégâts sont limités au chroot !

---

### 🔐 Séparation des privilèges

| Processus | Utilisateur | Droits |
|-----------|-------------|--------|
| master | root | Supervision uniquement |
| smtpd | postfix | Lecture réseau |
| cleanup | postfix | Écriture queue |
| qmgr | postfix | Gestion queue |
| local | root | Écriture mailbox (nécessaire) |
| smtp | postfix | Écriture réseau |

---

Même si `smtpd` est compromis, l'attaquant ne peut pas :
- Modifier les fichiers de config
- Lire les mailbox des utilisateurs
- Écrire ailleurs que dans la queue

---

## Processus auxiliaires

### 🔍 anvil

**Rôle** : Compteur de connexions

Surveille et limite :
- Nombre de connexions par IP
- Taux de connexions
- Détection de comportements suspects

---

### 🗑️ proxymap

**Rôle** : Proxy pour les lookups (DB, LDAP)

Centralise les connexions aux bases externes :
- Évite trop de connexions simultanées
- Cache les résultats
- Optimise les performances

---

### 🔑 tlsproxy

**Rôle** : Proxy TLS

Gère les connexions TLS :
- Négociation SSL/TLS
- Vérification des certificats
- Chiffrement/déchiffrement

### 📝 postlog

**Rôle** : Logger centralisé

Reçoit les logs de tous les processus chrootés et les écrit dans syslog.

---

## Visualiser l'architecture en action

### 🔍 Voir les processus actifs

```bash
ps aux | grep postfix
```

Vous devriez voir :

```
root     postfix  master
postfix  postfix  qmgr
postfix  postfix  pickup
postfix  postfix  cleanup
postfix  postfix  smtpd
...
```

---

### 📊 Surveiller les connexions

```bash
sudo postfix status
```

```bash
# Connexions actives sur le port 25
sudo ss -tnp | grep :25
```

### 📬 Voir l'activité des queues

```bash
watch -n 1 'mailq | head -n 20'
```

---

## Debugging de l'architecture

### 🐛 Mode verbose

Activer les logs détaillés :

```bash
sudo postconf -e "debug_peer_list = example.com"
sudo postconf -e "debug_peer_level = 2"
sudo systemctl reload postfix
```

---

### 🔬 Suivre un message spécifique

Dans les logs, chaque message a un **Queue ID** unique :

```
postfix/smtpd[1234]: ABC123: client=example.com[1.2.3.4]
postfix/cleanup[1235]: ABC123: message-id=<test@example.com>
postfix/qmgr[1236]: ABC123: from=<sender@example.com>, size=1234
postfix/smtp[1237]: ABC123: to=<dest@example.com>, status=sent
```

---

Pour suivre le message `ABC123` :

```bash
sudo grep ABC123 /var/log/mail.log
```

### 📈 Statistiques en temps réel

```bash
# Statistiques globales
sudo postqueue -p | tail -n 1

# Statistiques par destination
sudo qshape active
sudo qshape deferred
```

---

## Schéma récapitulatif complet

```
┌─────────────────────────────────────────────────────┐
│                    MASTER (root)                     │
│         Surveille et gère tous les processus        │
└────────────────────┬────────────────────────────────┘
                     │
      ┌──────────────┼──────────────┐
      │              │              │
      ▼              ▼              ▼
┌─────────┐    ┌─────────┐    ┌─────────┐
│ smtpd   │    │ pickup  │    │  local  │
│(postfix)│    │(postfix)│    │ (root)  │
└────┬────┘    └────┬────┘    └────┬────┘
     │              │              │
     └──────┬───────┴──────────────┘
            ▼
      ┌─────────┐
      │ cleanup │
      │(postfix)│
      └────┬────┘
           │
     ┌─────▼─────────────────┐
     │   QUEUES (fichiers)   │
     │  incoming → active    │
     │      → deferred       │
     └─────┬─────────────────┘
           │
      ┌────▼────┐
      │  qmgr   │
      │(postfix)│
      └────┬────┘
           │
     ┌─────┼─────┐
     ▼           ▼
┌─────────┐ ┌─────────┐
│  smtp   │ │ bounce  │
│(postfix)│ │(postfix)│
└─────────┘ └─────────┘
     │
     ▼
 Internet
```

---

## Points clés à retenir

### 💡 Architecture

**Master = Chef d'orchestre** : Lance et surveille tous les processus - Configuré via `master.cf`

**Processus spécialisés** : `smtpd` (Réception) - `smtp` (Envoi) - `qmgr` (Gestion des files) - `cleanup` (Normalisation) - `local` (Livraison locale)

**Files d'attente** : `maildrop` → `incoming` → `active` → livraison - `deferred` pour les échecs temporaires - `hold` pour mise en attente manuelle

**Sécurité** : Chroot pour la plupart des processus - Séparation des privilèges - Principe du moindre privilège

**Communication** : Sockets Unix entre processus - Fichiers dans les queues - Pas de communication directe

---

## Exercice pratique

### 🎯 Exercice 1 : Observer l'architecture

1. Listez tous les processus Postfix actifs
2. Identifiez le PID du master
3. Comptez combien de processus `smtpd` tournent

### 🎯 Exercice 2 : Suivre un message

1. Envoyez un email de test
2. Notez son Queue ID dans les logs
3. Suivez son parcours complet à travers les processus

### 🎯 Exercice 3 : Explorer les queues

1. Créez des messages en deferred (envoi vers domaine inexistant)
2. Explorez le contenu de `/var/spool/postfix/deferred/`
3. Utilisez `postcat` pour lire un message

```bash
sudo postcat -q QUEUE_ID
```

### 🎯 Exercice 4 : Modifier master.cf

1. Augmentez le nombre max de processus `smtpd` à 200
2. Ajoutez le service submission sur le port 587
3. Rechargez Postfix et vérifiez que le port est ouvert

---

## Prochaine étape

Maintenant que vous comprenez **comment** Postfix fonctionne, nous allons plonger dans le **fichier de configuration principale** : `main.cf`

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Configuration du main.cf <carbon:arrow-right class="inline"/>
  </span>
</div>

