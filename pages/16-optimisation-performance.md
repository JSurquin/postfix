---
layout: new-section
routeAlias: 'optimisation-performance'
---

<a name="optimisation-performance" id="optimisation-performance"></a>

# Optimisation et Performance 🚀

Rendre PostgreSQL ultra-rapide

---

# Plan du module 📋

- EXPLAIN et ANALYZE
- Index appropriés
- Statistiques et VACUUM
- Configuration PostgreSQL
- Requêtes optimisées
- Partitionnement
- Connection pooling

---

# EXPLAIN : Analyser les requêtes 📊

**EXPLAIN = Le GPS de vos requêtes SQL ! 🗺️**

*Analogie* : Votre requête est LENTE ? EXPLAIN vous dit POURQUOI !

C'est comme Google Maps qui vous montre :
- Le chemin que vous allez prendre
- Combien de temps ça va prendre
- S'il y a des bouchons

**Les 2 commandes essentielles** :

**EXPLAIN** = La prévision 🔮
```sql
EXPLAIN SELECT * FROM produits WHERE prix > 100;
```
💡 PostgreSQL dit : "Voici ce que JE PRÉVOIS de faire"

**EXPLAIN ANALYZE** = La réalité ✅
```sql
EXPLAIN ANALYZE SELECT * FROM produits WHERE prix > 100;
```
💡 PostgreSQL dit : "Voici ce que j'AI VRAIMENT fait + le temps réel"

---

**Ce que vous verrez** 👀 :
```
Seq Scan on produits  (cost=0.00..431.00 rows=100 width=32)
  Filter: (prix > 100)
```

🐌 **Seq Scan** = Il lit TOUTE la table ligne par ligne (LENT !)
- Comme chercher un mot dans un livre en lisant page par page

```
Index Scan using idx_prix on produits  (cost=0.42..8.44 rows=100)
  Index Cond: (prix > 100)
```

⚡ **Index Scan** = Il utilise un index (RAPIDE !)
- Comme utiliser l'index d'un livre pour aller direct à la bonne page

💡 **Règle d'or** : TOUJOURS utiliser EXPLAIN ANALYZE si votre requête est lente !

---

# Lire EXPLAIN 👀

```
Seq Scan on produits  (cost=0.00..431.00 rows=100 width=32)
  Filter: (prix > 100)
  
Index Scan using idx_prix on produits  (cost=0.42..8.44 rows=100 width=32)
  Index Cond: (prix > 100)
```

**cost** : Estimation du coût  
**rows** : Nombre de lignes estimé  
**width** : Taille moyenne d'une ligne

---

# Types de scan 🔍

```sql
-- Sequential Scan : Balaye toute la table
Seq Scan on produits

-- Index Scan : Utilise un index
Index Scan using idx_produits_prix

-- Index Only Scan : Tout dans l'index
Index Only Scan using idx_covering

-- Bitmap Index Scan : Combine plusieurs index
Bitmap Index Scan on idx1, idx2
```

---

# Statistiques avec ANALYZE 📈

```sql
-- Mettre à jour les statistiques
ANALYZE produits;

-- Toutes les tables
ANALYZE;

-- Verbose
ANALYZE VERBOSE produits;

-- Automatique avec autovacuum (défaut)
```

---

# VACUUM : Nettoyage 🧹

```sql
-- VACUUM simple : Libère l'espace
VACUUM produits;

-- VACUUM FULL : Réorganise complètement (bloque la table)
VACUUM FULL produits;

-- VACUUM ANALYZE : Nettoie + met à jour stats
VACUUM ANALYZE produits;

-- Automatique : autovacuum (activé par défaut)
```

---

# Configuration PostgreSQL ⚙️

**postgresql.conf - Mémoire**

```bash
# Mémoire partagée (25% de la RAM)
shared_buffers = 2GB

# Cache système estimé (75% de la RAM)
effective_cache_size = 6GB

# Mémoire par opération de tri
work_mem = 50MB

# Mémoire maintenance (1-2 GB)
maintenance_work_mem = 1GB
```

---

**Checkpoints et WAL**

```bash
# Taille max WAL avant checkpoint
max_wal_size = 2GB
min_wal_size = 1GB

# Checkpoint timeout
checkpoint_completion_target = 0.9

# WAL buffers
wal_buffers = 16MB
```

---

**Parallélisme**

```bash
# Nombre de workers parallèles
max_worker_processes = 8
max_parallel_workers = 8
max_parallel_workers_per_gather = 4

# Coût pour activer parallélisme
parallel_tuple_cost = 0.1
parallel_setup_cost = 1000
```

---

**Pour SSD**

```bash
# SSD : accès aléatoire rapide
random_page_cost = 1.1  # Au lieu de 4.0

# I/O concurrents
effective_io_concurrency = 200
```

---

# Index stratégiques 📑

**Index composites : ordre des colonnes**

```sql
-- Si requête WHERE a = X AND b = Y
CREATE INDEX idx_a_b ON table(a, b);  -- ✅

-- Si requête WHERE b = Y (seul)
CREATE INDEX idx_b ON table(b);  -- Nécessaire en plus
```

---

**Index partiels**

```sql
-- Indexer uniquement ce qui est utilisé
CREATE INDEX idx_actifs 
ON produits(nom) 
WHERE actif = true;

-- 10x plus petit qu'un index complet !
```

---

**Index covering**

```sql
-- Index contenant toutes les colonnes nécessaires
CREATE INDEX idx_covering 
ON produits(categorie, prix, nom);

-- Requête servie uniquement par l'index
SELECT nom, prix 
FROM produits 
WHERE categorie = 'Informatique';
-- Index Only Scan !
```

---

# Optimiser les requêtes 🎯

**Éviter SELECT ***

```sql
-- ❌ MAUVAIS
SELECT * FROM produits WHERE id = 10;

-- ✅ BON
SELECT id, nom, prix FROM produits WHERE id = 10;
```

---

**LIMIT pour pagination**

```sql
-- Pagination efficace
SELECT * FROM articles
ORDER BY date_publication DESC
LIMIT 20 OFFSET 40;

-- Ou avec clé
SELECT * FROM articles
WHERE id > last_seen_id
ORDER BY id
LIMIT 20;
```

---

**EXISTS vs IN**

```sql
-- ✅ MIEUX : EXISTS
SELECT * FROM clients c
WHERE EXISTS (
    SELECT 1 FROM commandes cmd 
    WHERE cmd.client_id = c.id
);

-- ❌ MOINS BON : IN (si beaucoup de résultats)
SELECT * FROM clients
WHERE id IN (SELECT client_id FROM commandes);
```

---

**Éviter les fonctions sur colonnes indexées**

```sql
-- ❌ MAUVAIS : N'utilise pas l'index
SELECT * FROM utilisateurs
WHERE LOWER(email) = 'alice@example.com';

-- ✅ BON : Index d'expression
CREATE INDEX idx_email_lower ON utilisateurs(LOWER(email));
```

---

# Partitionnement de tables 📦

**Pour grandes tables (> 100 GB)**

```sql
-- Partitionnement par plage de dates
CREATE TABLE ventes (
    id BIGSERIAL,
    date_vente DATE NOT NULL,
    montant NUMERIC
) PARTITION BY RANGE (date_vente);

CREATE TABLE ventes_2024_q1 PARTITION OF ventes
    FOR VALUES FROM ('2024-01-01') TO ('2024-04-01');
    
CREATE TABLE ventes_2024_q2 PARTITION OF ventes
    FOR VALUES FROM ('2024-04-01') TO ('2024-07-01');
```

---

**Avantages**

- Requêtes plus rapides (scan moins de données)
- Maintenance facile (DROP partition)
- Archivage simplifié
- Index plus petits

---

# Connection Pooling 🔄

**pgBouncer - Pooler externe**

```ini
# pgbouncer.ini
[databases]
mabase = host=localhost dbname=mabase

[pgbouncer]
listen_addr = *
listen_port = 6432
auth_type = md5
auth_file = /etc/pgbouncer/userlist.txt
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 25
```

---

**Modes de pooling**

- **session** : Connexion gardée jusqu'à déconnexion client
- **transaction** : Connexion rendue après chaque transaction
- **statement** : Connexion rendue après chaque requête

**transaction** est le plus efficace généralement

---

# Monitoring et métriques 📊

**Requêtes lentes**

```sql
-- pg_stat_statements (extension)
CREATE EXTENSION pg_stat_statements;

-- Top requêtes lentes
SELECT 
    query,
    calls,
    mean_exec_time,
    total_exec_time
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
```

---

**Cache hit ratio**

```sql
-- Doit être > 95%
SELECT 
    sum(heap_blks_read) as heap_read,
    sum(heap_blks_hit) as heap_hit,
    sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) * 100 as cache_hit_ratio
FROM pg_statio_user_tables;
```

---

**Index inutilisés**

```sql
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY pg_relation_size(indexrelid) DESC;
```

---

# Bloat (gonflement) 💨

**Identifier le bloat**

```sql
SELECT 
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size,
    n_dead_tup,
    n_live_tup,
    ROUND(n_dead_tup * 100.0 / NULLIF(n_live_tup + n_dead_tup, 0), 2) AS dead_ratio
FROM pg_stat_user_tables
WHERE n_dead_tup > 1000
ORDER BY n_dead_tup DESC;
```

---

**Résoudre le bloat**

```sql
-- VACUUM régulier
VACUUM ANALYZE table_name;

-- Si bloat important
VACUUM FULL table_name;  -- Bloque la table !

-- Ou pg_repack (extension)
pg_repack -d mabase -t table_name
```

---

# Bonnes pratiques 👍

1. **Index appropriés** mais pas trop
2. **ANALYZE** après gros changements
3. **VACUUM** régulier (autovacuum suffit souvent)
4. **Monitoring** continu avec pg_stat_statements
5. **Partitionnement** pour grandes tables
6. **Connection pooling** pour haute concurrence
7. **SSD** pour meilleures performances
8. **Requêtes optimisées** : SELECT colonnes nécessaires

---

# Résumé 📝

✅ EXPLAIN ANALYZE pour comprendre
✅ Index stratégiques et partiels
✅ VACUUM et ANALYZE réguliers
✅ Configuration mémoire appropriée
✅ Partitionnement si > 100 GB
✅ Connection pooling avec pgBouncer
✅ Monitoring avec pg_stat_statements
✅ Optimiser requêtes (EXISTS, LIMIT, colonnes)

---

# Questions ? 🙋

**À suivre** : Sécurité et gestion des utilisateurs

