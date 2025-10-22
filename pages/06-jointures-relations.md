---
layout: new-section
routeAlias: 'jointures-relations'
---

<a name="jointures-relations" id="jointures-relations"></a>

# Jointures et Relations 🔗

Maîtrisons l'art de combiner des données de plusieurs tables

---

# Plan du module 📋

- Clés primaires et étrangères
- INNER JOIN
- LEFT JOIN (LEFT OUTER JOIN)
- RIGHT JOIN (RIGHT OUTER JOIN)
- FULL JOIN (FULL OUTER JOIN)
- CROSS JOIN
- SELF JOIN
- Jointures multiples
- Bonnes pratiques

---

# Relations entre tables 🔄

**Pourquoi plusieurs tables ? 🤔**

*Analogie* : C'est comme organiser des livres dans une bibliothèque !

**Mauvaise méthode** ❌ : Tout dans un énorme cahier
```
Livre: "Harry Potter" | Auteur: "J.K. Rowling" | Email: "jk@..." 
Livre: "Philosopher Stone" | Auteur: "J.K. Rowling" | Email: "jk@..."
```
→ On répète "J.K. Rowling" et son email partout !

**Bonne méthode** ✅ : Séparer en 2 cahiers
```
Cahier AUTEURS:
  ID: 1 | Nom: "J.K. Rowling" | Email: "jk@..."

Cahier LIVRES:
  Titre: "Harry Potter" | Auteur_ID: 1
  Titre: "Philosopher Stone" | Auteur_ID: 1
```
→ Les infos de l'auteur sont écrites UNE SEULE FOIS !

---

**3 types de relations**

**1️⃣ One-to-One (1:1)** - Un pour un uniquement
- *Exemple* : Une personne = Un passeport
- *En SQL* : Utilisateurs ↔ Profils détaillés

**2️⃣ One-to-Many (1:N)** - Un pour plusieurs ⭐ **LE PLUS COURANT**
- *Exemple* : Un auteur peut écrire PLUSIEURS livres
- *En SQL* : Auteurs (1) ↔ Livres (N)

**3️⃣ Many-to-Many (N:N)** - Plusieurs pour plusieurs
- *Exemple* : Un étudiant suit PLUSIEURS cours, un cours a PLUSIEURS étudiants
- *En SQL* : Nécessite une table de liaison (on verra ça !)

---

# Clés primaires et étrangères 🔑

**Les clés = Le système de liens entre les tables ! 🔗**

**Clé primaire (PRIMARY KEY)** = La carte d'identité unique

*Analogie* : Comme votre numéro de sécurité sociale - il n'y en a qu'UN pour vous !

```sql
CREATE TABLE auteurs (
    id SERIAL PRIMARY KEY,        -- ✨ Clé primaire
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE
);
```

💡 **PRIMARY KEY** signifie :
- Chaque auteur a UN numéro unique (1, 2, 3...)
- Pas de doublon possible
- Jamais NULL (vide)
- *Pourquoi ?* Pour pouvoir dire "l'auteur numéro 1" sans ambiguïté !

---

**Clé étrangère (FOREIGN KEY)** = Le lien vers une autre table

*Analogie* : C'est comme noter "Écrit par l'auteur numéro 1" au lieu de réécrire toutes les infos de l'auteur !

```sql
CREATE TABLE livres (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(200) NOT NULL,
    auteur_id INTEGER REFERENCES auteurs(id),  -- 🔗 Clé étrangère
    prix NUMERIC(10,2)
);
```

💡 **FOREIGN KEY (auteur_id)** signifie :
- `auteur_id` = Le numéro de l'auteur qui a écrit ce livre
- Il DOIT exister dans la table `auteurs` !
- *Protection* : Impossible de mettre auteur_id = 999 si l'auteur 999 n'existe pas

---

**Version avec nom** (pour mieux organiser) :
```sql
CREATE TABLE livres (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(200) NOT NULL,
    auteur_id INTEGER,
    CONSTRAINT fk_auteur               -- Nom de la contrainte
      FOREIGN KEY (auteur_id)          -- Colonne locale
      REFERENCES auteurs(id)           -- Table et colonne cible
);
```

🎯 **En pratique** :
```sql
-- ✅ OK : L'auteur 1 existe
INSERT INTO livres (titre, auteur_id) VALUES ('Harry Potter', 1);

-- ❌ ERREUR : L'auteur 999 n'existe pas !
INSERT INTO livres (titre, auteur_id) VALUES ('Livre mystère', 999);
-- PostgreSQL dit : "Non ! Cet auteur n'existe pas !"
```

---

# Exemple complet : Blog 📝

**Créons les tables**

```sql
-- Table des auteurs
CREATE TABLE auteurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    date_inscription TIMESTAMP DEFAULT NOW()
);
```

---

```sql
-- Table des articles
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    auteur_id INTEGER REFERENCES auteurs(id),
    titre VARCHAR(200) NOT NULL,
    contenu TEXT,
    publie BOOLEAN DEFAULT false,
    date_publication TIMESTAMP
);
```

---

```sql
-- Table des commentaires
CREATE TABLE commentaires (
    id SERIAL PRIMARY KEY,
    article_id INTEGER REFERENCES articles(id),
    auteur_nom VARCHAR(100),
    contenu TEXT NOT NULL,
    date_commentaire TIMESTAMP DEFAULT NOW()
);
```

---

**Insérer des données**

```sql
-- Insérer des auteurs
INSERT INTO auteurs (nom, email) VALUES
    ('Alice Martin', 'alice@blog.com'),
    ('Bob Durand', 'bob@blog.com'),
    ('Charlie Leroy', 'charlie@blog.com')
RETURNING id;
```

---

```sql
-- Insérer des articles (supposons Alice = 1, Bob = 2)
INSERT INTO articles (auteur_id, titre, contenu, publie, date_publication) VALUES
    (1, 'Introduction à PostgreSQL', 'PostgreSQL est...', true, NOW()),
    (1, 'Les jointures SQL', 'Les jointures permettent...', true, NOW()),
    (2, 'Optimisation de requêtes', 'Pour optimiser...', true, NOW()),
    (2, 'Sécurité des bases', 'Brouillon...', false, NULL);
```

---

```sql
-- Insérer des commentaires
INSERT INTO commentaires (article_id, auteur_nom, contenu) VALUES
    (1, 'Lecteur1', 'Excellent article !'),
    (1, 'Lecteur2', 'Très instructif'),
    (2, 'Lecteur1', 'Merci pour ces explications'),
    (3, 'Lecteur3', 'Article intéressant');
```

---

# INNER JOIN 🎯

**LA jointure la plus utilisée ! (80% des cas)**

**INNER JOIN = "Montre-moi SEULEMENT ce qui matche des DEUX côtés"**

*Analogie parfaite* 🎨 : Imaginez 2 listes de contacts :
- **Liste A** : Vos amis Facebook
- **Liste B** : Vos contacts téléphone

**INNER JOIN** = Les personnes qui sont DANS LES DEUX LISTES
- Alice est sur Facebook ET dans vos contacts → ✅ Apparaît
- Bob est SEULEMENT sur Facebook → ❌ N'apparaît pas
- Charlie est SEULEMENT dans vos contacts → ❌ N'apparaît pas

---

**Syntaxe de base** 📝 :

```sql
SELECT colonnes
FROM table1
INNER JOIN table2 ON table1.colonne = table2.colonne;
```

💡 **Décomposition** :
- `FROM table1` = Table de départ
- `INNER JOIN table2` = Table à joindre
- `ON ...` = Condition de correspondance

---

**Exemple concret : Articles avec leurs auteurs** 📚

**Situation** :
- Table `articles` : id, titre, auteur_id
- Table `auteurs` : id, nom

**Ce qu'on veut** : Afficher les articles AVEC le nom de l'auteur

```sql
SELECT 
    articles.titre,
    articles.date_publication,
    auteurs.nom AS auteur
FROM articles
INNER JOIN auteurs ON articles.auteur_id = auteurs.id;
```

🎯 **Traduction en français** :
1. Prends la table `articles`
2. Joins-la avec la table `auteurs`
3. **Condition** : `auteur_id` (dans articles) = `id` (dans auteurs)
4. Affiche : le titre, la date, et le nom de l'auteur

💡 **Résultat** : Seulement les articles qui ONT un auteur !
- Article avec auteur_id = 5 et auteur 5 existe → ✅ Affiché
- Article avec auteur_id = NULL → ❌ PAS affiché (pas de correspondance)

---

**Version PRO : Avec alias** (pour écrire moins !) 🚀

```sql
SELECT 
    a.titre,              -- 'a' = alias pour 'articles'
    a.date_publication,
    au.nom AS auteur,     -- 'au' = alias pour 'auteurs'
    au.email
FROM articles a                              -- Alias 'a'
INNER JOIN auteurs au ON a.auteur_id = au.id -- Alias 'au'
WHERE a.publie = true
ORDER BY a.date_publication DESC;
```

💡 **Pourquoi des alias ?**
- Plus court à écrire : `a.titre` au lieu de `articles.titre`
- Plus lisible
- **Indispensable** pour les jointures complexes !

🎯 **Cette requête** :
1. Joint articles et auteurs
2. Filtre : seulement les articles publiés
3. Trie : du plus récent au plus ancien

---

**Plusieurs colonnes dans SELECT**

```sql
SELECT 
    a.id AS article_id,
    a.titre,
    a.contenu,
    a.date_publication,
    au.id AS auteur_id,
    au.nom AS auteur_nom,
    au.email AS auteur_email
FROM articles a
INNER JOIN auteurs au ON a.auteur_id = au.id
WHERE a.publie = true;
```

---

# LEFT JOIN (LEFT OUTER JOIN) ⬅️

**La 2ème jointure la plus utilisée ! (15% des cas)**

**LEFT JOIN = "Montre TOUT de la table de gauche, même si pas de correspondance à droite"**

*Analogie VIP* 🎪 : Vous organisez une soirée :
- **Table de gauche** = Votre liste d'invités
- **Table de droite** = Les personnes qui sont venues

**LEFT JOIN** = Afficher TOUS vos invités :
- Alice invitée ET venue → ✅ "Alice - Présente"
- Bob invité mais PAS venu → ✅ "Bob - Absent" (NULL)
- Charlie PAS invité mais venu → ❌ N'apparaît pas

💡 **Règle d'or** : On garde TOUT de la table de GAUCHE !

---

**Syntaxe** 📝 :

```sql
SELECT colonnes
FROM table1           -- Table de GAUCHE (on garde tout)
LEFT JOIN table2 ON table1.colonne = table2.colonne;
                     -- Table de DROITE (peut être NULL)
```

---

**Exemple 1 : Tous les auteurs avec leurs articles** 📚

**Situation** : On veut voir TOUS les auteurs, même ceux qui n'ont pas encore écrit d'article !

```sql
SELECT 
    au.nom,
    a.titre
FROM auteurs au              -- GAUCHE : tous les auteurs
LEFT JOIN articles a ON au.id = a.auteur_id
ORDER BY au.nom;
```

**Résultat possible** :
```
Alice Martin | "Introduction à PostgreSQL"
Alice Martin | "Les jointures SQL"
Bob Durand   | "Optimisation de requêtes"
Charlie Leroy| NULL   ← Pas encore d'article !
```

💡 Charlie apparaît même sans article (NULL = pas de correspondance)

---

**Exemple 2 : Trouver les auteurs SANS articles** 🔍

**Super astuce** : LEFT JOIN + WHERE NULL !

```sql
SELECT 
    au.nom,
    au.email
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id
WHERE a.id IS NULL;     -- ← Articles = NULL = pas d'article !
```

🎯 **Traduction** :
1. Prends TOUS les auteurs
2. Joins avec articles (même si pas d'article)
3. Garde SEULEMENT ceux où article = NULL

💡 **Usage pratique** : "Qui n'a pas encore contribué ?"

---

**Exemple 3 : Compter les articles par auteur** 📊

```sql
SELECT 
    au.nom,
    COUNT(a.id) AS nombre_articles
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id
GROUP BY au.id, au.nom
ORDER BY nombre_articles DESC;
```

**Résultat possible** :
```
Alice Martin  | 5 articles
Bob Durand    | 3 articles
Charlie Leroy | 0 articles  ← Apparaît avec 0 !
```

💡 **Pourquoi LEFT JOIN ici ?** 
- INNER JOIN cacherait Charlie (0 article)
- LEFT JOIN montre TOUT le monde, même avec 0

🎯 **Usage** : Statistiques complètes, tableaux de bord

---

# RIGHT JOIN (RIGHT OUTER JOIN) ➡️

**Syntaxe**

```sql
SELECT colonnes
FROM table1
RIGHT JOIN table2 ON table1.colonne = table2.colonne;
```

**Retourne toutes les lignes de la table de droite + correspondances de gauche**

---

**Exemple (équivalent au LEFT JOIN inversé)**

```sql
-- Ces deux requêtes sont équivalentes :

-- Avec LEFT JOIN
SELECT au.nom, a.titre
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id;

-- Avec RIGHT JOIN
SELECT au.nom, a.titre
FROM articles a
RIGHT JOIN auteurs au ON a.auteur_id = au.id;
```

---

**Note : Préférez LEFT JOIN**

En pratique, on utilise rarement RIGHT JOIN car on peut toujours le remplacer par un LEFT JOIN en inversant l'ordre des tables. LEFT JOIN est plus lisible.

---

# FULL JOIN (FULL OUTER JOIN) ↔️

**Syntaxe**

```sql
SELECT colonnes
FROM table1
FULL JOIN table2 ON table1.colonne = table2.colonne;
```

**Retourne toutes les lignes des deux tables, avec NULL où il n'y a pas de correspondance**

---

**Exemple pratique**

```sql
-- Tous les auteurs ET tous les articles (même orphelins)
SELECT 
    au.nom AS auteur,
    a.titre AS article
FROM auteurs au
FULL JOIN articles a ON au.id = a.auteur_id
ORDER BY au.nom NULLS LAST, a.titre;
```

---

**Trouver les enregistrements sans correspondance**

```sql
-- Auteurs sans articles OU articles sans auteur
SELECT 
    au.nom,
    a.titre
FROM auteurs au
FULL JOIN articles a ON au.id = a.auteur_id
WHERE au.id IS NULL OR a.id IS NULL;
```

---

# Comparaison des JOIN 📊

**Schéma visuel**

```
INNER JOIN : ∩ (intersection)
LEFT JOIN  : ⊂ (tout à gauche + intersection)
RIGHT JOIN : ⊃ (tout à droite + intersection)
FULL JOIN  : ∪ (union complète)
```

---

**Table de comparaison**

| JOIN | Table A | Table B | Résultat |
|------|---------|---------|----------|
| INNER | 5 lignes | 3 lignes | 3 correspondances |
| LEFT | 5 lignes | 3 lignes | 5 lignes (2 avec NULL) |
| RIGHT | 5 lignes | 3 lignes | 3 lignes |
| FULL | 5 lignes | 3 lignes | 5 lignes (2 avec NULL) |

---

# CROSS JOIN ✖️

**Produit cartésien**

```sql
SELECT colonnes
FROM table1
CROSS JOIN table2;
```

**Chaque ligne de table1 combinée avec chaque ligne de table2**

---

**Exemple : Toutes les combinaisons**

```sql
-- Si auteurs = 3 lignes et articles = 4 lignes
-- Résultat = 3 × 4 = 12 lignes
SELECT 
    au.nom,
    a.titre
FROM auteurs au
CROSS JOIN articles a;
```

---

**Cas d'usage : Génération de combinaisons**

```sql
-- Créer un calendrier de disponibilités
SELECT 
    employe.nom,
    jour.jour_semaine
FROM employes employe
CROSS JOIN (
    SELECT unnest(ARRAY['Lundi', 'Mardi', 'Mercredi', 'Jeudi', 'Vendredi']) AS jour_semaine
) jour;
```

---

# SELF JOIN 🔁

**Joindre une table avec elle-même**

```sql
-- Table d'employés avec manager
CREATE TABLE employes (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    manager_id INTEGER REFERENCES employes(id)
);
```

---

**Insérer des données hiérarchiques**

```sql
INSERT INTO employes (id, nom, manager_id) VALUES
    (1, 'PDG', NULL),
    (2, 'Directeur IT', 1),
    (3, 'Directeur RH', 1),
    (4, 'Dev Senior', 2),
    (5, 'Dev Junior', 4);
```

---

**Requête avec SELF JOIN**

```sql
-- Employés avec leur manager
SELECT 
    e.nom AS employe,
    m.nom AS manager
FROM employes e
LEFT JOIN employes m ON e.manager_id = m.id
ORDER BY e.id;
```

---

**Trouver les collègues (même manager)**

```sql
SELECT 
    e1.nom AS employe1,
    e2.nom AS employe2,
    m.nom AS manager_commun
FROM employes e1
JOIN employes e2 ON e1.manager_id = e2.manager_id
JOIN employes m ON e1.manager_id = m.id
WHERE e1.id < e2.id;  -- Éviter les doublons
```

---

# Jointures multiples 🔗🔗🔗

**Joindre plus de 2 tables**

```sql
-- Articles avec auteurs et commentaires
SELECT 
    a.titre,
    au.nom AS auteur,
    c.auteur_nom AS commentateur,
    c.contenu AS commentaire
FROM articles a
INNER JOIN auteurs au ON a.auteur_id = au.id
LEFT JOIN commentaires c ON a.id = c.article_id
WHERE a.publie = true
ORDER BY a.date_publication DESC, c.date_commentaire;
```

---

**Ordre des jointures important**

```sql
-- Tous les auteurs, leurs articles, et les commentaires
SELECT 
    au.nom AS auteur,
    a.titre AS article,
    c.contenu AS commentaire
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id
LEFT JOIN commentaires c ON a.id = c.article_id
ORDER BY au.nom, a.titre;
```

---

**Statistiques avec jointures multiples**

```sql
-- Nombre d'articles et de commentaires par auteur
SELECT 
    au.nom,
    COUNT(DISTINCT a.id) AS nb_articles,
    COUNT(c.id) AS nb_commentaires
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id
LEFT JOIN commentaires c ON a.id = c.article_id
GROUP BY au.id, au.nom
ORDER BY nb_articles DESC;
```

---

# USING : simplification de syntaxe 🎯

**Quand les colonnes ont le même nom**

```sql
-- Avec ON
SELECT a.titre, au.nom
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id;

-- Avec USING (si la colonne s'appelait 'id' dans les deux tables)
-- Attention : nécessite le même nom de colonne
SELECT a.titre, au.nom
FROM articles a
JOIN auteurs au USING (id);  -- Seulement si les noms correspondent
```

---

**USING avec plusieurs colonnes**

```sql
-- Jointure sur plusieurs colonnes
SELECT *
FROM table1 t1
JOIN table2 t2 USING (colonne1, colonne2);

-- Équivalent à
SELECT *
FROM table1 t1
JOIN table2 t2 ON t1.colonne1 = t2.colonne1 
              AND t1.colonne2 = t2.colonne2;
```

---

# NATURAL JOIN ⚠️

**Jointure automatique sur colonnes de même nom**

```sql
-- Join automatiquement sur toutes les colonnes de même nom
SELECT * FROM table1 NATURAL JOIN table2;
```

**⚠️ À ÉVITER : Dangereux et peu prévisible !**

---

# Conditions dans les jointures 🔍

**Condition dans ON vs WHERE**

```sql
-- Condition dans ON (filtre avant la jointure)
SELECT au.nom, a.titre
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id AND a.publie = true;

-- Condition dans WHERE (filtre après la jointure)
SELECT au.nom, a.titre
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id
WHERE a.publie = true;
```

**Résultats différents avec LEFT JOIN !**

---

**Exemple de la différence**

```sql
-- Tous les auteurs + leurs articles publiés
SELECT au.nom, a.titre
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id AND a.publie = true;
-- Résultat : Tous les auteurs, même sans articles publiés

-- Seulement les auteurs ayant des articles publiés
SELECT au.nom, a.titre
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id
WHERE a.publie = true;
-- Résultat : Seulement auteurs avec articles publiés
```

---

# Relations Many-to-Many 🔗🔗

**Créer une table de liaison**

```sql
-- Tags pour les articles
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(50) UNIQUE NOT NULL
);

CREATE TABLE articles_tags (
    article_id INTEGER REFERENCES articles(id),
    tag_id INTEGER REFERENCES tags(id),
    PRIMARY KEY (article_id, tag_id)
);
```

---

**Insérer des données**

```sql
-- Créer des tags
INSERT INTO tags (nom) VALUES
    ('PostgreSQL'),
    ('SQL'),
    ('Base de données'),
    ('Tutorial'),
    ('Avancé');

-- Associer des tags aux articles
INSERT INTO articles_tags (article_id, tag_id) VALUES
    (1, 1), (1, 2), (1, 4),  -- Article 1 : PostgreSQL, SQL, Tutorial
    (2, 2), (2, 4),          -- Article 2 : SQL, Tutorial
    (3, 2), (3, 5);          -- Article 3 : SQL, Avancé
```

---

**Requête Many-to-Many**

```sql
-- Articles avec leurs tags
SELECT 
    a.titre,
    t.nom AS tag
FROM articles a
JOIN articles_tags at ON a.id = at.article_id
JOIN tags t ON at.tag_id = t.id
ORDER BY a.titre, t.nom;
```

---

**Agréger les tags**

```sql
-- Articles avec tous leurs tags en une ligne
SELECT 
    a.titre,
    STRING_AGG(t.nom, ', ' ORDER BY t.nom) AS tags
FROM articles a
LEFT JOIN articles_tags at ON a.id = at.article_id
LEFT JOIN tags t ON at.tag_id = t.id
GROUP BY a.id, a.titre
ORDER BY a.titre;
```

---

**Trouver les articles avec un tag spécifique**

```sql
-- Articles avec le tag 'PostgreSQL'
SELECT a.titre, a.date_publication
FROM articles a
JOIN articles_tags at ON a.id = at.article_id
JOIN tags t ON at.tag_id = t.id
WHERE t.nom = 'PostgreSQL';
```

---

**Trouver les articles avec plusieurs tags**

```sql
-- Articles ayant à la fois 'SQL' ET 'Tutorial'
SELECT a.titre
FROM articles a
JOIN articles_tags at1 ON a.id = at1.article_id
JOIN tags t1 ON at1.tag_id = t1.id AND t1.nom = 'SQL'
JOIN articles_tags at2 ON a.id = at2.article_id
JOIN tags t2 ON at2.tag_id = t2.id AND t2.nom = 'Tutorial';
```

---

# Sous-requêtes dans les jointures 🔄

**Joindre avec une sous-requête**

```sql
-- Articles avec le nombre de commentaires
SELECT 
    a.titre,
    a.date_publication,
    stats.nb_commentaires
FROM articles a
LEFT JOIN (
    SELECT 
        article_id,
        COUNT(*) AS nb_commentaires
    FROM commentaires
    GROUP BY article_id
) stats ON a.id = stats.article_id
ORDER BY stats.nb_commentaires DESC;
```

---

**CTE pour plus de clarté**

```sql
WITH stats_commentaires AS (
    SELECT 
        article_id,
        COUNT(*) AS nb_commentaires
    FROM commentaires
    GROUP BY article_id
)
SELECT 
    a.titre,
    COALESCE(sc.nb_commentaires, 0) AS nb_commentaires
FROM articles a
LEFT JOIN stats_commentaires sc ON a.id = sc.article_id
ORDER BY sc.nb_commentaires DESC;
```

---

# Performance des jointures 🚀

**Index sur les clés étrangères**

```sql
-- Créer des index pour accélérer les jointures
CREATE INDEX idx_articles_auteur_id ON articles(auteur_id);
CREATE INDEX idx_commentaires_article_id ON commentaires(article_id);
CREATE INDEX idx_articles_tags_article_id ON articles_tags(article_id);
CREATE INDEX idx_articles_tags_tag_id ON articles_tags(tag_id);
```

---

**Analyser une requête**

```sql
-- EXPLAIN pour voir le plan d'exécution
EXPLAIN SELECT 
    a.titre,
    au.nom
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id;

-- EXPLAIN ANALYZE pour exécuter et mesurer
EXPLAIN ANALYZE SELECT 
    a.titre,
    au.nom
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id;
```

---

# Bonnes pratiques 👍

**1. Toujours utiliser des alias**

```sql
-- ✅ BON : Lisible et maintenable
SELECT 
    a.titre,
    au.nom AS auteur
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id;

-- ❌ MAUVAIS : Long et répétitif
SELECT 
    articles.titre,
    auteurs.nom AS auteur
FROM articles
JOIN auteurs ON articles.auteur_id = auteurs.id;
```

---

**2. Spécifier les colonnes explicitement**

```sql
-- ✅ BON
SELECT a.id, a.titre, au.nom
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id;

-- ❌ MAUVAIS : ambiguïté possible
SELECT *
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id;
```

---

**3. Utiliser des contraintes de clés étrangères**

```sql
-- ✅ BON : Garantit l'intégrité
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    auteur_id INTEGER REFERENCES auteurs(id) ON DELETE CASCADE
);

-- ❌ MAUVAIS : Pas de garantie d'intégrité
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    auteur_id INTEGER
);
```

---

**4. Choisir le bon type de jointure**

- **INNER JOIN** : Seulement les correspondances
- **LEFT JOIN** : Tous les enregistrements de gauche
- **RIGHT JOIN** : Rarement utilisé (utiliser LEFT JOIN)
- **FULL JOIN** : Tous les enregistrements des deux côtés
- **CROSS JOIN** : Produit cartésien (attention à la taille !)

---

# Exercice pratique 🎯

**Créer un système de gestion de cours**

1. Table `professeurs` : id, nom, specialite
2. Table `cours` : id, nom, professeur_id
3. Table `etudiants` : id, nom, email
4. Table `inscriptions` : etudiant_id, cours_id, note

---

**Solution - Partie 1**

```sql
CREATE TABLE professeurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    specialite VARCHAR(100)
);

CREATE TABLE cours (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(200) NOT NULL,
    professeur_id INTEGER REFERENCES professeurs(id)
);
```

---

```sql
CREATE TABLE etudiants (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);

CREATE TABLE inscriptions (
    etudiant_id INTEGER REFERENCES etudiants(id),
    cours_id INTEGER REFERENCES cours(id),
    note NUMERIC(4,2) CHECK (note >= 0 AND note <= 20),
    PRIMARY KEY (etudiant_id, cours_id)
);
```

---

**Solution - Partie 2 : Données**

```sql
INSERT INTO professeurs (nom, specialite) VALUES
    ('Prof. Martin', 'Informatique'),
    ('Prof. Durand', 'Mathématiques'),
    ('Prof. Leroy', 'Physique');

INSERT INTO cours (nom, professeur_id) VALUES
    ('Base de données', 1),
    ('Programmation Python', 1),
    ('Algèbre linéaire', 2),
    ('Mécanique quantique', 3);
```

---

```sql
INSERT INTO etudiants (nom, email) VALUES
    ('Alice Bernard', 'alice@school.com'),
    ('Bob Petit', 'bob@school.com'),
    ('Charlie Grand', 'charlie@school.com');

INSERT INTO inscriptions (etudiant_id, cours_id, note) VALUES
    (1, 1, 15.5),
    (1, 2, 16.0),
    (2, 1, 14.0),
    (2, 3, 17.5),
    (3, 2, 13.0),
    (3, 4, 18.0);
```

---

**Solution - Partie 3 : Requêtes**

```sql
-- 1. Tous les cours avec leur professeur
SELECT c.nom AS cours, p.nom AS professeur, p.specialite
FROM cours c
JOIN professeurs p ON c.professeur_id = p.id;

-- 2. Étudiants inscrits à chaque cours
SELECT 
    c.nom AS cours,
    e.nom AS etudiant,
    i.note
FROM cours c
JOIN inscriptions i ON c.id = i.cours_id
JOIN etudiants e ON i.etudiant_id = e.id
ORDER BY c.nom, e.nom;
```

---

```sql
-- 3. Moyenne par étudiant
SELECT 
    e.nom,
    ROUND(AVG(i.note), 2) AS moyenne
FROM etudiants e
JOIN inscriptions i ON e.id = i.etudiant_id
GROUP BY e.id, e.nom
ORDER BY moyenne DESC;

-- 4. Cours sans inscriptions
SELECT c.nom AS cours
FROM cours c
LEFT JOIN inscriptions i ON c.id = i.cours_id
WHERE i.cours_id IS NULL;
```

---

```sql
-- 5. Nombre d'étudiants par professeur
SELECT 
    p.nom AS professeur,
    COUNT(DISTINCT i.etudiant_id) AS nb_etudiants
FROM professeurs p
JOIN cours c ON p.id = c.professeur_id
LEFT JOIN inscriptions i ON c.id = i.cours_id
GROUP BY p.id, p.nom
ORDER BY nb_etudiants DESC;
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Clés primaires (PRIMARY KEY) et étrangères (FOREIGN KEY)
✅ INNER JOIN : seulement les correspondances
✅ LEFT JOIN : toutes les lignes de gauche + correspondances
✅ FULL JOIN : toutes les lignes des deux tables
✅ SELF JOIN : joindre une table avec elle-même
✅ Jointures multiples : combiner plusieurs tables
✅ Many-to-Many : table de liaison
✅ Index pour optimiser les jointures

---

# Questions ? 🙋

Des questions sur les jointures et relations ?

**À suivre** : Fonctions d'agrégation avancées

