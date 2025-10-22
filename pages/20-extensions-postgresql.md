---
layout: new-section
routeAlias: 'extensions-postgresql'
---

<a name="extensions-postgresql" id="extensions-postgresql"></a>

# Extensions PostgreSQL 🧩

Étendre les fonctionnalités

---

# Gérer les extensions 📦

**Extensions = Des super-pouvoirs pour PostgreSQL ! 🦸**

*Analogie* : C'est comme installer des plugins sur WordPress ou des extensions sur Chrome !

**PostgreSQL de base** = Déjà très puissant ✅
**+ Extensions** = ULTRA-PUISSANT ! 🚀

**Exemples de super-pouvoirs** :
- 📍 **PostGIS** : Géolocalisation (trouver le restaurant le plus proche)
- 🔑 **uuid-ossp** : Générer des identifiants uniques universels
- 📊 **pg_stat_statements** : Voir les requêtes les plus lentes
- 🔍 **pg_trgm** : Recherche floue ("Postgre" trouve "PostgreSQL")

---

**Voir les extensions disponibles** 👀 :
```sql
SELECT * FROM pg_available_extensions;
```
💡 Vous verrez des dizaines d'extensions possibles !

---

**Installer une extension** ⬇️ :
```sql
CREATE EXTENSION "uuid-ossp";
```
💡 C'est instantané, comme installer une app !

---

**Voir les extensions installées** 📋 :
```sql
\dx
```
💡 Liste toutes vos extensions actives

---

**Supprimer une extension** 🗑️ :
```sql
DROP EXTENSION "uuid-ossp";
```

⚠️ **Astuce** : Installez les extensions dans la base où vous en avez besoin, pas dans toutes !

---

# uuid-ossp : UUID 🔑

```sql
CREATE EXTENSION "uuid-ossp";

-- Générer UUID
SELECT uuid_generate_v4();

-- Table avec UUID
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email VARCHAR(255)
);
```

---

# pg_trgm : Recherche floue 🔍

```sql
CREATE EXTENSION pg_trgm;

-- Similarité
SELECT similarity('hello', 'helo');  -- 0.5

-- Recherche floue
SELECT * FROM produits
WHERE nom % 'laptop';  -- Trouve "Laptop", "Lptop", etc.

-- Index
CREATE INDEX idx_nom_trgm ON produits USING GIN (nom gin_trgm_ops);
```

---

# hstore : Clé-valeur 🗝️

```sql
CREATE EXTENSION hstore;

-- Colonne hstore
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    attributs HSTORE
);

-- Insérer
INSERT INTO produits (attributs) VALUES
    ('couleur => rouge, taille => XL');

-- Requêter
SELECT * FROM produits WHERE attributs->'couleur' = 'rouge';
```

---

# PostGIS : Données géographiques 🗺️

```sql
CREATE EXTENSION postgis;

-- Table avec géométrie
CREATE TABLE lieux (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    position GEOGRAPHY(POINT, 4326)
);

-- Distance
SELECT ST_Distance(
    ST_MakePoint(2.35, 48.86),  -- Paris
    ST_MakePoint(-0.13, 51.51)   -- London
);
```

---

# pg_stat_statements : Statistiques 📊

```sql
CREATE EXTENSION pg_stat_statements;

-- Top requêtes
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

# Questions ? 🙋

**À suivre** : Réplication et haute disponibilité

