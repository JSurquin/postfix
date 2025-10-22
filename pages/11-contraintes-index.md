---
layout: new-section
routeAlias: 'contraintes-index'
---

<a name="contraintes-index" id="contraintes-index"></a>

# Contraintes et Index 🔐

Garantir l'intégrité des données et optimiser les performances

---

# Plan du module 📋

- Contraintes d'intégrité
- PRIMARY KEY
- FOREIGN KEY
- UNIQUE
- CHECK
- NOT NULL
- Index B-tree, Hash, GIN, GIST
- Index partiels et d'expression
- Optimisation des index

---

# Contraintes d'intégrité 🛡️

**Contraintes = Les règles du jeu ! 📏**

*Analogie* : C'est comme les règles dans un sport - elles empêchent de faire n'importe quoi !

**Pourquoi des contraintes ?** 🤔
Pour protéger vos données contre les erreurs :
- Empêcher les doublons
- Forcer des valeurs cohérentes
- Garantir des liens entre tables

**Les 6 types de contraintes** :

1️⃣ **PRIMARY KEY** = Carte d'identité unique 🆔
   - Chaque ligne a UN identifiant unique
   - *Exemple* : Numéro de sécurité sociale

2️⃣ **FOREIGN KEY** = Lien vers une autre table 🔗
   - Force une référence valide
   - *Exemple* : Un livre doit avoir un auteur qui existe

3️⃣ **UNIQUE** = Pas de doublon 🚫
   - Aucune valeur en double
   - *Exemple* : Un email ne peut pas être utilisé 2 fois

4️⃣ **CHECK** = Validation personnalisée ✅
   - Vérifier une condition
   - *Exemple* : L'âge doit être >= 18

5️⃣ **NOT NULL** = Obligatoire ⚠️
   - La valeur ne peut pas être vide
   - *Exemple* : Un nom est obligatoire

6️⃣ **EXCLUSION** = Éviter les chevauchements 📅
   - Empêcher des conflits temporels
   - *Exemple* : Deux réservations ne peuvent pas se chevaucher

💡 **En résumé** : Les contraintes = Vos gardes du corps pour vos données !

---

# PRIMARY KEY 🔑

**Définition inline**

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(200)
);
```

---

**Définition en contrainte de table**

```sql
CREATE TABLE produits (
    id SERIAL,
    nom VARCHAR(200),
    PRIMARY KEY (id)
);

-- Avec nom de contrainte
CREATE TABLE produits (
    id SERIAL,
    nom VARCHAR(200),
    CONSTRAINT pk_produits PRIMARY KEY (id)
);
```

---

**Clé primaire composite**

```sql
CREATE TABLE inscriptions (
    etudiant_id INTEGER,
    cours_id INTEGER,
    date_inscription DATE,
    PRIMARY KEY (etudiant_id, cours_id)
);
```

---

**Ajouter PRIMARY KEY après création**

```sql
-- Ajouter une clé primaire
ALTER TABLE produits
ADD PRIMARY KEY (id);

-- Avec nom
ALTER TABLE produits
ADD CONSTRAINT pk_produits PRIMARY KEY (id);
```

---

# FOREIGN KEY 🔗

**Référence simple**

```sql
CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    client_id INTEGER REFERENCES clients(id),
    date_commande TIMESTAMP
);
```

---

**Avec actions ON DELETE / ON UPDATE**

```sql
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    auteur_id INTEGER REFERENCES auteurs(id) ON DELETE CASCADE,
    categorie_id INTEGER REFERENCES categories(id) ON DELETE SET NULL,
    titre VARCHAR(300) NOT NULL
);
```

---

**Actions disponibles**

```sql
-- CASCADE : Suppression en cascade
ON DELETE CASCADE

-- SET NULL : Mettre à NULL
ON DELETE SET NULL

-- SET DEFAULT : Valeur par défaut
ON DELETE SET DEFAULT

-- RESTRICT : Empêcher (défaut)
ON DELETE RESTRICT

-- NO ACTION : Vérifier en fin de transaction
ON DELETE NO ACTION
```

---

**Clé étrangère composite**

```sql
CREATE TABLE notes (
    id SERIAL PRIMARY KEY,
    etudiant_id INTEGER,
    cours_id INTEGER,
    note NUMERIC(4,2),
    FOREIGN KEY (etudiant_id, cours_id) 
        REFERENCES inscriptions(etudiant_id, cours_id)
);
```

---

**Ajouter FOREIGN KEY après création**

```sql
ALTER TABLE commandes
ADD CONSTRAINT fk_client
FOREIGN KEY (client_id) 
REFERENCES clients(id) 
ON DELETE CASCADE;
```

---

# UNIQUE 🎯

**Contrainte UNIQUE inline**

```sql
CREATE TABLE utilisateurs (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE,
    nom VARCHAR(100)
);
```

---

**Contrainte UNIQUE de table**

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(50),
    nom VARCHAR(200),
    UNIQUE (sku)
);

-- Avec nom
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(50),
    nom VARCHAR(200),
    CONSTRAINT uk_sku UNIQUE (sku)
);
```

---

**UNIQUE composite**

```sql
-- Combinaison unique
CREATE TABLE reservations (
    id SERIAL PRIMARY KEY,
    salle_id INTEGER,
    date_reservation DATE,
    heure_debut TIME,
    UNIQUE (salle_id, date_reservation, heure_debut)
);
```

---

**UNIQUE vs PRIMARY KEY**

```sql
-- Une table peut avoir :
-- - 1 seule PRIMARY KEY
-- - Plusieurs UNIQUE

CREATE TABLE exemple (
    id SERIAL PRIMARY KEY,           -- 1 seule PK
    email VARCHAR(255) UNIQUE,       -- UNIQUE 1
    telephone VARCHAR(20) UNIQUE,    -- UNIQUE 2
    numero_ss VARCHAR(15) UNIQUE     -- UNIQUE 3
);
```

---

# CHECK : Contraintes personnalisées ✔️

**CHECK simple**

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(200),
    prix NUMERIC(10,2) CHECK (prix > 0),
    stock INTEGER CHECK (stock >= 0),
    reduction NUMERIC(3,2) CHECK (reduction BETWEEN 0 AND 1)
);
```

---

**CHECK avec plusieurs colonnes**

```sql
CREATE TABLE periodes (
    id SERIAL PRIMARY KEY,
    date_debut DATE NOT NULL,
    date_fin DATE NOT NULL,
    CHECK (date_fin > date_debut)
);
```

---

**CHECK avec nom et expressions complexes**

```sql
CREATE TABLE employes (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    age INTEGER,
    salaire NUMERIC(10,2),
    CONSTRAINT chk_age_adulte CHECK (age >= 18),
    CONSTRAINT chk_salaire_positif CHECK (salaire > 0),
    CONSTRAINT chk_salaire_age CHECK (
        (age < 25 AND salaire <= 30000) OR
        (age >= 25)
    )
);
```

---

**Ajouter CHECK après création**

```sql
ALTER TABLE produits
ADD CONSTRAINT chk_prix_logique 
CHECK (prix > 0 AND prix < 100000);
```

---

# NOT NULL 📝

**Définir NOT NULL**

```sql
CREATE TABLE utilisateurs (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    nom VARCHAR(100) NOT NULL,
    telephone VARCHAR(20)  -- Nullable
);
```

---

**Ajouter/Supprimer NOT NULL**

```sql
-- Ajouter NOT NULL
ALTER TABLE utilisateurs
ALTER COLUMN telephone SET NOT NULL;

-- Supprimer NOT NULL
ALTER TABLE utilisateurs
ALTER COLUMN telephone DROP NOT NULL;
```

---

# EXCLUSION : Contraintes d'exclusion 🚫

**Éviter les chevauchements**

```sql
CREATE EXTENSION btree_gist;

CREATE TABLE reservations (
    id SERIAL PRIMARY KEY,
    salle_id INTEGER,
    periode TSRANGE,
    EXCLUDE USING GIST (
        salle_id WITH =,
        periode WITH &&
    )
);
```

---

**Exemple d'utilisation**

```sql
-- OK
INSERT INTO reservations (salle_id, periode)
VALUES (1, '[2025-01-15 09:00, 2025-01-15 10:00)');

-- OK (différente salle)
INSERT INTO reservations (salle_id, periode)
VALUES (2, '[2025-01-15 09:00, 2025-01-15 10:00)');

-- ERREUR : Chevauchement !
INSERT INTO reservations (salle_id, periode)
VALUES (1, '[2025-01-15 09:30, 2025-01-15 10:30)');
```

---

# Index : Optimiser les requêtes 🚀

**Types d'index**

1. **B-tree** : Index par défaut, général
2. **Hash** : Égalité uniquement
3. **GIN** : Texte, JSONB, tableaux
4. **GiST** : Géométrie, plages
5. **BRIN** : Grandes tables séquentielles
6. **SP-GiST** : Structures non équilibrées

---

# Index B-tree 🌳

**Index simple**

```sql
-- Créer un index B-tree (par défaut)
CREATE INDEX idx_utilisateurs_email ON utilisateurs(email);

-- Explicitement B-tree
CREATE INDEX idx_produits_prix ON produits USING BTREE (prix);
```

---

**Quand utiliser B-tree**

- Comparaisons : `=`, `<`, `<=`, `>`, `>=`
- Pattern matching : `LIKE 'abc%'` (pas `'%abc'`)
- IN, BETWEEN
- ORDER BY
- MIN/MAX

```sql
-- Ces requêtes bénéficient d'un index B-tree
SELECT * FROM produits WHERE prix > 100;
SELECT * FROM produits WHERE nom LIKE 'Laptop%';
SELECT * FROM produits ORDER BY prix;
```

---

**Index composite (multi-colonnes)**

```sql
CREATE INDEX idx_commandes_client_date 
ON commandes(client_id, date_commande);

-- Bénéfique pour :
SELECT * FROM commandes WHERE client_id = 1;
SELECT * FROM commandes WHERE client_id = 1 AND date_commande > '2025-01-01';

-- PAS bénéfique pour :
SELECT * FROM commandes WHERE date_commande > '2025-01-01';  -- Pas client_id
```

---

**Ordre des colonnes important !**

```sql
-- Index (A, B, C) utilisable pour :
-- ✅ WHERE A = x
-- ✅ WHERE A = x AND B = y
-- ✅ WHERE A = x AND B = y AND C = z
-- ❌ WHERE B = y
-- ❌ WHERE C = z
```

---

**Index UNIQUE**

```sql
-- Index unique (aussi une contrainte)
CREATE UNIQUE INDEX idx_unique_email ON utilisateurs(email);

-- Équivalent à
ALTER TABLE utilisateurs ADD CONSTRAINT uk_email UNIQUE (email);
```

---

# Index partiels 🎯

**Indexer uniquement certaines lignes**

```sql
-- Uniquement les produits en stock
CREATE INDEX idx_produits_disponibles 
ON produits(nom) 
WHERE stock > 0;

-- Uniquement les articles publiés
CREATE INDEX idx_articles_publies
ON articles(date_publication)
WHERE publie = true;
```

---

**Avantages des index partiels**

- Plus petits donc plus rapides
- Moins d'espace disque
- Mises à jour plus rapides

```sql
-- Cette requête utilise l'index partiel
SELECT * FROM produits 
WHERE stock > 0 
AND nom LIKE 'Laptop%';
```

---

# Index d'expression 🔧

**Indexer une expression calculée**

```sql
-- Index sur LOWER(email) pour recherche insensible à la casse
CREATE INDEX idx_email_lower ON utilisateurs(LOWER(email));

-- Requête utilisant l'index
SELECT * FROM utilisateurs WHERE LOWER(email) = 'alice@example.com';
```

---

**Autres exemples**

```sql
-- Index sur l'année
CREATE INDEX idx_commandes_annee 
ON commandes(EXTRACT(YEAR FROM date_commande));

-- Index sur calcul
CREATE INDEX idx_produits_prix_ttc 
ON produits((prix * 1.20));

-- Index sur concatenation
CREATE INDEX idx_nom_complet 
ON utilisateurs((prenom || ' ' || nom));
```

---

# Index GIN : Texte et JSONB 📦

**Index pour recherche full-text**

```sql
-- Index GIN sur texte
CREATE INDEX idx_articles_contenu 
ON articles USING GIN (to_tsvector('french', contenu));

-- Requête full-text
SELECT * FROM articles
WHERE to_tsvector('french', contenu) @@ to_tsquery('french', 'postgresql & base');
```

---

**Index GIN sur JSONB**

```sql
-- Index GIN sur colonne JSONB
CREATE INDEX idx_produits_caracteristiques 
ON produits USING GIN (caracteristiques);

-- Requête JSON
SELECT * FROM produits
WHERE caracteristiques @> '{"marque": "Dell"}';

SELECT * FROM produits
WHERE caracteristiques ? 'ram';
```

---

**Index GIN sur tableaux**

```sql
-- Index sur tableau
CREATE INDEX idx_articles_tags 
ON articles USING GIN (tags);

-- Requête sur tableau
SELECT * FROM articles WHERE tags @> ARRAY['PostgreSQL'];
SELECT * FROM articles WHERE 'SQL' = ANY(tags);
```

---

# Index GiST : Géométrie et plages 🗺️

**Index pour types géométriques**

```sql
CREATE EXTENSION postgis;

CREATE INDEX idx_lieux_position 
ON lieux USING GIST (position);

-- Requête spatiale
SELECT * FROM lieux
WHERE ST_DWithin(position, ST_MakePoint(2.35, 48.86), 5000);
```

---

**Index pour plages (ranges)**

```sql
CREATE INDEX idx_reservations_periode 
ON reservations USING GIST (periode);

-- Requête sur plages
SELECT * FROM reservations
WHERE periode && '[2025-01-15 09:00, 2025-01-15 10:00)'::TSRANGE;
```

---

# Index BRIN : Grandes tables 📊

**Block Range INdex pour tables très grandes**

```sql
-- Index BRIN (très compact)
CREATE INDEX idx_logs_date 
ON logs USING BRIN (date_log);

-- Idéal pour :
-- - Tables > 100 GB
-- - Données séquentielles/triées
-- - Requêtes sur plages
```

---

**Comparaison BRIN vs B-tree**

| | BRIN | B-tree |
|---|------|--------|
| Taille | Très petit | Normal |
| Vitesse création | Très rapide | Normale |
| Précision | Approximative | Exacte |
| Maintenance | Minimale | Normale |
| Idéal pour | Tables énormes triées | Usage général |

---

# Gestion des index 🛠️

**Lister les index**

```sql
-- Index d'une table
\d table_name

-- Tous les index
SELECT tablename, indexname, indexdef
FROM pg_indexes
WHERE schemaname = 'public';

-- Taille des index
SELECT 
    indexname,
    pg_size_pretty(pg_relation_size(indexname::regclass))
FROM pg_indexes
WHERE tablename = 'produits';
```

---

**Reconstruire un index**

```sql
-- Reconstruire (bloque la table)
REINDEX INDEX idx_produits_nom;

-- Reconstruire tous les index d'une table
REINDEX TABLE produits;

-- Reconstruire sans bloquer (PostgreSQL 12+)
REINDEX INDEX CONCURRENTLY idx_produits_nom;
```

---

**Supprimer un index**

```sql
-- Supprimer un index
DROP INDEX idx_produits_nom;

-- Si existe
DROP INDEX IF EXISTS idx_produits_nom;

-- Sans bloquer
DROP INDEX CONCURRENTLY idx_produits_nom;
```

---

# Créer index sans bloquer 🔓

**CREATE INDEX CONCURRENTLY**

```sql
-- Création sans bloquer les écritures (plus lent)
CREATE INDEX CONCURRENTLY idx_produits_nom 
ON produits(nom);

-- Utile en production pour ne pas bloquer les utilisateurs
```

**⚠️ Ne peut pas être dans une transaction**

---

# EXPLAIN : Analyser l'utilisation des index 📊

**Vérifier qu'un index est utilisé**

```sql
-- Voir le plan d'exécution
EXPLAIN SELECT * FROM produits WHERE nom = 'Laptop';

-- Avec coûts réels
EXPLAIN ANALYZE SELECT * FROM produits WHERE nom = 'Laptop';
```

---

**Exemple de résultat**

```
Index Scan using idx_produits_nom on produits
  Index Cond: (nom = 'Laptop'::text)
  Rows: 1
  Cost: 0.15..8.17
```

---

**Seq Scan vs Index Scan**

```sql
-- Sequential Scan : Balaye toute la table
Seq Scan on produits
  Filter: (prix > 100)

-- Index Scan : Utilise un index
Index Scan using idx_produits_prix on produits
  Index Cond: (prix > 100)
```

**PostgreSQL choisit automatiquement le meilleur plan**

---

# Statistiques et ANALYZE 📈

**Mettre à jour les statistiques**

```sql
-- Analyser une table (met à jour les statistiques)
ANALYZE produits;

-- Analyser toutes les tables
ANALYZE;

-- Automatique avec autovacuum (par défaut)
```

---

**Importance d'ANALYZE**

- PostgreSQL utilise les statistiques pour choisir les index
- Après gros INSERT/UPDATE/DELETE, lancer ANALYZE
- Autovacuum le fait automatiquement

```sql
-- Voir les statistiques
SELECT * FROM pg_stats WHERE tablename = 'produits';
```

---

# Index inutilisés 🗑️

**Trouver les index non utilisés**

```sql
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch,
    pg_size_pretty(pg_relation_size(indexrelid)) AS size
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY pg_relation_size(indexrelid) DESC;
```

---

# Bonnes pratiques 👍

**1. Indexer les clés étrangères**

```sql
-- ✅ BON : FK avec index
CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    client_id INTEGER REFERENCES clients(id)
);
CREATE INDEX idx_commandes_client ON commandes(client_id);

-- ❌ MAUVAIS : FK sans index (jointures lentes)
```

---

**2. Index sur colonnes WHERE fréquentes**

```sql
-- Si souvent :
SELECT * FROM produits WHERE categorie = 'Informatique';

-- Créer :
CREATE INDEX idx_produits_categorie ON produits(categorie);
```

---

**3. Éviter sur-indexation**

```sql
-- ❌ MAUVAIS : Trop d'index
CREATE INDEX idx1 ON produits(nom);
CREATE INDEX idx2 ON produits(nom, prix);
CREATE INDEX idx3 ON produits(nom, prix, stock);
CREATE INDEX idx4 ON produits(prix);

-- ✅ MIEUX : Index ciblés
CREATE INDEX idx_nom_prix ON produits(nom, prix);
CREATE INDEX idx_prix ON produits(prix);
```

---

**4. Index partiels pour cas spécifiques**

```sql
-- ✅ BON : Index uniquement le nécessaire
CREATE INDEX idx_produits_actifs 
ON produits(nom) 
WHERE actif = true;

-- Au lieu de
CREATE INDEX idx_produits_nom ON produits(nom);
```

---

**5. Surveiller la taille des index**

```sql
-- Voir les plus gros index
SELECT 
    indexname,
    pg_size_pretty(pg_relation_size(indexrelid)) AS size
FROM pg_stat_user_indexes
ORDER BY pg_relation_size(indexrelid) DESC
LIMIT 10;
```

---

# Exercice pratique 🎯

**Optimiser une application e-commerce**

1. Créer tables `produits`, `commandes`, `clients`
2. Ajouter contraintes appropriées
3. Créer index pour ces requêtes :
   - Produits par catégorie
   - Commandes d'un client
   - Recherche produit par nom
   - Articles publiés récemment

---

**Solution - Partie 1**

```sql
CREATE TABLE clients (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    nom VARCHAR(100) NOT NULL,
    date_inscription TIMESTAMP DEFAULT NOW(),
    CONSTRAINT chk_email_format CHECK (email ~ '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')
);

CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(50) UNIQUE NOT NULL,
    nom VARCHAR(200) NOT NULL,
    categorie VARCHAR(100) NOT NULL,
    prix NUMERIC(10,2) NOT NULL CHECK (prix > 0),
    stock INTEGER NOT NULL DEFAULT 0 CHECK (stock >= 0),
    actif BOOLEAN DEFAULT true,
    date_ajout TIMESTAMP DEFAULT NOW()
);
```

---

```sql
CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    client_id INTEGER NOT NULL REFERENCES clients(id) ON DELETE RESTRICT,
    montant_total NUMERIC(10,2) NOT NULL CHECK (montant_total >= 0),
    statut VARCHAR(50) NOT NULL DEFAULT 'nouvelle',
    date_commande TIMESTAMP DEFAULT NOW(),
    CONSTRAINT chk_statut CHECK (statut IN ('nouvelle', 'payee', 'expediee', 'livree', 'annulee'))
);
```

---

**Solution - Partie 2 : Index**

```sql
-- 1. Produits par catégorie (uniquement actifs)
CREATE INDEX idx_produits_categorie_actif 
ON produits(categorie) 
WHERE actif = true;

-- 2. Commandes d'un client
CREATE INDEX idx_commandes_client 
ON commandes(client_id);

-- 3. Recherche produit par nom (insensible à la casse)
CREATE INDEX idx_produits_nom_lower 
ON produits(LOWER(nom));
```

---

```sql
-- 4. Articles récents (30 derniers jours)
CREATE INDEX idx_produits_recents 
ON produits(date_ajout DESC)
WHERE date_ajout > NOW() - INTERVAL '30 days';

-- 5. Performance des jointures
CREATE INDEX idx_commandes_client_date 
ON commandes(client_id, date_commande DESC);
```

---

**Solution - Partie 3 : Vérifier**

```sql
-- Tester les requêtes
EXPLAIN ANALYZE
SELECT * FROM produits 
WHERE categorie = 'Informatique' AND actif = true;

EXPLAIN ANALYZE
SELECT * FROM commandes 
WHERE client_id = 1 
ORDER BY date_commande DESC;

EXPLAIN ANALYZE
SELECT * FROM produits 
WHERE LOWER(nom) LIKE '%laptop%';
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Contraintes : PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK, NOT NULL
✅ Index B-tree : usage général
✅ Index GIN : JSONB, tableaux, full-text
✅ Index GiST : géométrie, plages
✅ Index partiels : optimiser espace
✅ Index d'expression : calculer à l'avance
✅ EXPLAIN pour analyser
✅ Éviter sur-indexation

---

# Questions ? 🙋

Des questions sur les contraintes et les index ?

**À suivre** : Vues et vues matérialisées

