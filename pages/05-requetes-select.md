---
layout: new-section
routeAlias: 'requetes-select'
---

<a name="requetes-select" id="requetes-select"></a>

# Requêtes SELECT de base 🔍

Maîtrisons l'art d'interroger les données avec SELECT

---

# Plan du module 📋

- Structure de base SELECT
- Filtrage avec WHERE
- Tri avec ORDER BY
- Limitation avec LIMIT et OFFSET
- Opérateurs de comparaison
- Opérateurs logiques
- Pattern matching
- Requêtes avancées

---

# Structure d'une requête SELECT 📐

**SELECT = Votre baguette magique pour interroger les données ! 🪄**

**Syntaxe complète** (ne vous inquiétez pas, on va détailler !) :

```sql
SELECT colonnes        -- Ce que je veux voir
FROM table            -- Dans quelle table
WHERE condition       -- Avec quels filtres
GROUP BY colonnes     -- Comment regrouper
HAVING condition      -- Filtres sur les groupes
ORDER BY colonnes     -- Comment trier
LIMIT nombre          -- Combien de résultats
OFFSET nombre;        -- À partir de quel numéro
```

💡 **Rassurez-vous** : Vous n'utiliserez JAMAIS toutes ces parties en même temps !
- 90% du temps : juste SELECT + FROM + WHERE
- C'est comme une voiture : vous n'utilisez pas tous les boutons à chaque trajet !

---

**Comment PostgreSQL lit votre requête** 🤔

**ATTENTION : L'ordre d'exécution n'est PAS l'ordre d'écriture !**

Vous écrivez :
```sql
SELECT nom FROM utilisateurs WHERE age > 25;
```

Mais PostgreSQL lit dans cet ordre :

1️⃣ **FROM** utilisateurs = "Ok, je regarde la table utilisateurs"
2️⃣ **WHERE** age > 25 = "Je garde seulement ceux de plus de 25 ans"
3️⃣ **SELECT** nom = "J'affiche seulement la colonne nom"

*Analogie* : C'est comme faire ses courses :
1. Aller au supermarché (FROM)
2. Filtrer ce qui nous intéresse (WHERE)
3. Mettre dans le panier ce qu'on veut (SELECT)

💡 **Pourquoi c'est important ?** Ça explique pourquoi certaines requêtes ne fonctionnent pas !

---

# Sélection de colonnes 📊

**Toutes les colonnes**

```sql
-- Toutes les colonnes
SELECT * FROM utilisateurs;

-- ⚠️ Éviter en production : mauvaise performance
-- Préférer spécifier les colonnes nécessaires
```

---

**Colonnes spécifiques**

```sql
-- Colonnes explicites (recommandé)
SELECT nom, email FROM utilisateurs;

-- Plusieurs colonnes
SELECT id, nom, email, age FROM utilisateurs;

-- Avec alias
SELECT 
    id,
    nom AS nom_complet,
    email AS adresse_email,
    age AS annees
FROM utilisateurs;
```

---

**Expressions dans SELECT**

```sql
-- Calculs
SELECT 
    nom,
    age,
    age + 10 AS age_dans_10_ans
FROM utilisateurs;

-- Concaténation
SELECT 
    nom || ' (' || email || ')' AS contact
FROM utilisateurs;

-- Fonctions
SELECT 
    UPPER(nom) AS nom_majuscules,
    LENGTH(email) AS longueur_email
FROM utilisateurs;
```

---

# Filtrage avec WHERE 🎯

**WHERE = Le filtre à café de vos données ! ☕**

Imaginez une soirée : vous voulez inviter seulement les personnes de plus de 18 ans.
WHERE fait exactement ça : il filtre !

**Égalité exacte** = Chercher quelque chose de précis :
```sql
SELECT * FROM utilisateurs WHERE age = 30;
```
💡 *Traduction* : "Montre-moi SEULEMENT les personnes de 30 ans exactement"

---

**Inégalité** = Tout SAUF quelque chose :
```sql
SELECT * FROM utilisateurs WHERE age != 30;
-- OU
SELECT * FROM utilisateurs WHERE age <> 30;
```
💡 Les deux s'écrivent `!=` ou `<>` (c'est pareil !)
*Traduction* : "Tous SAUF les 30 ans"

---

**Comparaisons** = Plus grand, plus petit :
```sql
-- Plus grand que 30 (31, 32, 33...)
SELECT * FROM utilisateurs WHERE age > 30;

-- Plus grand ou égal à 30 (30, 31, 32...)
SELECT * FROM utilisateurs WHERE age >= 30;

-- Plus petit que 30 (29, 28, 27...)
SELECT * FROM utilisateurs WHERE age < 30;

-- Plus petit ou égal à 30 (30, 29, 28...)
SELECT * FROM utilisateurs WHERE age <= 30;
```

🎯 **Astuce mémo** :
- `>` = La bouche du crocodile mange le plus grand !
- `>=` = Inclut le nombre lui-même
- `<` = L'inverse
- `<=` = Inclut également le nombre

⚠️ **Erreur classique** :
```sql
-- ❌ FAUX
WHERE age = > 30  -- Espace entre = et >

-- ✅ CORRECT
WHERE age >= 30  -- Pas d'espace !
```

---

**Conditions multiples - AND** 🤝

**AND = Les DEUX conditions DOIVENT être vraies**

*Analogie* : Pour entrer en boîte de nuit, vous devez avoir 18+ ans **ET** avoir une carte d'identité
- 25 ans MAIS pas de carte → ❌ 
- Carte MAIS 16 ans → ❌ 
- 25 ans ET carte → ✅ 

```sql
-- Chercher les gens entre 25 et 35 ans
SELECT * FROM utilisateurs
WHERE age > 25 AND age < 35;
```

💡 *Traduction* : "Plus de 25 ET moins de 35"
- age = 30 → ✅ (les deux conditions OK)
- age = 20 → ❌ (pas plus de 25)
- age = 40 → ❌ (pas moins de 35)

---

**Plusieurs AND** 📝 :
```sql
SELECT * FROM utilisateurs
WHERE age > 25 
  AND age < 35 
  AND nom LIKE 'A%';
```

**TOUTES** les conditions doivent être vraies :
- ✅ Alice, 30 ans (3 conditions OK)
- ❌ Alice, 20 ans (âge KO)
- ❌ Bob, 30 ans (nom KO)

---

**Conditions multiples - OR** 🎭

**OR = AU MOINS UNE condition doit être vraie**

*Analogie* : Pour avoir une réduction au ciné : étudiant **OU** -18 ans **OU** sénior
- Il suffit d'UNE seule condition !

```sql
-- Tarif réduit : jeunes OU seniors
SELECT * FROM utilisateurs
WHERE age < 25 OR age > 40;
```

💡 *Traduction* : "Moins de 25 OU plus de 40"
- age = 20 → ✅ (moins de 25)
- age = 50 → ✅ (plus de 40)
- age = 30 → ❌ (aucune condition)

---

**Combiner AND et OR** ⚠️ **ATTENTION : PIÈGE !**

```sql
-- ✅ CORRECT (avec parenthèses)
SELECT * FROM utilisateurs
WHERE (age < 25 OR age > 40) 
  AND nom LIKE 'A%';
```

💡 *Traduction* : "(Jeune OU senior) ET nom commence par A"
- Alice, 20 ans → ✅
- Alice, 50 ans → ✅
- Bob, 20 ans → ❌ (nom ne commence pas par A)

🔴 **ERREUR FRÉQUENTE** (sans parenthèses) :
```sql
-- ❌ PAS CE QUE VOUS VOULEZ
WHERE age < 25 OR age > 40 AND nom LIKE 'A%';
```
Ça veut dire : "(age < 25) OU (age > 40 ET nom A%)"
➡️ **Toujours mettre des parenthèses avec AND/OR !**

---

**Opérateur NOT**

```sql
-- Inverse une condition
SELECT * FROM utilisateurs
WHERE NOT age = 30;

-- Équivalent à
SELECT * FROM utilisateurs
WHERE age != 30;

-- Avec IN
SELECT * FROM utilisateurs
WHERE NOT (age IN (25, 30, 35));
```

---

**Opérateur IN**

```sql
-- Vérifier si valeur dans une liste
SELECT * FROM utilisateurs
WHERE age IN (25, 30, 35);

-- Équivalent à
SELECT * FROM utilisateurs
WHERE age = 25 OR age = 30 OR age = 35;

-- Avec texte
SELECT * FROM utilisateurs
WHERE nom IN ('Alice', 'Bob', 'Charlie');
```

---

**Opérateur BETWEEN**

```sql
-- Entre deux valeurs (inclusif)
SELECT * FROM utilisateurs
WHERE age BETWEEN 25 AND 35;

-- Équivalent à
SELECT * FROM utilisateurs
WHERE age >= 25 AND age <= 35;

-- Avec dates
SELECT * FROM commandes
WHERE date_commande BETWEEN '2025-01-01' AND '2025-12-31';
```

---

**Tests NULL**

```sql
-- IS NULL : valeur est NULL
SELECT * FROM utilisateurs
WHERE telephone IS NULL;

-- IS NOT NULL : valeur n'est pas NULL
SELECT * FROM utilisateurs
WHERE telephone IS NOT NULL;

-- ❌ ERREUR : ne pas utiliser = NULL
SELECT * FROM utilisateurs
WHERE telephone = NULL;  -- Ne fonctionne pas !
```

---

# Pattern matching avec LIKE 🔤

**Wildcards**

- `%` : N'importe quelle séquence (0 ou plusieurs caractères)
- `_` : Un seul caractère

```sql
-- Commence par 'A'
SELECT * FROM utilisateurs WHERE nom LIKE 'A%';

-- Finit par 'son'
SELECT * FROM utilisateurs WHERE nom LIKE '%son';

-- Contient 'mar'
SELECT * FROM utilisateurs WHERE nom LIKE '%mar%';
```

---

**Exemples avec LIKE**

```sql
-- Deuxième lettre est 'a'
SELECT * FROM utilisateurs WHERE nom LIKE '_a%';

-- Exactement 5 caractères
SELECT * FROM utilisateurs WHERE nom LIKE '_____';

-- Emails Gmail
SELECT * FROM utilisateurs WHERE email LIKE '%@gmail.com';

-- Codes postaux parisiens
SELECT * FROM adresses WHERE code_postal LIKE '75%';
```

---

**ILIKE : insensible à la casse**

```sql
-- LIKE est sensible à la casse
SELECT * FROM utilisateurs WHERE nom LIKE 'alice%';  -- Ne trouve pas "Alice"

-- ILIKE est insensible à la casse (PostgreSQL spécifique)
SELECT * FROM utilisateurs WHERE nom ILIKE 'alice%';  -- Trouve "Alice", "alice", "ALICE"
```

---

**NOT LIKE**

```sql
-- Exclure un pattern
SELECT * FROM utilisateurs
WHERE email NOT LIKE '%@gmail.com';

-- Exclure plusieurs patterns
SELECT * FROM utilisateurs
WHERE email NOT LIKE '%@gmail.com'
  AND email NOT LIKE '%@yahoo.com';
```

---

# Expressions régulières 🔧

**Opérateurs ~ et ~***

```sql
-- ~ : sensible à la casse
SELECT * FROM utilisateurs WHERE nom ~ '^A';  -- Commence par A majuscule

-- ~* : insensible à la casse
SELECT * FROM utilisateurs WHERE nom ~* '^a';  -- Commence par a ou A

-- !~ : ne correspond pas (sensible)
SELECT * FROM utilisateurs WHERE nom !~ '^A';

-- !~* : ne correspond pas (insensible)
SELECT * FROM utilisateurs WHERE nom !~* '^a';
```

---

**Exemples d'expressions régulières**

```sql
-- Emails valides (simplifié)
SELECT * FROM utilisateurs
WHERE email ~* '^[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$';

-- Numéros de téléphone français
SELECT * FROM clients
WHERE telephone ~ '^0[1-9][0-9]{8}$';

-- Codes postaux (5 chiffres)
SELECT * FROM adresses
WHERE code_postal ~ '^\d{5}$';
```

---

# Tri avec ORDER BY 📈

**Tri ascendant (défaut)**

```sql
-- Tri croissant sur l'âge
SELECT * FROM utilisateurs ORDER BY age;

-- Explicitement ascendant
SELECT * FROM utilisateurs ORDER BY age ASC;

-- Tri sur texte (alphabétique)
SELECT * FROM utilisateurs ORDER BY nom;
```

---

**Tri descendant**

```sql
-- Tri décroissant
SELECT * FROM utilisateurs ORDER BY age DESC;

-- Les plus jeunes d'abord
SELECT nom, age FROM utilisateurs ORDER BY age ASC;

-- Les plus âgés d'abord
SELECT nom, age FROM utilisateurs ORDER BY age DESC;
```

---

**Tri sur plusieurs colonnes**

```sql
-- Tri sur age puis nom
SELECT * FROM utilisateurs
ORDER BY age DESC, nom ASC;

-- D'abord par ville, puis par nom
SELECT nom, ville FROM utilisateurs
ORDER BY ville, nom;

-- Avec directions différentes
SELECT * FROM produits
ORDER BY categorie ASC, prix DESC;
```

---

**Tri avec NULL**

```sql
-- NULLS FIRST : NULL en premier
SELECT * FROM utilisateurs
ORDER BY telephone NULLS FIRST;

-- NULLS LAST : NULL en dernier (défaut)
SELECT * FROM utilisateurs
ORDER BY telephone NULLS LAST;

-- Combinaison
SELECT * FROM utilisateurs
ORDER BY age DESC NULLS LAST, nom ASC;
```

---

**Tri sur expressions**

```sql
-- Tri sur calcul
SELECT nom, prix, prix * 1.2 AS prix_ttc
FROM produits
ORDER BY prix_ttc DESC;

-- Tri sur fonction
SELECT nom, email
FROM utilisateurs
ORDER BY LENGTH(nom) DESC;

-- Tri sur CASE
SELECT nom, age
FROM utilisateurs
ORDER BY CASE WHEN age < 30 THEN 1 ELSE 2 END, age;
```

---

# Limitation avec LIMIT 🔢

**LIMIT : limiter le nombre de résultats**

```sql
-- Les 5 premiers résultats
SELECT * FROM utilisateurs LIMIT 5;

-- Les 10 premiers par ordre d'âge
SELECT * FROM utilisateurs
ORDER BY age DESC
LIMIT 10;
```

---

**OFFSET : sauter des résultats**

```sql
-- Sauter les 5 premiers, prendre les 10 suivants
SELECT * FROM utilisateurs
ORDER BY id
LIMIT 10 OFFSET 5;

-- Pagination : page 1 (10 résultats)
SELECT * FROM produits LIMIT 10 OFFSET 0;

-- Page 2
SELECT * FROM produits LIMIT 10 OFFSET 10;

-- Page 3
SELECT * FROM produits LIMIT 10 OFFSET 20;
```

---

**Calcul de pagination**

```sql
-- Formule : OFFSET = (numero_page - 1) * resultats_par_page

-- Page 1, 20 résultats par page
SELECT * FROM articles LIMIT 20 OFFSET 0;

-- Page 2
SELECT * FROM articles LIMIT 20 OFFSET 20;

-- Page 5
SELECT * FROM articles LIMIT 20 OFFSET 80;
```

---

# Opérateurs de comparaison avancés 🎲

**Comparaison de texte**

```sql
-- Égalité stricte
SELECT * FROM produits WHERE nom = 'Laptop';

-- Comparaison insensible à la casse
SELECT * FROM produits WHERE LOWER(nom) = LOWER('laptop');

-- Comparaison avec ILIKE
SELECT * FROM produits WHERE nom ILIKE 'laptop';
```

---

**Comparaison de dates**

```sql
-- Date exacte
SELECT * FROM commandes
WHERE date_commande = '2025-01-15';

-- Avant une date
SELECT * FROM commandes
WHERE date_commande < '2025-01-01';

-- Période
SELECT * FROM commandes
WHERE date_commande >= '2025-01-01'
  AND date_commande < '2025-02-01';
```

---

**Fonctions de date dans WHERE**

```sql
-- Aujourd'hui
SELECT * FROM evenements
WHERE date_evenement = CURRENT_DATE;

-- Cette semaine
SELECT * FROM commandes
WHERE date_commande >= CURRENT_DATE - INTERVAL '7 days';

-- Ce mois
SELECT * FROM ventes
WHERE EXTRACT(MONTH FROM date_vente) = EXTRACT(MONTH FROM CURRENT_DATE)
  AND EXTRACT(YEAR FROM date_vente) = EXTRACT(YEAR FROM CURRENT_DATE);
```

---

**Comparaison avec ANY et ALL**

```sql
-- ANY : au moins une valeur de la liste
SELECT * FROM produits
WHERE prix > ANY(ARRAY[10, 20, 30]);  -- prix > 10

-- ALL : toutes les valeurs de la liste
SELECT * FROM produits
WHERE prix > ALL(ARRAY[10, 20, 30]);  -- prix > 30

-- Avec sous-requête (nous verrons plus tard)
SELECT * FROM produits p1
WHERE prix > ANY(SELECT prix FROM produits WHERE categorie = 'Informatique');
```

---

# DISTINCT : valeurs uniques 🎯

**Éliminer les doublons**

```sql
-- Toutes les villes (sans doublons)
SELECT DISTINCT ville FROM utilisateurs;

-- Toutes les combinaisons ville/pays uniques
SELECT DISTINCT ville, pays FROM utilisateurs;

-- Compter les valeurs uniques
SELECT COUNT(DISTINCT ville) AS nb_villes FROM utilisateurs;
```

---

**DISTINCT ON (PostgreSQL spécifique)**

```sql
-- Première occurrence par ville
SELECT DISTINCT ON (ville) ville, nom, age
FROM utilisateurs
ORDER BY ville, age DESC;

-- Produit le moins cher par catégorie
SELECT DISTINCT ON (categorie) categorie, nom, prix
FROM produits
ORDER BY categorie, prix ASC;
```

---

# Agrégations de base 📊

**Fonctions d'agrégation courantes**

```sql
-- Compter les lignes
SELECT COUNT(*) FROM utilisateurs;

-- Compter les valeurs non-NULL
SELECT COUNT(telephone) FROM utilisateurs;

-- Somme
SELECT SUM(prix) FROM commandes;
```

---

```sql
-- Moyenne
SELECT AVG(age) FROM utilisateurs;

-- Min et Max
SELECT MIN(age) AS age_min, MAX(age) AS age_max
FROM utilisateurs;

-- Plusieurs agrégations
SELECT 
    COUNT(*) AS total,
    AVG(age) AS age_moyen,
    MIN(age) AS age_min,
    MAX(age) AS age_max
FROM utilisateurs;
```

---

# Expressions conditionnelles CASE 🔀

**CASE simple**

```sql
SELECT 
    nom,
    age,
    CASE
        WHEN age < 18 THEN 'Mineur'
        WHEN age >= 18 AND age < 65 THEN 'Adulte'
        ELSE 'Senior'
    END AS categorie_age
FROM utilisateurs;
```

---

**CASE avec plusieurs conditions**

```sql
SELECT 
    nom,
    prix,
    CASE
        WHEN prix < 10 THEN 'Pas cher'
        WHEN prix >= 10 AND prix < 50 THEN 'Moyen'
        WHEN prix >= 50 AND prix < 100 THEN 'Cher'
        ELSE 'Très cher'
    END AS gamme_prix
FROM produits;
```

---

**CASE dans ORDER BY**

```sql
-- Trier par priorité personnalisée
SELECT nom, statut
FROM taches
ORDER BY
    CASE statut
        WHEN 'urgent' THEN 1
        WHEN 'important' THEN 2
        WHEN 'normal' THEN 3
        ELSE 4
    END;
```

---

**CASE dans UPDATE (aperçu)**

```sql
-- Augmentation différenciée selon le prix
UPDATE produits
SET prix = CASE
    WHEN prix < 20 THEN prix * 1.10
    WHEN prix >= 20 AND prix < 100 THEN prix * 1.05
    ELSE prix * 1.02
END;
```

---

# Fonctions de chaînes 🔤

**Manipulation de texte**

```sql
-- Concaténation
SELECT nom || ' ' || prenom AS nom_complet FROM utilisateurs;
SELECT CONCAT(nom, ' ', prenom) AS nom_complet FROM utilisateurs;

-- Majuscules/Minuscules
SELECT UPPER(nom) FROM utilisateurs;
SELECT LOWER(email) FROM utilisateurs;
SELECT INITCAP('hello world');  -- Hello World
```

---

**Extraction et recherche**

```sql
-- Longueur
SELECT nom, LENGTH(nom) AS longueur FROM utilisateurs;

-- Sous-chaîne
SELECT SUBSTRING(nom FROM 1 FOR 3) FROM utilisateurs;
SELECT SUBSTRING(nom, 1, 3) FROM utilisateurs;  -- Équivalent

-- Position
SELECT POSITION('@' IN email) FROM utilisateurs;
```

---

**Nettoyage de texte**

```sql
-- Supprimer espaces
SELECT TRIM('  hello  ');           -- 'hello'
SELECT LTRIM('  hello');            -- 'hello'
SELECT RTRIM('hello  ');            -- 'hello'

-- Remplacer
SELECT REPLACE(nom, 'a', 'X') FROM utilisateurs;

-- Remplir
SELECT LPAD(id::TEXT, 5, '0') FROM produits;  -- '00042'
SELECT RPAD(nom, 10, '.') FROM produits;      -- 'Laptop....'
```

---

# Fonctions numériques 🔢

**Fonctions mathématiques**

```sql
-- Arrondi
SELECT ROUND(3.14159, 2);           -- 3.14
SELECT CEIL(3.14);                  -- 4
SELECT FLOOR(3.99);                 -- 3

-- Valeur absolue
SELECT ABS(-42);                    -- 42

-- Puissance et racine
SELECT POWER(2, 3);                 -- 8
SELECT SQRT(16);                    -- 4
```

---

**Fonctions sur les prix**

```sql
-- Prix TTC (TVA 20%)
SELECT 
    nom,
    prix AS prix_ht,
    ROUND(prix * 1.20, 2) AS prix_ttc
FROM produits;

-- Remise de 15%
SELECT 
    nom,
    prix,
    ROUND(prix * 0.85, 2) AS prix_remise
FROM produits;
```

---

# Sous-requêtes simples 🔄

**Sous-requête dans WHERE**

```sql
-- Utilisateurs plus âgés que la moyenne
SELECT nom, age
FROM utilisateurs
WHERE age > (SELECT AVG(age) FROM utilisateurs);

-- Produits plus chers que le produit 'Laptop'
SELECT nom, prix
FROM produits
WHERE prix > (SELECT prix FROM produits WHERE nom = 'Laptop');
```

---

**Sous-requête avec IN**

```sql
-- Clients ayant passé au moins une commande
SELECT nom
FROM clients
WHERE id IN (SELECT client_id FROM commandes);

-- Produits jamais commandés
SELECT nom
FROM produits
WHERE id NOT IN (SELECT produit_id FROM lignes_commande);
```

---

# Exemples pratiques complets 🎯

**Exemple 1 : E-commerce**

```sql
-- Top 10 des produits les plus chers disponibles
SELECT 
    nom,
    categorie,
    prix,
    stock
FROM produits
WHERE stock > 0
ORDER BY prix DESC
LIMIT 10;
```

---

**Exemple 2 : Blog**

```sql
-- Articles publiés ce mois avec leurs auteurs
SELECT 
    a.titre,
    u.nom AS auteur,
    a.date_publication
FROM articles a
JOIN utilisateurs u ON a.auteur_id = u.id
WHERE a.publie = true
  AND EXTRACT(MONTH FROM a.date_publication) = EXTRACT(MONTH FROM CURRENT_DATE)
  AND EXTRACT(YEAR FROM a.date_publication) = EXTRACT(YEAR FROM CURRENT_DATE)
ORDER BY a.date_publication DESC;
```

---

**Exemple 3 : Gestion d'événements**

```sql
-- Événements à venir dans les 30 prochains jours
SELECT 
    titre,
    date_evenement,
    lieu,
    EXTRACT(DAY FROM date_evenement - CURRENT_DATE) AS jours_restants
FROM evenements
WHERE date_evenement > CURRENT_DATE
  AND date_evenement <= CURRENT_DATE + INTERVAL '30 days'
ORDER BY date_evenement;
```

---

# Optimisation des requêtes SELECT 🚀

**Bonnes pratiques**

1. **Sélectionner uniquement les colonnes nécessaires**
```sql
-- ❌ Mauvais
SELECT * FROM utilisateurs;

-- ✅ Bon
SELECT id, nom, email FROM utilisateurs;
```

---

2. **Utiliser des index appropriés**
```sql
-- Créer un index sur une colonne souvent filtrée
CREATE INDEX idx_utilisateurs_email ON utilisateurs(email);

-- Requête qui bénéficie de l'index
SELECT * FROM utilisateurs WHERE email = 'alice@example.com';
```

---

3. **Limiter les résultats**
```sql
-- ❌ Récupérer toutes les lignes sans raison
SELECT * FROM logs;

-- ✅ Limiter pour l'interface utilisateur
SELECT * FROM logs ORDER BY date DESC LIMIT 100;
```

---

4. **Utiliser WHERE plutôt que HAVING quand possible**
```sql
-- ❌ Moins efficace
SELECT ville, COUNT(*)
FROM utilisateurs
GROUP BY ville
HAVING ville = 'Paris';

-- ✅ Plus efficace
SELECT ville, COUNT(*)
FROM utilisateurs
WHERE ville = 'Paris'
GROUP BY ville;
```

---

# Exercice pratique 🎯

**Créer et interroger une base de données de films**

1. Créer une table `films` avec : id, titre, realisateur, annee, note, duree_minutes
2. Insérer au moins 10 films
3. Écrire des requêtes pour :
   - Films sortis après 2010
   - Films de plus de 2h
   - Films avec note > 8/10
   - Top 5 des meilleurs films

---

**Solution - Partie 1**

```sql
CREATE TABLE films (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(200) NOT NULL,
    realisateur VARCHAR(100),
    annee INTEGER,
    note NUMERIC(3,1) CHECK (note >= 0 AND note <= 10),
    duree_minutes INTEGER
);
```

---

**Solution - Partie 2**

```sql
INSERT INTO films (titre, realisateur, annee, note, duree_minutes) VALUES
    ('Inception', 'Christopher Nolan', 2010, 8.8, 148),
    ('The Matrix', 'Wachowski', 1999, 8.7, 136),
    ('Interstellar', 'Christopher Nolan', 2014, 8.6, 169),
    ('Pulp Fiction', 'Quentin Tarantino', 1994, 8.9, 154),
    ('Forrest Gump', 'Robert Zemeckis', 1994, 8.8, 142),
    ('The Dark Knight', 'Christopher Nolan', 2008, 9.0, 152),
    ('Fight Club', 'David Fincher', 1999, 8.8, 139),
    ('The Godfather', 'Francis Ford Coppola', 1972, 9.2, 175),
    ('The Shawshank Redemption', 'Frank Darabont', 1994, 9.3, 142),
    ('Django Unchained', 'Quentin Tarantino', 2012, 8.4, 165);
```

---

**Solution - Partie 3**

```sql
-- Films sortis après 2010
SELECT titre, annee FROM films
WHERE annee > 2010
ORDER BY annee;

-- Films de plus de 2h (120 minutes)
SELECT titre, duree_minutes FROM films
WHERE duree_minutes > 120
ORDER BY duree_minutes DESC;
```

---

```sql
-- Films avec note > 8/10
SELECT titre, note FROM films
WHERE note > 8.0
ORDER BY note DESC;

-- Top 5 des meilleurs films
SELECT titre, realisateur, note
FROM films
ORDER BY note DESC
LIMIT 5;
```

---

**Requêtes bonus**

```sql
-- Films de Christopher Nolan
SELECT titre, annee, note
FROM films
WHERE realisateur = 'Christopher Nolan'
ORDER BY annee;

-- Durée moyenne des films par décennie
SELECT 
    (annee / 10) * 10 AS decennie,
    COUNT(*) AS nombre_films,
    ROUND(AVG(duree_minutes)) AS duree_moyenne
FROM films
GROUP BY decennie
ORDER BY decennie;
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ SELECT avec WHERE, ORDER BY, LIMIT, OFFSET
✅ Opérateurs : =, <>, <, >, <=, >=, IN, BETWEEN, LIKE
✅ Opérateurs logiques : AND, OR, NOT
✅ Pattern matching : LIKE, ILIKE, expressions régulières
✅ DISTINCT pour valeurs uniques
✅ Fonctions d'agrégation : COUNT, SUM, AVG, MIN, MAX
✅ CASE pour logique conditionnelle
✅ Fonctions de chaînes et numériques

---

# Questions ? 🙋

Des questions sur les requêtes SELECT ?

**À suivre** : Jointures et relations entre tables

