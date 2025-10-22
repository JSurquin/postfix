---
layout: new-section
routeAlias: 'sous-requetes-cte'
---

<a name="sous-requetes-cte" id="sous-requetes-cte"></a>

# Sous-requêtes et CTE 🔄

Maîtrisons les requêtes imbriquées et les expressions de table communes

---

# Plan du module 📋

- Sous-requêtes scalaires
- Sous-requêtes dans WHERE
- Sous-requêtes dans FROM
- EXISTS et NOT EXISTS
- CTE (Common Table Expressions)
- CTE récursives
- Bonnes pratiques

---

# Sous-requêtes scalaires 🔢

**Sous-requête = Une requête DANS une requête ! 🎭**

*Analogie* : C'est comme des poupées russes - une requête contient une autre requête !

**Sous-requête scalaire** = Retourne UNE SEULE valeur (un nombre, un texte...)

```sql
-- Comparer chaque produit au prix moyen
SELECT 
    nom,
    prix,
    (SELECT AVG(prix) FROM produits) AS prix_moyen,
    prix - (SELECT AVG(prix) FROM produits) AS diff_moyenne
FROM produits;
```

💡 **Comment ça marche ?**
1. PostgreSQL calcule d'abord `(SELECT AVG(prix) FROM produits)` → ex: 50€
2. Puis utilise ce résultat pour chaque ligne

🎯 **Résultat possible** :
```
Laptop | 900€ | 50€ | +850€  (bien au-dessus de la moyenne)
Souris | 20€  | 50€ | -30€   (en dessous de la moyenne)
```

⚠️ **Important** : La sous-requête scalaire doit retourner UNE SEULE valeur !
- ✅ `SELECT AVG(prix)` → un seul nombre
- ❌ `SELECT prix` → plusieurs valeurs = ERREUR

---

**Dans SELECT**

```sql
-- Nombre total de commandes pour chaque client
SELECT 
    c.nom,
    c.email,
    (SELECT COUNT(*) 
     FROM commandes cmd 
     WHERE cmd.client_id = c.id) AS nb_commandes
FROM clients c;
```

---

# Sous-requêtes dans WHERE 🎯

**Comparaison simple**

```sql
-- Produits plus chers que la moyenne
SELECT nom, prix
FROM produits
WHERE prix > (SELECT AVG(prix) FROM produits);

-- Produit le plus cher
SELECT nom, prix
FROM produits
WHERE prix = (SELECT MAX(prix) FROM produits);
```

---

**Opérateur IN**

```sql
-- Clients ayant passé au moins une commande
SELECT nom, email
FROM clients
WHERE id IN (SELECT client_id FROM commandes);

-- Produits jamais commandés
SELECT nom, prix
FROM produits
WHERE id NOT IN (SELECT produit_id FROM lignes_commande WHERE produit_id IS NOT NULL);
```

---

**Opérateurs ANY et ALL**

```sql
-- Produits plus chers que n'importe quel produit de catégorie 'Accessoires'
SELECT nom, prix, categorie
FROM produits
WHERE prix > ANY (
    SELECT prix 
    FROM produits 
    WHERE categorie = 'Accessoires'
);
```

---

```sql
-- Produits plus chers que TOUS les produits de catégorie 'Accessoires'
SELECT nom, prix, categorie
FROM produits
WHERE prix > ALL (
    SELECT prix 
    FROM produits 
    WHERE categorie = 'Accessoires'
);
```

---

# EXISTS et NOT EXISTS ✔️❌

**Vérifier l'existence**

```sql
-- Clients ayant au moins une commande
SELECT c.nom, c.email
FROM clients c
WHERE EXISTS (
    SELECT 1 
    FROM commandes cmd 
    WHERE cmd.client_id = c.id
);
```

---

**NOT EXISTS**

```sql
-- Clients sans aucune commande
SELECT c.nom, c.email
FROM clients c
WHERE NOT EXISTS (
    SELECT 1 
    FROM commandes cmd 
    WHERE cmd.client_id = c.id
);
```

---

**EXISTS vs IN**

```sql
-- Avec IN (peut être lent si beaucoup de résultats)
SELECT nom FROM clients
WHERE id IN (SELECT client_id FROM commandes);

-- Avec EXISTS (souvent plus rapide)
SELECT nom FROM clients c
WHERE EXISTS (
    SELECT 1 FROM commandes cmd 
    WHERE cmd.client_id = c.id
);
```

**EXISTS est généralement plus performant !**

---

# Sous-requêtes dans FROM 📋

**Table dérivée**

```sql
-- Statistiques par catégorie, puis moyenne globale
SELECT 
    AVG(prix_moyen) AS moyenne_des_moyennes,
    MAX(nb_produits) AS max_produits_par_categorie
FROM (
    SELECT 
        categorie,
        AVG(prix) AS prix_moyen,
        COUNT(*) AS nb_produits
    FROM produits
    GROUP BY categorie
) stats_categorie;
```

---

**Avec alias obligatoire**

```sql
-- ⚠️ ERREUR : Pas d'alias
SELECT * FROM (SELECT * FROM produits WHERE prix > 100);  -- ERREUR

-- ✅ CORRECT : Avec alias
SELECT * FROM (
    SELECT * FROM produits WHERE prix > 100
) AS produits_chers;
```

---

**Jointures avec sous-requêtes**

```sql
-- Top 3 clients par montant de commandes
SELECT 
    c.nom,
    stats.total,
    stats.nb_commandes
FROM clients c
JOIN (
    SELECT 
        client_id,
        SUM(montant) AS total,
        COUNT(*) AS nb_commandes
    FROM commandes
    GROUP BY client_id
) stats ON c.id = stats.client_id
ORDER BY stats.total DESC
LIMIT 3;
```

---

# CTE : Common Table Expressions 🎯

**Syntaxe WITH**

```sql
WITH nom_cte AS (
    SELECT ...
)
SELECT * FROM nom_cte;
```

**Plus lisible que les sous-requêtes imbriquées !**

---

**Exemple simple**

```sql
-- Produits chers avec CTE
WITH produits_chers AS (
    SELECT nom, prix, categorie
    FROM produits
    WHERE prix > 100
)
SELECT * FROM produits_chers
ORDER BY prix DESC;
```

---

**Plusieurs CTE**

```sql
-- Statistiques clients et produits
WITH stats_clients AS (
    SELECT 
        COUNT(*) AS nb_clients,
        AVG(EXTRACT(YEAR FROM age(date_inscription))) AS anciennete_moyenne
    FROM clients
),
stats_produits AS (
    SELECT 
        COUNT(*) AS nb_produits,
        AVG(prix) AS prix_moyen
    FROM produits
)
SELECT * FROM stats_clients, stats_produits;
```

---

**CTE réutilisable**

```sql
WITH produits_populaires AS (
    SELECT 
        p.id,
        p.nom,
        COUNT(lc.id) AS nb_ventes
    FROM produits p
    JOIN lignes_commande lc ON p.id = lc.produit_id
    GROUP BY p.id, p.nom
    HAVING COUNT(lc.id) > 10
)
SELECT 
    pp.nom,
    pp.nb_ventes,
    p.prix
FROM produits_populaires pp
JOIN produits p ON pp.id = p.id
ORDER BY pp.nb_ventes DESC;
```

---

**CTE chaînées**

```sql
WITH commandes_recentes AS (
    SELECT *
    FROM commandes
    WHERE date_commande >= CURRENT_DATE - INTERVAL '30 days'
),
clients_actifs AS (
    SELECT DISTINCT client_id
    FROM commandes_recentes
),
stats AS (
    SELECT 
        c.nom,
        COUNT(cr.id) AS nb_commandes,
        SUM(cr.montant) AS total
    FROM clients c
    JOIN clients_actifs ca ON c.id = ca.client_id
    JOIN commandes_recentes cr ON c.id = cr.client_id
    GROUP BY c.id, c.nom
)
SELECT * FROM stats
ORDER BY total DESC;
```

---

# CTE récursives 🔁

**Syntaxe de base**

```sql
WITH RECURSIVE nom_cte AS (
    -- Cas de base (ancre)
    SELECT ...
    
    UNION ALL
    
    -- Cas récursif
    SELECT ...
    FROM nom_cte
    WHERE condition_arret
)
SELECT * FROM nom_cte;
```

---

**Exemple : Séquence de nombres**

```sql
-- Générer les nombres de 1 à 10
WITH RECURSIVE nombres AS (
    -- Cas de base
    SELECT 1 AS n
    
    UNION ALL
    
    -- Cas récursif
    SELECT n + 1
    FROM nombres
    WHERE n < 10
)
SELECT * FROM nombres;
```

---

**Hiérarchie organisationnelle**

```sql
CREATE TABLE employes (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    manager_id INTEGER REFERENCES employes(id)
);

INSERT INTO employes (id, nom, manager_id) VALUES
    (1, 'PDG', NULL),
    (2, 'Directeur IT', 1),
    (3, 'Directeur RH', 1),
    (4, 'Dev Senior', 2),
    (5, 'Dev Junior', 4);
```

---

**Parcourir la hiérarchie**

```sql
-- Tous les subordonnés du PDG
WITH RECURSIVE hierarchie AS (
    -- Cas de base : le PDG
    SELECT id, nom, manager_id, 1 AS niveau
    FROM employes
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Cas récursif : les subordonnés
    SELECT e.id, e.nom, e.manager_id, h.niveau + 1
    FROM employes e
    JOIN hierarchie h ON e.manager_id = h.id
)
SELECT 
    REPEAT('  ', niveau - 1) || nom AS organigramme,
    niveau
FROM hierarchie
ORDER BY niveau, nom;
```

---

**Résultat de la hiérarchie**

```
organigramme          | niveau
----------------------|--------
PDG                   | 1
  Directeur IT        | 2
  Directeur RH        | 2
    Dev Senior        | 3
      Dev Junior      | 4
```

---

**Chemins dans un graphe**

```sql
CREATE TABLE routes (
    ville_depart VARCHAR(50),
    ville_arrivee VARCHAR(50),
    distance INTEGER
);

INSERT INTO routes VALUES
    ('Paris', 'Lyon', 465),
    ('Lyon', 'Marseille', 315),
    ('Paris', 'Bordeaux', 580),
    ('Bordeaux', 'Marseille', 650);
```

---

```sql
-- Trouver tous les chemins depuis Paris
WITH RECURSIVE chemins AS (
    -- Cas de base : routes directes depuis Paris
    SELECT 
        ville_depart,
        ville_arrivee,
        distance,
        ARRAY[ville_depart, ville_arrivee] AS chemin,
        distance AS distance_totale
    FROM routes
    WHERE ville_depart = 'Paris'
    
    UNION ALL
    
    -- Cas récursif : continuer le chemin
    SELECT 
        r.ville_depart,
        r.ville_arrivee,
        r.distance,
        c.chemin || r.ville_arrivee,
        c.distance_totale + r.distance
    FROM routes r
    JOIN chemins c ON r.ville_depart = c.ville_arrivee
    WHERE NOT r.ville_arrivee = ANY(c.chemin)  -- Éviter les cycles
)
SELECT 
    chemin,
    distance_totale
FROM chemins
ORDER BY distance_totale;
```

---

**Séries temporelles**

```sql
-- Générer tous les jours du mois
WITH RECURSIVE jours AS (
    SELECT DATE '2025-01-01' AS jour
    
    UNION ALL
    
    SELECT jour + INTERVAL '1 day'
    FROM jours
    WHERE jour < DATE '2025-01-31'
)
SELECT 
    jour,
    TO_CHAR(jour, 'Day') AS jour_semaine
FROM jours;
```

---

# LATERAL : Sous-requêtes corrélées 🔗

**Joindre avec une sous-requête qui référence la table de gauche**

```sql
-- Top 3 produits par catégorie
SELECT 
    c.nom AS categorie,
    p.nom AS produit,
    p.prix
FROM categories c
CROSS JOIN LATERAL (
    SELECT nom, prix
    FROM produits
    WHERE categorie_id = c.id
    ORDER BY prix DESC
    LIMIT 3
) p
ORDER BY c.nom, p.prix DESC;
```

---

**Comparaison sans LATERAL**

```sql
-- Sans LATERAL (moins lisible)
SELECT 
    c.nom,
    p.nom,
    p.prix
FROM categories c
JOIN produits p ON c.id = p.categorie_id
WHERE p.id IN (
    SELECT id
    FROM produits p2
    WHERE p2.categorie_id = c.id
    ORDER BY prix DESC
    LIMIT 3
);
```

---

# Sous-requêtes dans UPDATE et DELETE ✏️🗑️

**UPDATE avec sous-requête**

```sql
-- Mettre à jour le prix moyen par catégorie
UPDATE produits p
SET prix = (
    SELECT AVG(prix)
    FROM produits p2
    WHERE p2.categorie = p.categorie
)
WHERE prix IS NULL;
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
```

---

**UPDATE avec CTE**

```sql
-- Calculer d'abord, puis mettre à jour
WITH prix_moyens AS (
    SELECT 
        categorie,
        AVG(prix) AS prix_moyen
    FROM produits
    GROUP BY categorie
)
UPDATE produits p
SET prix = pm.prix_moyen
FROM prix_moyens pm
WHERE p.categorie = pm.categorie
  AND p.prix IS NULL;
```

---

# Optimisation des sous-requêtes 🚀

**Index pour les sous-requêtes**

```sql
-- Index pour EXISTS
CREATE INDEX idx_commandes_client_id ON commandes(client_id);

-- Requête bénéficiant de l'index
SELECT * FROM clients c
WHERE EXISTS (
    SELECT 1 FROM commandes cmd 
    WHERE cmd.client_id = c.id
);
```

---

**EXPLAIN pour analyser**

```sql
-- Analyser la sous-requête
EXPLAIN ANALYZE
SELECT nom, prix
FROM produits
WHERE prix > (SELECT AVG(prix) FROM produits);

-- Comparer avec une CTE
EXPLAIN ANALYZE
WITH prix_moyen AS (
    SELECT AVG(prix) AS moyenne FROM produits
)
SELECT nom, prix
FROM produits, prix_moyen
WHERE prix > moyenne;
```

---

# Matérialisation des CTE 🎯

**Par défaut, PostgreSQL peut inliner les CTE**

```sql
-- CTE normale (peut être inlinée)
WITH cte AS (
    SELECT * FROM produits WHERE prix > 100
)
SELECT * FROM cte WHERE categorie = 'Informatique';
```

---

**Forcer la matérialisation**

```sql
-- Forcer l'exécution une fois (PostgreSQL 12+)
WITH cte AS MATERIALIZED (
    SELECT * FROM produits WHERE prix > 100
)
SELECT * FROM cte WHERE categorie = 'Informatique';

-- Empêcher la matérialisation
WITH cte AS NOT MATERIALIZED (
    SELECT * FROM produits WHERE prix > 100
)
SELECT * FROM cte WHERE categorie = 'Informatique';
```

---

# Cas d'usage pratiques 💼

**Exemple 1 : Analyse de cohortes**

```sql
WITH cohortes AS (
    SELECT 
        client_id,
        DATE_TRUNC('month', MIN(date_commande)) AS mois_premiere_commande
    FROM commandes
    GROUP BY client_id
),
activite_mensuelle AS (
    SELECT 
        c.mois_premiere_commande,
        DATE_TRUNC('month', cmd.date_commande) AS mois_activite,
        COUNT(DISTINCT cmd.client_id) AS clients_actifs
    FROM cohortes c
    JOIN commandes cmd ON c.client_id = cmd.client_id
    GROUP BY c.mois_premiere_commande, DATE_TRUNC('month', cmd.date_commande)
)
SELECT 
    mois_premiere_commande,
    mois_activite,
    clients_actifs
FROM activite_mensuelle
ORDER BY mois_premiere_commande, mois_activite;
```

---

**Exemple 2 : Classement et percentiles**

```sql
WITH stats_produits AS (
    SELECT 
        nom,
        prix,
        NTILE(4) OVER (ORDER BY prix) AS quartile,
        PERCENT_RANK() OVER (ORDER BY prix) AS percentile
    FROM produits
)
SELECT 
    quartile,
    COUNT(*) AS nb_produits,
    ROUND(AVG(prix), 2) AS prix_moyen,
    MIN(prix) AS prix_min,
    MAX(prix) AS prix_max
FROM stats_produits
GROUP BY quartile
ORDER BY quartile;
```

---

**Exemple 3 : Détection d'anomalies**

```sql
WITH stats AS (
    SELECT 
        AVG(prix) AS moyenne,
        STDDEV(prix) AS ecart_type
    FROM produits
)
SELECT 
    p.nom,
    p.prix,
    s.moyenne,
    s.ecart_type,
    ABS(p.prix - s.moyenne) / s.ecart_type AS z_score
FROM produits p, stats s
WHERE ABS(p.prix - s.moyenne) > 2 * s.ecart_type
ORDER BY z_score DESC;
```

---

# Exercice pratique 🎯

**Analyse de blog**

1. Créer tables : `auteurs`, `articles`, `commentaires`
2. Avec CTE, trouver :
   - Auteurs les plus actifs
   - Articles sans commentaires
   - Évolution mensuelle des publications
   - Hiérarchie de commentaires (réponses)

---

**Solution - Partie 1 : Tables**

```sql
CREATE TABLE auteurs (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL
);

CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    auteur_id INTEGER REFERENCES auteurs(id),
    titre VARCHAR(200) NOT NULL,
    date_publication DATE NOT NULL
);
```

---

```sql
CREATE TABLE commentaires (
    id SERIAL PRIMARY KEY,
    article_id INTEGER REFERENCES articles(id),
    parent_id INTEGER REFERENCES commentaires(id),
    auteur_nom VARCHAR(100),
    contenu TEXT NOT NULL,
    date_commentaire TIMESTAMP DEFAULT NOW()
);
```

---

**Solution - Partie 2 : Données**

```sql
INSERT INTO auteurs (nom) VALUES ('Alice'), ('Bob'), ('Charlie');

INSERT INTO articles (auteur_id, titre, date_publication) VALUES
    (1, 'PostgreSQL Basics', '2025-01-01'),
    (1, 'Advanced SQL', '2025-01-15'),
    (2, 'Database Design', '2025-02-01'),
    (3, 'Performance Tips', '2025-02-15');
```

---

```sql
INSERT INTO commentaires (article_id, parent_id, auteur_nom, contenu) VALUES
    (1, NULL, 'Reader1', 'Great article!'),
    (1, 1, 'Alice', 'Thanks!'),
    (1, NULL, 'Reader2', 'Very helpful'),
    (2, NULL, 'Reader3', 'Interesting'),
    (3, NULL, 'Reader1', 'Nice tips');
```

---

**Solution - Partie 3 : Requêtes**

```sql
-- 1. Auteurs les plus actifs
WITH stats_auteurs AS (
    SELECT 
        a.id,
        a.nom,
        COUNT(ar.id) AS nb_articles,
        MAX(ar.date_publication) AS dernier_article
    FROM auteurs a
    LEFT JOIN articles ar ON a.id = ar.auteur_id
    GROUP BY a.id, a.nom
)
SELECT * FROM stats_auteurs
ORDER BY nb_articles DESC;
```

---

```sql
-- 2. Articles sans commentaires
SELECT a.titre, a.date_publication
FROM articles a
WHERE NOT EXISTS (
    SELECT 1 FROM commentaires c 
    WHERE c.article_id = a.id
);
```

---

```sql
-- 3. Évolution mensuelle
WITH publications_mensuelles AS (
    SELECT 
        TO_CHAR(date_publication, 'YYYY-MM') AS mois,
        COUNT(*) AS nb_articles
    FROM articles
    GROUP BY TO_CHAR(date_publication, 'YYYY-MM')
)
SELECT 
    mois,
    nb_articles,
    SUM(nb_articles) OVER (ORDER BY mois) AS cumul
FROM publications_mensuelles
ORDER BY mois;
```

---

```sql
-- 4. Hiérarchie de commentaires
WITH RECURSIVE fil_discussion AS (
    -- Commentaires racines
    SELECT 
        id,
        article_id,
        parent_id,
        auteur_nom,
        contenu,
        1 AS niveau,
        ARRAY[id] AS chemin
    FROM commentaires
    WHERE parent_id IS NULL
    
    UNION ALL
    
    -- Réponses
    SELECT 
        c.id,
        c.article_id,
        c.parent_id,
        c.auteur_nom,
        c.contenu,
        fd.niveau + 1,
        fd.chemin || c.id
    FROM commentaires c
    JOIN fil_discussion fd ON c.parent_id = fd.id
)
SELECT 
    REPEAT('  ', niveau - 1) || auteur_nom AS discussion,
    contenu,
    niveau
FROM fil_discussion
ORDER BY article_id, chemin;
```

---

# Bonnes pratiques 👍

**1. Préférer CTE aux sous-requêtes complexes**

```sql
-- ✅ BON : Lisible avec CTE
WITH produits_chers AS (
    SELECT * FROM produits WHERE prix > 100
)
SELECT * FROM produits_chers WHERE stock > 0;

-- ❌ MAUVAIS : Sous-requête imbriquée
SELECT * FROM (
    SELECT * FROM produits WHERE prix > 100
) AS sub WHERE stock > 0;
```

---

**2. Nommer clairement les CTE**

```sql
-- ✅ BON : Nom descriptif
WITH clients_actifs_janvier AS (
    SELECT DISTINCT client_id 
    FROM commandes 
    WHERE date_commande >= '2025-01-01'
)
SELECT * FROM clients_actifs_janvier;

-- ❌ MAUVAIS : Nom vague
WITH cte1 AS (...)
SELECT * FROM cte1;
```

---

**3. Limiter la profondeur des récursions**

```sql
-- Ajouter une limite de sécurité
WITH RECURSIVE suite AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM suite WHERE n < 1000  -- Limite
)
SELECT * FROM suite;
```

---

**4. Utiliser EXISTS au lieu de IN pour grandes tables**

```sql
-- ✅ Meilleure performance
SELECT * FROM clients c
WHERE EXISTS (
    SELECT 1 FROM commandes 
    WHERE client_id = c.id
);

-- ❌ Peut être lent
SELECT * FROM clients
WHERE id IN (SELECT client_id FROM commandes);
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Sous-requêtes : dans SELECT, WHERE, FROM
✅ EXISTS : vérifier l'existence (performant)
✅ CTE : WITH pour plus de lisibilité
✅ CTE récursives : hiérarchies, graphes, séries
✅ LATERAL : sous-requêtes corrélées
✅ Matérialisation : contrôler l'exécution des CTE
✅ Optimisation : index, EXPLAIN, choix EXISTS vs IN

---

# Questions ? 🙋

Des questions sur les sous-requêtes et les CTE ?

**À suivre** : Manipulation de données (INSERT, UPDATE, DELETE)

