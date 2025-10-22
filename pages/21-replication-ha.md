---
layout: new-section
routeAlias: 'replication-ha'
---

<a name="replication-ha" id="replication-ha"></a>

# Réplication et HA 🔄

Haute disponibilité et répartition de charge

---

# Types de réplication 📡

**Réplication = Avoir plusieurs copies de votre base ! 📋📋📋**

*Analogie* : C'est comme avoir plusieurs serveurs de jeu Minecraft :
- Serveur principal (Master) = Le vrai serveur
- Serveurs secondaires (Replicas) = Copies qui se synchronisent

**Pourquoi faire ça ?** 🤔

1️⃣ **Haute disponibilité** 🔄
- Si le serveur principal plante → Un replica prend le relais automatiquement !
- *Analogie* : Comme avoir un pneu de secours dans votre voiture

2️⃣ **Performance** ⚡
- Lectures sur les replicas → Le master n'est pas surchargé
- *Exemple* : Site web avec 1 million de visiteurs

3️⃣ **Sauvegardes** 💾
- Faire les backups sur un replica → Pas d'impact sur le master

---

**2 types de réplication** :

**Réplication physique (Streaming)** ⭐ **LA PLUS UTILISÉE**
- 📦 Copie TOUT (fichiers binaires)
- ✅ Ultra-rapide et fiable
- ✅ Répliques exactes à 100%
- ❌ Tout ou rien (impossible de choisir quoi répliquer)

**Réplication logique** 🎯
- 📝 Copie seulement ce que vous voulez (certaines tables)
- ✅ Flexible (choisir les tables)
- ✅ Peut répliquer entre différentes versions de PostgreSQL
- ✅ Peut avoir plusieurs masters (avancé !)
- ❌ Plus complexe à configurer

💡 **Pour débuter** : Utilisez la réplication physique (streaming) !

---

# Streaming Replication : Master 👑

**postgresql.conf (master)**

```bash
wal_level = replica
max_wal_senders = 5
wal_keep_size = 1GB
```

**pg_hba.conf**

```bash
host replication replicator 192.168.1.0/24 scram-sha-256
```

```sql
CREATE USER replicator WITH REPLICATION PASSWORD 'secret';
```

---

# Streaming Replication : Replica 📖

```bash
# Créer réplica
pg_basebackup -h master_host -D /pgdata -U replicator -P --wal-method=stream

# postgresql.conf (replica)
hot_standby = on

# postgresql.auto.conf
primary_conninfo = 'host=master_host port=5432 user=replicator password=secret'
```

---

# Réplication logique 🔗

**Master (publisher)**

```sql
CREATE PUBLICATION my_publication FOR TABLE produits, commandes;
```

**Replica (subscriber)**

```sql
CREATE SUBSCRIPTION my_subscription
CONNECTION 'host=master_host dbname=mabase user=replicator password=secret'
PUBLICATION my_publication;
```

---

# Failover automatique : Patroni 🚨

```yaml
# patroni.yml
scope: postgres-cluster
name: node1

etcd:
  hosts: etcd1:2379,etcd2:2379

postgresql:
  data_dir: /pgdata
  listen: 0.0.0.0:5432
```

---

# pgBouncer : Load Balancing ⚖️

```ini
[databases]
mabase = host=master_host

[pgbouncer]
listen_addr = *
listen_port = 6432
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 25
```

---

# HAProxy : Répartition de charge 🔀

```bash
frontend pgsql_front
    bind *:5000
    default_backend pgsql_back

backend pgsql_back
    balance roundrobin
    option httpchk
    server pg1 192.168.1.10:5432 check
    server pg2 192.168.1.11:5432 check
```

---

# Monitoring haute disponibilité 📊

```sql
-- Lag de réplication
SELECT 
    client_addr,
    state,
    sent_lsn,
    write_lsn,
    flush_lsn,
    replay_lsn,
    pg_wal_lsn_diff(sent_lsn, replay_lsn) AS lag_bytes
FROM pg_stat_replication;
```

---

# Bonnes pratiques 👍

1. **Monitoring** continu du lag
2. **Tests de failover** réguliers
3. **Backups** même avec réplication
4. **3 nœuds minimum** (quorum)
5. **Réseau rapide** entre nœuds
6. **Alertes** sur lag élevé

---

# Questions ? 🙋

**À suivre** : Exercices pratiques

