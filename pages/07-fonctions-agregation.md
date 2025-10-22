---
layout: new-section
routeAlias: 'fonctions-agregation'
---

<a name="fonctions-agregation" id="fonctions-agregation"></a>

# Fonctions d'agrégation 📈

Maîtrisons l'analyse et le regroupement de données

---

# Plan du module 📋

- Fonctions d'agrégation de base
- GROUP BY
- HAVING
- Fonctions statistiques
- Fonctions de fenêtrage (Window Functions)
- ROLLUP et CUBE
- Agrégations conditionnelles
- Bonnes pratiques

---

# Fonctions d'agrégation de base 🔢

**Les agrégations = Faire des calculs sur PLUSIEURS lignes ! 📊**

*Analogie* : C'est comme utiliser la calculatrice sur une colonne Excel entière !

**Les 5 fonctions que vous utiliserez tout le temps** ⭐

**COUNT** = Compter 🔢
```sql
SELECT COUNT(*) FROM produits;  -- Combien de produits ?
```
💡 *Traduction* : "Dis-moi combien il y a de lignes"

**SUM** = Additionner ➕
```sql
SELECT SUM(prix) FROM produits;  -- Total de tous les prix
```
💡 *Exemple* : 10€ + 20€ + 30€ = 60€

**AVG** = Moyenne (Average) 📊
```sql
SELECT AVG(prix) FROM produits;  -- Prix moyen
```
💡 *Exemple* : (10 + 20 + 30) / 3 = 20€

**MIN** = Le plus petit 👇
```sql
SELECT MIN(prix) FROM produits;  -- Le moins cher
```
💡 *Exemple* : Parmi 10€, 20€, 30€ → 10€

**MAX** = Le plus grand 👆
```sql
SELECT MAX(prix) FROM produits;  -- Le plus cher
```
💡 *Exemple* : Parmi 10€, 20€, 30€ → 30€

---

**Exemple complet** 🎯 :
```sql
SELECT 
    COUNT(*) AS nombre_produits,    -- 3 produits
    SUM(prix) AS total,             -- 60€
    AVG(prix) AS prix_moyen,        -- 20€
    MIN(prix) AS moins_cher,        -- 10€
    MAX(prix) AS plus_cher          -- 30€
FROM produits;
```

💡 **Une seule requête** → Toutes les statistiques !

---

# COUNT : Compter 🔢

**Différentes variantes**

```sql
-- Compter toutes les lignes
SELECT COUNT(*) FROM utilisateurs;

-- Compter les valeurs non-NULL
SELECT COUNT(telephone) FROM utilisateurs;

-- Compter les valeurs distinctes
SELECT COUNT(DISTINCT ville) FROM utilisateurs;
```

---

**COUNT avec conditions**

```sql
-- Compter avec filtre
SELECT COUNT(*) FROM produits WHERE prix > 50;

-- Compter plusieurs conditions
SELECT 
    COUNT(*) AS total,
    COUNT(*) FILTER (WHERE prix > 100) AS produits_chers,
    COUNT(*) FILTER (WHERE stock = 0) AS en_rupture
FROM produits;
```

---

# SUM : Somme ➕

**Calculer des totaux**

```sql
-- Somme totale des prix
SELECT SUM(prix) FROM produits;

-- Somme avec calcul
SELECT SUM(prix * quantite) AS total_stock FROM produits;

-- Arrondir la somme
SELECT ROUND(SUM(prix), 2) AS total FROM commandes;
```

---

**SUM avec conditions**

```sql
-- Somme conditionnelle
SELECT 
    SUM(prix) AS total_general,
    SUM(prix) FILTER (WHERE categorie = 'Informatique') AS total_informatique
FROM produits;

-- Somme avec CASE
SELECT 
    SUM(CASE WHEN categorie = 'Informatique' THEN prix ELSE 0 END) AS total_info
FROM produits;
```

---

# AVG : Moyenne 📊

**Calculer des moyennes**

```sql
-- Moyenne simple
SELECT AVG(prix) FROM produits;

-- Moyenne arrondie
SELECT ROUND(AVG(prix), 2) AS prix_moyen FROM produits;

-- Moyenne avec filtre
SELECT AVG(note) FROM evaluations WHERE note IS NOT NULL;
```

---

**Moyenne vs médiane**

```sql
-- Moyenne (sensible aux valeurs extrêmes)
SELECT AVG(salaire) FROM employes;

-- Médiane (valeur du milieu)
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salaire) AS mediane
FROM employes;
```

---

# MIN et MAX : Extrêmes ⬇️⬆️

**Trouver les valeurs limites**

```sql
-- Prix minimum et maximum
SELECT 
    MIN(prix) AS prix_min,
    MAX(prix) AS prix_max
FROM produits;

-- Dates limites
SELECT 
    MIN(date_commande) AS premiere_commande,
    MAX(date_commande) AS derniere_commande
FROM commandes;
```

---

**MIN/MAX avec autres colonnes**

```sql
-- ⚠️ ERREUR : Mélanger agrégation et colonnes normales
SELECT nom, MIN(prix) FROM produits;  -- ERREUR !

-- ✅ Solution 1 : Sous-requête
SELECT nom, prix
FROM produits
WHERE prix = (SELECT MIN(prix) FROM produits);

-- ✅ Solution 2 : Window function (nous verrons plus tard)
SELECT DISTINCT ON (1) nom, prix
FROM produits
ORDER BY prix
LIMIT 1;
```

---

# GROUP BY : Regrouper 📦

**Syntaxe de base**

```sql
SELECT colonne_groupe, fonction_agregation(colonne)
FROM table
GROUP BY colonne_groupe;
```

---

**Exemple simple**

```sql
-- Nombre de produits par catégorie
SELECT 
    categorie,
    COUNT(*) AS nombre_produits
FROM produits
GROUP BY categorie;

-- Prix moyen par catégorie
SELECT 
    categorie,
    ROUND(AVG(prix), 2) AS prix_moyen
FROM produits
GROUP BY categorie
ORDER BY prix_moyen DESC;
```

---

**GROUP BY sur plusieurs colonnes**

```sql
-- Statistiques par catégorie et marque
SELECT 
    categorie,
    marque,
    COUNT(*) AS nombre,
    AVG(prix) AS prix_moyen,
    SUM(stock) AS stock_total
FROM produits
GROUP BY categorie, marque
ORDER BY categorie, marque;
```

---

**Règle importante avec GROUP BY**

```sql
-- ❌ ERREUR : Colonne non agrégée sans GROUP BY
SELECT categorie, nom, AVG(prix)
FROM produits
GROUP BY categorie;  -- ERREUR : nom n'est pas agrégé

-- ✅ CORRECT
SELECT categorie, AVG(prix)
FROM produits
GROUP BY categorie;

-- ✅ CORRECT : Inclure toutes les colonnes non agrégées
SELECT categorie, nom, prix
FROM produits
GROUP BY categorie, nom, prix;
```

---

# HAVING : Filtrer les groupes 🎯

**WHERE vs HAVING**

- **WHERE** : Filtre les lignes AVANT regroupement
- **HAVING** : Filtre les groupes APRÈS regroupement

---

**Exemples HAVING**

```sql
-- Catégories avec plus de 5 produits
SELECT 
    categorie,
    COUNT(*) AS nombre
FROM produits
GROUP BY categorie
HAVING COUNT(*) > 5;

-- Prix moyen > 100
SELECT 
    categorie,
    ROUND(AVG(prix), 2) AS prix_moyen
FROM produits
GROUP BY categorie
HAVING AVG(prix) > 100;
```

---

**Combiner WHERE et HAVING**

```sql
-- Produits en stock : catégories avec prix moyen > 50
SELECT 
    categorie,
    COUNT(*) AS nombre,
    ROUND(AVG(prix), 2) AS prix_moyen
FROM produits
WHERE stock > 0              -- Filtre AVANT regroupement
GROUP BY categorie
HAVING AVG(prix) > 50        -- Filtre APRÈS regroupement
ORDER BY prix_moyen DESC;
```

---

# STRING_AGG : Concaténer 🔗

**Agréger du texte**

```sql
-- Liste des noms par catégorie
SELECT 
    categorie,
    STRING_AGG(nom, ', ' ORDER BY nom) AS produits
FROM produits
GROUP BY categorie;
```

---

**Avec ORDER BY personnalisé**

```sql
-- Top 3 produits par catégorie (par prix)
SELECT 
    categorie,
    STRING_AGG(nom, ', ' ORDER BY prix DESC) AS top_produits
FROM (
    SELECT DISTINCT ON (categorie, nom) 
        categorie, nom, prix
    FROM produits
    ORDER BY categorie, prix DESC
    LIMIT 3
) sub
GROUP BY categorie;
```

---

# ARRAY_AGG : Tableau 📋

**Créer des tableaux**

```sql
-- Tableau des prix par catégorie
SELECT 
    categorie,
    ARRAY_AGG(prix ORDER BY prix) AS liste_prix
FROM produits
GROUP BY categorie;

-- Tableau des noms
SELECT 
    categorie,
    ARRAY_AGG(DISTINCT marque) AS marques
FROM produits
GROUP BY categorie;
```

---

# JSON_AGG : Format JSON 📦

**Agréger en JSON**

```sql
-- Créer un objet JSON par groupe
SELECT 
    categorie,
    JSON_AGG(
        JSON_BUILD_OBJECT(
            'nom', nom,
            'prix', prix,
            'stock', stock
        ) ORDER BY prix
    ) AS produits
FROM produits
GROUP BY categorie;
```

---

# Fonctions statistiques avancées 📊

**STDDEV : Écart-type**

```sql
-- Écart-type des prix
SELECT 
    categorie,
    ROUND(AVG(prix), 2) AS moyenne,
    ROUND(STDDEV(prix), 2) AS ecart_type
FROM produits
GROUP BY categorie;
```

---

**VARIANCE : Variance**

```sql
-- Variance des prix
SELECT 
    categorie,
    ROUND(VARIANCE(prix), 2) AS variance
FROM produits
GROUP BY categorie;
```

---

**PERCENTILE : Percentiles**

```sql
-- Quartiles des prix
SELECT 
    categorie,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY prix) AS q1,
    PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY prix) AS mediane,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY prix) AS q3
FROM produits
GROUP BY categorie;
```

---

# Window Functions : Fonctions de fenêtrage 🪟

**Qu'est-ce qu'une window function ?**

Une fonction qui calcule sur un ensemble de lignes liées à la ligne courante, SANS regrouper les résultats.

---

**ROW_NUMBER : Numéroter les lignes**

```sql
-- Numéroter tous les produits par prix
SELECT 
    nom,
    prix,
    ROW_NUMBER() OVER (ORDER BY prix DESC) AS rang
FROM produits;
```

---

**PARTITION BY : Fenêtrage par groupe**

```sql
-- Numéroter les produits par catégorie
SELECT 
    categorie,
    nom,
    prix,
    ROW_NUMBER() OVER (PARTITION BY categorie ORDER BY prix DESC) AS rang_categorie
FROM produits;
```

---

**RANK et DENSE_RANK**

```sql
-- RANK : Laisse des trous en cas d'égalité
SELECT 
    nom,
    prix,
    RANK() OVER (ORDER BY prix DESC) AS rang
FROM produits;

-- DENSE_RANK : Pas de trous
SELECT 
    nom,
    prix,
    DENSE_RANK() OVER (ORDER BY prix DESC) AS rang_dense
FROM produits;
```

---

**Exemple : Top 3 par catégorie**

```sql
-- Les 3 produits les plus chers par catégorie
SELECT * FROM (
    SELECT 
        categorie,
        nom,
        prix,
        ROW_NUMBER() OVER (PARTITION BY categorie ORDER BY prix DESC) AS rang
    FROM produits
) sub
WHERE rang <= 3
ORDER BY categorie, rang;
```

---

**LAG et LEAD : Valeurs précédentes/suivantes**

```sql
-- Comparer avec le produit précédent
SELECT 
    nom,
    prix,
    LAG(prix) OVER (ORDER BY prix) AS prix_precedent,
    prix - LAG(prix) OVER (ORDER BY prix) AS difference
FROM produits;
```

---

```sql
-- Comparer avec le produit suivant
SELECT 
    nom,
    prix,
    LEAD(prix) OVER (ORDER BY prix) AS prix_suivant
FROM produits;
```

---

**FIRST_VALUE et LAST_VALUE**

```sql
-- Comparer chaque produit au plus cher de sa catégorie
SELECT 
    categorie,
    nom,
    prix,
    FIRST_VALUE(prix) OVER (
        PARTITION BY categorie 
        ORDER BY prix DESC
    ) AS prix_max_categorie,
    ROUND((prix / FIRST_VALUE(prix) OVER (
        PARTITION BY categorie 
        ORDER BY prix DESC
    ) * 100), 2) AS pourcentage_max
FROM produits;
```

---

**Moyenne mobile (Moving Average)**

```sql
-- Moyenne sur 3 valeurs (ligne courante et 2 précédentes)
SELECT 
    date_vente,
    montant,
    AVG(montant) OVER (
        ORDER BY date_vente
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS moyenne_mobile
FROM ventes
ORDER BY date_vente;
```

---

**Cumul (Running Total)**

```sql
-- Somme cumulée
SELECT 
    date_vente,
    montant,
    SUM(montant) OVER (
        ORDER BY date_vente
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumul
FROM ventes
ORDER BY date_vente;
```

---

# ROLLUP : Sous-totaux 📊

**Créer des sous-totaux hiérarchiques**

```sql
-- Ventes par année, mois, et totaux
SELECT 
    EXTRACT(YEAR FROM date_vente) AS annee,
    EXTRACT(MONTH FROM date_vente) AS mois,
    SUM(montant) AS total
FROM ventes
GROUP BY ROLLUP(
    EXTRACT(YEAR FROM date_vente),
    EXTRACT(MONTH FROM date_vente)
)
ORDER BY annee NULLS FIRST, mois NULLS FIRST;
```

---

**Résultat avec ROLLUP**

```
annee | mois | total
------|------|-------
NULL  | NULL | 50000  (total général)
2025  | NULL | 30000  (total 2025)
2025  | 1    | 10000  (janvier 2025)
2025  | 2    | 20000  (février 2025)
2024  | NULL | 20000  (total 2024)
2024  | 12   | 20000  (décembre 2024)
```

---

# CUBE : Toutes les combinaisons 🎲

**Créer tous les sous-totaux possibles**

```sql
-- Toutes les combinaisons de catégorie et marque
SELECT 
    categorie,
    marque,
    COUNT(*) AS nombre,
    SUM(prix) AS total
FROM produits
GROUP BY CUBE(categorie, marque)
ORDER BY categorie NULLS FIRST, marque NULLS FIRST;
```

---

**Résultat avec CUBE**

```
categorie    | marque     | nombre | total
-------------|------------|--------|-------
NULL         | NULL       | 100    | 10000  (total général)
NULL         | Dell       | 20     | 2000   (total Dell)
NULL         | HP         | 30     | 3000   (total HP)
Informatique | NULL       | 50     | 5000   (total Informatique)
Informatique | Dell       | 15     | 1500
Informatique | HP         | 20     | 2000
...
```

---

# GROUPING SETS : Regroupements personnalisés 🎯

**Définir exactement les regroupements souhaités**

```sql
-- Regroupements spécifiques
SELECT 
    categorie,
    marque,
    SUM(prix) AS total
FROM produits
GROUP BY GROUPING SETS (
    (categorie, marque),
    (categorie),
    ()
)
ORDER BY categorie NULLS LAST, marque NULLS LAST;
```

---

# FILTER : Agrégation conditionnelle 🔍

**Filtrer pendant l'agrégation**

```sql
-- Statistiques par catégorie avec conditions
SELECT 
    categorie,
    COUNT(*) AS total,
    COUNT(*) FILTER (WHERE prix > 100) AS chers,
    COUNT(*) FILTER (WHERE stock = 0) AS rupture,
    AVG(prix) FILTER (WHERE stock > 0) AS prix_moyen_dispo
FROM produits
GROUP BY categorie;
```

---

**Alternative avec CASE**

```sql
-- Équivalent avec CASE
SELECT 
    categorie,
    COUNT(*) AS total,
    COUNT(CASE WHEN prix > 100 THEN 1 END) AS chers,
    SUM(CASE WHEN stock = 0 THEN 1 ELSE 0 END) AS rupture
FROM produits
GROUP BY categorie;
```

---

# Agrégations imbriquées ⚙️

**Agréger des agrégations**

```sql
-- Moyenne des moyennes par catégorie
SELECT 
    AVG(prix_moyen) AS moyenne_globale
FROM (
    SELECT 
        categorie,
        AVG(prix) AS prix_moyen
    FROM produits
    GROUP BY categorie
) sous_requete;
```

---

**Top N par groupe**

```sql
-- Les 2 clients ayant le plus dépensé par ville
SELECT ville, nom, total_depense
FROM (
    SELECT 
        ville,
        nom,
        SUM(montant_commande) AS total_depense,
        ROW_NUMBER() OVER (PARTITION BY ville ORDER BY SUM(montant_commande) DESC) AS rang
    FROM clients c
    JOIN commandes cmd ON c.id = cmd.client_id
    GROUP BY ville, nom, c.id
) sub
WHERE rang <= 2
ORDER BY ville, rang;
```

---

# Exemple complet : Analyse de ventes 📊

**Schéma de base**

```sql
CREATE TABLE ventes (
    id SERIAL PRIMARY KEY,
    date_vente DATE,
    produit VARCHAR(100),
    categorie VARCHAR(50),
    quantite INTEGER,
    prix_unitaire NUMERIC(10,2),
    region VARCHAR(50)
);
```

---

**Requête d'analyse complète**

```sql
SELECT 
    categorie,
    region,
    COUNT(*) AS nb_ventes,
    SUM(quantite) AS quantite_totale,
    ROUND(SUM(quantite * prix_unitaire), 2) AS chiffre_affaires,
    ROUND(AVG(quantite * prix_unitaire), 2) AS panier_moyen,
    MIN(date_vente) AS premiere_vente,
    MAX(date_vente) AS derniere_vente
FROM ventes
WHERE date_vente >= CURRENT_DATE - INTERVAL '1 year'
GROUP BY categorie, region
HAVING SUM(quantite * prix_unitaire) > 1000
ORDER BY chiffre_affaires DESC;
```

---

# Exercice pratique 🎯

**Analyser des données de e-commerce**

1. Créer une table `commandes` avec : id, client_id, date_commande, montant, statut
2. Créer une table `clients` avec : id, nom, ville, pays
3. Écrire des requêtes pour :
   - Nombre de commandes par statut
   - Chiffre d'affaires par pays
   - Top 5 des clients
   - Évolution mensuelle

---

**Solution - Partie 1**

```sql
CREATE TABLE clients (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    ville VARCHAR(100),
    pays VARCHAR(100)
);

CREATE TABLE commandes (
    id SERIAL PRIMARY KEY,
    client_id INTEGER REFERENCES clients(id),
    date_commande DATE NOT NULL,
    montant NUMERIC(10,2) NOT NULL,
    statut VARCHAR(50) NOT NULL
);
```

---

**Solution - Partie 2 : Données**

```sql
INSERT INTO clients (nom, ville, pays) VALUES
    ('Alice Martin', 'Paris', 'France'),
    ('Bob Smith', 'London', 'UK'),
    ('Charlie Durand', 'Lyon', 'France'),
    ('Diana Garcia', 'Madrid', 'Spain'),
    ('Eve Johnson', 'London', 'UK');
```

---

```sql
INSERT INTO commandes (client_id, date_commande, montant, statut) VALUES
    (1, '2025-01-05', 150.00, 'livre'),
    (1, '2025-01-12', 200.00, 'livre'),
    (2, '2025-01-08', 300.00, 'en_cours'),
    (3, '2025-01-10', 450.00, 'livre'),
    (3, '2025-01-15', 100.00, 'livre'),
    (4, '2025-01-18', 250.00, 'livre'),
    (5, '2025-01-20', 180.00, 'en_cours');
```

---

**Solution - Partie 3 : Requêtes**

```sql
-- 1. Nombre de commandes par statut
SELECT 
    statut,
    COUNT(*) AS nombre,
    ROUND(SUM(montant), 2) AS total
FROM commandes
GROUP BY statut
ORDER BY nombre DESC;
```

---

```sql
-- 2. Chiffre d'affaires par pays
SELECT 
    c.pays,
    COUNT(cmd.id) AS nb_commandes,
    ROUND(SUM(cmd.montant), 2) AS chiffre_affaires,
    ROUND(AVG(cmd.montant), 2) AS panier_moyen
FROM clients c
LEFT JOIN commandes cmd ON c.id = cmd.client_id
GROUP BY c.pays
ORDER BY chiffre_affaires DESC NULLS LAST;
```

---

```sql
-- 3. Top 5 des clients
SELECT 
    c.nom,
    c.ville,
    COUNT(cmd.id) AS nb_commandes,
    ROUND(SUM(cmd.montant), 2) AS total_depense
FROM clients c
LEFT JOIN commandes cmd ON c.id = cmd.client_id
GROUP BY c.id, c.nom, c.ville
ORDER BY total_depense DESC NULLS LAST
LIMIT 5;
```

---

```sql
-- 4. Évolution mensuelle
SELECT 
    TO_CHAR(date_commande, 'YYYY-MM') AS mois,
    COUNT(*) AS nb_commandes,
    ROUND(SUM(montant), 2) AS chiffre_affaires,
    ROUND(AVG(montant), 2) AS panier_moyen
FROM commandes
GROUP BY TO_CHAR(date_commande, 'YYYY-MM')
ORDER BY mois;
```

---

# Bonnes pratiques 👍

**1. Nommer les agrégations**

```sql
-- ✅ BON : Avec alias explicites
SELECT 
    categorie,
    COUNT(*) AS nombre_produits,
    AVG(prix) AS prix_moyen
FROM produits
GROUP BY categorie;

-- ❌ MAUVAIS : Sans alias
SELECT categorie, COUNT(*), AVG(prix)
FROM produits
GROUP BY categorie;
```

---

**2. Utiliser COALESCE pour les NULL**

```sql
-- Gérer les divisions par zéro et NULL
SELECT 
    categorie,
    COALESCE(SUM(montant), 0) AS total,
    COALESCE(ROUND(SUM(montant) / NULLIF(COUNT(*), 0), 2), 0) AS moyenne
FROM ventes
GROUP BY categorie;
```

---

**3. Optimiser avec index**

```sql
-- Index sur colonnes de GROUP BY
CREATE INDEX idx_produits_categorie ON produits(categorie);

-- Index composé pour GROUP BY multiple
CREATE INDEX idx_ventes_region_date ON ventes(region, date_vente);
```

---

**4. Utiliser EXPLAIN pour analyser**

```sql
-- Analyser la performance
EXPLAIN ANALYZE
SELECT 
    categorie,
    COUNT(*),
    AVG(prix)
FROM produits
GROUP BY categorie;
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Fonctions de base : COUNT, SUM, AVG, MIN, MAX
✅ GROUP BY : Regrouper les données
✅ HAVING : Filtrer les groupes
✅ Window Functions : Analyses avancées sans regrouper
✅ ROLLUP, CUBE : Sous-totaux hiérarchiques
✅ STRING_AGG, ARRAY_AGG : Agrégation de texte
✅ FILTER : Agrégation conditionnelle
✅ Fonctions statistiques : STDDEV, PERCENTILE

---

# Questions ? 🙋

Des questions sur les fonctions d'agrégation ?

**À suivre** : Sous-requêtes et CTE (Common Table Expressions)

