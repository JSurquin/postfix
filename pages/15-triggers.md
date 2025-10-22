---
layout: new-section
routeAlias: 'triggers'
---

<a name="triggers" id="triggers"></a>

# Triggers ⚡

Automatiser les actions lors de modifications de données

---

# Plan du module 📋

- Qu'est-ce qu'un trigger
- Types de triggers
- Créer des triggers
- Variables OLD et NEW
- Trigger functions
- Event triggers
- Cas d'usage pratiques

---

# Qu'est-ce qu'un trigger ? 🤔

**Trigger = Action automatique qui se déclenche toute seule ! ⚡**

*Analogie* : C'est comme les phares automatiques d'une voiture !
- Dès qu'il fait noir → 💡 Les phares s'allument AUTOMATIQUEMENT
- Vous ne faites RIEN, ça se fait tout seul !

**En PostgreSQL** :
```
Quelqu'un INSERT/UPDATE/DELETE une ligne 
    → 🎯 PostgreSQL exécute AUTOMATIQUEMENT une fonction
```

**Exemples concrets** 💡 :

1️⃣ **Audit automatique**
- Quelqu'un supprime un client → Le trigger sauvegarde qui et quand

2️⃣ **Mise à jour auto de la date**
- Quelqu'un modifie un article → Le trigger met `date_modification = NOW()`

3️⃣ **Validation de données**
- Quelqu'un insère un prix négatif → Le trigger refuse !

4️⃣ **Calcul automatique**
- Quelqu'un ajoute une ligne de commande → Le trigger recalcule le total

💡 **En résumé** : Les triggers = Vos assistants automatiques qui travaillent en arrière-plan !

---

# Créer un trigger 🔧

**2 étapes : Fonction + Trigger**

```sql
-- 1. Créer la fonction trigger
CREATE OR REPLACE FUNCTION update_timestamp()
RETURNS TRIGGER AS $$
BEGIN
    NEW.date_maj = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 2. Créer le trigger
CREATE TRIGGER trg_update_timestamp
BEFORE UPDATE ON produits
FOR EACH ROW
EXECUTE FUNCTION update_timestamp();
```

---

# Types de triggers ⚙️

**Timing : BEFORE vs AFTER**

```sql
-- BEFORE : Avant modification (peut modifier les données)
CREATE TRIGGER trg_before
BEFORE INSERT OR UPDATE ON table
FOR EACH ROW
EXECUTE FUNCTION fonction();

-- AFTER : Après modification (données déjà modifiées)
CREATE TRIGGER trg_after
AFTER INSERT OR UPDATE ON table
FOR EACH ROW
EXECUTE FUNCTION fonction();
```

---

**FOR EACH ROW vs FOR EACH STATEMENT**

```sql
-- FOR EACH ROW : Exécuté pour chaque ligne
CREATE TRIGGER trg_row
AFTER INSERT ON table
FOR EACH ROW
EXECUTE FUNCTION fonction();

-- FOR EACH STATEMENT : Exécuté une fois par commande
CREATE TRIGGER trg_statement
AFTER INSERT ON table
FOR EACH STATEMENT
EXECUTE FUNCTION fonction();
```

---

# Variables OLD et NEW 📦

**Dans les fonctions trigger**

- **NEW** : Nouvelle valeur (INSERT, UPDATE)
- **OLD** : Ancienne valeur (UPDATE, DELETE)
- **TG_OP** : Opération ('INSERT', 'UPDATE', 'DELETE')
- **TG_TABLE_NAME** : Nom de la table

```sql
CREATE OR REPLACE FUNCTION audit_changes()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        INSERT INTO audit_log VALUES (NEW.id, 'INSERT', NOW());
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO audit_log VALUES (OLD.id, 'UPDATE', NOW());
    ELSIF TG_OP = 'DELETE' THEN
        INSERT INTO audit_log VALUES (OLD.id, 'DELETE', NOW());
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

---

# Trigger d'audit 📝

**Tracer les modifications**

```sql
CREATE TABLE audit_log (
    id SERIAL PRIMARY KEY,
    table_name VARCHAR(100),
    operation VARCHAR(10),
    user_name VARCHAR(100),
    changed_at TIMESTAMP DEFAULT NOW(),
    old_data JSONB,
    new_data JSONB
);
```

---

```sql
CREATE OR REPLACE FUNCTION audit_trigger()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO audit_log (table_name, operation, user_name, old_data, new_data)
    VALUES (
        TG_TABLE_NAME,
        TG_OP,
        CURRENT_USER,
        CASE WHEN TG_OP != 'INSERT' THEN row_to_json(OLD) END,
        CASE WHEN TG_OP != 'DELETE' THEN row_to_json(NEW) END
    );
    
    IF TG_OP = 'DELETE' THEN
        RETURN OLD;
    ELSE
        RETURN NEW;
    END IF;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_audit_produits
AFTER INSERT OR UPDATE OR DELETE ON produits
FOR EACH ROW
EXECUTE FUNCTION audit_trigger();
```

---

# Trigger de validation ✔️

**Valider avant insertion**

```sql
CREATE OR REPLACE FUNCTION validate_email()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.email !~ '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$' THEN
        RAISE EXCEPTION 'Email invalide: %', NEW.email;
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_validate_email
BEFORE INSERT OR UPDATE ON utilisateurs
FOR EACH ROW
EXECUTE FUNCTION validate_email();
```

---

# Trigger calculé automatique 🔢

**Calculer des totaux**

```sql
CREATE OR REPLACE FUNCTION calculer_total_commande()
RETURNS TRIGGER AS $$
DECLARE
    v_total NUMERIC;
BEGIN
    SELECT SUM(quantite * prix_unitaire)
    INTO v_total
    FROM lignes_commande
    WHERE commande_id = NEW.commande_id;
    
    UPDATE commandes
    SET montant_total = COALESCE(v_total, 0)
    WHERE id = NEW.commande_id;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_calc_total
AFTER INSERT OR UPDATE OR DELETE ON lignes_commande
FOR EACH ROW
EXECUTE FUNCTION calculer_total_commande();
```

---

# Trigger de contrainte 🔐

**Maintenir cohérence**

```sql
CREATE OR REPLACE FUNCTION check_stock_disponible()
RETURNS TRIGGER AS $$
DECLARE
    v_stock INTEGER;
BEGIN
    SELECT stock INTO v_stock
    FROM produits
    WHERE id = NEW.produit_id;
    
    IF v_stock < NEW.quantite THEN
        RAISE EXCEPTION 'Stock insuffisant (dispo: %)', v_stock;
    END IF;
    
    -- Diminuer le stock
    UPDATE produits
    SET stock = stock - NEW.quantite
    WHERE id = NEW.produit_id;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_check_stock
BEFORE INSERT ON lignes_commande
FOR EACH ROW
EXECUTE FUNCTION check_stock_disponible();
```

---

# Trigger conditionnel 🎯

**WHEN pour filtrer**

```sql
CREATE TRIGGER trg_prix_change
AFTER UPDATE ON produits
FOR EACH ROW
WHEN (OLD.prix IS DISTINCT FROM NEW.prix)
EXECUTE FUNCTION log_prix_change();

-- S'exécute uniquement si le prix change
```

---

# Event Triggers 🎭

**Triggers sur événements DDL**

```sql
CREATE OR REPLACE FUNCTION prevent_table_drop()
RETURNS event_trigger AS $$
BEGIN
    RAISE EXCEPTION 'Suppression de table interdite !';
END;
$$ LANGUAGE plpgsql;

CREATE EVENT TRIGGER no_drop_table
ON ddl_command_end
WHEN TAG IN ('DROP TABLE')
EXECUTE FUNCTION prevent_table_drop();
```

---

# Désactiver / Supprimer triggers 🗑️

```sql
-- Désactiver
ALTER TABLE produits DISABLE TRIGGER trg_audit_produits;

-- Activer
ALTER TABLE produits ENABLE TRIGGER trg_audit_produits;

-- Désactiver tous
ALTER TABLE produits DISABLE TRIGGER ALL;

-- Supprimer
DROP TRIGGER trg_audit_produits ON produits;
```

---

# Ordre d'exécution 📋

**Si plusieurs triggers sur même événement**

1. Triggers BEFORE par ordre alphabétique
2. Modification de la ligne
3. Triggers AFTER par ordre alphabétique

```sql
-- Contrôler l'ordre avec nommage
CREATE TRIGGER trg_01_validate ...
CREATE TRIGGER trg_02_calculate ...
CREATE TRIGGER trg_03_audit ...
```

---

# Trigger INSTEAD OF 🔄

**Pour les vues**

```sql
CREATE VIEW produits_actifs AS
SELECT * FROM produits WHERE actif = true;

CREATE OR REPLACE FUNCTION insert_produit_actif()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO produits (nom, prix, actif)
    VALUES (NEW.nom, NEW.prix, true);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_insert_actif
INSTEAD OF INSERT ON produits_actifs
FOR EACH ROW
EXECUTE FUNCTION insert_produit_actif();
```

---

# Cas pratiques 💼

**1. Historique des modifications**

```sql
CREATE TABLE produits_history (
    id SERIAL PRIMARY KEY,
    produit_id INTEGER,
    operation VARCHAR(10),
    ancien_prix NUMERIC,
    nouveau_prix NUMERIC,
    changed_by VARCHAR(100),
    changed_at TIMESTAMP DEFAULT NOW()
);

CREATE OR REPLACE FUNCTION save_history()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'UPDATE' AND OLD.prix != NEW.prix THEN
        INSERT INTO produits_history (produit_id, operation, ancien_prix, nouveau_prix, changed_by)
        VALUES (NEW.id, 'PRICE_CHANGE', OLD.prix, NEW.prix, CURRENT_USER);
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_history
AFTER UPDATE ON produits
FOR EACH ROW
EXECUTE FUNCTION save_history();
```

---

**2. Soft delete**

```sql
CREATE OR REPLACE FUNCTION soft_delete()
RETURNS TRIGGER AS $$
BEGIN
    UPDATE produits
    SET deleted_at = NOW(), actif = false
    WHERE id = OLD.id;
    
    RETURN NULL;  -- Empêche le DELETE réel
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_soft_delete
BEFORE DELETE ON produits
FOR EACH ROW
EXECUTE FUNCTION soft_delete();
```

---

**3. Synchronisation de cache**

```sql
CREATE OR REPLACE FUNCTION refresh_cache()
RETURNS TRIGGER AS $$
BEGIN
    DELETE FROM cache_produits WHERE produit_id = NEW.id;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_refresh_cache
AFTER UPDATE ON produits
FOR EACH ROW
EXECUTE FUNCTION refresh_cache();
```

---

# Bonnes pratiques 👍

**1. Nommer clairement**

```sql
-- ✅ BON : Nom descriptif
CREATE TRIGGER trg_audit_utilisateurs_insert
CREATE TRIGGER trg_validate_email_before_update

-- ❌ MAUVAIS : Nom vague
CREATE TRIGGER trigger1
```

---

**2. Attention aux performances**

```sql
-- ❌ MAUVAIS : Trigger lourd
CREATE OR REPLACE FUNCTION heavy_trigger()
RETURNS TRIGGER AS $$
BEGIN
    -- Calculs lourds sur toute la table
    PERFORM COUNT(*) FROM huge_table;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- ✅ MIEUX : Opération légère
```

---

**3. Éviter les boucles infinies**

```sql
-- ⚠️ ATTENTION : Boucle possible
CREATE TRIGGER trg_update_a
AFTER UPDATE ON table_a
FOR EACH ROW
EXECUTE FUNCTION update_table_b();  -- Met à jour table_b

CREATE TRIGGER trg_update_b
AFTER UPDATE ON table_b
FOR EACH ROW
EXECUTE FUNCTION update_table_a();  -- Met à jour table_a !

-- 💥 Boucle infinie !
```

---

**4. Tester les triggers**

```sql
-- Test unitaire
BEGIN;

INSERT INTO produits (nom, prix) VALUES ('Test', 10);

-- Vérifier audit_log
SELECT * FROM audit_log WHERE table_name = 'produits' ORDER BY id DESC LIMIT 1;

ROLLBACK;
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ Triggers : automatiser actions sur INSERT/UPDATE/DELETE
✅ BEFORE/AFTER : timing d'exécution
✅ FOR EACH ROW/STATEMENT : granularité
✅ OLD/NEW : accès valeurs
✅ Audit, validation, calculs automatiques
✅ INSTEAD OF pour vues updatable
✅ Event triggers pour DDL
✅ Attention performances et boucles

---

# Questions ? 🙋

Des questions sur les triggers ?

**À suivre** : Optimisation et performance

