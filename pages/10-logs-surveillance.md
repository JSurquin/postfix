---
layout: new-section
routeAlias: 'logs-surveillance'
---

<a name="logs-surveillance" id="logs-surveillance"></a>

# Logs et Surveillance

<div class="mt-2">
  📊 Analyser, surveiller et comprendre votre serveur mail
</div>

---

# Introduction aux logs

Les logs sont votre **meilleur ami** pour :
- Comprendre ce qui se passe
- Diagnostiquer les problèmes
- Détecter les attaques
- Optimiser les performances
- Auditer l'activité

---

## Emplacement des logs

### 📂 Ubuntu/Debian

```
/var/log/mail.log        # Tous les logs mail
/var/log/mail.err        # Erreurs uniquement
/var/log/mail.warn       # Avertissements
```

### 📂 Rocky Linux / Red Hat

```
/var/log/maillog         # Tous les logs mail
```

### 📂 Postfix 3.4+

Postfix peut logger directement dans son propre fichier :

```bash
# Dans main.cf
maillog_file = /var/log/postfix.log
```

---

# Structure d'une ligne de log

```
Dec 13 10:30:15 mail postfix/smtpd[1234]: ABC123DEF: client=example.com[1.2.3.4]
```

---

**Décryptage** : `Dec 13 10:30:15` (Date et heure) - `mail` (Hostname du serveur) - `postfix/smtpd[1234]` (Service Postfix et PID) - `ABC123DEF` (Queue ID du message) - `client=example.com[1.2.3.4]` (Informations supplémentaires)

---

## Suivre un message de bout en bout

Tous les logs d'un même message partagent le même **Queue ID**.

---

### 🔍 Exemple : Message ABC123DEF

```
Dec 13 10:30:15 mail postfix/smtpd[1234]: ABC123DEF: client=sender.com[1.2.3.4]
Dec 13 10:30:15 mail postfix/cleanup[1235]: ABC123DEF: message-id=<test@sender.com>
Dec 13 10:30:15 mail postfix/qmgr[1236]: ABC123DEF: from=<user@sender.com>, size=1234
Dec 13 10:30:16 mail postfix/smtp[1237]: ABC123DEF: to=<dest@example.com>, status=sent
Dec 13 10:30:16 mail postfix/qmgr[1236]: ABC123DEF: removed
```

---

**Parcours complet** : 

- 1. **smtpd** (Message reçu de `sender.com`)

- 2. **cleanup** (Nettoyage et attribution d'un message-id)

- 3. **qmgr** (Mise en queue, expéditeur et taille)

- 4. **smtp** (Envoi réussi vers `dest@example.com`)

- 5. **qmgr** (Message supprimé de la queue)

### 🔎 Rechercher un message

```bash
sudo grep ABC123DEF /var/log/mail.log
```

---

# Types de messages courants

## ✅ Messages réussis

```
postfix/smtp[1234]: ABC123: to=<user@example.com>, relay=mail.example.com[1.2.3.4]:25, delay=0.52, delays=0.01/0/0.02/0.49, dsn=2.0.0, status=sent (250 2.0.0 Ok: queued as DEF456)
```

**Informations clés** : `to` (Destinataire) - `relay` (Serveur qui a accepté l'email) - `delay` (Délai total en secondes) - `delays` (Détails a/b/c/d : temps avant queue/dans queue/connexion/transmission) - `dsn` (Code de statut 2.x.x = succès) - `status=sent` (Envoi réussi)

---

## ⏱️ Messages en différé

```
postfix/smtp[1234]: ABC123: to=<user@example.com>, relay=mail.example.com[1.2.3.4]:25, delay=5.2, delays=0.01/5/0.02/0.17, dsn=4.4.1, status=deferred (connect to mail.example.com[1.2.3.4]:25: Connection timed out)
```

**Code 4.x.x** = Erreur temporaire - Le message sera réessayé plus tard.

## ❌ Messages rejetés

```
postfix/smtpd[1234]: NOQUEUE: reject: RCPT from unknown[1.2.3.4]: 554 5.7.1 <spammer@spam.com>: Sender address rejected: Access denied; from=<spammer@spam.com> to=<user@example.com>
```

**NOQUEUE** : Message rejeté avant même d'entrer en queue - **554 5.7.1** : Code d'erreur permanente - **Sender address rejected** : Raison du rejet

## 🔥 Messages rebondis (bounce)

```
postfix/smtp[1234]: ABC123: to=<invalid@nonexistent.com>, relay=mail.nonexistent.com[1.2.3.4]:25, delay=2.5, delays=0.01/0/0.5/2, dsn=5.1.1, status=bounced (host mail.nonexistent.com[1.2.3.4] said: 550 5.1.1 <invalid@nonexistent.com>: Recipient address rejected: User unknown)
```

**Code 5.x.x** = Erreur permanente - Un email de bounce sera envoyé à l'expéditeur.

---

# Commandes utiles

## 📜 Suivre les logs en temps réel

```bash
# Ubuntu/Debian
sudo tail -f /var/log/mail.log

# Rocky Linux
sudo tail -f /var/log/maillog

# Avec grep pour filtrer
sudo tail -f /var/log/mail.log | grep postfix
```

---

## 🔍 Rechercher dans les logs

```bash
# Par Queue ID
sudo grep ABC123 /var/log/mail.log

# Par adresse email
sudo grep "user@example.com" /var/log/mail.log

# Erreurs uniquement
sudo grep "error\|warning\|fatal" /var/log/mail.log
```

---

## 📊 Statistiques

```bash
# Compter les messages envoyés aujourd'hui
sudo grep "$(date +%b\ %e)" /var/log/mail.log | grep "status=sent" | wc -l

# Compter les rejets
sudo grep "reject:" /var/log/mail.log | wc -l
```

---

## 📈 Messages par heure

```bash
sudo grep "status=sent" /var/log/mail.log | awk '{print $1, $2, $3}' | uniq -c
```

---

## 🔝 Top des domaines destinataires

```bash
sudo grep "status=sent" /var/log/mail.log | grep -oP 'to=<[^>]+>' | sed 's/.*@//' | sed 's/>//' | sort | uniq -c | sort -rn | head -20
```

---

## 🚫 Top des rejets

```bash
sudo grep "reject:" /var/log/mail.log | awk '{print $7}' | sort | uniq -c | sort -rn
```

---

# Outils d'analyse de logs

## pflogsumm

Générateur de rapports pour Postfix.

---

### 📦 Installation

```bash
# Ubuntu/Debian
sudo apt install pflogsumm

# Rocky Linux
sudo dnf install postfix-perl-scripts
```

---

### 📊 Générer un rapport

```bash
# Rapport du jour
sudo pflogsumm /var/log/mail.log

# Rapport de la veille
sudo pflogsumm /var/log/mail.log.1

# Rapport détaillé
sudo pflogsumm -d today /var/log/mail.log
```

---

**Exemple de sortie** :

```
Grand Totals
------------
  messages received:        1234
  messages sent:            1156
  messages deferred:          45
  messages bounced:           12
  messages rejected:         789
  bytes received:        5.2 MB
  bytes sent:            4.8 MB
```

---

### ⏰ Automatiser avec cron

```bash
# Rapport quotidien envoyé par email
0 6 * * * /usr/sbin/pflogsumm -d yesterday /var/log/mail.log | mail -s "Postfix Report" admin@example.com
```

---

## Logwatch

Analyseur de logs système général (pas que Postfix).

---

### 📦 Installation

```bash
# Ubuntu/Debian
sudo apt install logwatch

# Rocky Linux
sudo dnf install logwatch
```

---

### 📊 Rapport Postfix

```bash
sudo logwatch --service postfix --range today --detail high
```

---

## GoAccess

Analyseur de logs en temps réel avec interface web.

(Plus orienté Apache/Nginx, mais peut s'adapter)

---

## Elasticsearch + Kibana

Pour les gros volumes, centralisez les logs dans Elasticsearch et visualisez avec Kibana.

(Avancé, pour le module perfectionnement)

---

# Surveillance en temps réel

## Monitoring basique avec scripts

### 📊 Script de surveillance

```bash
#!/bin/bash
# monitor-postfix.sh

echo "=== État de Postfix ==="
systemctl status postfix --no-pager | head -3
echo ""

echo "=== Queue ==="
mailq | tail -1
echo ""

echo "=== Activité récente (5 dernières minutes) ==="
```

---

```bash
SINCE=$(date -d '5 minutes ago' '+%b %e %H:%M')
echo "Envoyés : $(sudo grep "$SINCE" /var/log/mail.log | grep "status=sent" | wc -l)"
echo "Rejetés : $(sudo grep "$SINCE" /var/log/mail.log | grep "reject:" | wc -l)"
echo "Différés : $(sudo grep "$SINCE" /var/log/mail.log | grep "status=deferred" | wc -l)"
```

---

### ⏰ Automatiser

```bash
# Toutes les 5 minutes
*/5 * * * * /usr/local/bin/monitor-postfix.sh
```

---

## Alertes automatiques

### 🚨 Alerter si queue trop grosse

```bash
#!/bin/bash
# alert-queue.sh

QUEUE_SIZE=$(mailq | tail -1 | awk '{print $5}')
THRESHOLD=1000

if [ "$QUEUE_SIZE" -gt "$THRESHOLD" ]; then
    echo "Queue size: $QUEUE_SIZE" | mail -s "ALERT: Postfix queue" admin@example.com
fi
```

---

### 🚨 Alerter si trop de rejets

```bash
#!/bin/bash
# alert-rejects.sh

REJECTS=$(sudo grep "$(date +%b\ %e)" /var/log/mail.log | grep "reject:" | wc -l)
THRESHOLD=500

if [ "$REJECTS" -gt "$THRESHOLD" ]; then
    echo "Rejects today: $REJECTS" | mail -s "ALERT: High rejects" admin@example.com
fi
```

---

## Monitoring avec Prometheus + Grafana

### 📦 Installation de l'exporteur Postfix

```bash
git clone https://github.com/kumina/postfix_exporter
cd postfix_exporter
go build
sudo cp postfix_exporter /usr/local/bin/
```

---

### ⚙️ Créer un service systemd

```ini
[Unit]
Description=Postfix Exporter
After=network.target

[Service]
Type=simple
User=postfix
ExecStart=/usr/local/bin/postfix_exporter \
  --postfix.logfile_path=/var/log/mail.log
Restart=always

[Install]
WantedBy=multi-user.target
```

---

```bash
sudo systemctl enable postfix-exporter
sudo systemctl start postfix-exporter
```

---

### 📊 Configuration Prometheus

```yaml
scrape_configs:
  - job_name: 'postfix'
    static_configs:
      - targets: ['localhost:9154']
```

---

### 📈 Dashboard Grafana

Importer le dashboard ID **11733** depuis Grafana.com

Vous aurez de magnifiques graphiques ! 📊

---

# Rotation des logs

Les logs peuvent devenir très gros !

Il faut les **rotationner** (archiver et compresser).

---

## Configuration logrotate

Fichier `/etc/logrotate.d/postfix` :

```
/var/log/mail.log {
    daily
    rotate 30
    missingok
    notifempty
    compress
    delaycompress
    sharedscripts
    postrotate
        systemctl reload postfix > /dev/null
    endscript
}
```

---

**Explication** :

- `daily` : Rotation quotidienne
- `rotate 30` : Garder 30 jours d'archives
- `missingok` : Pas d'erreur si fichier manquant
- `notifempty` : Ne pas rotationner si vide
- `compress` : Compresser les archives
- `delaycompress` : Ne pas compresser immédiatement
- `postrotate` : Commande après rotation

---

### 🔄 Tester la rotation

```bash
sudo logrotate -f /etc/logrotate.d/postfix
```

---

# Debugging avancé

## Activer le mode verbose

```bash
# Dans main.cf
debug_peer_list = example.com, 1.2.3.4
debug_peer_level = 2
```

---

```bash
sudo systemctl reload postfix
```

Tous les échanges avec `example.com` ou `1.2.3.4` seront loggés en détail.

---

## Tracer un processus spécifique

```bash
# Suivre les logs d'un processus
sudo tail -f /var/log/mail.log | grep "smtpd\[1234\]"
```

---

## Inspecter un message dans la queue

```bash
# Voir le contenu complet
sudo postcat -q ABC123DEF

# Voir seulement les headers
sudo postcat -q ABC123DEF | head -50
```

---

# 🚀 Trucs de Pro pour le Debug Postfix

## Les commandes qui sauvent la vie

Ces astuces vont vous faire gagner un temps fou !

---

## 🔥 Alias Bash à mettre dans ~/.bashrc

```bash
# Alias pour les logs mail
alias maillog='sudo tail -f /var/log/mail.log'
alias mailgrep='sudo grep -i'
alias maillogs='sudo less /var/log/mail.log'

# Alias Postfix queue
alias mailq='sudo postqueue -p'
alias mailflush='sudo postqueue -f'
alias mailcount='sudo postqueue -p | tail -1'

# Alias statistiques rapides
alias mailstats='sudo pflogsumm -d today /var/log/mail.log'
alias mailrejects='sudo grep "reject:" /var/log/mail.log | tail -20'
alias mailerrors='sudo grep -E "error|warning|fatal" /var/log/mail.log | tail -20'
```

---

**Comment utiliser** :

```bash
# Ajouter au fichier
echo "alias maillog='sudo tail -f /var/log/mail.log'" >> ~/.bashrc

# Recharger le shell
source ~/.bashrc

# Maintenant vous pouvez juste taper :
maillog
```

---

## 🎯 Fonction Bash : Tracer un email facilement

```bash
# Ajouter dans ~/.bashrc
trace_mail() {
    if [ -z "$1" ]; then
        echo "Usage: trace_mail <email_address ou queue_id>"
        return 1
    fi
    echo "🔍 Recherche de: $1"
    sudo grep -i "$1" /var/log/mail.log | grep --color=auto "$1"
}
```

---

**Utilisation** :

```bash
# Par adresse email
trace_mail user@example.com

# Par Queue ID
trace_mail ABC123DEF
```

---

## 🔍 Recherche avancée : Trouver pourquoi un mail est rejeté

```bash
# Voir tous les rejets des 5 dernières minutes
sudo grep "$(date --date='5 minutes ago' '+%b %e %H:')" /var/log/mail.log | grep "reject:"
```

---

## 🎯 Fonction : Analyser les rejets par raison

```bash
# Ajouter dans ~/.bashrc
analyze_rejects() {
    echo "📊 Top 10 des raisons de rejet:"
    sudo grep "reject:" /var/log/mail.log | \
      sed 's/.*reject: //' | \
      cut -d';' -f1 | \
      sort | uniq -c | sort -rn | head -10
}
```

---

**Exemple de sortie** :

```
📊 Top 10 des raisons de rejet:
    523 RCPT from unknown: 554 5.7.1 Service unavailable
    312 Client host rejected: cannot find your hostname
    156 Helo command rejected: need fully-qualified hostname
     89 Sender address rejected: Domain not found
     45 Recipient address rejected: User unknown
```

---

## 🚀 Recherche ultra-rapide : Derniers messages d'une adresse

```bash
# Fonction à ajouter dans ~/.bashrc
last_mails() {
    if [ -z "$1" ]; then
        echo "Usage: last_mails <email_address> [nombre]"
        return 1
    fi
    local count=${2:-10}
    echo "📧 Derniers $count messages de/vers: $1"
    sudo grep -i "$1" /var/log/mail.log | tail -n $count
}
```

---

**Utilisation** :

```bash
# Voir les 10 derniers messages
last_mails user@example.com

# Voir les 50 derniers messages
last_mails user@example.com 50
```

---

## 📊 Fonction : Statistiques en temps réel

```bash
# Ajouter dans ~/.bashrc
mail_live_stats() {
    local duration=${1:-5}
    echo "📊 Statistiques des $duration dernières minutes:"
    local since=$(date -d "$duration minutes ago" '+%b %e %H:%M')
    
    local sent=$(sudo grep "$since" /var/log/mail.log 2>/dev/null | grep -c "status=sent")
    local rejected=$(sudo grep "$since" /var/log/mail.log 2>/dev/null | grep -c "reject:")
    local deferred=$(sudo grep "$since" /var/log/mail.log 2>/dev/null | grep -c "status=deferred")
    local bounced=$(sudo grep "$since" /var/log/mail.log 2>/dev/null | grep -c "status=bounced")
    
    echo "✅ Envoyés    : $sent"
    echo "❌ Rejetés    : $rejected"
    echo "⏱️  Différés   : $deferred"
    echo "🔙 Rebondis   : $bounced"
}
```

---

## 🎯 Debug : Voir TOUT ce qui se passe pour une IP

```bash
# Fonction pour débugger une IP spécifique
debug_ip() {
    if [ -z "$1" ]; then
        echo "Usage: debug_ip <ip_address>"
        return 1
    fi
    echo "🔍 Toute l'activité de $1:"
    sudo tail -f /var/log/mail.log | grep --color=auto "$1"
}
```

---

**Utilisation** :

```bash
# Suivre en temps réel une IP
debug_ip 192.168.1.100
```

---

## 🔥 One-liner magique : Identifier les spammeurs

```bash
# Top des IPs qui ont le plus de rejets
sudo grep 'reject:' /var/log/mail.log | \
  grep -oP '\[\d+\.\d+\.\d+\.\d+\]' | \
  sort | uniq -c | sort -rn | head -20 | \
  while read count ip; do
    echo "🚫 $count rejets depuis $ip"
  done
```

---

## 🎯 Fonction : Suivre un message de bout en bout

```bash
# Fonction ultime pour tracer un message
track_message() {
    if [ -z "$1" ]; then
        echo "Usage: track_message <queue_id>"
        return 1
    fi
    
    echo "🔍 Parcours complet du message $1:"
    echo ""
    sudo grep "$1" /var/log/mail.log | while read line; do
        # Extraire les infos importantes
        timestamp=$(echo "$line" | awk '{print $1, $2, $3}')
        service=$(echo "$line" | grep -oP 'postfix/\w+')
        info=$(echo "$line" | cut -d':' -f4-)
        
        echo "⏰ $timestamp"
        echo "   📦 $service"
        echo "   ℹ️  $info"
        echo ""
    done
}
```

---

## 🚀 Checker rapide : Le serveur va bien ?

```bash
# Ajouter dans ~/.bashrc
postfix_health() {
    echo "🏥 État de santé Postfix:"
    echo ""
    
    # Service actif ?
    if systemctl is-active --quiet postfix; then
        echo "✅ Service: ACTIF"
    else
        echo "❌ Service: INACTIF"
    fi
    
    # Taille de la queue
    local queue_size=$(sudo postqueue -p | tail -1 | awk '{print $5}')
    echo "📬 Queue: $queue_size messages"
    
    # Activité récente (5 min)
    local recent=$(sudo grep "$(date -d '5 minutes ago' '+%b %e %H:')" /var/log/mail.log 2>/dev/null | wc -l)
    echo "📊 Activité (5min): $recent lignes de log"
    
    # Erreurs récentes
    local errors=$(sudo grep "$(date -d '5 minutes ago' '+%b %e %H:')" /var/log/mail.log 2>/dev/null | grep -ic "error")
    if [ $errors -gt 0 ]; then
        echo "⚠️  Erreurs (5min): $errors"
    else
        echo "✅ Erreurs (5min): 0"
    fi
}
```

---

## 💡 Astuce : Colorer les logs pour mieux voir

```bash
# Fonction pour colorer les logs
colorlog() {
    sudo tail -f /var/log/mail.log | \
      sed -e 's/\(status=sent\)/\o033[32m\1\o033[0m/' \
          -e 's/\(reject:\)/\o033[31m\1\o033[0m/' \
          -e 's/\(status=deferred\)/\o033[33m\1\o033[0m/' \
          -e 's/\(error\|warning\|fatal\)/\o033[31;1m\1\o033[0m/'
}
```

---

**Résultat** :
- `status=sent` en vert ✅
- `reject:` en rouge ❌
- `status=deferred` en jaune ⚠️
- `error/warning/fatal` en rouge gras 🔥

---

## 🎯 Script complet : Le couteau suisse du debug

```bash
#!/bin/bash
# postfix-debug.sh - Outil ultime de debug Postfix

case "$1" in
    queue)
        echo "📬 État de la queue:"
        sudo postqueue -p
        ;;
    live)
        echo "📊 Logs en temps réel (Ctrl+C pour quitter):"
        sudo tail -f /var/log/mail.log
        ;;
    errors)
        echo "❌ Erreurs récentes:"
        sudo grep -E "error|warning|fatal" /var/log/mail.log | tail -20
        ;;
```

---

```bash
    rejects)
        echo "🚫 Rejets récents:"
        sudo grep "reject:" /var/log/mail.log | tail -20
        ;;
    stats)
        echo "📊 Statistiques du jour:"
        sudo pflogsumm -d today /var/log/mail.log 2>/dev/null || \
          echo "pflogsumm non installé. Installez avec: sudo apt install pflogsumm"
        ;;
    health)
        echo "🏥 Santé du serveur:"
        postfix_health
        ;;
    trace)
        if [ -z "$2" ]; then
            echo "Usage: $0 trace <email|queue_id>"
            exit 1
        fi
        echo "🔍 Recherche de: $2"
        sudo grep -i "$2" /var/log/mail.log
        ;;
```

---

```bash
    *)
        echo "🛠️  Postfix Debug Tool"
        echo ""
        echo "Usage: $0 {queue|live|errors|rejects|stats|health|trace <id>}"
        echo ""
        echo "Commandes:"
        echo "  queue    - Afficher la queue"
        echo "  live     - Logs en temps réel"
        echo "  errors   - Voir les erreurs"
        echo "  rejects  - Voir les rejets"
        echo "  stats    - Statistiques du jour"
        echo "  health   - État de santé"
        echo "  trace    - Tracer un email/queue_id"
        ;;
esac
```

---

**Installation du script** :

```bash
# Télécharger ou créer le script
sudo nano /usr/local/bin/postfix-debug.sh

# Rendre exécutable
sudo chmod +x /usr/local/bin/postfix-debug.sh

# Créer un alias
echo "alias pfdebug='/usr/local/bin/postfix-debug.sh'" >> ~/.bashrc
source ~/.bashrc
```

---

**Utilisation** :

```bash
pfdebug queue      # Voir la queue
pfdebug live       # Logs temps réel
pfdebug errors     # Voir erreurs
pfdebug stats      # Statistiques
pfdebug health     # Santé du serveur
pfdebug trace ABC123  # Tracer un message
```

---

## 📝 Fichier de config : Postfix en mode debug max

Si vous avez un problème complexe, activez temporairement le debug complet :

```bash
# Dans main.cf
debug_peer_list = 0.0.0.0/0
debug_peer_level = 3
```

---

⚠️ **ATTENTION** : Mode très verbeux ! Les logs vont grossir rapidement.

**Remettre normal après debug** :

```bash
# Dans main.cf
#debug_peer_list = 0.0.0.0/0
#debug_peer_level = 3
```

---

## 🎓 Récapitulatif des outils de debug

**Outils de base** :
- `mailq` : Voir la queue
- `postcat -q QUEUEID` : Voir un message
- `postqueue -f` : Forcer l'envoi de la queue
- `postsuper -d QUEUEID` : Supprimer un message

---

**Recherche dans les logs** :
- `grep QUEUEID /var/log/mail.log` : Tracer un message
- `grep "reject:" /var/log/mail.log` : Voir les rejets
- `grep "status=deferred" /var/log/mail.log` : Voir les différés

---

**Outils avancés** :
- `pflogsumm` : Statistiques détaillées
- `postfix-debug.sh` : Script tout-en-un
- Fonctions bash personnalisées : gain de temps massif

---

## 💡 Conseil pro final

Créez un fichier `/root/postfix-cheatsheet.txt` avec vos commandes favorites :

```bash
# Voir les rejets du jour
grep "$(date +%b\ %e)" /var/log/mail.log | grep "reject:"

# Queue vide ?
postqueue -p | tail -1

# Dernière erreur
grep -E "error|fatal" /var/log/mail.log | tail -1

# Top IP rejetées
grep 'reject:' /var/log/mail.log | grep -oP '\[\d+\.\d+\.\d+\.\d+\]' | sort | uniq -c | sort -rn | head -10
```

---

# Exercices pratiques

## 🎯 Exercice 1 : Suivre un message

1. Envoyez un email de test
2. Notez son Queue ID dans les logs
3. Tracez son parcours complet avec `grep`

---

## 🎯 Exercice 2 : Statistiques

1. Comptez les emails envoyés aujourd'hui
2. Comptez les emails rejetés
3. Identifiez les 5 domaines destinataires les plus fréquents

---

## 🎯 Exercice 3 : pflogsumm

1. Installez pflogsumm
2. Générez un rapport du jour
3. Analysez les résultats

---

## 🎯 Exercice 4 : Script de monitoring

1. Créez un script qui affiche :
   - État de Postfix
   - Taille de la queue
   - Nombre d'emails envoyés dans la dernière heure
2. Automatisez-le avec cron

---

## 🎯 Exercice 5 : Alertes

1. Créez un script qui alerte si la queue dépasse 100 messages
2. Testez-le en créant des messages en deferred
3. Vérifiez la réception de l'alerte

---

# Points clés à retenir

## 💡 Logs

**Emplacement** :
- Ubuntu/Debian : `/var/log/mail.log`
- Rocky Linux : `/var/log/maillog`

**Queue ID** : Identifiant unique pour tracer un message

---

## 💡 Commandes essentielles

```bash
# Suivre en temps réel
sudo tail -f /var/log/mail.log

# Rechercher
sudo grep QUEUE_ID /var/log/mail.log

# Statistiques
pflogsumm /var/log/mail.log
```

---

## 💡 Codes de statut

**2.x.x** : Succès

**4.x.x** : Erreur temporaire (deferred)

**5.x.x** : Erreur permanente (bounce)

---

## 💡 Surveillance

**Automatiser** : Scripts + cron

**Alerter** : Mail si problème

**Analyser** : pflogsumm, Logwatch

**Visualiser** : Prometheus + Grafana

---

## Prochaine étape

Vous savez maintenant surveiller votre serveur ! Passons à la **sauvegarde et restauration** pour sécuriser vos données ! 💾

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Module suivant : Sauvegarde et restauration <carbon:arrow-right class="inline"/>
  </span>
</div>

