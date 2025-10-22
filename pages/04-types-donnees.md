---
layout: new-section
routeAlias: 'types-donnees'
---

<a name="types-donnees" id="types-donnees"></a>

# Types de données PostgreSQL 📊

Découvrons la richesse des types de données disponibles dans PostgreSQL

---

# Plan du module 📋

- Types numériques
- Types caractères
- Types date et heure
- Types booléens
- Types UUID
- Types JSON
- Types tableaux
- Types personnalisés

---

# Types numériques entiers 🔢

**C'est comme choisir la taille de votre valise ! 🧳**

Selon combien de "choses" vous voulez ranger, vous choisissez la bonne taille.

| Type | Taille | Plage | Usage typique |
|------|--------|-------|---------------|
| SMALLINT | 2 octets | -32 768 à 32 767 | Âges, scores |
| INTEGER | 4 octets | -2 milliards à +2 milliards | Usage général |
| BIGINT | 8 octets | -9 trillions à +9 trillions | Très gros nombres |

💡 **Comment choisir ?**
- **SMALLINT** : Pour des petits nombres (ex: âge, note sur 20)
- **INTEGER** : Le choix par défaut (99% des cas)
- **BIGINT** : Pour des ÉNORMES nombres (ex: vues YouTube, population mondiale)

---

**Exemples concrets** 📝 :

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,      -- INTEGER auto-incrémenté (1, 2, 3...)
    stock SMALLINT,             -- Stock : jamais 1 million en stock !
    vues BIGINT DEFAULT 0,      -- Vues : peut être énorme !
    prix_centimes INTEGER       -- Prix : 1999 = 19,99€
);
```

🎯 **Traduction** :
- `id SERIAL` = Numéro automatique pour chaque produit
- `stock SMALLINT` = Le stock ne dépassera pas 32 000
- `vues BIGINT` = Les vues peuvent être énormes (millions)
- `prix_centimes INTEGER` = Prix en centimes (évite les problèmes de virgule)

⚠️ **Erreur fréquente** : Utiliser BIGINT partout "au cas où"
- ❌ Gaspillage de mémoire !
- ✅ Utilisez INTEGER par défaut, BIGINT seulement si nécessaire

---

**Types auto-incrémentés**

```sql
-- SERIAL = INTEGER auto-incrémenté
CREATE TABLE test1 (id SERIAL);

-- BIGSERIAL = BIGINT auto-incrémenté
CREATE TABLE test2 (id BIGSERIAL);

-- SMALLSERIAL = SMALLINT auto-incrémenté
CREATE TABLE test3 (id SMALLSERIAL);
```

---

# Types numériques décimaux 💰

**Nombres à virgule**

| Type | Description | Usage |
|------|-------------|-------|
| NUMERIC(p,s) | Précision exacte | Valeurs monétaires |
| DECIMAL(p,s) | Identique à NUMERIC | Alias |
| REAL | 4 octets, 6 décimales | Scientifique |
| DOUBLE PRECISION | 8 octets, 15 décimales | Scientifique |

---

**Exemples avec NUMERIC**

```sql
CREATE TABLE finances (
    id SERIAL PRIMARY KEY,
    prix NUMERIC(10,2),        -- 10 chiffres total, 2 après virgule
    taux NUMERIC(5,4),         -- Ex: 0.1234 (12.34%)
    montant DECIMAL(12,2)      -- Alias de NUMERIC
);

INSERT INTO finances (prix, taux, montant) VALUES
    (99.99, 0.1234, 1500.50);
```

---

**NUMERIC vs REAL/DOUBLE PRECISION**

```sql
-- NUMERIC : précision exacte
SELECT 0.1 + 0.2 = 0.3;  -- true avec NUMERIC

-- REAL/DOUBLE : approximation
SELECT 0.1::REAL + 0.2::REAL = 0.3::REAL;  -- peut être false

-- Utilisez NUMERIC pour les calculs financiers !
```

---

# Types caractères 📝

**Stocker du texte : 3 options ! ✍️**

**Imaginez que vous achetez des boîtes pour ranger du texte** 📦

| Type | Description | Usage | Analogie |
|------|-------------|-------|----------|
| CHAR(n) | Taille FIXE, complété par des espaces | Codes (ex: code postal) | Boîte rigide |
| VARCHAR(n) | Taille VARIABLE (max n) | Noms, emails | Boîte souple |
| TEXT | Taille ILLIMITÉE | Articles, descriptions | Carton extensible |

💡 **Lequel choisir ?**

**CHAR(n)** - Longueur EXACTE 
```sql
code_postal CHAR(5)  -- Toujours 5 caractères : "75001"
```
- Utile pour les codes fixes (code pays, plaques d'immatriculation)
- *Piège* : Si vous mettez "ABC" dans CHAR(5), ça devient "ABC  " (avec espaces)

**VARCHAR(n)** - Longueur VARIABLE (max n)
```sql
nom VARCHAR(100)  -- Max 100 caractères
```
- **Le plus utilisé !** Pour noms, emails, titres
- Économise de l'espace : "Alice" prend 5 caractères, pas 100

**TEXT** - Longueur ILLIMITÉE
```sql
description TEXT  -- Peut contenir un roman entier !
```
- Pour les grands textes : articles, commentaires, descriptions
- Aucune limite de taille

---

**Exemples et différences**

```sql
CREATE TABLE textes (
    code CHAR(5),              -- Toujours 5 caractères
    nom VARCHAR(100),          -- Maximum 100 caractères
    description TEXT           -- Pas de limite
);

INSERT INTO textes VALUES
    ('ABC', 'Test', 'Une longue description...');

-- CHAR complète avec des espaces
-- 'ABC  ' (3 caractères + 2 espaces)
```

---

**Quel type choisir ?**

- **CHAR(n)** : Codes fixes (codes postaux, ISO)
- **VARCHAR(n)** : Texte avec limite (noms, emails)
- **TEXT** : Contenu long sans limite (articles, commentaires)

```sql
CREATE TABLE utilisateurs (
    code_postal CHAR(5),           -- '75001'
    email VARCHAR(255),            -- Limite raisonnable
    bio TEXT                       -- Pas de limite
);
```

---

# Types date et heure ⏰

**Types disponibles**

| Type | Description | Exemple |
|------|-------------|---------|
| DATE | Date uniquement | 2025-01-15 |
| TIME | Heure uniquement | 14:30:00 |
| TIMESTAMP | Date + heure | 2025-01-15 14:30:00 |
| TIMESTAMPTZ | Date + heure + timezone | 2025-01-15 14:30:00+01 |
| INTERVAL | Durée | 3 days 04:30:00 |

---

**Exemples d'utilisation**

```sql
CREATE TABLE evenements (
    id SERIAL PRIMARY KEY,
    date_evenement DATE,
    heure_debut TIME,
    date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    date_modification TIMESTAMPTZ DEFAULT NOW(),
    duree INTERVAL
);
```

---

**Insérer des dates et heures**

```sql
INSERT INTO evenements (date_evenement, heure_debut, duree) VALUES
    ('2025-03-15', '14:30:00', '2 hours'),
    ('2025-04-20', '09:00', INTERVAL '1 day 3 hours'),
    (CURRENT_DATE, CURRENT_TIME, '30 minutes');
```

---

**Fonctions de date et heure**

```sql
-- Date et heure actuelles
SELECT CURRENT_DATE;           -- 2025-01-15
SELECT CURRENT_TIME;           -- 14:30:00.123456+01
SELECT CURRENT_TIMESTAMP;      -- 2025-01-15 14:30:00.123456
SELECT NOW();                  -- Identique à CURRENT_TIMESTAMP
```

---

**Extraire des parties de date**

```sql
SELECT EXTRACT(YEAR FROM TIMESTAMP '2025-03-15 14:30:00');   -- 2025
SELECT EXTRACT(MONTH FROM DATE '2025-03-15');                -- 3
SELECT EXTRACT(DAY FROM DATE '2025-03-15');                  -- 15
SELECT EXTRACT(HOUR FROM TIMESTAMP '2025-03-15 14:30:00');   -- 14

-- Alternatives
SELECT DATE_PART('year', DATE '2025-03-15');                 -- 2025
```

---

**Calculs avec dates**

```sql
-- Ajouter/soustraire des intervalles
SELECT DATE '2025-01-15' + INTERVAL '1 month';     -- 2025-02-15
SELECT DATE '2025-01-15' - INTERVAL '7 days';      -- 2025-01-08
SELECT NOW() + INTERVAL '2 hours 30 minutes';

-- Différence entre dates
SELECT DATE '2025-12-31' - DATE '2025-01-01';      -- 364 jours
SELECT AGE(DATE '2025-01-15', DATE '2000-01-01');  -- 25 years 14 days
```

---

**Formater des dates**

```sql
-- TO_CHAR : formater une date
SELECT TO_CHAR(NOW(), 'DD/MM/YYYY');               -- 15/01/2025
SELECT TO_CHAR(NOW(), 'DD Mon YYYY');              -- 15 Jan 2025
SELECT TO_CHAR(NOW(), 'HH24:MI:SS');               -- 14:30:00
SELECT TO_CHAR(NOW(), 'Day, DD Month YYYY');       -- Wednesday, 15 January 2025
```

---

**Parser des dates**

```sql
-- TO_DATE : convertir une chaîne en date
SELECT TO_DATE('15/01/2025', 'DD/MM/YYYY');

-- TO_TIMESTAMP : convertir en timestamp
SELECT TO_TIMESTAMP('15-01-2025 14:30:00', 'DD-MM-YYYY HH24:MI:SS');
```

---

# Type booléen ✔️

**TRUE, FALSE ou NULL**

```sql
CREATE TABLE taches (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(200),
    terminee BOOLEAN DEFAULT false,
    importante BOOLEAN
);

INSERT INTO taches (titre, terminee, importante) VALUES
    ('Finir le rapport', true, true),
    ('Appeler le client', false, true),
    ('Prendre un café', false, false);
```

---

**Requêtes avec booléens**

```sql
-- Sélectionner les tâches terminées
SELECT * FROM taches WHERE terminee = true;
-- Ou plus simple
SELECT * FROM taches WHERE terminee;

-- Tâches non terminées
SELECT * FROM taches WHERE terminee = false;
-- Ou
SELECT * FROM taches WHERE NOT terminee;
```

---

**Valeurs acceptées pour les booléens**

```sql
-- TRUE peut être : TRUE, 't', 'true', 'y', 'yes', 'on', '1'
-- FALSE peut être : FALSE, 'f', 'false', 'n', 'no', 'off', '0'

INSERT INTO taches (titre, terminee) VALUES
    ('Test 1', 't'),
    ('Test 2', 'yes'),
    ('Test 3', '1');
```

---

# Type UUID 🔑

**Identifiants uniques universels**

```sql
-- Activer l'extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE sessions (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id INTEGER,
    token UUID DEFAULT uuid_generate_v4(),
    date_creation TIMESTAMP DEFAULT NOW()
);
```

---

**Insérer et utiliser des UUID**

```sql
-- Insertion automatique
INSERT INTO sessions (user_id) VALUES (1);

-- Insertion manuelle
INSERT INTO sessions (id, user_id) VALUES
    ('a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', 2);

-- Recherche
SELECT * FROM sessions
WHERE id = 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11';
```

---

# Types JSON 📦

**JSON vs JSONB**

| Type | Description | Performance |
|------|-------------|-------------|
| JSON | Texte JSON brut | Stockage rapide |
| JSONB | JSON binaire | Requêtes rapides ✅ |

**Utilisez JSONB sauf raison particulière !**

---

**Créer une table avec JSON**

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    caracteristiques JSONB,
    metadata JSON
);
```

---

**Insérer des données JSON**

```sql
INSERT INTO produits (nom, caracteristiques) VALUES
    ('Laptop', '{"marque": "Dell", "ram": "16GB", "prix": 899.99}'),
    ('Souris', '{"marque": "Logitech", "type": "sans fil", "dpi": 1600}'),
    ('Clavier', '{"marque": "Corsair", "type": "mécanique", "switches": "Cherry MX"}');
```

---

**Requêter des données JSON**

```sql
-- Opérateur -> : retourne JSON
SELECT caracteristiques->'marque' FROM produits;

-- Opérateur ->> : retourne TEXT
SELECT caracteristiques->>'marque' FROM produits;

-- Accès imbriqué
SELECT caracteristiques->'specs'->>'cpu' FROM produits;
```

---

**Filtrer sur des données JSON**

```sql
-- Rechercher une valeur spécifique
SELECT * FROM produits
WHERE caracteristiques->>'marque' = 'Dell';

-- Vérifier l'existence d'une clé
SELECT * FROM produits
WHERE caracteristiques ? 'ram';

-- Opérateur @> : contient
SELECT * FROM produits
WHERE caracteristiques @> '{"marque": "Logitech"}';
```

---

**Fonctions JSON**

```sql
-- jsonb_each : décomposer en lignes
SELECT jsonb_each(caracteristiques) FROM produits;

-- jsonb_object_keys : lister les clés
SELECT DISTINCT jsonb_object_keys(caracteristiques) FROM produits;

-- jsonb_array_length : longueur d'un tableau
SELECT jsonb_array_length('[1,2,3,4,5]'::jsonb);  -- 5
```

---

**Modifier des données JSON**

```sql
-- Ajouter/modifier une clé
UPDATE produits
SET caracteristiques = caracteristiques || '{"couleur": "noir"}'
WHERE nom = 'Laptop';

-- Supprimer une clé
UPDATE produits
SET caracteristiques = caracteristiques - 'prix'
WHERE nom = 'Souris';
```

---

# Types tableaux 📋

**Colonnes de type tableau**

```sql
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(200),
    tags TEXT[],                    -- Tableau de texte
    notes INTEGER[],                -- Tableau d'entiers
    prix NUMERIC(10,2)[]           -- Tableau de décimaux
);
```

---

**Insérer des tableaux**

```sql
INSERT INTO articles (titre, tags, notes) VALUES
    ('PostgreSQL', ARRAY['database', 'sql', 'postgres'], ARRAY[5, 4, 5]),
    ('Python', '{"programming", "scripting", "web"}', '{5, 5, 4}');
    -- Syntaxe ARRAY ou '{}'
```

---

**Requêter des tableaux**

```sql
-- Accéder à un élément (index commence à 1 !)
SELECT tags[1] FROM articles;

-- Vérifier si un élément existe
SELECT * FROM articles WHERE 'sql' = ANY(tags);

-- Vérifier si contient tous les éléments
SELECT * FROM articles WHERE tags @> ARRAY['database', 'sql'];

-- Longueur du tableau
SELECT titre, array_length(tags, 1) FROM articles;
```

---

**Fonctions sur les tableaux**

```sql
-- array_append : ajouter un élément
UPDATE articles
SET tags = array_append(tags, 'tutorial')
WHERE titre = 'PostgreSQL';

-- array_remove : supprimer un élément
UPDATE articles
SET tags = array_remove(tags, 'postgres')
WHERE titre = 'PostgreSQL';

-- unnest : transformer tableau en lignes
SELECT unnest(tags) FROM articles;
```

---

# Types énumérés (ENUM) 🎯

**Créer un type énuméré**

```sql
-- Définir un type ENUM
CREATE TYPE statut_commande AS ENUM (
    'en_attente',
    'confirme',
    'expedie',
    'livre',
    'annule'
);
```

---

**Utiliser un ENUM**

```sql
CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    numero_commande VARCHAR(50),
    statut statut_commande DEFAULT 'en_attente',
    date_commande TIMESTAMP DEFAULT NOW()
);

INSERT INTO commandes (numero_commande, statut) VALUES
    ('CMD-001', 'confirme'),
    ('CMD-002', 'en_attente');
```

---

**Avantages des ENUM**

- Validation automatique des valeurs
- Stockage efficace
- Auto-complétion dans les outils
- Documentation intégrée

```sql
-- Ceci échoue : valeur invalide
INSERT INTO commandes (numero_commande, statut)
VALUES ('CMD-003', 'traitement');  -- ERROR
```

---

**Voir et modifier les ENUM**

```sql
-- Lister les valeurs d'un ENUM
SELECT enum_range(NULL::statut_commande);

-- Ajouter une valeur
ALTER TYPE statut_commande ADD VALUE 'retourne';

-- Renommer une valeur (PostgreSQL 10+)
ALTER TYPE statut_commande RENAME VALUE 'annule' TO 'annulee';
```

---

# Types géométriques 📐

**Types de base**

```sql
CREATE TABLE geometrie (
    id SERIAL PRIMARY KEY,
    point_data POINT,
    ligne_data LINE,
    cercle_data CIRCLE,
    boite_data BOX,
    polygone_data POLYGON
);
```

---

**Exemples de géométrie**

```sql
-- Point : (x, y)
INSERT INTO geometrie (point_data) VALUES
    (POINT(10, 20));

-- Cercle : <(x, y), rayon>
INSERT INTO geometrie (cercle_data) VALUES
    (CIRCLE(POINT(0, 0), 5));

-- Box : (x1, y1), (x2, y2)
INSERT INTO geometrie (boite_data) VALUES
    (BOX(POINT(0, 0), POINT(10, 10)));
```

---

**Note : PostGIS pour la géométrie avancée**

Pour des opérations géospatiales sérieuses, utilisez l'extension **PostGIS** :

```sql
CREATE EXTENSION postgis;

-- Types avancés : GEOMETRY, GEOGRAPHY
-- Fonctions : ST_Distance, ST_Contains, ST_Intersects, etc.
```

---

# Types réseau 🌐

**Types pour adresses réseau**

```sql
CREATE TABLE serveurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    adresse_ip INET,              -- IPv4 ou IPv6
    sous_reseau CIDR,             -- Notation CIDR
    adresse_mac MACADDR           -- Adresse MAC
);
```

---

**Exemples d'utilisation**

```sql
INSERT INTO serveurs (nom, adresse_ip, sous_reseau, adresse_mac) VALUES
    ('Serveur Web', '192.168.1.100', '192.168.1.0/24', '08:00:2b:01:02:03'),
    ('Serveur DB', '10.0.0.50', '10.0.0.0/8', '08:00:2b:01:02:04');

-- Vérifier si IP dans un sous-réseau
SELECT * FROM serveurs
WHERE adresse_ip << INET '192.168.1.0/24';
```

---

# Types binaires 🔢

**Stocker des données binaires**

```sql
CREATE TABLE fichiers (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(255),
    contenu BYTEA,               -- Données binaires
    taille INTEGER
);

-- Insérer des données binaires (en hexadécimal)
INSERT INTO fichiers (nom, contenu, taille) VALUES
    ('test.bin', '\xDEADBEEF'::bytea, 4);
```

---

# Types personnalisés 🛠️

**Créer un type composite**

```sql
-- Type adresse
CREATE TYPE adresse AS (
    rue VARCHAR(200),
    ville VARCHAR(100),
    code_postal CHAR(5),
    pays VARCHAR(100)
);
```

---

**Utiliser un type composite**

```sql
CREATE TABLE clients (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    adresse_facturation adresse,
    adresse_livraison adresse
);

INSERT INTO clients (nom, adresse_facturation) VALUES
    ('Alice Martin', ROW('12 rue de la Paix', 'Paris', '75001', 'France'));
```

---

**Accéder aux champs d'un type composite**

```sql
-- Accéder à un champ
SELECT (adresse_facturation).ville FROM clients;

-- Ou avec alias
SELECT c.adresse_facturation.ville FROM clients c;
```

---

# Types spéciaux 🎲

**Autres types utiles**

```sql
CREATE TABLE types_speciaux (
    id SERIAL PRIMARY KEY,
    plage_entiers INT4RANGE,      -- Plage d'entiers
    plage_dates DATERANGE,        -- Plage de dates
    plage_timestamps TSRANGE,     -- Plage de timestamps
    donnees_xml XML,              -- Données XML
    recherche_texte TSVECTOR     -- Recherche full-text
);
```

---

**Exemple avec les plages (ranges)**

```sql
-- Plage d'entiers : [1, 10) (inclus 1, exclu 10)
INSERT INTO types_speciaux (plage_entiers) VALUES
    ('[1,10)');

-- Vérifier si une valeur est dans la plage
SELECT * FROM types_speciaux
WHERE plage_entiers @> 5;  -- true

-- Chevauchement de plages
SELECT '[1,5)'::INT4RANGE && '[3,7)'::INT4RANGE;  -- true
```

---

# Conversion de types (casting) 🔄

**Syntaxes de conversion**

```sql
-- Syntaxe CAST
SELECT CAST('123' AS INTEGER);

-- Syntaxe PostgreSQL :: (recommandée)
SELECT '123'::INTEGER;

-- Fonction de conversion
SELECT TO_NUMBER('123', '999');
```

---

**Conversions courantes**

```sql
-- Texte vers nombre
SELECT '42'::INTEGER;
SELECT '3.14'::NUMERIC(5,2);

-- Nombre vers texte
SELECT 42::TEXT;
SELECT 3.14::VARCHAR;

-- Date vers texte
SELECT NOW()::TEXT;
SELECT DATE '2025-01-15'::VARCHAR;
```

---

**Conversions de dates**

```sql
-- Texte vers date
SELECT '2025-01-15'::DATE;
SELECT TO_DATE('15/01/2025', 'DD/MM/YYYY');

-- Timestamp vers date
SELECT NOW()::DATE;

-- Date vers timestamp
SELECT DATE '2025-01-15'::TIMESTAMP;
```

---

# Valeurs NULL et types 🔍

**NULL avec différents types**

```sql
CREATE TABLE test_null (
    nombre INTEGER,
    texte TEXT,
    date_val DATE,
    json_val JSONB
);

-- NULL est valide pour tous les types (sauf NOT NULL)
INSERT INTO test_null VALUES (NULL, NULL, NULL, NULL);
```

---

**Fonctions pour gérer NULL**

```sql
-- COALESCE : première valeur non-NULL
SELECT COALESCE(NULL, NULL, 'valeur', 'autre');  -- 'valeur'

-- NULLIF : retourne NULL si égalité
SELECT NULLIF(10, 10);  -- NULL
SELECT NULLIF(10, 20);  -- 10

-- IS NULL / IS NOT NULL
SELECT * FROM test_null WHERE nombre IS NULL;
```

---

# Taille des types et performance 💾

**Tailles de stockage**

| Type | Taille |
|------|--------|
| BOOLEAN | 1 octet |
| SMALLINT | 2 octets |
| INTEGER | 4 octets |
| BIGINT | 8 octets |
| NUMERIC | variable |
| TEXT/VARCHAR | 1 octet + longueur |
| DATE | 4 octets |
| TIMESTAMP | 8 octets |
| UUID | 16 octets |

---

**Choisir le bon type**

```sql
-- ❌ Trop gros pour un âge
CREATE TABLE personnes (age BIGINT);

-- ✅ Approprié
CREATE TABLE personnes (age SMALLINT);

-- ❌ Limitation inutile si le contenu est variable
CREATE TABLE articles (contenu VARCHAR(1000));

-- ✅ Pas de limite
CREATE TABLE articles (contenu TEXT);
```

---

# Contraintes de types 🔒

**CHECK avec types**

```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    prix NUMERIC(10,2) CHECK (prix > 0),
    stock INTEGER CHECK (stock >= 0),
    reduction NUMERIC(3,2) CHECK (reduction BETWEEN 0 AND 1)
);

-- Ceci échoue
INSERT INTO produits (nom, prix, stock) VALUES
    ('Test', -10, 5);  -- ERROR: prix doit être > 0
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Types numériques : INTEGER, BIGINT, NUMERIC, REAL
✅ Types texte : CHAR, VARCHAR, TEXT
✅ Types temporels : DATE, TIME, TIMESTAMP, INTERVAL
✅ BOOLEAN : true/false/null
✅ UUID : identifiants uniques
✅ JSONB : données structurées flexibles
✅ Tableaux : colonnes multi-valeurs
✅ ENUM : valeurs contraintes

---

# Exercice pratique 🎯

**Créer une base de e-commerce**

1. Table `clients` avec :
   - ID, nom, email, date_inscription
2. Table `produits` avec :
   - ID, nom, description, prix (NUMERIC), stock, caractéristiques (JSONB)
3. Table `commandes` avec :
   - ID, client_id, statut (ENUM), date_commande, tags (tableau)

---

**Solution - Partie 1**

```sql
-- Type ENUM pour le statut
CREATE TYPE statut_cmd AS ENUM ('nouvelle', 'traitee', 'expediee', 'livree');

-- Table clients
CREATE TABLE clients (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    date_inscription TIMESTAMP DEFAULT NOW()
);
```

---

**Solution - Partie 2**

```sql
-- Table produits
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(200) NOT NULL,
    description TEXT,
    prix NUMERIC(10,2) NOT NULL CHECK (prix > 0),
    stock INTEGER NOT NULL DEFAULT 0 CHECK (stock >= 0),
    caracteristiques JSONB
);
```

---

**Solution - Partie 3**

```sql
-- Table commandes
CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    client_id INTEGER REFERENCES clients(id),
    statut statut_cmd DEFAULT 'nouvelle',
    date_commande TIMESTAMP DEFAULT NOW(),
    tags TEXT[]
);
```

---

**Données de test**

```sql
-- Insérer des clients
INSERT INTO clients (nom, email) VALUES
    ('Alice Martin', 'alice@example.com'),
    ('Bob Durand', 'bob@example.com');

-- Insérer des produits
INSERT INTO produits (nom, prix, stock, caracteristiques) VALUES
    ('Laptop', 899.99, 10, '{"marque": "Dell", "ram": "16GB"}'),
    ('Souris', 29.99, 50, '{"marque": "Logitech", "type": "sans fil"}');
```

---

```sql
-- Insérer des commandes
INSERT INTO commandes (client_id, statut, tags) VALUES
    (1, 'nouvelle', ARRAY['urgent', 'prioritaire']),
    (2, 'traitee', ARRAY['standard']);
```

---

# Questions ? 🙋

Des questions sur les types de données ?

**À suivre** : Requêtes SELECT avancées

