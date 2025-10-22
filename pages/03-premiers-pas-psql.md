---
layout: new-section
routeAlias: 'premiers-pas-psql'
---

<a name="premiers-pas-psql" id="premiers-pas-psql"></a>

# Premiers pas avec PostgreSQL 🚀

Découvrons les bases de PostgreSQL et créons notre première base de données

---

# Plan du module 📋

- Connexion à PostgreSQL
- Créer une base de données
- Créer des tables simples
- Insérer des données
- Consulter les données
- Concepts de base

---

# Se connecter à PostgreSQL 🔌

**C'est comme se connecter à WiFi ! 📡**

Avant de travailler, il faut "se brancher" à PostgreSQL.

**psql** = Votre télécommande pour parler à PostgreSQL
- Comme une console de jeu où vous tapez des commandes
- C'est l'outil officiel, gratuit et toujours disponible

**Option 1 : Connexion la plus simple** 🏠
```bash
psql -U postgres
```
*Explication* : 
- `psql` = lancer l'outil
- `-U postgres` = se connecter en tant qu'utilisateur "postgres" (le super-admin par défaut)
- 💡 C'est comme se connecter en administrateur sur votre ordinateur

**Option 2 : Se connecter à UNE base précise** 🎯
```bash
psql -U postgres -d mabase
```
- `-d mabase` = ouvrir directement la base de données "mabase"
- *Analogie* : Ouvrir directement un dossier spécifique au lieu du bureau

**Option 3 : Se connecter à distance** 🌐
```bash
psql -U postgres -h 192.168.1.100 -d mabase
```
- `-h 192.168.1.100` = l'adresse IP du serveur PostgreSQL
- Comme appeler quelqu'un sur Zoom plutôt que lui parler en face à face

---

**Avec URL de connexion**

```bash
# Format : postgresql://utilisateur:motdepasse@hote:port/base
psql postgresql://postgres:motdepasse@localhost:5432/postgres
```

---

# Commandes psql essentielles 💻

**Navigation et information**

```sql
-- Lister toutes les bases de données
\l

-- Se connecter à une base de données
\c mabase

-- Afficher la base de données courante
SELECT current_database();
```

---

```sql
-- Lister les tables de la base courante
\dt

-- Décrire la structure d'une table
\d matable

-- Lister tous les schémas
\dn

-- Lister les utilisateurs/rôles
\du
```

---

```sql
-- Afficher les commandes psql disponibles
\?

-- Afficher l'aide SQL
\h

-- Aide sur une commande spécifique
\h CREATE TABLE

-- Quitter psql
\q
```

---

# Créer votre première base de données 🎯

**C'est comme créer un nouveau classeur Excel ! 📁**

Une **base de données** = un grand dossier qui va contenir toutes vos tables (feuilles)

**Version ultra-simple** (pour débuter) :
```sql
CREATE DATABASE ma_premiere_base;
```

💡 **C'est tout !** Vous venez de créer votre première base de données !

---

**Version avec options** (pour les curieux) :
```sql
CREATE DATABASE formation
    WITH
    OWNER = postgres           -- Qui en est propriétaire
    ENCODING = 'UTF8'          -- Pour gérer les accents (é, à, ç)
    LC_COLLATE = 'fr_FR.UTF-8' -- Tri à la française
    LC_CTYPE = 'fr_FR.UTF-8';  -- Caractères français
```

⚠️ **Ne vous inquiétez pas** : La version simple suffit à 99% du temps !

🎯 **En pratique** : 
- `ma_premiere_base` = nom de votre base (choisissez ce que vous voulez)
- Comme créer un nouveau dossier "Mes Documents" sur votre ordinateur

---

**Se connecter à la nouvelle base**

```sql
-- Dans psql
\c formation

-- Vérifier la connexion
SELECT current_database();
```

---

# Schémas PostgreSQL 📐

**Qu'est-ce qu'un schéma ?**

Un schéma est un espace de noms qui contient des objets (tables, vues, fonctions, etc.)

- Schéma par défaut : `public`
- Permet d'organiser les objets
- Évite les conflits de noms

---

**Créer et utiliser des schémas**

```sql
-- Créer un schéma
CREATE SCHEMA ventes;

-- Créer une table dans un schéma
CREATE TABLE ventes.produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100)
);

-- Lister les schémas
\dn
```

---

```sql
-- Utiliser un schéma par défaut
SET search_path TO ventes, public;

-- Voir le search_path
SHOW search_path;

-- Supprimer un schéma
DROP SCHEMA ventes CASCADE;  -- CASCADE supprime aussi les objets
```

---

# Créer votre première table 📊

**Une table = une feuille Excel avec des colonnes ! 📋**

Imaginez un carnet d'adresses :
- Chaque ligne = une personne
- Chaque colonne = une information (nom, email, etc.)

**Créons une table "utilisateurs"** :

```sql
CREATE TABLE utilisateurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    age INTEGER,
    date_inscription TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Décryptage ligne par ligne** 🔍 :

📌 `id SERIAL PRIMARY KEY`
- `id` = Numéro automatique pour chaque personne
- *Analogie* : Comme votre numéro de sécurité sociale
- PostgreSQL l'incrémente automatiquement (1, 2, 3...)

📝 `nom VARCHAR(100) NOT NULL`
- `VARCHAR(100)` = Texte de maximum 100 caractères
- `NOT NULL` = OBLIGATOIRE (on ne peut pas laisser vide)
- *Analogie* : Vous ne pouvez pas avoir une personne sans nom !

📧 `email VARCHAR(255) UNIQUE NOT NULL`
- `UNIQUE` = Pas de doublon (chaque email est unique)
- *Analogie* : Impossible d'avoir 2 comptes avec le même email

🎂 `age INTEGER`
- `INTEGER` = Nombre entier
- Pas de `NOT NULL` = OPTIONNEL (on peut le laisser vide)

📅 `date_inscription TIMESTAMP DEFAULT CURRENT_TIMESTAMP`
- `TIMESTAMP` = Date + heure précise
- `DEFAULT CURRENT_TIMESTAMP` = Se remplit automatiquement avec la date d'aujourd'hui
- *Analogie* : Comme un tampon "date d'entrée" automatique

💡 **Résumé** : Cette table peut stocker des utilisateurs avec leur nom, email, âge et date d'inscription !

---

**Explication de la structure**

- `id SERIAL` : Colonne auto-incrémentée
- `PRIMARY KEY` : Identifiant unique
- `VARCHAR(n)` : Chaîne de caractères avec longueur max
- `NOT NULL` : Valeur obligatoire
- `UNIQUE` : Valeur unique dans la table
- `DEFAULT` : Valeur par défaut

---

**Voir la structure de la table**

```sql
-- Description détaillée
\d utilisateurs

-- Structure plus complète
\d+ utilisateurs

-- Informations système
SELECT column_name, data_type, character_maximum_length
FROM information_schema.columns
WHERE table_name = 'utilisateurs';
```

---

# Insérer des données 📝

**Remplir votre tableau ! ✏️**

C'est comme ajouter une ligne dans Excel !

**Méthode recommandée** (claire et sûre) :
```sql
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Alice Dupont', 'alice@example.com', 30);
```

**Explication mot à mot** 📖 :
- `INSERT INTO utilisateurs` = "J'ajoute dans la table utilisateurs"
- `(nom, email, age)` = "Je vais remplir ces colonnes précises"
- `VALUES (...)` = "Voici les valeurs"

💡 **Astuce** : Remarquez qu'on ne donne PAS l'`id` ni la `date_inscription`
- L'`id` est automatique (SERIAL)
- La `date_inscription` a une valeur par défaut (DEFAULT)

---

**Insérer plusieurs personnes d'un coup** 🚀 :
```sql
INSERT INTO utilisateurs (nom, email, age) VALUES
    ('Alice Dupont', 'alice@example.com', 30),
    ('Bob Martin', 'bob@example.com', 25),
    ('Charlie Leroy', 'charlie@example.com', 28);
```

🎯 **Pratique** : 1 seule commande = 3 utilisateurs ajoutés !

⚠️ **Erreur courante** : Essayer d'ajouter deux fois le même email
```sql
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Alice2', 'alice@example.com', 25);
-- ❌ ERREUR ! Email déjà utilisé (UNIQUE constraint)
```

---

**Insérer plusieurs lignes**

```sql
INSERT INTO utilisateurs (nom, email, age) VALUES
    ('Charlie Durand', 'charlie@example.com', 28),
    ('Diana Leroy', 'diana@example.com', 32),
    ('Édouard Blanc', 'edouard@example.com', 45),
    ('Fatima Benali', 'fatima@example.com', 27);
```

---

**Récupérer les valeurs insérées**

```sql
-- RETURNING : retourne les valeurs insérées
INSERT INTO utilisateurs (nom, email, age)
VALUES ('George Petit', 'george@example.com', 35)
RETURNING *;

-- Retourner uniquement l'ID
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Henri Moreau', 'henri@example.com', 40)
RETURNING id;
```

---

# Consulter les données 🔍

**Regarder ce qu'il y a dans votre tableau ! 👀**

C'est THE commande la plus utilisée en SQL !

**Voir TOUT** 🌟 :
```sql
SELECT * FROM utilisateurs;
```

*Explication* :
- `SELECT` = "Montre-moi..."
- `*` = "... TOUT" (toutes les colonnes)
- `FROM utilisateurs` = "... dans la table utilisateurs"
- 💡 C'est comme ouvrir un fichier Excel et tout voir

---

**Voir seulement certaines colonnes** 🎯 :
```sql
SELECT nom, email FROM utilisateurs;
```

*Pourquoi faire ça ?*
- Plus rapide (moins de données à afficher)
- Plus clair (on ne voit que ce qui nous intéresse)
- *Analogie* : Masquer des colonnes dans Excel

---

**Limiter le nombre de résultats** 🔢 :
```sql
SELECT * FROM utilisateurs LIMIT 5;
```

💡 **Super utile** quand vous avez 1 million de lignes !
- `LIMIT 5` = "Montre-moi seulement les 5 premières"
- *Analogie* : Voir un aperçu au lieu de tout télécharger

⚠️ **Astuce de pro** : TOUJOURS utiliser LIMIT au début pour tester !
```sql
-- ✅ BON : Tester d'abord avec LIMIT
SELECT * FROM gros_tableau LIMIT 10;

-- ❌ DANGEREUX : Sans LIMIT sur une énorme table
SELECT * FROM gros_tableau;  -- Peut prendre 10 minutes !
```

---

**Filtrer avec WHERE**

```sql
-- Condition simple
SELECT * FROM utilisateurs WHERE age > 30;

-- Plusieurs conditions (ET)
SELECT * FROM utilisateurs WHERE age > 25 AND age < 35;

-- Plusieurs conditions (OU)
SELECT * FROM utilisateurs WHERE age < 25 OR age > 40;
```

---

```sql
-- Recherche de texte
SELECT * FROM utilisateurs WHERE nom LIKE 'A%';  -- Commence par A
SELECT * FROM utilisateurs WHERE email LIKE '%@example.com';

-- IN : valeurs dans une liste
SELECT * FROM utilisateurs WHERE age IN (25, 30, 35);

-- BETWEEN : entre deux valeurs
SELECT * FROM utilisateurs WHERE age BETWEEN 25 AND 35;
```

---

**Trier les résultats**

```sql
-- Tri ascendant (par défaut)
SELECT * FROM utilisateurs ORDER BY age;

-- Tri descendant
SELECT * FROM utilisateurs ORDER BY age DESC;

-- Tri sur plusieurs colonnes
SELECT * FROM utilisateurs ORDER BY age DESC, nom ASC;
```

---

**DISTINCT : valeurs uniques**

```sql
-- Voir tous les âges différents
SELECT DISTINCT age FROM utilisateurs ORDER BY age;

-- Compter les valeurs uniques
SELECT COUNT(DISTINCT age) FROM utilisateurs;
```

---

# Modifier des données ✏️

**UPDATE : mettre à jour des lignes**

```sql
-- Mettre à jour un utilisateur
UPDATE utilisateurs
SET age = 31
WHERE nom = 'Alice Dupont';

-- Mettre à jour plusieurs colonnes
UPDATE utilisateurs
SET age = 26, email = 'bob.martin@example.com'
WHERE nom = 'Bob Martin';
```

---

```sql
-- Mettre à jour avec calcul
UPDATE utilisateurs
SET age = age + 1
WHERE age < 30;

-- Retourner les lignes modifiées
UPDATE utilisateurs
SET age = 28
WHERE nom = 'Charlie Durand'
RETURNING *;
```

---

# Supprimer des données 🗑️

**DELETE : supprimer des lignes**

```sql
-- Supprimer un utilisateur spécifique
DELETE FROM utilisateurs WHERE nom = 'Henri Moreau';

-- Supprimer avec condition
DELETE FROM utilisateurs WHERE age < 25;

-- Retourner les lignes supprimées
DELETE FROM utilisateurs WHERE age > 50 RETURNING *;
```

---

**⚠️ ATTENTION : Suppression totale**

```sql
-- Ceci supprime TOUTES les lignes !
DELETE FROM utilisateurs;

-- Mieux : TRUNCATE (plus rapide, réinitialise les séquences)
TRUNCATE TABLE utilisateurs;
```

---

# Exemple complet : Blog simple 📰

**Créer les tables**

```sql
-- Table des auteurs
CREATE TABLE auteurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    bio TEXT,
    date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

```sql
-- Table des articles
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    auteur_id INTEGER REFERENCES auteurs(id),
    titre VARCHAR(200) NOT NULL,
    contenu TEXT NOT NULL,
    publie BOOLEAN DEFAULT false,
    date_publication TIMESTAMP,
    date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

**Insérer des données**

```sql
-- Insérer des auteurs
INSERT INTO auteurs (nom, email, bio) VALUES
    ('Alice Martin', 'alice@blog.com', 'Développeuse passionnée'),
    ('Bob Durand', 'bob@blog.com', 'Expert PostgreSQL')
RETURNING id;
```

---

```sql
-- Insérer des articles (supposons que Alice a l'ID 1)
INSERT INTO articles (auteur_id, titre, contenu, publie, date_publication) VALUES
    (1, 'Introduction à PostgreSQL', 'PostgreSQL est un excellent SGBD...', true, NOW()),
    (1, 'Les index dans PostgreSQL', 'Les index permettent d''optimiser...', true, NOW()),
    (2, 'Transactions ACID', 'ACID signifie Atomicité, Cohérence...', false, NULL);
```

---

**Requêtes sur le blog**

```sql
-- Articles publiés
SELECT titre, date_publication
FROM articles
WHERE publie = true
ORDER BY date_publication DESC;

-- Nombre d'articles par auteur
SELECT auteur_id, COUNT(*) as nb_articles
FROM articles
GROUP BY auteur_id;
```

---

```sql
-- Détails d'un article avec son auteur (jointure simple)
SELECT 
    articles.titre,
    articles.contenu,
    auteurs.nom as auteur,
    articles.date_publication
FROM articles
JOIN auteurs ON articles.auteur_id = auteurs.id
WHERE articles.id = 1;
```

---

# Types de commentaires 💬

**Commentaires SQL**

```sql
-- Ceci est un commentaire sur une ligne

/* Ceci est un commentaire
   sur plusieurs lignes */

SELECT nom, email  -- Commentaire en fin de ligne
FROM utilisateurs;
```

---

**Commentaires sur les objets**

```sql
-- Commenter une table
COMMENT ON TABLE utilisateurs IS 'Table des utilisateurs du système';

-- Commenter une colonne
COMMENT ON COLUMN utilisateurs.email IS 'Adresse email unique de l''utilisateur';

-- Voir les commentaires
\d+ utilisateurs
```

---

# Gestion des NULL 🔍

**NULL représente l'absence de valeur**

```sql
-- Insérer avec des NULL
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Test User', 'test@example.com', NULL);

-- Rechercher les NULL
SELECT * FROM utilisateurs WHERE age IS NULL;

-- Rechercher les non-NULL
SELECT * FROM utilisateurs WHERE age IS NOT NULL;
```

---

**Fonctions pour gérer NULL**

```sql
-- COALESCE : retourne la première valeur non-NULL
SELECT nom, COALESCE(age, 0) as age
FROM utilisateurs;

-- NULLIF : retourne NULL si les deux valeurs sont égales
SELECT NULLIF(age, 0) FROM utilisateurs;
```

---

# Alias de colonnes et tables 🏷️

**Alias de colonnes**

```sql
-- AS pour renommer une colonne dans les résultats
SELECT nom AS nom_complet, email AS adresse_email
FROM utilisateurs;

-- AS est optionnel
SELECT nom nom_complet, email adresse_email
FROM utilisateurs;
```

---

**Alias de tables**

```sql
-- Pratique pour les noms longs
SELECT u.nom, u.email
FROM utilisateurs AS u
WHERE u.age > 30;

-- Indispensable pour les auto-jointures (que nous verrons plus tard)
SELECT u1.nom, u2.nom
FROM utilisateurs u1, utilisateurs u2
WHERE u1.age = u2.age AND u1.id != u2.id;
```

---

# Opérateurs utiles 🔧

**Opérateurs de comparaison**

```sql
-- Égalité
SELECT * FROM utilisateurs WHERE age = 30;

-- Inégalité
SELECT * FROM utilisateurs WHERE age != 30;
SELECT * FROM utilisateurs WHERE age <> 30;  -- Synonyme

-- Comparaisons
SELECT * FROM utilisateurs WHERE age > 30;
SELECT * FROM utilisateurs WHERE age >= 30;
SELECT * FROM utilisateurs WHERE age < 30;
SELECT * FROM utilisateurs WHERE age <= 30;
```

---

**Opérateurs logiques**

```sql
-- AND : toutes les conditions doivent être vraies
SELECT * FROM utilisateurs WHERE age > 25 AND age < 35;

-- OR : au moins une condition doit être vraie
SELECT * FROM utilisateurs WHERE age < 25 OR age > 40;

-- NOT : inverse la condition
SELECT * FROM utilisateurs WHERE NOT (age > 30);
```

---

**Opérateurs de pattern matching**

```sql
-- LIKE : recherche avec wildcards
-- % : n'importe quelle séquence de caractères
-- _ : un seul caractère
SELECT * FROM utilisateurs WHERE nom LIKE 'A%';        -- Commence par A
SELECT * FROM utilisateurs WHERE nom LIKE '%martin%';  -- Contient martin
SELECT * FROM utilisateurs WHERE nom LIKE '_ob%';      -- 2e lettre = ob

-- ILIKE : LIKE insensible à la casse (PostgreSQL spécifique)
SELECT * FROM utilisateurs WHERE nom ILIKE 'alice%';
```

---

# Informations système 📊

**Métadonnées de la base**

```sql
-- Version de PostgreSQL
SELECT version();

-- Date et heure actuelle
SELECT NOW(), CURRENT_DATE, CURRENT_TIME;

-- Utilisateur courant
SELECT current_user, session_user;
```

---

```sql
-- Taille de la base de données
SELECT pg_size_pretty(pg_database_size(current_database()));

-- Liste de toutes les tables avec leur taille
SELECT 
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) as size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;
```

---

# Bonnes pratiques 👍

**Conventions de nommage**

- **Tables** : minuscules, pluriel (ex: `utilisateurs`, `articles`)
- **Colonnes** : minuscules, snake_case (ex: `date_creation`, `nom_complet`)
- **Clés primaires** : généralement `id`
- **Clés étrangères** : `table_id` (ex: `auteur_id`)

---

**Toujours spécifier les colonnes dans INSERT**

```sql
-- ✅ BON : explicite et maintenable
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Test', 'test@example.com', 25);

-- ❌ MAUVAIS : fragile si la structure change
INSERT INTO utilisateurs
VALUES (DEFAULT, 'Test', 'test@example.com', 25, DEFAULT);
```

---

**Utiliser des transactions pour les modifications multiples**

```sql
-- Commencer une transaction
BEGIN;

UPDATE utilisateurs SET age = 31 WHERE id = 1;
UPDATE articles SET publie = true WHERE auteur_id = 1;

-- Valider les changements
COMMIT;

-- Ou annuler en cas d'erreur
-- ROLLBACK;
```

---

# Erreurs courantes et solutions 🐛

**Erreur : relation does not exist**

```sql
-- Erreur
SELECT * FROM Utilisateurs;
-- ERROR: relation "utilisateurs" does not exist

-- PostgreSQL met les noms en minuscules par défaut
SELECT * FROM utilisateurs;  -- ✅
```

---

**Erreur : duplicate key value**

```sql
-- Si email doit être UNIQUE
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Test', 'alice@example.com', 25);
-- ERROR: duplicate key value violates unique constraint

-- Solution : utiliser un email différent ou UPDATE
```

---

**Erreur : null value in column violates not-null constraint**

```sql
-- Si nom est NOT NULL
INSERT INTO utilisateurs (email, age)
VALUES ('test@example.com', 25);
-- ERROR: null value in column "nom" violates not-null constraint

-- Solution : fournir une valeur pour nom
INSERT INTO utilisateurs (nom, email, age)
VALUES ('Test User', 'test@example.com', 25);  -- ✅
```

---

# Exercice pratique 🎯

**Créer une base de données de bibliothèque**

1. Créer une base de données `bibliotheque`
2. Créer une table `livres` avec :
   - id (clé primaire)
   - titre (texte, obligatoire)
   - auteur (texte, obligatoire)
   - annee_publication (entier)
   - isbn (texte, unique)
   - disponible (booléen, défaut true)

---

3. Insérer au moins 5 livres différents
4. Écrire des requêtes pour :
   - Afficher tous les livres disponibles
   - Trouver les livres publiés après 2000
   - Compter le nombre total de livres
   - Trouver les livres d'un auteur spécifique

---

**Solution - Partie 1**

```sql
-- 1. Créer la base de données
CREATE DATABASE bibliotheque;
\c bibliotheque

-- 2. Créer la table
CREATE TABLE livres (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(200) NOT NULL,
    auteur VARCHAR(100) NOT NULL,
    annee_publication INTEGER,
    isbn VARCHAR(20) UNIQUE,
    disponible BOOLEAN DEFAULT true
);
```

---

**Solution - Partie 2**

```sql
-- 3. Insérer des livres
INSERT INTO livres (titre, auteur, annee_publication, isbn) VALUES
    ('1984', 'George Orwell', 1949, '978-0451524935'),
    ('Le Seigneur des Anneaux', 'J.R.R. Tolkien', 1954, '978-0544003415'),
    ('Harry Potter à l''école des sorciers', 'J.K. Rowling', 1997, '978-2070584628'),
    ('Le Petit Prince', 'Antoine de Saint-Exupéry', 1943, '978-0156012195'),
    ('Da Vinci Code', 'Dan Brown', 2003, '978-2709624992');
```

---

**Solution - Partie 3**

```sql
-- 4. Requêtes

-- Livres disponibles
SELECT * FROM livres WHERE disponible = true;

-- Livres publiés après 2000
SELECT titre, auteur, annee_publication
FROM livres
WHERE annee_publication > 2000;
```

---

```sql
-- Nombre total de livres
SELECT COUNT(*) as nombre_livres FROM livres;

-- Livres d'un auteur spécifique
SELECT titre, annee_publication
FROM livres
WHERE auteur = 'J.K. Rowling';
```

---

# Commandes de nettoyage 🧹

**Supprimer des objets**

```sql
-- Supprimer une table
DROP TABLE IF EXISTS utilisateurs;

-- Supprimer une base de données (depuis une autre base)
DROP DATABASE IF EXISTS test_db;

-- Supprimer un schéma et tout son contenu
DROP SCHEMA IF EXISTS ventes CASCADE;
```

---

**Vider une table sans la supprimer**

```sql
-- DELETE : supprime les lignes une par une
DELETE FROM utilisateurs;

-- TRUNCATE : plus rapide, réinitialise les séquences
TRUNCATE TABLE utilisateurs;

-- TRUNCATE CASCADE : vide aussi les tables liées
TRUNCATE TABLE auteurs CASCADE;
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Connexion avec psql et commandes de base (\l, \dt, \d, \q)
✅ CREATE DATABASE et CREATE TABLE
✅ INSERT, SELECT, UPDATE, DELETE
✅ WHERE, ORDER BY, LIMIT, DISTINCT
✅ Opérateurs : =, <>, >, <, LIKE, IN, BETWEEN
✅ Gestion des NULL avec IS NULL et COALESCE
✅ Alias de colonnes et tables
✅ Bonnes pratiques de nommage

---

# Questions ? 🙋

Des questions sur ces premiers pas avec PostgreSQL ?

**À suivre** : Types de données en détail

