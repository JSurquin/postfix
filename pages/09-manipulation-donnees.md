---
layout: new-section
routeAlias: 'manipulation-donnees'
---

<a name="manipulation-donnees" id="manipulation-donnees"></a>

# Manipulation de données ✏️

Maîtrisons INSERT, UPDATE, DELETE et UPSERT

---

# Plan du module 📋

- INSERT : Insérer des données
- UPDATE : Mettre à jour
- DELETE : Supprimer
- RETURNING : Récupérer les valeurs
- UPSERT : INSERT ... ON CONFLICT
- COPY : Import/Export en masse
- Truncate
- Bonnes pratiques

---

# INSERT : Insérer des données 📝

**INSERT = Ajouter une nouvelle ligne dans votre tableau ! ✏️**

*Analogie* : C'est comme ajouter une nouvelle ligne dans Excel !

**Syntaxe de base** :
```sql
INSERT INTO table (colonne1, colonne2, ...)
VALUES (valeur1, valeur2, ...);
```

💡 **Décomposition** :
- `INSERT INTO table` = "J'ajoute dans la table..."
- `(colonne1, colonne2)` = "...dans ces colonnes..."
- `VALUES (...)` = "...ces valeurs"

---

**Exemple simple** 👤 :

```sql
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Alice Martin', 'alice@example.com', 30);
```

🎯 **Traduction** :
"Ajoute dans la table utilisateurs une personne avec :
- nom = Alice Martin
- email = alice@example.com  
- age = 30"

✅ **Résultat** : Une nouvelle ligne apparaît !

---

**Colonnes auto-générées** 🤖 :

```sql
INSERT INTO utilisateurs (nom, email)
VALUES ('Bob Durand', 'bob@example.com');
```

💡 **Remarquez** : On ne donne PAS l'`id` ni la `date_inscription`
- `id` → PostgreSQL génère automatiquement (1, 2, 3...)
- `date_inscription` → Utilise DEFAULT (date actuelle)

⚠️ **Important** : Ne donnez que ce qui est nécessaire !

---

**Insérer plusieurs lignes**

```sql
-- INSERT multiple (efficace)
INSERT INTO produits (nom, prix, stock) VALUES
    ('Laptop', 899.99, 10),
    ('Souris', 29.99, 50),
    ('Clavier', 79.99, 30),
    ('Écran', 299.99, 15);
```

---

**INSERT sans spécifier les colonnes**

```sql
-- ⚠️ Doit respecter l'ordre exact de toutes les colonnes
INSERT INTO utilisateurs
VALUES (DEFAULT, 'Charlie Leroy', 'charlie@example.com', 28, DEFAULT);

-- ✅ Préférez toujours spécifier les colonnes !
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Charlie Leroy', 'charlie@example.com', 28);
```

---

# INSERT depuis SELECT 📊

**Copier des données d'une table à l'autre**

```sql
-- Créer une table de sauvegarde
CREATE TABLE utilisateurs_backup (LIKE utilisateurs);

-- Copier toutes les données
INSERT INTO utilisateurs_backup
SELECT * FROM utilisateurs;

-- Copier avec filtre
INSERT INTO utilisateurs_backup
SELECT * FROM utilisateurs WHERE age > 30;
```

---

**INSERT avec transformation**

```sql
-- Insérer avec calculs
INSERT INTO commandes_archive (client_id, annee, total)
SELECT 
    client_id,
    EXTRACT(YEAR FROM date_commande),
    SUM(montant)
FROM commandes
WHERE date_commande < '2024-01-01'
GROUP BY client_id, EXTRACT(YEAR FROM date_commande);
```

---

# RETURNING : Récupérer les valeurs insérées 🔄

**Retourner toutes les colonnes**

```sql
-- Voir l'ID généré automatiquement
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Diana Petit', 'diana@example.com', 25)
RETURNING *;

-- Résultat :
-- id | nom          | email             | age | date_creation
-- 15 | Diana Petit  | diana@example.com | 25  | 2025-01-15 10:30:00
```

---

**Retourner uniquement l'ID**

```sql
INSERT INTO produits (nom, prix, stock)
VALUES ('Webcam', 59.99, 20)
RETURNING id;

-- Résultat : 42
```

---

**RETURNING avec calculs**

```sql
INSERT INTO commandes (client_id, montant_ht, tva)
VALUES (1, 100.00, 20.00)
RETURNING id, montant_ht + tva AS montant_ttc;
```

---

**RETURNING avec INSERT multiple**

```sql
-- Retourne toutes les lignes insérées
INSERT INTO produits (nom, prix) VALUES
    ('Produit A', 10.00),
    ('Produit B', 20.00),
    ('Produit C', 30.00)
RETURNING id, nom;
```

---

# UPDATE : Mettre à jour des données 🔄

**Syntaxe de base**

```sql
UPDATE table
SET colonne1 = valeur1, colonne2 = valeur2, ...
WHERE condition;
```

**⚠️ N'OUBLIEZ JAMAIS WHERE (sauf intention de tout modifier) !**

---

**UPDATE simple**

```sql
-- Mettre à jour un utilisateur
UPDATE utilisateurs
SET age = 31
WHERE id = 1;

-- Mettre à jour plusieurs colonnes
UPDATE utilisateurs
SET age = 32, email = 'alice.martin@example.com'
WHERE nom = 'Alice Martin';
```

---

**UPDATE avec calculs**

```sql
-- Augmenter tous les prix de 10%
UPDATE produits
SET prix = prix * 1.10;

-- Diminuer le stock
UPDATE produits
SET stock = stock - 1
WHERE id = 5;

-- Avec condition
UPDATE produits
SET prix = prix * 0.9
WHERE stock > 100;
```

---

**UPDATE avec CASE**

```sql
-- Remise différenciée selon le prix
UPDATE produits
SET prix = CASE
    WHEN prix < 20 THEN prix * 0.95
    WHEN prix >= 20 AND prix < 100 THEN prix * 0.90
    ELSE prix * 0.85
END
WHERE categorie = 'Informatique';
```

---

**UPDATE depuis une autre table (FROM)**

```sql
-- Mettre à jour avec des données d'une autre table
UPDATE produits p
SET stock = s.nouveau_stock
FROM stock_temporaire s
WHERE p.id = s.produit_id;

-- Avec jointure
UPDATE commandes cmd
SET statut = 'expedie'
FROM clients c
WHERE cmd.client_id = c.id
  AND c.pays = 'France'
  AND cmd.statut = 'prepare';
```

---

**UPDATE avec sous-requête**

```sql
-- Mettre le prix à la moyenne de la catégorie
UPDATE produits p
SET prix = (
    SELECT AVG(prix)
    FROM produits p2
    WHERE p2.categorie = p.categorie
)
WHERE prix IS NULL;
```

---

**UPDATE avec RETURNING**

```sql
-- Voir les lignes modifiées
UPDATE produits
SET prix = prix * 1.10
WHERE categorie = 'Informatique'
RETURNING id, nom, prix AS nouveau_prix;
```

---

# DELETE : Supprimer des données 🗑️

**Syntaxe de base**

```sql
DELETE FROM table
WHERE condition;
```

**⚠️ SANS WHERE = SUPPRESSION TOTALE !**

---

**DELETE simple**

```sql
-- Supprimer un utilisateur
DELETE FROM utilisateurs WHERE id = 10;

-- Supprimer avec condition
DELETE FROM produits WHERE stock = 0 AND prix < 10;

-- Supprimer les anciens
DELETE FROM logs WHERE date_log < CURRENT_DATE - INTERVAL '1 year';
```

---

**DELETE avec RETURNING**

```sql
-- Voir ce qui a été supprimé
DELETE FROM produits
WHERE stock = 0
RETURNING id, nom, prix;
```

---

**DELETE avec sous-requête**

```sql
-- Supprimer les produits jamais commandés
DELETE FROM produits
WHERE id NOT IN (
    SELECT DISTINCT produit_id 
    FROM lignes_commande
    WHERE produit_id IS NOT NULL
);

-- Avec EXISTS (plus performant)
DELETE FROM produits p
WHERE NOT EXISTS (
    SELECT 1 FROM lignes_commande lc
    WHERE lc.produit_id = p.id
);
```

---

**DELETE avec JOIN (PostgreSQL)**

```sql
-- Supprimer avec jointure
DELETE FROM commandes cmd
USING clients c
WHERE cmd.client_id = c.id
  AND c.date_inscription < '2020-01-01';
```

---

# TRUNCATE : Vider une table rapidement 🚀

**Différence avec DELETE**

```sql
-- DELETE : Supprime ligne par ligne (lent, peut être rollback)
DELETE FROM ma_table;

-- TRUNCATE : Vide toute la table (rapide, réinitialise sequences)
TRUNCATE TABLE ma_table;
```

---

**TRUNCATE avec CASCADE**

```sql
-- Erreur si des clés étrangères pointent vers la table
TRUNCATE TABLE clients;  -- ERROR si commandes.client_id référence

-- CASCADE : vide aussi les tables liées
TRUNCATE TABLE clients CASCADE;

-- RESTART IDENTITY : réinitialise les sequences
TRUNCATE TABLE produits RESTART IDENTITY;
```

---

**Différences DELETE vs TRUNCATE**

| | DELETE | TRUNCATE |
|---|--------|----------|
| Vitesse | Lent sur grandes tables | Très rapide |
| WHERE | ✅ Oui | ❌ Non |
| Triggers | ✅ Activés | ❌ Pas activés |
| Rollback | ✅ Possible | ✅ Possible (dans transaction) |
| Sequence | Conserve | Réinitialise (avec RESTART) |

---

# UPSERT : INSERT ... ON CONFLICT 🔄

**Insérer ou mettre à jour si existe déjà**

```sql
-- Syntaxe
INSERT INTO table (colonnes)
VALUES (valeurs)
ON CONFLICT (colonne_conflit)
DO UPDATE SET colonne = valeur;
```

---

**Exemple simple**

```sql
-- Table avec contrainte UNIQUE sur email
CREATE TABLE utilisateurs (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    nom VARCHAR(100),
    connexions INTEGER DEFAULT 0
);
```

---

```sql
-- Insérer ou incrémenter le compteur
INSERT INTO utilisateurs (email, nom, connexions)
VALUES ('alice@example.com', 'Alice Martin', 1)
ON CONFLICT (email)
DO UPDATE SET 
    connexions = utilisateurs.connexions + 1,
    nom = EXCLUDED.nom;
```

---

**EXCLUDED : Valeurs proposées à l'insertion**

```sql
INSERT INTO produits (sku, nom, prix, stock)
VALUES ('LAP-001', 'Laptop Pro', 999.99, 10)
ON CONFLICT (sku)
DO UPDATE SET 
    prix = EXCLUDED.prix,
    stock = produits.stock + EXCLUDED.stock,
    date_maj = NOW();
```

---

**ON CONFLICT DO NOTHING**

```sql
-- Ignorer silencieusement les doublons
INSERT INTO tags (nom)
VALUES ('PostgreSQL'), ('SQL'), ('Database')
ON CONFLICT (nom) DO NOTHING;
```

---

**ON CONFLICT sur contrainte nommée**

```sql
-- Créer une contrainte
CREATE UNIQUE INDEX idx_produit_sku ON produits(sku);

-- Utiliser le nom de la contrainte
INSERT INTO produits (sku, nom, prix)
VALUES ('LAP-001', 'Laptop', 899.99)
ON CONFLICT ON CONSTRAINT idx_produit_sku
DO UPDATE SET prix = EXCLUDED.prix;
```

---

**UPSERT avec WHERE**

```sql
-- Ne mettre à jour que si condition vraie
INSERT INTO produits (sku, nom, prix)
VALUES ('LAP-001', 'Laptop', 999.99)
ON CONFLICT (sku)
DO UPDATE SET prix = EXCLUDED.prix
WHERE produits.prix < EXCLUDED.prix;  -- Seulement si nouveau prix > ancien
```

---

**UPSERT avec RETURNING**

```sql
INSERT INTO utilisateurs (email, nom)
VALUES ('bob@example.com', 'Bob Durand')
ON CONFLICT (email)
DO UPDATE SET nom = EXCLUDED.nom
RETURNING id, email, nom, 
    CASE WHEN xmax = 0 THEN 'inserted' ELSE 'updated' END AS action;
```

---

# COPY : Import/Export en masse 📦

**Exporter vers un fichier**

```sql
-- Exporter vers CSV
COPY produits TO '/tmp/produits.csv' WITH CSV HEADER;

-- Avec délimiteur personnalisé
COPY produits TO '/tmp/produits.txt' WITH (DELIMITER '|', HEADER true);

-- Colonnes spécifiques
COPY (SELECT nom, prix FROM produits) TO '/tmp/export.csv' WITH CSV HEADER;
```

---

**Importer depuis un fichier**

```sql
-- Importer depuis CSV
COPY produits FROM '/tmp/produits.csv' WITH CSV HEADER;

-- Colonnes spécifiques
COPY produits (nom, prix, stock) FROM '/tmp/import.csv' WITH CSV HEADER;

-- Avec options
COPY produits FROM '/tmp/produits.csv' WITH (
    FORMAT csv,
    HEADER true,
    DELIMITER ',',
    QUOTE '"',
    ESCAPE '\',
    NULL 'NULL'
);
```

---

**\copy dans psql**

```sql
-- Depuis psql (pas besoin de permissions serveur)
\copy produits TO 'produits.csv' WITH CSV HEADER

\copy produits FROM 'produits.csv' WITH CSV HEADER
```

---

# Gestion des séquences 🔢

**Séquences automatiques avec SERIAL**

```sql
CREATE TABLE test (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100)
);

-- En arrière-plan, PostgreSQL crée :
-- CREATE SEQUENCE test_id_seq;
-- id INTEGER DEFAULT nextval('test_id_seq')
```

---

**Manipuler les séquences**

```sql
-- Valeur actuelle
SELECT currval('test_id_seq');

-- Prochaine valeur
SELECT nextval('test_id_seq');

-- Réinitialiser
ALTER SEQUENCE test_id_seq RESTART WITH 1;

-- Définir la valeur
SELECT setval('test_id_seq', 100);
```

---

**Corriger une séquence désynchronisée**

```sql
-- Après un COPY ou INSERT manuel, la séquence peut être désynchronisée
SELECT setval('produits_id_seq', (SELECT MAX(id) FROM produits));

-- Ou automatiquement
SELECT setval('produits_id_seq', COALESCE((SELECT MAX(id) FROM produits), 1));
```

---

# DEFAULT VALUES 📋

**Utiliser les valeurs par défaut**

```sql
-- Toutes les colonnes avec leur défaut
INSERT INTO utilisateurs DEFAULT VALUES;

-- Spécifier DEFAULT pour certaines colonnes
INSERT INTO produits (nom, prix, stock)
VALUES ('Test', 10.00, DEFAULT);
```

---

# Insertion conditionnelle 🎯

**INSERT seulement si n'existe pas**

```sql
-- Méthode 1 : ON CONFLICT DO NOTHING
INSERT INTO tags (nom)
VALUES ('PostgreSQL')
ON CONFLICT (nom) DO NOTHING;

-- Méthode 2 : WHERE NOT EXISTS
INSERT INTO tags (nom)
SELECT 'PostgreSQL'
WHERE NOT EXISTS (
    SELECT 1 FROM tags WHERE nom = 'PostgreSQL'
);
```

---

# Batch operations 📦

**Insérer depuis VALUES multiples**

```sql
-- Générer des données de test
INSERT INTO test_data (valeur)
SELECT generate_series(1, 1000);

-- Avec calculs
INSERT INTO dates_test (date_val)
SELECT CURRENT_DATE + (n || ' days')::INTERVAL
FROM generate_series(1, 365) AS n;
```

---

**UPDATE en masse efficace**

```sql
-- Mettre à jour depuis une table temporaire
CREATE TEMP TABLE updates (
    id INTEGER,
    nouveau_prix NUMERIC(10,2)
);

COPY updates FROM '/tmp/prix.csv' WITH CSV HEADER;

UPDATE produits p
SET prix = u.nouveau_prix
FROM updates u
WHERE p.id = u.id;

DROP TABLE updates;
```

---

# Transactions et manipulation de données 💼

**Grouper les modifications**

```sql
BEGIN;

INSERT INTO commandes (client_id, montant)
VALUES (1, 150.00)
RETURNING id INTO v_commande_id;

INSERT INTO lignes_commande (commande_id, produit_id, quantite)
VALUES 
    (v_commande_id, 10, 2),
    (v_commande_id, 15, 1);

UPDATE produits SET stock = stock - 2 WHERE id = 10;
UPDATE produits SET stock = stock - 1 WHERE id = 15;

COMMIT;
```

---

**Rollback en cas d'erreur**

```sql
BEGIN;

UPDATE comptes SET solde = solde - 100 WHERE id = 1;
UPDATE comptes SET solde = solde + 100 WHERE id = 2;

-- Vérifier la cohérence
SELECT SUM(solde) FROM comptes;

-- Si OK
COMMIT;
-- Sinon
-- ROLLBACK;
```

---

# Exercice pratique 🎯

**Gestion de stock e-commerce**

1. Créer tables `produits`, `commandes`, `lignes_commande`
2. Insérer des produits
3. Créer une commande qui :
   - Diminue le stock
   - Enregistre la commande
   - Ajoute les lignes
4. Utiliser UPSERT pour un cache de vues produit

---

**Solution - Partie 1 : Tables**

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(50) UNIQUE NOT NULL,
    nom VARCHAR(200) NOT NULL,
    prix NUMERIC(10,2) NOT NULL CHECK (prix > 0),
    stock INTEGER NOT NULL DEFAULT 0 CHECK (stock >= 0)
);
```

---

```sql
CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    client_email VARCHAR(255) NOT NULL,
    total NUMERIC(10,2) NOT NULL,
    date_commande TIMESTAMP DEFAULT NOW(),
    statut VARCHAR(50) DEFAULT 'nouvelle'
);

CREATE TABLE lignes_commande (
    id SERIAL PRIMARY KEY,
    commande_id INTEGER REFERENCES commandes(id),
    produit_id INTEGER REFERENCES produits(id),
    quantite INTEGER NOT NULL CHECK (quantite > 0),
    prix_unitaire NUMERIC(10,2) NOT NULL
);
```

---

```sql
CREATE TABLE vues_produits (
    produit_id INTEGER PRIMARY KEY REFERENCES produits(id),
    nombre_vues INTEGER DEFAULT 0,
    derniere_vue TIMESTAMP DEFAULT NOW()
);
```

---

**Solution - Partie 2 : Données**

```sql
INSERT INTO produits (sku, nom, prix, stock) VALUES
    ('LAP-001', 'Laptop Pro 15"', 999.99, 10),
    ('MOU-001', 'Souris sans fil', 29.99, 50),
    ('CLV-001', 'Clavier mécanique', 79.99, 30);
```

---

**Solution - Partie 3 : Passer une commande**

```sql
BEGIN;

-- 1. Créer la commande
INSERT INTO commandes (client_email, total)
VALUES ('client@example.com', 0)
RETURNING id AS commande_id;

-- Supposons commande_id = 1

-- 2. Ajouter les lignes et diminuer le stock
WITH ligne AS (
    INSERT INTO lignes_commande (commande_id, produit_id, quantite, prix_unitaire)
    VALUES (1, 1, 2, 999.99)
    RETURNING produit_id, quantite
)
UPDATE produits p
SET stock = stock - l.quantite
FROM ligne l
WHERE p.id = l.produit_id
  AND p.stock >= l.quantite;
```

---

```sql
-- 3. Calculer et mettre à jour le total
UPDATE commandes
SET total = (
    SELECT SUM(quantite * prix_unitaire)
    FROM lignes_commande
    WHERE commande_id = 1
)
WHERE id = 1;

COMMIT;
```

---

**Solution - Partie 4 : Cache de vues**

```sql
-- Incrémenter le compteur de vues (UPSERT)
INSERT INTO vues_produits (produit_id, nombre_vues, derniere_vue)
VALUES (1, 1, NOW())
ON CONFLICT (produit_id)
DO UPDATE SET 
    nombre_vues = vues_produits.nombre_vues + 1,
    derniere_vue = NOW();
```

---

# Bonnes pratiques 👍

**1. Toujours utiliser WHERE sauf intention explicite**

```sql
-- ⚠️ DANGEREUX
UPDATE produits SET prix = 0;  -- Tout à zéro !

-- ✅ SÉCURISÉ
UPDATE produits SET prix = 0 WHERE id = 10;

-- Vérifier d'abord
SELECT COUNT(*) FROM produits WHERE condition;
-- Puis mettre à jour
UPDATE produits SET ... WHERE condition;
```

---

**2. Utiliser RETURNING pour confirmer**

```sql
-- Voir ce qui a été modifié
DELETE FROM logs WHERE date < '2024-01-01'
RETURNING id, date, COUNT(*) OVER() AS total_supprime;
```

---

**3. Transactions pour cohérence**

```sql
-- ✅ BON : Transaction atomique
BEGIN;
UPDATE comptes SET solde = solde - 100 WHERE id = 1;
UPDATE comptes SET solde = solde + 100 WHERE id = 2;
COMMIT;

-- ❌ MAUVAIS : Sans transaction
UPDATE comptes SET solde = solde - 100 WHERE id = 1;
-- Si erreur ici, données incohérentes !
UPDATE comptes SET solde = solde + 100 WHERE id = 2;
```

---

**4. Utiliser COPY pour grandes quantités**

```sql
-- ✅ BON : COPY (très rapide)
COPY produits FROM 'import.csv' WITH CSV HEADER;

-- ❌ LENT : INSERT multiple pour millions de lignes
INSERT INTO produits VALUES (...), (...), ...;
```

---

**5. Préférer UPSERT à SELECT puis INSERT/UPDATE**

```sql
-- ❌ MAUVAIS : Race condition possible
SELECT * FROM cache WHERE key = 'x';
IF found THEN
    UPDATE cache SET value = 'y' WHERE key = 'x';
ELSE
    INSERT INTO cache VALUES ('x', 'y');

-- ✅ BON : Atomique
INSERT INTO cache VALUES ('x', 'y')
ON CONFLICT (key) DO UPDATE SET value = EXCLUDED.value;
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ INSERT : simple, multiple, depuis SELECT
✅ UPDATE : avec WHERE, calculs, FROM
✅ DELETE : avec WHERE, sous-requêtes
✅ RETURNING : récupérer les valeurs modifiées
✅ UPSERT : INSERT ON CONFLICT DO UPDATE
✅ TRUNCATE : vider rapidement une table
✅ COPY : import/export en masse
✅ Transactions pour la cohérence

---

# Questions ? 🙋

Des questions sur la manipulation de données ?

**À suivre** : Création et gestion de tables

