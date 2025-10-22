---
layout: new-section
routeAlias: 'creation-gestion-tables'
---

<a name="creation-gestion-tables" id="creation-gestion-tables"></a>

# Création et gestion de tables 🏗️

Maîtrisons la structure des données avec DDL (Data Definition Language)

---

# Plan du module 📋

- CREATE TABLE
- ALTER TABLE
- DROP TABLE
- Hériter de tables
- Tables temporaires
- Tables partitionnées
- Schémas
- Bonnes pratiques

---

# CREATE TABLE : Créer une table 📊

**CREATE TABLE = Créer un nouveau tableau ! 🏗️**

*Analogie* : C'est comme créer une nouvelle feuille Excel avec des colonnes précises !

**Syntaxe de base** :
```sql
CREATE TABLE nom_table (
    colonne1 type CONTRAINTES,
    colonne2 type CONTRAINTES,
    ...
);
```

💡 **Décomposition** :
- `CREATE TABLE` = "Créer une nouvelle table"
- `nom_table` = Le nom que vous choisissez
- Entre parenthèses = Liste des colonnes avec leurs types

---

**Exemple complet expliqué** 📝 :

```sql
CREATE TABLE utilisateurs (
    id SERIAL PRIMARY KEY,                    -- 1. Numéro auto
    nom VARCHAR(100) NOT NULL,                -- 2. Nom obligatoire
    email VARCHAR(255) UNIQUE NOT NULL,       -- 3. Email unique et obligatoire
    age INTEGER CHECK (age >= 18),            -- 4. Age minimum 18 ans
    date_inscription TIMESTAMP DEFAULT NOW(), -- 5. Date auto
    actif BOOLEAN DEFAULT true                -- 6. Actif par défaut
);
```

**Explications ligne par ligne** 🔍 :

1️⃣ **id SERIAL PRIMARY KEY**
   - Numéro automatique (1, 2, 3...)
   - Identifiant unique de chaque utilisateur

2️⃣ **nom VARCHAR(100) NOT NULL**
   - Texte de max 100 caractères
   - `NOT NULL` = OBLIGATOIRE

3️⃣ **email VARCHAR(255) UNIQUE NOT NULL**
   - `UNIQUE` = Pas de doublon possible
   - Impossible d'avoir 2 comptes avec le même email

4️⃣ **age INTEGER CHECK (age >= 18)**
   - `CHECK` = Validation personnalisée
   - Refuse si age < 18

5️⃣ **date_inscription TIMESTAMP DEFAULT NOW()**
   - `DEFAULT NOW()` = Date actuelle automatique

6️⃣ **actif BOOLEAN DEFAULT true**
   - Par défaut à TRUE (actif)

💡 **Après cette commande** : La table existe mais est VIDE !
- C'est le moule, pas les données
- Comme créer les colonnes d'un tableau Excel

---

**Types de colonnes courants**

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(50) UNIQUE NOT NULL,
    nom VARCHAR(200) NOT NULL,
    description TEXT,
    prix NUMERIC(10,2) NOT NULL CHECK (prix > 0),
    stock INTEGER DEFAULT 0 CHECK (stock >= 0),
    poids DECIMAL(8,3),  -- kg
    dimensions JSONB,
    tags TEXT[],
    date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    date_maj TIMESTAMP
);
```

---

# Clés primaires 🔑

**Clé primaire simple**

```sql
CREATE TABLE clients (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100)
);

-- Équivalent à
CREATE TABLE clients (
    id SERIAL,
    nom VARCHAR(100),
    PRIMARY KEY (id)
);
```

---

**Clé primaire composite**

```sql
CREATE TABLE inscriptions (
    etudiant_id INTEGER,
    cours_id INTEGER,
    date_inscription DATE DEFAULT CURRENT_DATE,
    PRIMARY KEY (etudiant_id, cours_id)
);
```

---

**Clé primaire UUID**

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE sessions (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id INTEGER,
    token VARCHAR(255),
    date_creation TIMESTAMP DEFAULT NOW()
);
```

---

# Clés étrangères 🔗

**Référence simple**

```sql
CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    client_id INTEGER REFERENCES clients(id),
    date_commande TIMESTAMP DEFAULT NOW(),
    total NUMERIC(10,2)
);
```

---

**Avec actions ON DELETE / ON UPDATE**

```sql
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    auteur_id INTEGER REFERENCES auteurs(id) ON DELETE CASCADE,
    categorie_id INTEGER REFERENCES categories(id) ON DELETE SET NULL,
    titre VARCHAR(200)
);
```

---

**Actions disponibles**

- **CASCADE** : Supprime/modifie en cascade
- **SET NULL** : Met à NULL
- **SET DEFAULT** : Met la valeur par défaut
- **RESTRICT** : Empêche la suppression (défaut)
- **NO ACTION** : Comme RESTRICT mais vérifie après

```sql
CREATE TABLE lignes_commande (
    id SERIAL PRIMARY KEY,
    commande_id INTEGER REFERENCES commandes(id) ON DELETE CASCADE,
    produit_id INTEGER REFERENCES produits(id) ON DELETE RESTRICT
);
```

---

# IF NOT EXISTS : Éviter les erreurs 🛡️

```sql
-- Crée la table seulement si elle n'existe pas
CREATE TABLE IF NOT EXISTS utilisateurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100)
);

-- Évite l'erreur "table already exists"
```

---

# CREATE TABLE ... LIKE 📋

**Copier la structure d'une table**

```sql
-- Copie uniquement la structure (pas les données)
CREATE TABLE utilisateurs_backup (LIKE utilisateurs);

-- Avec contraintes et index
CREATE TABLE utilisateurs_backup (
    LIKE utilisateurs INCLUDING ALL
);
```

---

**Options LIKE**

```sql
-- Copier seulement certains éléments
CREATE TABLE ma_copie (
    LIKE table_origine 
    INCLUDING DEFAULTS
    INCLUDING CONSTRAINTS
    INCLUDING INDEXES
);
```

---

# CREATE TABLE ... AS 🔄

**Créer une table depuis une requête**

```sql
-- Créer et remplir en une fois
CREATE TABLE produits_chers AS
SELECT * FROM produits WHERE prix > 100;

-- Avec calculs
CREATE TABLE stats_mensuelles AS
SELECT 
    DATE_TRUNC('month', date_vente) AS mois,
    COUNT(*) AS nb_ventes,
    SUM(montant) AS total
FROM ventes
GROUP BY DATE_TRUNC('month', date_vente);
```

---

**Structure uniquement (sans données)**

```sql
-- Créer la structure mais aucune ligne
CREATE TABLE produits_vide AS
SELECT * FROM produits
WHERE false;
```

---

# ALTER TABLE : Modifier une table 🔧

**Ajouter une colonne**

```sql
-- Colonne simple
ALTER TABLE utilisateurs
ADD COLUMN telephone VARCHAR(20);

-- Avec valeur par défaut
ALTER TABLE utilisateurs
ADD COLUMN pays VARCHAR(100) DEFAULT 'France';

-- Colonne NOT NULL avec défaut
ALTER TABLE produits
ADD COLUMN actif BOOLEAN NOT NULL DEFAULT true;
```

---

**Supprimer une colonne**

```sql
-- Supprimer une colonne
ALTER TABLE utilisateurs
DROP COLUMN telephone;

-- Avec CASCADE (supprime aussi les dépendances)
ALTER TABLE produits
DROP COLUMN categorie_id CASCADE;
```

---

**Renommer une colonne**

```sql
ALTER TABLE utilisateurs
RENAME COLUMN nom TO nom_complet;
```

---

**Modifier le type d'une colonne**

```sql
-- Changer le type
ALTER TABLE produits
ALTER COLUMN prix TYPE NUMERIC(12,2);

-- Avec conversion USING
ALTER TABLE utilisateurs
ALTER COLUMN age TYPE VARCHAR(10) USING age::VARCHAR;

-- Augmenter la taille
ALTER TABLE utilisateurs
ALTER COLUMN nom TYPE VARCHAR(200);
```

---

**Modifier la valeur par défaut**

```sql
-- Ajouter un défaut
ALTER TABLE produits
ALTER COLUMN stock SET DEFAULT 0;

-- Supprimer le défaut
ALTER TABLE produits
ALTER COLUMN stock DROP DEFAULT;
```

---

**Modifier NOT NULL**

```sql
-- Ajouter NOT NULL
ALTER TABLE utilisateurs
ALTER COLUMN email SET NOT NULL;

-- Supprimer NOT NULL
ALTER TABLE utilisateurs
ALTER COLUMN telephone DROP NOT NULL;
```

---

**Ajouter des contraintes**

```sql
-- PRIMARY KEY
ALTER TABLE produits
ADD PRIMARY KEY (id);

-- UNIQUE
ALTER TABLE utilisateurs
ADD CONSTRAINT uk_email UNIQUE (email);

-- CHECK
ALTER TABLE produits
ADD CONSTRAINT chk_prix CHECK (prix > 0);

-- FOREIGN KEY
ALTER TABLE commandes
ADD CONSTRAINT fk_client 
FOREIGN KEY (client_id) REFERENCES clients(id);
```

---

**Supprimer des contraintes**

```sql
-- Supprimer une contrainte par nom
ALTER TABLE utilisateurs
DROP CONSTRAINT uk_email;

-- Supprimer PRIMARY KEY
ALTER TABLE produits
DROP CONSTRAINT produits_pkey;
```

---

**Renommer une table**

```sql
ALTER TABLE utilisateurs
RENAME TO users;
```

---

**Changer le schéma d'une table**

```sql
ALTER TABLE public.produits
SET SCHEMA ventes;
```

---

# DROP TABLE : Supprimer une table 🗑️

**Suppression simple**

```sql
-- Supprimer une table
DROP TABLE produits;

-- Si elle existe
DROP TABLE IF EXISTS produits;
```

---

**Suppression avec CASCADE**

```sql
-- Supprime aussi les dépendances (vues, FK, etc.)
DROP TABLE clients CASCADE;

-- Empêche la suppression si dépendances (défaut)
DROP TABLE clients RESTRICT;
```

---

**Supprimer plusieurs tables**

```sql
DROP TABLE IF EXISTS 
    table1, 
    table2, 
    table3 
CASCADE;
```

---

# Schémas : Organiser les tables 📂

**Créer un schéma**

```sql
-- Créer un schéma
CREATE SCHEMA ventes;

-- Avec autorisation
CREATE SCHEMA ventes AUTHORIZATION user_ventes;

-- Si n'existe pas
CREATE SCHEMA IF NOT EXISTS ventes;
```

---

**Utiliser des schémas**

```sql
-- Créer une table dans un schéma
CREATE TABLE ventes.produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(200)
);

-- Requête sur un schéma
SELECT * FROM ventes.produits;

-- Définir le schéma par défaut
SET search_path TO ventes, public;

-- Maintenant on peut omettre le préfixe
SELECT * FROM produits;  -- Cherche dans ventes puis public
```

---

**Lister les schémas**

```sql
-- Tous les schémas
SELECT schema_name 
FROM information_schema.schemata;

-- Dans psql
\dn
```

---

**Supprimer un schéma**

```sql
-- Schéma vide uniquement
DROP SCHEMA ventes;

-- Avec tout son contenu
DROP SCHEMA ventes CASCADE;
```

---

# Tables temporaires 🕐

**Table temporaire de session**

```sql
-- Existe uniquement pendant la session
CREATE TEMP TABLE temp_calculs (
    id SERIAL,
    valeur NUMERIC(10,2)
);

-- Automatiquement supprimée à la fin de la session
```

---

**Table temporaire de transaction**

```sql
CREATE TEMP TABLE temp_data (
    id INTEGER
) ON COMMIT DROP;  -- Supprimée à la fin de la transaction

-- Autres options
ON COMMIT DELETE ROWS;  -- Vide mais conservée
ON COMMIT PRESERVE ROWS;  -- Conservée avec données (défaut)
```

---

**Cas d'usage**

```sql
-- Import et nettoyage de données
CREATE TEMP TABLE import_brut (
    ligne TEXT
);

COPY import_brut FROM '/tmp/data.txt';

-- Nettoyer et insérer dans la table finale
INSERT INTO donnees_finales
SELECT process(ligne) FROM import_brut WHERE ligne IS NOT NULL;

DROP TABLE import_brut;
```

---

# Tables non journalisées (UNLOGGED) ⚡

**Plus rapides mais pas durables**

```sql
CREATE UNLOGGED TABLE cache (
    key VARCHAR(255) PRIMARY KEY,
    value TEXT,
    expires_at TIMESTAMP
);
```

**Avantages** : Très rapide (pas de WAL)  
**Inconvénients** : Données perdues en cas de crash

---

**Cas d'usage**

- Cache applicatif
- Tables temporaires de calcul
- Données facilement régénérables
- Sessions web

**⚠️ Ne JAMAIS utiliser pour des données critiques !**

---

# Tables partitionnées 📊

**Partitionnement par plage (RANGE)**

```sql
-- Table parente
CREATE TABLE ventes (
    id SERIAL,
    date_vente DATE NOT NULL,
    montant NUMERIC(10,2)
) PARTITION BY RANGE (date_vente);
```

---

```sql
-- Partitions filles
CREATE TABLE ventes_2024_q1 PARTITION OF ventes
    FOR VALUES FROM ('2024-01-01') TO ('2024-04-01');

CREATE TABLE ventes_2024_q2 PARTITION OF ventes
    FOR VALUES FROM ('2024-04-01') TO ('2024-07-01');

CREATE TABLE ventes_2024_q3 PARTITION OF ventes
    FOR VALUES FROM ('2024-07-01') TO ('2024-10-01');
```

---

**Partitionnement par liste (LIST)**

```sql
CREATE TABLE commandes (
    id SERIAL,
    pays VARCHAR(50) NOT NULL,
    montant NUMERIC(10,2)
) PARTITION BY LIST (pays);

CREATE TABLE commandes_france PARTITION OF commandes
    FOR VALUES IN ('France', 'FR');

CREATE TABLE commandes_uk PARTITION OF commandes
    FOR VALUES IN ('UK', 'United Kingdom');

CREATE TABLE commandes_autres PARTITION OF commandes
    DEFAULT;
```

---

**Partitionnement par hachage (HASH)**

```sql
CREATE TABLE logs (
    id BIGSERIAL,
    message TEXT,
    user_id INTEGER
) PARTITION BY HASH (user_id);

CREATE TABLE logs_0 PARTITION OF logs
    FOR VALUES WITH (MODULUS 4, REMAINDER 0);

CREATE TABLE logs_1 PARTITION OF logs
    FOR VALUES WITH (MODULUS 4, REMAINDER 1);

-- etc. jusqu'à remainder 3
```

---

**Avantages du partitionnement**

- **Performance** : Requêtes plus rapides sur grandes tables
- **Maintenance** : Supprimer facilement des anciennes données
- **Parallélisme** : Requêtes parallélisées sur partitions
- **Organisation** : Données logiquement séparées

---

# Héritage de tables (ancien système) 👨‍👦

**Créer une table héritée**

```sql
CREATE TABLE vehicules (
    id SERIAL PRIMARY KEY,
    marque VARCHAR(50),
    modele VARCHAR(50)
);

CREATE TABLE voitures (
    nb_portes INTEGER
) INHERITS (vehicules);

CREATE TABLE motos (
    cylindree INTEGER
) INHERITS (vehicules);
```

---

**Requêtes sur héritage**

```sql
-- Tous les véhicules (voitures + motos)
SELECT * FROM vehicules;

-- Seulement la table parent (sans enfants)
SELECT * FROM ONLY vehicules;

-- Seulement les voitures
SELECT * FROM voitures;
```

**⚠️ Héritage déprécié, préférez le partitionnement !**

---

# COMMENT : Documenter 📝

**Commenter une table**

```sql
COMMENT ON TABLE utilisateurs IS 
'Table des utilisateurs de l''application. 
Contient les informations personnelles et d''authentification.';
```

---

**Commenter des colonnes**

```sql
COMMENT ON COLUMN utilisateurs.email IS 
'Adresse email unique, utilisée pour la connexion';

COMMENT ON COLUMN utilisateurs.actif IS 
'false si le compte est suspendu ou supprimé';
```

---

**Voir les commentaires**

```sql
-- Dans psql
\d+ utilisateurs

-- Ou requête
SELECT 
    col_description('utilisateurs'::regclass, attnum) as column_comment,
    attname as column_name
FROM pg_attribute
WHERE attrelid = 'utilisateurs'::regclass
  AND attnum > 0;
```

---

# Informations système 📊

**Lister les tables**

```sql
-- Toutes les tables du schéma public
SELECT tablename 
FROM pg_tables 
WHERE schemaname = 'public';

-- Dans psql
\dt

-- Avec détails
\dt+
```

---

**Informations sur une table**

```sql
-- Description complète
\d nom_table
\d+ nom_table

-- Colonnes
SELECT column_name, data_type, character_maximum_length
FROM information_schema.columns
WHERE table_name = 'utilisateurs';
```

---

**Taille des tables**

```sql
-- Taille d'une table
SELECT pg_size_pretty(pg_total_relation_size('utilisateurs'));

-- Toutes les tables triées par taille
SELECT 
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;
```

---

# Exercice pratique 🎯

**Créer un système de blog complet**

1. Tables : `auteurs`, `categories`, `articles`, `commentaires`, `tags`, `articles_tags`
2. Utiliser schémas pour organiser
3. Ajouter contraintes et clés étrangères
4. Partitionner `articles` par année
5. Documenter avec COMMENT

---

**Solution - Partie 1**

```sql
-- Créer les schémas
CREATE SCHEMA IF NOT EXISTS blog;
CREATE SCHEMA IF NOT EXISTS stats;

SET search_path TO blog, public;
```

---

**Solution - Partie 2**

```sql
-- Table auteurs
CREATE TABLE auteurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    bio TEXT,
    date_inscription TIMESTAMP DEFAULT NOW()
);

COMMENT ON TABLE auteurs IS 'Auteurs du blog';
COMMENT ON COLUMN auteurs.bio IS 'Biographie courte de l''auteur';
```

---

```sql
-- Table catégories
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) UNIQUE NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT
);
```

---

```sql
-- Table articles partitionnée par année
CREATE TABLE articles (
    id SERIAL,
    auteur_id INTEGER NOT NULL REFERENCES auteurs(id) ON DELETE CASCADE,
    categorie_id INTEGER REFERENCES categories(id) ON DELETE SET NULL,
    titre VARCHAR(300) NOT NULL,
    slug VARCHAR(300) NOT NULL,
    contenu TEXT NOT NULL,
    publie BOOLEAN DEFAULT false,
    date_publication TIMESTAMP NOT NULL,
    vues INTEGER DEFAULT 0,
    PRIMARY KEY (id, date_publication)
) PARTITION BY RANGE (date_publication);
```

---

```sql
-- Partitions pour articles
CREATE TABLE articles_2024 PARTITION OF articles
    FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

CREATE TABLE articles_2025 PARTITION OF articles
    FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');

-- Index sur les partitions
CREATE INDEX idx_articles_2024_auteur ON articles_2024(auteur_id);
CREATE INDEX idx_articles_2025_auteur ON articles_2025(auteur_id);
```

---

```sql
-- Table commentaires
CREATE TABLE commentaires (
    id SERIAL PRIMARY KEY,
    article_id INTEGER NOT NULL,
    parent_id INTEGER REFERENCES commentaires(id) ON DELETE CASCADE,
    auteur_nom VARCHAR(100) NOT NULL,
    auteur_email VARCHAR(255),
    contenu TEXT NOT NULL,
    approuve BOOLEAN DEFAULT false,
    date_commentaire TIMESTAMP DEFAULT NOW(),
    CONSTRAINT fk_article FOREIGN KEY (article_id, date_publication) 
        REFERENCES articles(id, date_publication) ON DELETE CASCADE
);
```

---

```sql
-- Table tags
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(50) UNIQUE NOT NULL,
    slug VARCHAR(50) UNIQUE NOT NULL
);

-- Table de liaison many-to-many
CREATE TABLE articles_tags (
    article_id INTEGER,
    tag_id INTEGER REFERENCES tags(id) ON DELETE CASCADE,
    PRIMARY KEY (article_id, tag_id)
);
```

---

```sql
-- Table de stats (dans schéma stats)
CREATE TABLE stats.vues_quotidiennes (
    date DATE PRIMARY KEY,
    article_id INTEGER NOT NULL,
    nombre_vues INTEGER DEFAULT 0
);
```

---

# Bonnes pratiques 👍

**1. Nommer clairement les objets**

```sql
-- ✅ BON : Noms explicites
CREATE TABLE commandes_clients (...);
ALTER TABLE produits ADD CONSTRAINT chk_prix_positif CHECK (prix > 0);

-- ❌ MAUVAIS : Noms vagues
CREATE TABLE data (...);
ALTER TABLE t ADD CONSTRAINT c1 CHECK (p > 0);
```

---

**2. Toujours utiliser IF EXISTS / IF NOT EXISTS**

```sql
-- ✅ BON : Scripts idempotents
CREATE TABLE IF NOT EXISTS utilisateurs (...);
DROP TABLE IF EXISTS temp_data;

-- ❌ MAUVAIS : Échoue si existe déjà
CREATE TABLE utilisateurs (...);
```

---

**3. Documenter avec COMMENT**

```sql
-- ✅ BON : Documentation intégrée
COMMENT ON TABLE commandes IS 'Commandes clients avec historique complet';
COMMENT ON COLUMN commandes.statut IS 'nouvelle, payee, expediee, livree, annulee';
```

---

**4. Utiliser des schémas pour organiser**

```sql
-- ✅ BON : Organisation logique
CREATE SCHEMA ventes;
CREATE SCHEMA comptabilite;
CREATE SCHEMA stats;

CREATE TABLE ventes.commandes (...);
CREATE TABLE comptabilite.factures (...);
```

---

**5. Partitionner les grandes tables**

```sql
-- Pour tables > 100 GB ou requêtes sur plages de dates
CREATE TABLE logs (...) PARTITION BY RANGE (date_log);

-- Facilite la maintenance
DROP TABLE logs_2020;  -- Supprimer anciennes données instantanément
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ CREATE TABLE : structure, contraintes, types
✅ ALTER TABLE : modifier structure existante
✅ DROP TABLE : supprimer tables
✅ Schémas : organiser logiquement
✅ Tables temporaires : calculs temporaires
✅ Partitionnement : optimiser grandes tables
✅ COMMENT : documenter la structure
✅ IF EXISTS : scripts idempotents

---

# Questions ? 🙋

Des questions sur la création et gestion de tables ?

**À suivre** : Contraintes et index

