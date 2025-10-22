---
layout: new-section
routeAlias: 'sauvegarde-restauration'
---

<a name="sauvegarde-restauration" id="sauvegarde-restauration"></a>

# Sauvegarde et Restauration 💾

Protéger vos données

---

# pg_dump : Sauvegarde logique 📦

**Sauvegarder = Faire une copie de secours ! 💾**

*Analogie* : C'est comme sauvegarder vos photos sur Google Photos !
- Si votre téléphone tombe à l'eau → 📱💧
- Vos photos sont SAFE sur le cloud ! ☁️✅

**pg_dump** = L'outil de sauvegarde de PostgreSQL

**Sauvegarde complète** (la plus simple) 📝 :
```bash
pg_dump mabase > backup.sql
```
💡 Crée un fichier SQL avec TOUTES vos données

---

**Format personnalisé** (RECOMMANDÉ ⭐) :
```bash
pg_dump -Fc mabase > backup.dump
```
💡 **Pourquoi ?**
- Plus petit (compressé)
- Restauration sélective possible
- Plus rapide

---

**Compression maximale** 🗜️ :
```bash
pg_dump -Fc -Z9 mabase > backup.dump
```
💡 `-Z9` = Compression niveau 9 (max) → Fichier encore plus petit !

---

**Sauvegarder UNE SEULE table** 🎯 :
```bash
pg_dump -t produits mabase > produits.sql
```
💡 Utile pour exporter seulement une partie

---

**Sauvegarder SEULEMENT la structure** (sans données) 🏗️ :
```bash
pg_dump -s mabase > schema.sql
```
💡 Pratique pour créer une base vide identique

⚠️ **IMPORTANT** : Faites des sauvegardes RÉGULIÈRES !
- Quotidienne = Bien ✅
- Hebdomadaire = Minimum 📅
- Jamais = DANGER ! 💀

---

# pg_dumpall : Toutes les bases 🗂️

```bash
# Toutes les bases + rôles
pg_dumpall > all_databases.sql

# Seulement les rôles
pg_dumpall --roles-only > roles.sql

# Seulement les tablespaces
pg_dumpall --tablespaces-only > tablespaces.sql
```

---

# pg_restore : Restauration 🔄

```bash
# Restaurer
pg_restore -d mabase backup.dump

# Créer la base + restaurer
pg_restore -C -d postgres backup.dump

# Parallèle (4 jobs)
pg_restore -j 4 -d mabase backup.dump

# Table spécifique
pg_restore -t produits -d mabase backup.dump
```

---

# pg_basebackup : Sauvegarde physique 💿

```bash
# Backup complet du cluster
pg_basebackup -D /backup/pgdata -Ft -z -P

# Options
-D : Répertoire destination
-Ft : Format tar
-z : Compression gzip
-P : Progression
```

---

# WAL Archiving : Archivage continu 📜

**postgresql.conf**

```bash
wal_level = replica
archive_mode = on
archive_command = 'cp %p /archives/%f'
max_wal_senders = 3
```

---

# Point-in-Time Recovery (PITR) ⏰

```bash
# 1. Base backup
pg_basebackup -D /backup/base

# 2. Archiver les WAL

# 3. Restaurer à un point précis
# recovery.conf
restore_command = 'cp /archives/%f %p'
recovery_target_time = '2025-01-15 14:30:00'
```

---

# Automatisation avec cron 🤖

```bash
# Crontab : backup quotidien à 2h
0 2 * * * pg_dump -Fc mabase > /backup/mabase_$(date +\%Y\%m\%d).dump

# Rotation : garder 7 jours
find /backup -name "mabase_*.dump" -mtime +7 -delete
```

---

# Bonnes pratiques 👍

1. **Sauvegardes régulières** (quotidiennes minimum)
2. **Tester les restaurations** !
3. **Stockage externe** (S3, etc.)
4. **Chiffrement** des backups
5. **PITR** pour bases critiques
6. **Monitoring** des sauvegardes
7. **Documentation** des procédures

---

# Questions ? 🙋

**À suivre** : JSON et types avancés

