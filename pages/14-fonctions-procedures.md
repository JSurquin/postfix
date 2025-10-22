---
layout: new-section
routeAlias: 'fonctions-procedures'
---

<a name="fonctions-procedures" id="fonctions-procedures"></a>

# Fonctions et Procédures stockées 🔧

Créer de la logique réutilisable côté serveur

---

# Plan du module 📋

- Différence fonction vs procédure
- Langage PL/pgSQL
- Créer des fonctions
- Paramètres et retours
- Variables et structures de contrôle
- Gestion d'erreurs
- Fonctions avancées

---

# Fonction vs Procédure 🤔

**Fonctions & Procédures = Créer vos propres commandes ! 🛠️**

*Analogie* : C'est comme créer des macros dans Excel !

**Au lieu de réécrire 50 lignes de SQL à chaque fois**, vous créez une fonction !

---

**FONCTION** = Calcule et RETOURNE quelque chose 📊

```sql
CREATE FUNCTION calcul_tva(prix NUMERIC)
RETURNS NUMERIC AS $$
BEGIN
    RETURN prix * 1.20;  -- Prix TTC
END;
$$ LANGUAGE plpgsql;

-- Utilisation :
SELECT calcul_tva(100);  -- Retourne : 120
```

💡 **Caractéristiques** :
- ✅ Retourne UNE valeur
- ✅ Utilisable dans SELECT
- ❌ Pas de COMMIT/ROLLBACK

---

**PROCÉDURE** = Fait des actions, ne retourne RIEN 🎯

```sql
CREATE PROCEDURE archiver_vieilles_commandes()
LANGUAGE plpgsql AS $$
BEGIN
    INSERT INTO commandes_archivees SELECT * FROM commandes WHERE date < '2020-01-01';
    DELETE FROM commandes WHERE date < '2020-01-01';
    COMMIT;  -- ✅ Possible dans une procédure !
END;
$$;

-- Utilisation :
CALL archiver_vieilles_commandes();
```

💡 **Caractéristiques** :
- ❌ Ne retourne rien
- ✅ CALL pour l'exécuter
- ✅ Peut faire COMMIT/ROLLBACK

---

**Quand utiliser quoi ?** 🤔

📊 **Fonction** → Pour CALCULER quelque chose
- Exemple : Calculer un prix TTC, formater une date, etc.

🎯 **Procédure** → Pour FAIRE des actions
- Exemple : Archiver des données, nettoyer la base, etc.

💡 **Règle simple** : Si vous voulez utiliser le résultat dans SELECT → Fonction. Sinon → Procédure !

---

# Créer une fonction simple 📝

```sql
-- Fonction qui retourne un scalaire
CREATE FUNCTION aire_rectangle(largeur NUMERIC, hauteur NUMERIC)
RETURNS NUMERIC AS $$
BEGIN
    RETURN largeur * hauteur;
END;
$$ LANGUAGE plpgsql;

-- Utilisation
SELECT aire_rectangle(10, 5);  -- 50
```

---

**Fonction SQL simple**

```sql
-- Sans PL/pgSQL pour requêtes simples
CREATE FUNCTION get_prix_ttc(prix_ht NUMERIC)
RETURNS NUMERIC AS $$
    SELECT prix_ht * 1.20;
$$ LANGUAGE sql IMMUTABLE;

SELECT get_prix_ttc(100);  -- 120
```

---

# Paramètres de fonction 📥

**Types de paramètres**

```sql
CREATE FUNCTION test_params(
    p_in INTEGER,                    -- IN (défaut)
    p_out OUT INTEGER,               -- OUT
    p_inout INOUT INTEGER           -- INOUT
)
AS $$
BEGIN
    p_out := p_in * 2;
    p_inout := p_inout + 10;
END;
$$ LANGUAGE plpgsql;

-- Appel
SELECT * FROM test_params(5, 20);
-- Résultat : (10, 30)
```

---

**Paramètres avec valeurs par défaut**

```sql
CREATE FUNCTION saluer(nom VARCHAR DEFAULT 'Monde')
RETURNS TEXT AS $$
BEGIN
    RETURN 'Bonjour ' || nom || ' !';
END;
$$ LANGUAGE plpgsql;

SELECT saluer();           -- Bonjour Monde !
SELECT saluer('Alice');    -- Bonjour Alice !
```

---

**Paramètres nommés**

```sql
CREATE FUNCTION creer_utilisateur(
    p_nom VARCHAR,
    p_email VARCHAR,
    p_age INTEGER DEFAULT 18
)
RETURNS INTEGER AS $$
DECLARE
    v_id INTEGER;
BEGIN
    INSERT INTO utilisateurs (nom, email, age)
    VALUES (p_nom, p_email, p_age)
    RETURNING id INTO v_id;
    
    RETURN v_id;
END;
$$ LANGUAGE plpgsql;

-- Appel avec noms
SELECT creer_utilisateur(
    p_nom => 'Alice',
    p_email => 'alice@example.com'
);
```

---

# Variables et déclarations 📦

```sql
CREATE FUNCTION exemple_variables()
RETURNS TEXT AS $$
DECLARE
    v_compteur INTEGER := 0;
    v_nom VARCHAR(100);
    v_date DATE := CURRENT_DATE;
    v_total NUMERIC(10,2);
    v_record RECORD;
BEGIN
    v_compteur := v_compteur + 1;
    v_nom := 'Test';
    
    -- Récupérer depuis requête
    SELECT nom, prix INTO v_nom, v_total
    FROM produits WHERE id = 1;
    
    RETURN v_nom || ': ' || v_total::TEXT;
END;
$$ LANGUAGE plpgsql;
```

---

# Structures de contrôle ⚙️

**IF / ELSIF / ELSE**

```sql
CREATE FUNCTION categorie_age(age INTEGER)
RETURNS VARCHAR AS $$
BEGIN
    IF age < 18 THEN
        RETURN 'Mineur';
    ELSIF age >= 18 AND age < 65 THEN
        RETURN 'Adulte';
    ELSE
        RETURN 'Senior';
    END IF;
END;
$$ LANGUAGE plpgsql;
```

---

**CASE**

```sql
CREATE FUNCTION remise_client(nb_commandes INTEGER)
RETURNS NUMERIC AS $$
BEGIN
    RETURN CASE
        WHEN nb_commandes < 5 THEN 0
        WHEN nb_commandes < 10 THEN 0.05
        WHEN nb_commandes < 20 THEN 0.10
        ELSE 0.15
    END;
END;
$$ LANGUAGE plpgsql;
```

---

**Boucles LOOP**

```sql
CREATE FUNCTION compter_jusqua(n INTEGER)
RETURNS VOID AS $$
DECLARE
    i INTEGER := 1;
BEGIN
    LOOP
        EXIT WHEN i > n;
        RAISE NOTICE 'Compteur: %', i;
        i := i + 1;
    END LOOP;
END;
$$ LANGUAGE plpgsql;
```

---

**Boucles WHILE**

```sql
CREATE FUNCTION somme_jusqua(n INTEGER)
RETURNS INTEGER AS $$
DECLARE
    i INTEGER := 1;
    total INTEGER := 0;
BEGIN
    WHILE i <= n LOOP
        total := total + i;
        i := i + 1;
    END LOOP;
    
    RETURN total;
END;
$$ LANGUAGE plpgsql;
```

---

**Boucles FOR**

```sql
CREATE FUNCTION liste_nombres(debut INTEGER, fin INTEGER)
RETURNS TEXT AS $$
DECLARE
    i INTEGER;
    resultat TEXT := '';
BEGIN
    FOR i IN debut..fin LOOP
        resultat := resultat || i || ', ';
    END LOOP;
    
    RETURN TRIM(TRAILING ', ' FROM resultat);
END;
$$ LANGUAGE plpgsql;
```

---

**FOR avec requête**

```sql
CREATE FUNCTION lister_produits_chers()
RETURNS VOID AS $$
DECLARE
    produit RECORD;
BEGIN
    FOR produit IN 
        SELECT nom, prix FROM produits WHERE prix > 100
    LOOP
        RAISE NOTICE 'Produit: %, Prix: %', produit.nom, produit.prix;
    END LOOP;
END;
$$ LANGUAGE plpgsql;
```

---

# Retourner des ensembles 📊

**RETURNS SETOF**

```sql
CREATE FUNCTION produits_categorie(p_categorie VARCHAR)
RETURNS SETOF produits AS $$
BEGIN
    RETURN QUERY
    SELECT * FROM produits
    WHERE categorie = p_categorie;
END;
$$ LANGUAGE plpgsql;

-- Utilisation
SELECT * FROM produits_categorie('Informatique');
```

---

**RETURNS TABLE**

```sql
CREATE FUNCTION stats_par_categorie()
RETURNS TABLE(
    categorie VARCHAR,
    nb_produits BIGINT,
    prix_moyen NUMERIC
) AS $$
BEGIN
    RETURN QUERY
    SELECT 
        p.categorie,
        COUNT(*)::BIGINT,
        AVG(p.prix)
    FROM produits p
    GROUP BY p.categorie;
END;
$$ LANGUAGE plpgsql;
```

---

# Gestion d'erreurs 🚨

**EXCEPTION**

```sql
CREATE FUNCTION diviser(a NUMERIC, b NUMERIC)
RETURNS NUMERIC AS $$
BEGIN
    IF b = 0 THEN
        RAISE EXCEPTION 'Division par zéro interdite';
    END IF;
    
    RETURN a / b;
EXCEPTION
    WHEN division_by_zero THEN
        RAISE NOTICE 'Erreur: division par zéro';
        RETURN NULL;
    WHEN OTHERS THEN
        RAISE NOTICE 'Erreur inconnue: %', SQLERRM;
        RETURN NULL;
END;
$$ LANGUAGE plpgsql;
```

---

**RAISE pour messages**

```sql
CREATE FUNCTION exemple_raise()
RETURNS VOID AS $$
BEGIN
    RAISE DEBUG 'Message de debug';
    RAISE LOG 'Message log';
    RAISE INFO 'Message info';
    RAISE NOTICE 'Message notice';
    RAISE WARNING 'Message warning';
    RAISE EXCEPTION 'Erreur fatale';
END;
$$ LANGUAGE plpgsql;
```

---

# Procédures stockées 📋

**Créer une procédure**

```sql
CREATE PROCEDURE traiter_commandes()
LANGUAGE plpgsql AS $$
BEGIN
    -- Peut faire COMMIT/ROLLBACK
    UPDATE commandes
    SET statut = 'traitee'
    WHERE statut = 'nouvelle';
    
    COMMIT;
    
    DELETE FROM commandes
    WHERE statut = 'annulee'
    AND date_commande < CURRENT_DATE - INTERVAL '1 year';
    
    COMMIT;
END;
$$;

-- Appel
CALL traiter_commandes();
```

---

**Procédure avec paramètres**

```sql
CREATE PROCEDURE archiver_donnees(p_annee INTEGER)
LANGUAGE plpgsql AS $$
BEGIN
    -- Copier dans archive
    INSERT INTO commandes_archive
    SELECT * FROM commandes
    WHERE EXTRACT(YEAR FROM date_commande) = p_annee;
    
    COMMIT;
    
    -- Supprimer originaux
    DELETE FROM commandes
    WHERE EXTRACT(YEAR FROM date_commande) = p_annee;
    
    COMMIT;
    
    RAISE NOTICE 'Archivage année % terminé', p_annee;
END;
$$;

CALL archiver_donnees(2023);
```

---

# Fonctions d'agrégation personnalisées 📈

**Créer une agrégation**

```sql
-- Fonction de transition
CREATE FUNCTION mediane_transition(state NUMERIC[], val NUMERIC)
RETURNS NUMERIC[] AS $$
BEGIN
    RETURN array_append(state, val);
END;
$$ LANGUAGE plpgsql;

-- Fonction finale
CREATE FUNCTION mediane_final(state NUMERIC[])
RETURNS NUMERIC AS $$
DECLARE
    sorted NUMERIC[];
    len INTEGER;
BEGIN
    sorted := ARRAY(SELECT unnest(state) ORDER BY 1);
    len := array_length(sorted, 1);
    
    IF len % 2 = 0 THEN
        RETURN (sorted[len/2] + sorted[len/2 + 1]) / 2.0;
    ELSE
        RETURN sorted[(len + 1) / 2];
    END IF;
END;
$$ LANGUAGE plpgsql;

-- Créer l'agrégat
CREATE AGGREGATE mediane(NUMERIC) (
    SFUNC = mediane_transition,
    STYPE = NUMERIC[],
    FINALFUNC = mediane_final
);

-- Utilisation
SELECT categorie, mediane(prix)
FROM produits
GROUP BY categorie;
```

---

# Fonctions de fenêtrage personnalisées 🪟

**IMMUTABLE, STABLE, VOLATILE**

```sql
-- IMMUTABLE : Résultat constant pour mêmes params
CREATE FUNCTION aire_cercle(rayon NUMERIC)
RETURNS NUMERIC AS $$
BEGIN
    RETURN PI() * rayon * rayon;
END;
$$ LANGUAGE plpgsql IMMUTABLE;

-- STABLE : Résultat constant dans une transaction
CREATE FUNCTION prix_avec_taux_jour(prix NUMERIC)
RETURNS NUMERIC AS $$
BEGIN
    RETURN prix * (SELECT taux FROM taux_change WHERE date = CURRENT_DATE);
END;
$$ LANGUAGE plpgsql STABLE;

-- VOLATILE : Peut changer à chaque appel (défaut)
CREATE FUNCTION generer_token()
RETURNS TEXT AS $$
BEGIN
    RETURN md5(random()::TEXT || clock_timestamp()::TEXT);
END;
$$ LANGUAGE plpgsql VOLATILE;
```

---

# Fonctions de sécurité 🔐

**SECURITY DEFINER**

```sql
-- Fonction exécutée avec droits du créateur
CREATE FUNCTION augmenter_salaire(p_employe_id INTEGER, p_montant NUMERIC)
RETURNS VOID AS $$
BEGIN
    UPDATE employes
    SET salaire = salaire + p_montant
    WHERE id = p_employe_id;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Utilisateur sans droits sur employes peut appeler la fonction
```

---

# Fonctions polymorphiques 🔄

**ANYELEMENT pour types génériques**

```sql
CREATE FUNCTION premier_element(arr ANYARRAY)
RETURNS ANYELEMENT AS $$
BEGIN
    RETURN arr[1];
END;
$$ LANGUAGE plpgsql;

SELECT premier_element(ARRAY[1,2,3]);        -- 1
SELECT premier_element(ARRAY['a','b','c']);  -- 'a'
```

---

# Optimisation des fonctions 🚀

**PARALLEL SAFE**

```sql
CREATE FUNCTION calculer_score(val INTEGER)
RETURNS INTEGER AS $$
BEGIN
    RETURN val * 2 + 10;
END;
$$ LANGUAGE plpgsql PARALLEL SAFE;
```

---

**Inlining SQL simple**

```sql
-- PostgreSQL peut inline les fonctions SQL simples
CREATE FUNCTION get_nom_client(p_id INTEGER)
RETURNS VARCHAR AS $$
    SELECT nom FROM clients WHERE id = p_id;
$$ LANGUAGE sql STABLE;
```

---

# Bonnes pratiques 👍

**1. Préfixer les variables**

```sql
CREATE FUNCTION exemple_prefixes(p_id INTEGER)
RETURNS VARCHAR AS $$
DECLARE
    v_nom VARCHAR;  -- v_ pour variables
    l_temp VARCHAR; -- l_ pour locales
BEGIN
    SELECT nom INTO v_nom FROM utilisateurs WHERE id = p_id;
    RETURN v_nom;
END;
$$ LANGUAGE plpgsql;
```

---

**2. Gérer les erreurs**

```sql
CREATE FUNCTION fonction_securisee(p_id INTEGER)
RETURNS VARCHAR AS $$
BEGIN
    -- Code...
    RETURN resultat;
EXCEPTION
    WHEN no_data_found THEN
        RETURN NULL;
    WHEN OTHERS THEN
        RAISE LOG 'Erreur dans fonction_securisee: %', SQLERRM;
        RETURN NULL;
END;
$$ LANGUAGE plpgsql;
```

---

**3. Utiliser OR REPLACE**

```sql
-- Modifiable sans DROP
CREATE OR REPLACE FUNCTION ma_fonction()
RETURNS TEXT AS $$
BEGIN
    RETURN 'Version 2';
END;
$$ LANGUAGE plpgsql;
```

---

**4. Documenter**

```sql
CREATE FUNCTION calculer_remise(p_montant NUMERIC)
RETURNS NUMERIC AS $$
-- Calcule la remise selon le montant
-- Paramètres:
--   p_montant: Montant de la commande
-- Retourne:
--   Montant de la remise (0-15%)
BEGIN
    RETURN CASE
        WHEN p_montant < 100 THEN 0
        WHEN p_montant < 500 THEN p_montant * 0.05
        ELSE p_montant * 0.15
    END;
END;
$$ LANGUAGE plpgsql IMMUTABLE;

COMMENT ON FUNCTION calculer_remise IS 'Calcule la remise client selon le montant';
```

---

# Exercice pratique 🎯

Créer un système de gestion de stock avec fonctions :
1. `verifier_stock(produit_id, quantite)` : Vérifier disponibilité
2. `commander_produit(produit_id, quantite)` : Passer commande
3. `reapprovisionner(produit_id, quantite)` : Ajouter stock
4. Procédure `alertes_stock_bas()` : Envoyer alertes

---

**Solution**

```sql
-- 1. Vérifier stock
CREATE FUNCTION verifier_stock(p_produit_id INTEGER, p_quantite INTEGER)
RETURNS BOOLEAN AS $$
DECLARE
    v_stock INTEGER;
BEGIN
    SELECT stock INTO v_stock
    FROM produits
    WHERE id = p_produit_id;
    
    IF NOT FOUND THEN
        RAISE EXCEPTION 'Produit % non trouvé', p_produit_id;
    END IF;
    
    RETURN v_stock >= p_quantite;
END;
$$ LANGUAGE plpgsql;
```

---

```sql
-- 2. Commander produit
CREATE FUNCTION commander_produit(
    p_produit_id INTEGER,
    p_quantite INTEGER,
    p_client_id INTEGER
)
RETURNS INTEGER AS $$
DECLARE
    v_commande_id INTEGER;
    v_prix NUMERIC;
BEGIN
    -- Vérifier stock
    IF NOT verifier_stock(p_produit_id, p_quantite) THEN
        RAISE EXCEPTION 'Stock insuffisant';
    END IF;
    
    -- Récupérer prix
    SELECT prix INTO v_prix FROM produits WHERE id = p_produit_id;
    
    -- Créer commande
    INSERT INTO commandes (client_id, montant, date_commande)
    VALUES (p_client_id, v_prix * p_quantite, NOW())
    RETURNING id INTO v_commande_id;
    
    -- Diminuer stock
    UPDATE produits
    SET stock = stock - p_quantite
    WHERE id = p_produit_id;
    
    RETURN v_commande_id;
EXCEPTION
    WHEN OTHERS THEN
        RAISE NOTICE 'Erreur commande: %', SQLERRM;
        RETURN NULL;
END;
$$ LANGUAGE plpgsql;
```

---

```sql
-- 3. Réapprovisionner
CREATE FUNCTION reapprovisionner(p_produit_id INTEGER, p_quantite INTEGER)
RETURNS VOID AS $$
BEGIN
    UPDATE produits
    SET stock = stock + p_quantite,
        date_maj = NOW()
    WHERE id = p_produit_id;
    
    IF NOT FOUND THEN
        RAISE EXCEPTION 'Produit % non trouvé', p_produit_id;
    END IF;
    
    RAISE NOTICE 'Produit % réapprovisionné de %', p_produit_id, p_quantite;
END;
$$ LANGUAGE plpgsql;
```

---

```sql
-- 4. Alertes stock bas
CREATE PROCEDURE alertes_stock_bas()
LANGUAGE plpgsql AS $$
DECLARE
    produit RECORD;
BEGIN
    FOR produit IN
        SELECT id, nom, stock, stock_min
        FROM produits
        WHERE stock < stock_min
    LOOP
        RAISE WARNING 'Stock bas pour %: % (min: %)',
            produit.nom, produit.stock, produit.stock_min;
        
        -- Insérer dans table alertes
        INSERT INTO alertes (type, produit_id, message, date_alerte)
        VALUES (
            'stock_bas',
            produit.id,
            format('Stock: %s (min: %s)', produit.stock, produit.stock_min),
            NOW()
        );
        
        COMMIT;
    END LOOP;
END;
$$;

-- Appeler avec cron
CALL alertes_stock_bas();
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Fonctions : retournent valeur, utilisables dans SELECT
✅ Procédures : pas de retour, gestion transactions
✅ PL/pgSQL : langage procédural complet
✅ Variables, structures de contrôle, boucles
✅ Gestion d'erreurs avec EXCEPTION
✅ RETURNS SETOF / TABLE pour ensembles
✅ IMMUTABLE, STABLE, VOLATILE pour optimisation
✅ SECURITY DEFINER pour contrôle d'accès

---

# Questions ? 🙋

Des questions sur les fonctions et procédures ?

**À suivre** : Triggers

