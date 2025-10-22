---
layout: new-section
routeAlias: 'vues'
---

<a name="vues" id="vues"></a>

# Vues et Vues matérialisées 👁️

Simplifier les requêtes complexes et optimiser les performances

---

# Plan du module 📋

- Vues simples
- Vues updatable
- Vues matérialisées
- Refresh des vues
- WITH CHECK OPTION
- Sécurité avec les vues
- Bonnes pratiques

---

# Qu'est-ce qu'une vue ? 🤔

**Une vue = Un raccourci vers une requête complexe ! ⚡**

*Analogie* : C'est comme créer un favori/bookmark dans votre navigateur !

**Imaginez** que vous tapez souvent cette requête longue :
```sql
SELECT u.nom, u.email, COUNT(c.id) AS nb_commandes, SUM(c.total) AS montant
FROM utilisateurs u
LEFT JOIN commandes c ON u.id = c.user_id
WHERE u.actif = true
GROUP BY u.id, u.nom, u.email;
```

**Avec une vue**, vous pouvez faire :
```sql
CREATE VIEW utilisateurs_stats AS 
[la requête complexe ci-dessus];

-- Puis simplement :
SELECT * FROM utilisateurs_stats;  -- 🎉 Simple !
```

**Les avantages** ✨ :
- 💾 **Pas de données stockées** → C'est juste un "alias" de requête
- 🔄 **Toujours à jour** → Calculée à chaque appel
- 🎯 **Simplifie le code** → Réutilisable partout
- 🔒 **Sécurité** → Cacher certaines colonnes sensibles

💡 **En résumé** : Une vue = Donner un nom court à une requête longue !

---

# Créer une vue simple 📊

**Syntaxe de base**

```sql
CREATE VIEW nom_vue AS
SELECT ...
FROM ...
WHERE ...;
```

---

**Exemple simple**

```sql
-- Vue des produits disponibles
CREATE VIEW produits_disponibles AS
SELECT 
    id,
    nom,
    prix,
    stock
FROM produits
WHERE stock > 0 AND actif = true;
```

---

**Utiliser une vue**

```sql
-- Comme une table normale
SELECT * FROM produits_disponibles;

SELECT nom, prix 
FROM produits_disponibles 
WHERE prix < 100;
```

---

**Vue avec jointures**

```sql
CREATE VIEW articles_complets AS
SELECT 
    a.id,
    a.titre,
    a.date_publication,
    au.nom AS auteur,
    c.nom AS categorie,
    COUNT(com.id) AS nb_commentaires
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id
LEFT JOIN categories c ON a.categorie_id = c.id
LEFT JOIN commentaires com ON a.id = com.article_id
WHERE a.publie = true
GROUP BY a.id, a.titre, a.date_publication, au.nom, c.nom;
```

---

**Vue avec calculs**

```sql
CREATE VIEW statistiques_clients AS
SELECT 
    c.id,
    c.nom,
    c.email,
    COUNT(cmd.id) AS nb_commandes,
    COALESCE(SUM(cmd.montant), 0) AS total_depense,
    COALESCE(AVG(cmd.montant), 0) AS panier_moyen,
    MAX(cmd.date_commande) AS derniere_commande
FROM clients c
LEFT JOIN commandes cmd ON c.id = cmd.client_id
GROUP BY c.id, c.nom, c.email;
```

---

# OR REPLACE : Modifier une vue 🔄

```sql
-- Remplacer une vue existante
CREATE OR REPLACE VIEW produits_disponibles AS
SELECT 
    id,
    nom,
    prix,
    stock,
    categorie  -- Nouvelle colonne
FROM produits
WHERE stock > 0 AND actif = true;
```

**⚠️ Ne peut pas changer les types des colonnes existantes**

---

# Vues avec CTE 🎯

```sql
CREATE VIEW ventes_mensuelles AS
WITH ventes_par_mois AS (
    SELECT 
        DATE_TRUNC('month', date_vente) AS mois,
        produit_id,
        SUM(quantite) AS quantite_totale,
        SUM(montant) AS montant_total
    FROM ventes
    GROUP BY DATE_TRUNC('month', date_vente), produit_id
)
SELECT 
    vm.mois,
    p.nom AS produit,
    vm.quantite_totale,
    vm.montant_total
FROM ventes_par_mois vm
JOIN produits p ON vm.produit_id = p.id
ORDER BY vm.mois DESC, vm.montant_total DESC;
```

---

# Vues updatable (modifiables) ✏️

**Conditions pour être updatable**

1. SELECT depuis UNE seule table
2. Pas de DISTINCT, GROUP BY, HAVING
3. Pas de UNION, INTERSECT, EXCEPT
4. Pas de fonctions d'agrégation
5. Pas de WINDOW functions

---

**Vue simple updatable**

```sql
CREATE VIEW utilisateurs_actifs AS
SELECT id, nom, email, age
FROM utilisateurs
WHERE actif = true;

-- INSERT fonctionne
INSERT INTO utilisateurs_actifs (nom, email, age)
VALUES ('Alice Martin', 'alice@example.com', 30);

-- UPDATE fonctionne
UPDATE utilisateurs_actifs
SET age = 31
WHERE nom = 'Alice Martin';

-- DELETE fonctionne
DELETE FROM utilisateurs_actifs WHERE id = 10;
```

---

**WITH CHECK OPTION**

```sql
CREATE VIEW utilisateurs_actifs AS
SELECT id, nom, email, age, actif
FROM utilisateurs
WHERE actif = true
WITH CHECK OPTION;

-- ✅ OK
INSERT INTO utilisateurs_actifs (nom, email, actif)
VALUES ('Bob', 'bob@example.com', true);

-- ❌ ERREUR : viole la condition WHERE actif = true
INSERT INTO utilisateurs_actifs (nom, email, actif)
VALUES ('Charlie', 'charlie@example.com', false);
```

---

**WITH LOCAL vs CASCADED CHECK OPTION**

```sql
-- LOCAL : Vérifie uniquement cette vue
CREATE VIEW vue1 AS
SELECT * FROM utilisateurs WHERE age >= 18
WITH LOCAL CHECK OPTION;

-- CASCADED : Vérifie cette vue ET les sous-vues
CREATE VIEW vue2 AS
SELECT * FROM vue1 WHERE pays = 'France'
WITH CASCADED CHECK OPTION;
```

---

# Vues matérialisées 💾

**Stocke réellement les données**

```sql
CREATE MATERIALIZED VIEW stats_produits AS
SELECT 
    categorie,
    COUNT(*) AS nb_produits,
    AVG(prix) AS prix_moyen,
    SUM(stock) AS stock_total
FROM produits
GROUP BY categorie;
```

---

**Différences vue vs vue matérialisée**

| | Vue simple | Vue matérialisée |
|---|------------|------------------|
| Stockage | Aucun | Oui |
| Fraîcheur | Toujours à jour | Doit être rafraîchie |
| Performance | Calcul à chaque SELECT | Rapide (pré-calculé) |
| Espace disque | Aucun | Occupe de l'espace |
| Index | Non | Oui ! |

---

**Quand utiliser une vue matérialisée**

- Requêtes lourdes et lentes
- Données changent rarement
- Besoin de performance
- Rapports et tableaux de bord
- Agrégations complexes

---

# REFRESH : Actualiser une vue matérialisée 🔄

**Rafraîchir manuellement**

```sql
-- Rafraîchir (bloque la vue)
REFRESH MATERIALIZED VIEW stats_produits;

-- Rafraîchir sans bloquer (PostgreSQL 9.4+)
REFRESH MATERIALIZED VIEW CONCURRENTLY stats_produits;
```

---

**Rafraîchissement automatique avec cron**

```sql
-- Script à exécuter périodiquement
-- refresh_views.sql
REFRESH MATERIALIZED VIEW CONCURRENTLY stats_produits;
REFRESH MATERIALIZED VIEW CONCURRENTLY ventes_mensuelles;
REFRESH MATERIALIZED VIEW CONCURRENTLY top_clients;
```

```bash
# Crontab : tous les jours à 2h du matin
0 2 * * * psql -d mabase -f /path/to/refresh_views.sql
```

---

**Avec déclencheur (trigger)**

```sql
-- Fonction pour rafraîchir
CREATE OR REPLACE FUNCTION refresh_stats_produits()
RETURNS TRIGGER AS $$
BEGIN
    REFRESH MATERIALIZED VIEW CONCURRENTLY stats_produits;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql;

-- Trigger après modification de produits
CREATE TRIGGER trg_refresh_stats
AFTER INSERT OR UPDATE OR DELETE ON produits
FOR EACH STATEMENT
EXECUTE FUNCTION refresh_stats_produits();
```

---

# Index sur vues matérialisées 📊

**Créer des index**

```sql
-- Vue matérialisée
CREATE MATERIALIZED VIEW articles_recherche AS
SELECT 
    id,
    titre,
    contenu,
    auteur_id,
    to_tsvector('french', titre || ' ' || contenu) AS search_vector
FROM articles
WHERE publie = true;

-- Index pour recherche rapide
CREATE INDEX idx_articles_search 
ON articles_recherche USING GIN(search_vector);

CREATE INDEX idx_articles_auteur 
ON articles_recherche(auteur_id);
```

---

# WITH NO DATA : Créer sans données 📋

```sql
-- Créer la structure sans calculer les données
CREATE MATERIALIZED VIEW stats_lourdes AS
SELECT ...
FROM ...
WITH NO DATA;

-- Plus tard, populer
REFRESH MATERIALIZED VIEW stats_lourdes;
```

**Utile pour tester la structure avant calcul long**

---

# Dépendances entre vues 🔗

**Vue basée sur une autre vue**

```sql
-- Vue 1
CREATE VIEW produits_actifs AS
SELECT * FROM produits WHERE actif = true;

-- Vue 2 basée sur Vue 1
CREATE VIEW produits_chers AS
SELECT * FROM produits_actifs WHERE prix > 100;

-- Vue 3 basée sur Vue 2
CREATE VIEW produits_chers_stock AS
SELECT * FROM produits_chers WHERE stock > 0;
```

**⚠️ Attention aux performances avec vues imbriquées !**

---

# Informations système 📊

**Lister les vues**

```sql
-- Toutes les vues
SELECT table_name, view_definition
FROM information_schema.views
WHERE table_schema = 'public';

-- Dans psql
\dv

-- Vues matérialisées
\dm
```

---

**Voir la définition d'une vue**

```sql
-- Définition SQL
SELECT pg_get_viewdef('nom_vue', true);

-- Ou dans psql
\d+ nom_vue
```

---

**Dépendances d'une vue**

```sql
-- Tables/vues utilisées par une vue
SELECT DISTINCT
    source_ns.nspname AS source_schema,
    source_table.relname AS source_table
FROM pg_depend
JOIN pg_rewrite ON pg_depend.objid = pg_rewrite.oid
JOIN pg_class AS dependent_view ON pg_rewrite.ev_class = dependent_view.oid
JOIN pg_class AS source_table ON pg_depend.refobjid = source_table.oid
JOIN pg_namespace dependent_ns ON dependent_ns.oid = dependent_view.relnamespace
JOIN pg_namespace source_ns ON source_ns.oid = source_table.relnamespace
WHERE dependent_view.relname = 'nom_vue';
```

---

# Supprimer une vue 🗑️

```sql
-- Supprimer une vue
DROP VIEW produits_disponibles;

-- Si existe
DROP VIEW IF EXISTS produits_disponibles;

-- Avec dépendances
DROP VIEW produits_disponibles CASCADE;

-- Vue matérialisée
DROP MATERIALIZED VIEW stats_produits;
```

---

# Sécurité avec les vues 🔐

**Masquer des colonnes sensibles**

```sql
-- Table avec données sensibles
CREATE TABLE employes (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    email VARCHAR(255),
    salaire NUMERIC(10,2),    -- Sensible
    numero_ss VARCHAR(20),     -- Sensible
    departement VARCHAR(50)
);

-- Vue publique (sans infos sensibles)
CREATE VIEW employes_publics AS
SELECT 
    id,
    nom,
    email,
    departement
FROM employes;

-- Donner accès uniquement à la vue
GRANT SELECT ON employes_publics TO role_public;
```

---

**Filtrer les données par utilisateur**

```sql
-- Vue montrant uniquement les données de l'utilisateur connecté
CREATE VIEW mes_commandes AS
SELECT 
    id,
    date_commande,
    montant,
    statut
FROM commandes
WHERE client_id = (
    SELECT id FROM clients WHERE email = CURRENT_USER
);
```

---

# SECURITY BARRIER : Protection renforcée 🛡️

```sql
-- Vue avec barrière de sécurité
CREATE VIEW commandes_autorisees
WITH (security_barrier = true) AS
SELECT *
FROM commandes
WHERE client_id IN (
    SELECT client_id FROM autorisations WHERE user_id = CURRENT_USER
);
```

**Empêche l'optimiseur de déplacer les conditions de sécurité**

---

# Cas d'usage pratiques 💼

**1. Dashboard e-commerce**

```sql
CREATE MATERIALIZED VIEW dashboard_ventes AS
SELECT 
    DATE_TRUNC('day', date_vente) AS jour,
    COUNT(*) AS nb_ventes,
    SUM(montant) AS chiffre_affaires,
    AVG(montant) AS panier_moyen,
    COUNT(DISTINCT client_id) AS clients_uniques
FROM ventes
WHERE date_vente >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY DATE_TRUNC('day', date_vente);

CREATE INDEX idx_dashboard_jour ON dashboard_ventes(jour);

-- Rafraîchir toutes les heures
```

---

**2. Rapport de performance**

```sql
CREATE VIEW performance_vendeurs AS
SELECT 
    v.id AS vendeur_id,
    v.nom,
    COUNT(cmd.id) AS nb_ventes,
    SUM(cmd.montant) AS ca_total,
    AVG(cmd.montant) AS panier_moyen,
    RANK() OVER (ORDER BY SUM(cmd.montant) DESC) AS classement
FROM vendeurs v
LEFT JOIN commandes cmd ON v.id = cmd.vendeur_id
WHERE cmd.date_commande >= DATE_TRUNC('month', CURRENT_DATE)
GROUP BY v.id, v.nom;
```

---

**3. Cache de recherche**

```sql
CREATE MATERIALIZED VIEW produits_recherche AS
SELECT 
    p.id,
    p.sku,
    p.nom,
    p.description,
    p.prix,
    c.nom AS categorie,
    to_tsvector('french', 
        p.nom || ' ' || 
        COALESCE(p.description, '') || ' ' || 
        c.nom
    ) AS search_vector
FROM produits p
LEFT JOIN categories c ON p.categorie_id = c.id
WHERE p.actif = true;

CREATE INDEX idx_search ON produits_recherche USING GIN(search_vector);
```

---

# Exercice pratique 🎯

**Créer des vues pour un système de blog**

1. Vue `articles_publies` : articles avec auteurs
2. Vue matérialisée `stats_auteurs` : nb articles, commentaires
3. Vue `top_articles` : plus commentés du mois
4. Vue sécurisée `mes_articles` : articles de l'utilisateur

---

**Solution - Partie 1**

```sql
-- 1. Articles publiés avec infos complètes
CREATE VIEW articles_publies AS
SELECT 
    a.id,
    a.titre,
    a.slug,
    a.contenu,
    a.date_publication,
    au.nom AS auteur,
    au.email AS auteur_email,
    c.nom AS categorie,
    (SELECT COUNT(*) FROM commentaires WHERE article_id = a.id) AS nb_commentaires,
    a.vues
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id
LEFT JOIN categories c ON a.categorie_id = c.id
WHERE a.publie = true;
```

---

**Solution - Partie 2**

```sql
-- 2. Statistiques par auteur (matérialisée)
CREATE MATERIALIZED VIEW stats_auteurs AS
SELECT 
    au.id,
    au.nom,
    au.email,
    COUNT(DISTINCT a.id) AS nb_articles,
    COUNT(DISTINCT CASE WHEN a.publie THEN a.id END) AS nb_articles_publies,
    COUNT(c.id) AS nb_commentaires_recus,
    SUM(a.vues) AS total_vues,
    MAX(a.date_publication) AS dernier_article
FROM auteurs au
LEFT JOIN articles a ON au.id = a.auteur_id
LEFT JOIN commentaires c ON a.id = c.article_id
GROUP BY au.id, au.nom, au.email;

CREATE INDEX idx_stats_auteurs_articles 
ON stats_auteurs(nb_articles_publies DESC);
```

---

**Solution - Partie 3**

```sql
-- 3. Top articles du mois
CREATE VIEW top_articles_mois AS
SELECT 
    a.id,
    a.titre,
    au.nom AS auteur,
    COUNT(c.id) AS nb_commentaires,
    a.vues,
    ROW_NUMBER() OVER (ORDER BY COUNT(c.id) DESC, a.vues DESC) AS rang
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id
LEFT JOIN commentaires c ON a.id = c.article_id
WHERE a.publie = true
  AND a.date_publication >= DATE_TRUNC('month', CURRENT_DATE)
GROUP BY a.id, a.titre, au.nom, a.vues
ORDER BY nb_commentaires DESC, a.vues DESC
LIMIT 10;
```

---

**Solution - Partie 4**

```sql
-- 4. Vue sécurisée pour l'utilisateur connecté
CREATE VIEW mes_articles AS
SELECT 
    a.id,
    a.titre,
    a.slug,
    a.contenu,
    a.publie,
    a.date_publication,
    a.vues,
    (SELECT COUNT(*) FROM commentaires WHERE article_id = a.id) AS nb_commentaires
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id
WHERE au.email = CURRENT_USER;

-- Avec CHECK OPTION pour empêcher modification hors périmètre
CREATE OR REPLACE VIEW mes_articles AS
SELECT 
    a.id,
    a.titre,
    a.slug,
    a.contenu,
    a.publie,
    a.date_publication
FROM articles a
JOIN auteurs au ON a.auteur_id = au.id
WHERE au.email = CURRENT_USER
WITH CHECK OPTION;
```

---

# Bonnes pratiques 👍

**1. Nommer clairement**

```sql
-- ✅ BON : Nom descriptif
CREATE VIEW clients_premium_actifs AS ...

-- ❌ MAUVAIS : Nom vague
CREATE VIEW vue1 AS ...
```

---

**2. Documenter les vues**

```sql
CREATE VIEW statistiques_ventes AS
SELECT ...;

COMMENT ON VIEW statistiques_ventes IS 
'Statistiques de vente quotidiennes pour le dashboard. 
Rafraîchir toutes les heures via cron.
Utilise la vue matérialisée pour performance.';
```

---

**3. Vues matérialisées pour requêtes lourdes**

```sql
-- ✅ BON : Requête lente → vue matérialisée
CREATE MATERIALIZED VIEW rapport_mensuel AS
SELECT ... -- Agrégations complexes
FROM ... -- Multiples jointures
WHERE ... -- Calculs lourds

-- ❌ MAUVAIS : Vue simple pour requête lente
CREATE VIEW rapport_mensuel AS
SELECT ... -- Recalculé à chaque SELECT !
```

---

**4. Index sur vues matérialisées**

```sql
-- ✅ BON : Index pour accès rapide
CREATE MATERIALIZED VIEW stats AS ...;
CREATE INDEX idx_stats_date ON stats(date);
CREATE INDEX idx_stats_category ON stats(category);
```

---

**5. Rafraîchissement régulier**

```sql
-- Stratégies :
-- - Cron job quotidien/horaire
-- - Trigger après modifications
-- - Manuellement pour rapports
-- - CONCURRENTLY pour éviter blocage
```

---

**6. Éviter vues trop imbriquées**

```sql
-- ❌ MAUVAIS : 5 niveaux de vues
CREATE VIEW v5 AS SELECT * FROM v4;
CREATE VIEW v4 AS SELECT * FROM v3;
CREATE VIEW v3 AS SELECT * FROM v2;
-- ... Performance catastrophique

-- ✅ MIEUX : Limiter à 2-3 niveaux max
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Vues simples : requêtes nommées, toujours à jour
✅ Vues updatable : INSERT/UPDATE/DELETE possibles
✅ WITH CHECK OPTION : garantir contraintes
✅ Vues matérialisées : stockées, rapides, à rafraîchir
✅ REFRESH MATERIALIZED VIEW : actualiser
✅ Index sur vues matérialisées : optimiser
✅ Sécurité : masquer colonnes, filtrer données
✅ Documentation et nommage clairs

---

# Questions ? 🙋

Des questions sur les vues et vues matérialisées ?

**À suivre** : Transactions et gestion de concurrence

