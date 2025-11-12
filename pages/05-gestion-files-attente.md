---
layout: new-section
routeAlias: 'gestion-files-attente'
---

<a name="gestion-files-attente" id="gestion-files-attente"></a>

# Gestion des Files d'Attente

📬 Maîtriser le système de queues de Postfix

---

# Introduction aux files d'attente

Les files d'attente (queues) sont le **cœur** de Postfix. C'est là que les emails attendent d'être traités, envoyés, ou retentés en cas d'échec.

## Pourquoi des files d'attente ?

Imaginez un bureau de poste :
- Le courrier arrive → Il est trié
- Il attend d'être acheminé → File d'attente
- Il est envoyé → Livraison
- S'il y a un problème → Il revient pour réessayer plus tard

Postfix fait exactement pareil !

---

## Les différentes queues

Postfix utilise plusieurs files d'attente dans `/var/spool/postfix/` :

<small>

Le répertoire `/var/spool/postfix/` est donc :

L’endroit où Postfix met en file d’attente les emails avant qu’ils ne soient effectivement traités ou envoyés.

C’est littéralement un “spool directory”, une file d’attente persistante sur disque pour le courrier sortant et entrant.

</small>

### 📂 maildrop

**Rôle** : Point d'entrée pour les emails locaux - **Processus** : `pickup` surveille ce répertoire - **Durée** : Quelques secondes maximum

**Comment les emails arrivent ici** :

```bash
# Commande sendmail (fournie par Postfix)
echo "Test" | sendmail user@example.com

# La commande sendmail écrit dans maildrop/
```

---

### 📂 incoming (hold en Postfix récent)

**Rôle** : Emails en cours de nettoyage - **Processus** : `cleanup` traite les messages ici - **Durée** : Très courte (quelques secondes)

**Que fait cleanup ?** Ajoute les headers manquants (Message-ID, Date) - Complète les adresses - Extrait les destinataires - Prépare le message pour la queue

---

### 📂 active

**Rôle** : Messages en cours de livraison **immédiate** par Postfix.

**Processus** : `qmgr` (Queue Manager) gère cette queue 

**Taille limitée** : Par défaut 20 000 messages max

**Pourquoi limiter la taille ?** Si `active` était illimitée, Postfix pourrait charger des millions de messages en mémoire et crasher ! La limitation force Postfix à ne traiter que ce qu'il peut gérer.

---

### 📂 deferred

**Rôle** : Messages en échec temporaire par Postfix. 

**Processus** : `qmgr` planifie les retentatives.

**Durée** : Jusqu'à `maximal_queue_lifetime` (5 jours par défaut)

**Raisons courantes de defer** : Serveur destinataire injoignable 

- Timeout de connexion 

- Erreur temporaire (4xx SMTP) 

- Trop de connexions simultanées

**Algorithme de retry** :

```
Tentative 1 : immédiat
Tentative 2 : après 5 minutes (minimal_backoff_time)
Tentative 3 : après 10 minutes
Tentative 4 : après 20 minutes
Tentative 5 : après 40 minutes
...
Jusqu'à maximal_backoff_time (≈1h)
Puis toutes les heures jusqu'à 5 jours
```

---

### 📂 hold

**Rôle** : Messages mis en attente **manuellement** - **Processus** : Aucun automatique (administration manuelle) - **Durée** : Jusqu'à libération manuelle

**Cas d'usage** : Inspection manuelle d'emails suspects - Mise en pause temporaire de certains messages - Investigation de problèmes - Filtrage manuel

---

### 📂 corrupt

**Rôle** : Messages corrompus (fichiers illisibles) - **Processus** : Aucun (pour investigation) - **Durée** : Jusqu'à suppression manuelle

**Comment un message devient corrupt ?** Crash pendant l'écriture - Problème disque - Bug (très rare) - Manipulation manuelle incorrecte

---

## Visualiser les queues

### 📊 Commande mailq

La commande la plus utilisée pour voir l'état des queues :

```bash
mailq
# ou équivalent
postqueue -p
```

---

**Exemple de sortie** :

```
-Queue ID-  --Size-- ----Arrival Time---- -Sender/Recipient---
ABC123DEF     1234 Fri Dec 13 10:30:00  sender@example.com
                                         user@domain.com

DEF456GHI     5678 Fri Dec 13 10:35:00  admin@example.com
                         (connect to mail.server.com[1.2.3.4]:25: Connection refused)
                                         contact@server.com

-- 2 Kbytes in 2 Requests.
```

---

**Décryptage** : **ABC123DEF** (Queue ID du message) - **1234** (Taille en bytes) - **Fri Dec 13 10:30:00** (Date/heure d'arrivée) - **sender@example.com** (Expéditeur) - **user@domain.com** (Destinataire)

**Message en deferred** :

```
DEF456GHI     5678 Fri Dec 13 10:35:00  admin@example.com
                        (connect to mail.server.com[1.2.3.4]:25: Connection refused)
                                        contact@server.com
```

L'erreur est affichée entre parenthèses.

---

### 📈 Statistiques par destination

```bash
# Statistiques de la queue active
qshape active

# Statistiques de la queue deferred
qshape deferred
```

**Exemple de sortie** :

```
                        T  5 10 20 40 80 160 320 640 1280 1280+
               TOTAL 1234  0  0  0  0  0   5   10  50  100  1069
          example.com  500  0  0  0  0  0   0    5  25   50   420
          domain.com   300  0  0  0  0  0   2    3  20   30   245
          server.com   200  0  0  0  0  0   1    1   3   10   185
```

**Lecture** : **T** (Total de messages) - **Colonnes** (Nombre de messages par tranche d'âge en minutes) - **example.com** (500 messages pour ce domaine, dont 420 de plus de 1280 minutes - vieux !)

---

## Outils de gestion des queues

### 🔍 postqueue

**Voir la queue** :

```bash
postqueue -p
```

**Forcer l'envoi immédiat de tous les messages** :

```bash
sudo postqueue -f
```

(Utile après avoir résolu un problème réseau)

**Forcer l'envoi d'un message spécifique** :

```bash
sudo postqueue -i QUEUE_ID
```

---

### 🗑️ postsuper

**Supprimer un message** :

```bash
sudo postsuper -d QUEUE_ID
```

---

**Supprimer TOUS les messages** :

```bash
sudo postsuper -d ALL
```

⚠️ **Attention** : Ça supprime VRAIMENT tout !

---

**Supprimer tous les messages d'une queue spécifique** :

```bash
# Supprimer tous les deferred
sudo postsuper -d ALL deferred

# Supprimer tous les hold
sudo postsuper -d ALL hold
```

**Mettre un message en hold** :

```bash
sudo postsuper -h QUEUE_ID
```

---

**Libérer un message en hold** :

```bash
sudo postsuper -H QUEUE_ID
```

**Réorganiser les queues** (après un crash) :

```bash
sudo postsuper -s
```

### 📖 postcat

**Lire le contenu d'un message** :

```bash
sudo postcat -q QUEUE_ID
```

---

**Exemple de sortie** :

```
*** ENVELOPE RECORDS deferred/ABC123DEF ***
message_size:             1234             567
message_arrival_time: Fri Dec 13 10:30:00 2025
sender: sender@example.com
recipient: user@domain.com

*** MESSAGE CONTENTS deferred/ABC123DEF ***
Received: from client.example.com (client.example.com [1.2.3.4])
        by mail.example.com (Postfix) with ESMTP id ABC123DEF
        for <user@domain.com>; Fri, 13 Dec 2025 10:30:00 +0000
From: sender@example.com
To: user@domain.com
Subject: Test email
Date: Fri, 13 Dec 2025 10:30:00 +0000
Message-ID: <abc123@example.com>

This is a test email.
```

---

### 🔍 Autres commandes utiles

**Compter les messages en queue** :

```bash
mailq | tail -n 1
```

**Lister les Queue IDs uniquement** :

```bash
mailq | grep -E '^[A-F0-9]+' | awk '{print $1}'
```

**Compter les messages par domaine destinataire** :

```bash
mailq | grep '@' | awk '{print $NF}' | sort | uniq -c | sort -rn
```

---

## Scénarios courants

### 📤 Scénario 1 : Queue qui grossit

**Symptôme** : Le nombre de messages en queue augmente sans cesse

```bash
mailq | tail -n 1
# -- 50000 Kbytes in 10000 Requests.
```

---

**Causes possibles** :

1. **Serveur destinataire down** : Vérifiez avec `qshape deferred`
2. **Rate limiting** : Le destinataire limite le nombre d'emails acceptés
3. **Blacklist** : Votre IP est blacklistée
4. **Problème réseau** : Firewall, DNS, routing

---

**Diagnostic** :

```bash
# Voir les erreurs dans les logs
sudo tail -n 100 /var/log/mail.log | grep error

# Voir les destinations problématiques
qshape deferred | head -n 20
```

---

**Solutions** :

```bash
# Si problème résolu, forcer l'envoi
sudo postqueue -f

# Si messages corrompus ou sans espoir
sudo postsuper -d ALL deferred
```

---

### 🚫 Scénario 2 : Bounce storm

**Symptôme** : Explosion du nombre de bounces

Vous recevez des milliers de bounces pour des emails que vous n'avez pas envoyés !

**Cause** : Spoofing d'adresse

Des spammeurs utilisent votre domaine comme expéditeur. Les bounces vous reviennent.

---

**Solution** :

```bash
# Supprimer tous les bounces
sudo postsuper -d ALL bounce

# Configurer SPF/DKIM/DMARC (voir module dédié)
```

### ⏸️ Scénario 3 : Pause temporaire

**Besoin** : Arrêter l'envoi d'emails pendant une maintenance

---

**Solution** :

```bash
# Arrêter le traitement de la queue
sudo postsuper -h ALL

# Après maintenance, relancer
sudo postsuper -H ALL
sudo postqueue -f
```

---

### 🔍 Scénario 4 : Identifier les spams

**Symptôme** : Beaucoup d'emails suspects en queue


**Diagnostic** :

```bash
# Lister les expéditeurs
mailq | grep 'From:' | sort | uniq -c | sort -rn

# Rechercher un pattern
mailq | grep 'viagra'
```

---

**Solution** :

```bash
# Supprimer les emails d'un expéditeur spécifique
mailq | grep 'spammer@spam.com' | awk '{print $1}' | while read id; do
    sudo postsuper -d "$id"
done
```

---

## Configuration avancée des queues

### ⚙️ Paramètres dans main.cf

**Taille de la queue active** :

```bash
# Par défaut 20000
qmgr_message_active_limit = 20000

# Augmenter pour serveur puissant
qmgr_message_active_limit = 50000
```

---

**Nombre de destinataires actifs** :

```bash
# Par défaut 20000
qmgr_message_recipient_limit = 20000
```

---

**Fréquence de scan de la queue** :

```bash
# Par défaut toutes les 5 minutes
queue_run_delay = 300s

# Plus fréquent
queue_run_delay = 60s
```

---

**Backoff times (délais de retry)** :

```bash
# Minimum 5 minutes
minimal_backoff_time = 300s

# Maximum ~1 heure
maximal_backoff_time = 4000s
```

---

**Durée de vie maximum** :

```bash
# Messages normaux : 5 jours
maximal_queue_lifetime = 5d

# Bounces : 5 jours
bounce_queue_lifetime = 5d
```

---

**Avertissement de délai** :

```bash
# Prévenir l'expéditeur après 4h
delay_warning_time = 4h

# Désactiver
delay_warning_time = 0h
```

---

## Monitoring des queues

### 📊 Script de surveillance basique

```bash
#!/bin/bash
# check_queue.sh

QUEUE_COUNT=$(mailq | tail -n 1 | awk '{print $5}')
THRESHOLD=1000

if [ "$QUEUE_COUNT" -gt "$THRESHOLD" ]; then
    echo "ALERTE : $QUEUE_COUNT messages en queue !"
    # Envoyer une notification
    echo "Queue trop grosse" | mail -s "Alerte Postfix" admin@example.com
fi
```

---

**Automatiser avec cron** :

```bash
# Vérifier toutes les 15 minutes
*/15 * * * * /usr/local/bin/check_queue.sh
```

### 📈 Métriques à surveiller

**Nombre total de messages** :

```bash
mailq | tail -n 1 | awk '{print $5}'
```

---

**Taille totale de la queue** :

```bash
mailq | tail -n 1 | awk '{print $1}'
```


**Messages en deferred** :

```bash
find /var/spool/postfix/deferred -type f | wc -l
```

---

**Messages en active** :

```bash
find /var/spool/postfix/active -type f | wc -l
```

---

**Âge du message le plus ancien** :

```bash
qshape deferred | head -n 2 | tail -n 1
```

---

### 🔔 Alertes avec Prometheus + Grafana

Si vous utilisez Prometheus, exportez ces métriques :

```
postfix_queue_size{queue="active"}
postfix_queue_size{queue="deferred"}
postfix_queue_age_seconds{queue="deferred"}
postfix_bounce_total
```

---

## Nettoyage et maintenance

### 🧹 Nettoyage régulier

**Supprimer les vieux messages en deferred** :

```bash
# Supprimer les messages de plus de 3 jours
find /var/spool/postfix/deferred -type f -mtime +3 -delete
```

---

**Purger les bounces** :

```bash
sudo postsuper -d ALL bounce
```

**Vérifier l'intégrité de la queue** :

```bash
sudo postsuper -s
```

---

### 🔧 Réparation après crash

Si Postfix a crashé ou le serveur a redémarré brutalement :

```bash
# 1. Vérifier l'intégrité
sudo postsuper -s

# 2. Déplacer les corrupt
sudo postsuper -d ALL corrupt

# 3. Relancer les deferred
sudo postqueue -f
```

---

## Scripts utiles

### 🗑️ Supprimer les messages d'un expéditeur

```bash
#!/bin/bash
# delete_sender.sh <email>

SENDER=$1

mailq | grep -B 1 "$SENDER" | grep '^[A-F0-9]' | cut -d ' ' -f 1 | while read qid; do
    echo "Suppression : $qid"
    sudo postsuper -d "$qid"
done
```

---

**Utilisation** :

```bash
chmod +x delete_sender.sh
./delete_sender.sh spammer@spam.com
```

---

### 📧 Supprimer les messages vers un destinataire

```bash
#!/bin/bash
# delete_recipient.sh <email>

RECIPIENT=$1

mailq | grep -A 1 "$RECIPIENT" | grep '^[A-F0-9]' | cut -d ' ' -f 1 | while read qid; do
    echo "Suppression : $qid"
    sudo postsuper -d "$qid"
done
```

---

### 📊 Rapport quotidien de la queue

```bash
#!/bin/bash
# queue_report.sh

echo "=== Rapport de la queue Postfix ==="
echo "Date : $(date)"
echo ""
echo "Total messages : $(mailq | tail -n 1 | awk '{print $5}')"
echo "Taille totale : $(mailq | tail -n 1 | awk '{print $1}')"
echo ""
echo "Par queue :"
```

---

```bash
echo "  Active : $(find /var/spool/postfix/active -type f | wc -l)"
echo "  Deferred : $(find /var/spool/postfix/deferred -type f | wc -l)"
echo "  Hold : $(find /var/spool/postfix/hold -type f | wc -l)"
echo ""
echo "Top 10 destinations :"
qshape deferred | head -n 11
```

---

## Optimisation des performances

### ⚡ Pour gros volumes

Si vous envoyez des milliers d'emails par heure :

```bash
# Augmenter la queue active
qmgr_message_active_limit = 50000

# Plus de destinataires simultanés
qmgr_message_recipient_limit = 50000

# Plus de connexions par destination
smtp_destination_concurrency_limit = 50
```

---

```bash
# Traiter la queue plus souvent
queue_run_delay = 60s

# Plus de processus SMTP sortants
default_process_limit = 500
```

---

### 🐌 Pour petits serveurs

Si vous avez peu de ressources :

```bash
# Queue active réduite
qmgr_message_active_limit = 5000

# Moins de connexions simultanées
smtp_destination_concurrency_limit = 10
default_process_limit = 50
```

---

## Troubleshooting courant

### ❌ Problème : Messages bloqués en active

**Symptôme** : Messages dans active mais ne partent pas

---

**Diagnostic** :

```bash
# Voir les logs en temps réel
sudo tail -f /var/log/mail.log

# Voir le contenu du message
sudo postcat -q QUEUE_ID
```

---

**Causes courantes** :
- Serveur destinataire timeout
- Problème DNS
- Firewall bloquant
- Certificat TLS invalide

---

**Solution** :

```bash
# Forcer le retry
sudo postqueue -i QUEUE_ID

# Ou mettre en hold pour investigation
sudo postsuper -h QUEUE_ID
```

---

### ❌ Problème : Deferred qui ne retry pas

**Symptôme** : Messages restent en deferred sans tentative

**Cause** : Postfix ne scanne pas assez souvent la queue

**Solution** :

```bash
# Réduire queue_run_delay
queue_run_delay = 60s
```

---

```bash
# Ou forcer manuellement
sudo postqueue -f
```

### ❌ Problème : Queue qui ne se vide jamais

**Symptôme** : Toujours des milliers de messages en deferred

---

**Diagnostic** :

```bash
# Identifier la destination problématique
qshape deferred | head -n 10

# Voir les erreurs
sudo grep "domaine.com" /var/log/mail.log | tail -n 50
```

---

**Solutions** :
1. **Rate limiting** : Réduire `smtp_destination_rate_delay`
2. **Blacklist** : Vérifier sur https://mxtoolbox.com/blacklists.aspx
3. **Spam** : Améliorer SPF/DKIM/DMARC
4. **IP réputation** : Changer d'IP ou utiliser un relais

---

## Exercices pratiques

### 🎯 Exercice 1 : Explorer les queues

1. Envoyez 10 emails de test vers différents domaines
2. Listez la queue avec `mailq`
3. Identifiez les Queue IDs
4. Lisez le contenu d'un message avec `postcat`

---

### 🎯 Exercice 2 : Gérer les deferred

1. Envoyez un email vers un domaine inexistant
2. Attendez qu'il passe en deferred
3. Affichez les statistiques avec `qshape deferred`
4. Supprimez le message

---

### 🎯 Exercice 3 : Hold et release

1. Créez un script qui met tous les nouveaux messages en hold
2. Envoyez quelques emails
3. Vérifiez qu'ils sont en hold
4. Libérez-les manuellement

---

### 🎯 Exercice 4 : Script de nettoyage

1. Créez un script qui :
   - Liste tous les messages de plus de 2 jours en deferred
   - Les compte
   - Les supprime après confirmation

---

## Points clés à retenir

### 💡 Les queues

**Flux normal** :
maildrop → incoming → active → livraison

**En cas d'échec** :
active → deferred → retry → active (ou bounce après 5j)

---

<small>

**Commandes essentielles** :
- `mailq` / `postqueue -p` : Voir la queue
- `postsuper -d` : Supprimer
- `postqueue -f` : Forcer l'envoi
- `postcat -q` : Lire un message
- `qshape` : Statistiques

**Monitoring** :
- Surveiller la taille de la queue
- Alerter si > seuil
- Vérifier régulièrement les deferred
- Analyser les patterns d'erreurs

**Optimisation** :
- Ajuster `qmgr_message_active_limit`
- Configurer `queue_run_delay`
- Adapter `smtp_destination_concurrency_limit`

</small>

---

## Prochaine étape

Maintenant que vous savez gérer les queues, nous allons apprendre à configurer les **alias et les tables virtuelles** !

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Alias et tables virtuelles <carbon:arrow-right class="inline"/>
  </span>
</div>
