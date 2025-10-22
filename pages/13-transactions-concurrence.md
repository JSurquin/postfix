---
layout: new-section
routeAlias: 'transactions-concurrence'
---

<a name="transactions-concurrence" id="transactions-concurrence"></a>

# Transactions et concurrence 💼

Garantir la cohérence des données dans un environnement multi-utilisateurs

---

# Plan du module 📋

- Propriétés ACID
- Commandes de transaction
- Niveaux d'isolation
- Verrous (locks)
- Deadlocks
- MVCC
- Savepoints
- Bonnes pratiques

---

# Propriétés ACID ⚛️

**ACID = La garantie bancaire de PostgreSQL ! 🏦**

*Analogie* : Imaginez un transfert d'argent entre 2 comptes bancaires

**A - Atomicité** = Tout ou rien 💯
- *Exemple* : Soit les 100€ partent ET arrivent, soit RIEN ne se passe
- Pas de "l'argent a disparu" !

**C - Cohérence** = Toujours logique ✅
- *Exemple* : Vous ne pouvez pas avoir -500€ sur votre compte
- Les règles sont TOUJOURS respectées

**I - Isolation** = Pas de mélange 🔒
- *Exemple* : Si 2 personnes retirent en même temps, pas de problème !
- Chaque opération est "isolée" des autres

**D - Durabilité** = Pour toujours 💾
- *Exemple* : Une fois validé, c'est GRAVÉ dans le marbre
- Même si le serveur plante, vos données restent !

---

**Le problème SANS transaction** ❌ :

```sql
-- Transfert de 100€ du compte 1 vers le compte 2
UPDATE comptes SET solde = solde - 100 WHERE id = 1;  -- ✅ OK
-- 💥 CRASH ICI ! Le serveur plante !
UPDATE comptes SET solde = solde + 100 WHERE id = 2;  -- ❌ Jamais exécuté

-- RÉSULTAT : 100€ ont disparu dans la nature ! 😱
```

---

**La solution : TRANSACTION** ✅ :

```sql
BEGIN;  -- Début du "tout ou rien"
    UPDATE comptes SET solde = solde - 100 WHERE id = 1;
    UPDATE comptes SET solde = solde + 100 WHERE id = 2;
COMMIT;  -- OK, on valide TOUT

-- Si crash entre BEGIN et COMMIT : RIEN ne se passe !
-- Les 2 comptes restent inchangés 🎉
UPDATE comptes SET solde = solde - 100 WHERE id = 1;
UPDATE comptes SET solde = solde + 100 WHERE id = 2;
COMMIT;

-- En cas d'erreur, tout est annulé
```

---

# Commandes de transaction 🔧

**BEGIN / START TRANSACTION**

```sql
-- Démarrer une transaction
BEGIN;
-- ou
START TRANSACTION;

-- Commandes SQL...

-- Valider
COMMIT;

-- Ou annuler
ROLLBACK;
```

---

**Mode autocommit (défaut)**

```sql
-- Par défaut, chaque commande est une transaction
INSERT INTO produits (nom, prix) VALUES ('Laptop', 999.99);  -- Auto-COMMIT

-- Pour grouper plusieurs commandes
BEGIN;
INSERT INTO produits (nom, prix) VALUES ('Souris', 29.99);
INSERT INTO produits (nom, prix) VALUES ('Clavier', 79.99);
COMMIT;  -- Les 2 INSERT validés ensemble
```

---

**COMMIT : Valider les changements**

```sql
BEGIN;

INSERT INTO commandes (client_id, montant) VALUES (1, 150.00);
UPDATE produits SET stock = stock - 1 WHERE id = 10;

-- Tout est OK, valider
COMMIT;
```

---

**ROLLBACK : Annuler les changements**

```sql
BEGIN;

DELETE FROM produits WHERE prix < 10;

-- Oups, mauvaise idée !
ROLLBACK;  -- Annule le DELETE
```

---

**Transaction avec condition**

```sql
BEGIN;

UPDATE comptes SET solde = solde - 100 WHERE id = 1;

-- Vérifier le solde
SELECT solde FROM comptes WHERE id = 1;

-- Si solde < 0
ROLLBACK;
-- Sinon
-- COMMIT;
```

---

# Niveaux d'isolation 🔒

**4 niveaux standard SQL**

1. **Read Uncommitted** (PostgreSQL le traite comme Read Committed)
2. **Read Committed** (défaut PostgreSQL)
3. **Repeatable Read**
4. **Serializable**

---

**Problèmes de concurrence**

- **Dirty Read** : Lire des données non validées
- **Non-repeatable Read** : Même requête, résultat différent
- **Phantom Read** : Nouvelles lignes apparaissent
- **Serialization Anomaly** : Résultat impossible en mode série

---

**Read Committed (défaut) 📖**

```sql
-- Session 1
BEGIN;
SELECT solde FROM comptes WHERE id = 1;  -- 1000

-- Session 2
BEGIN;
UPDATE comptes SET solde = 500 WHERE id = 1;
COMMIT;

-- Session 1 (suite)
SELECT solde FROM comptes WHERE id = 1;  -- 500 (changé !)
COMMIT;
```

**Lit les données validées uniquement**

---

**Repeatable Read 🔁**

```sql
-- Définir le niveau
BEGIN ISOLATION LEVEL REPEATABLE READ;

SELECT solde FROM comptes WHERE id = 1;  -- 1000

-- Même si Session 2 modifie et COMMIT

SELECT solde FROM comptes WHERE id = 1;  -- 1000 (toujours !)
COMMIT;
```

**Vue cohérente pendant toute la transaction**

---

**Serializable 🔐**

```sql
BEGIN ISOLATION LEVEL SERIALIZABLE;

-- Transactions comme si exécutées en série
-- Échec si conflit de sérialisation

SELECT SUM(solde) FROM comptes;
INSERT INTO historique VALUES (NOW(), SUM(solde));

COMMIT;  -- Peut échouer si conflit
```

**Isolation maximale, peut échouer avec erreur de sérialisation**

---

**Choisir le niveau d'isolation**

| Niveau | Dirty Read | Non-rep. Read | Phantom | Sérialisation |
|--------|------------|---------------|---------|---------------|
| Read Committed | ✅ | ❌ | ❌ | ❌ |
| Repeatable Read | ✅ | ✅ | ✅ | ❌ |
| Serializable | ✅ | ✅ | ✅ | ✅ |

---

**Définir pour une transaction**

```sql
-- Pour cette transaction uniquement
BEGIN ISOLATION LEVEL REPEATABLE READ;
-- ...
COMMIT;

-- Pour la session
SET SESSION CHARACTERISTICS AS TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- Pour toute la base (postgresql.conf)
default_transaction_isolation = 'repeatable read'
```

---

# MVCC : Multi-Version Concurrency Control 🔄

**PostgreSQL utilise MVCC**

- Lecteurs n'attendent pas les écrivains
- Écrivains n'attendent pas les lecteurs
- Chaque transaction voit un snapshot
- Très performant pour la concurrence

---

**Comment ça marche**

```sql
-- Transaction 1
BEGIN;
UPDATE produits SET prix = 120 WHERE id = 1;
-- Version nouvelle (non validée) : prix = 120
-- Version ancienne (validée) : prix = 100

-- Transaction 2
BEGIN;
SELECT prix FROM produits WHERE id = 1;
-- Lit l'ancienne version : prix = 100

-- Transaction 1
COMMIT;

-- Transaction 2
SELECT prix FROM produits WHERE id = 1;
-- Read Committed : prix = 120
-- Repeatable Read : prix = 100 (snapshot)
COMMIT;
```

---

# Verrous (Locks) 🔐

**Types de verrous**

- **Row-level locks** : Sur des lignes
- **Table-level locks** : Sur tables entières
- **Advisory locks** : Verrous applicatifs

---

**Verrous de lignes automatiques**

```sql
-- UPDATE crée un verrou exclusif sur la ligne
UPDATE produits SET stock = stock - 1 WHERE id = 10;

-- Autre session doit attendre si modifie la même ligne
```

---

**SELECT FOR UPDATE : Verrou explicite**

```sql
BEGIN;

-- Verrouiller les lignes pour mise à jour
SELECT * FROM produits 
WHERE id = 10 
FOR UPDATE;

-- Autres transactions doivent attendre

UPDATE produits SET stock = stock - 1 WHERE id = 10;

COMMIT;  -- Libère le verrou
```

---

**Variantes de FOR UPDATE**

```sql
-- FOR UPDATE : Verrou exclusif
SELECT * FROM produits WHERE id = 10 FOR UPDATE;

-- FOR NO KEY UPDATE : Permet UPDATE de clés étrangères
SELECT * FROM produits WHERE id = 10 FOR NO KEY UPDATE;

-- FOR SHARE : Verrou partagé (plusieurs lecteurs, pas d'écriture)
SELECT * FROM produits WHERE id = 10 FOR SHARE;

-- FOR KEY SHARE : Permet SELECT FOR UPDATE d'autres tables
SELECT * FROM produits WHERE id = 10 FOR KEY SHARE;
```

---

**NOWAIT et SKIP LOCKED**

```sql
-- NOWAIT : Échoue immédiatement si verrouillé
SELECT * FROM produits WHERE id = 10 FOR UPDATE NOWAIT;
-- ERROR si verrouillé

-- SKIP LOCKED : Ignore les lignes verrouillées
SELECT * FROM taches 
WHERE statut = 'en_attente' 
ORDER BY priorite 
LIMIT 10 
FOR UPDATE SKIP LOCKED;
-- Utile pour files de travail
```

---

**Verrous de tables**

```sql
-- LOCK TABLE explicite
BEGIN;
LOCK TABLE produits IN EXCLUSIVE MODE;
-- Personne d'autre ne peut accéder
-- ...
COMMIT;

-- Modes : ACCESS SHARE, ROW SHARE, ROW EXCLUSIVE, SHARE UPDATE EXCLUSIVE,
--         SHARE, SHARE ROW EXCLUSIVE, EXCLUSIVE, ACCESS EXCLUSIVE
```

---

# Deadlocks : Interblocages 💀

**Qu'est-ce qu'un deadlock ?**

Deux transactions s'attendent mutuellement

```sql
-- Transaction 1
BEGIN;
UPDATE comptes SET solde = solde - 100 WHERE id = 1;
-- Attend Transaction 2...

-- Transaction 2
BEGIN;
UPDATE comptes SET solde = solde + 100 WHERE id = 2;
UPDATE comptes SET solde = solde + 100 WHERE id = 1;  -- Bloqué !

-- Transaction 1 (suite)
UPDATE comptes SET solde = solde - 100 WHERE id = 2;  -- Bloqué !

-- 💥 DEADLOCK détecté par PostgreSQL
```

---

**PostgreSQL détecte et résout**

```
ERROR: deadlock detected
DETAIL: Process 1234 waits for ShareLock on transaction 5678;
        blocked by process 5678.
        Process 5678 waits for ShareLock on transaction 1234;
        blocked by process 1234.
HINT: See server log for query details.
```

**Une transaction est annulée (ROLLBACK automatique)**

---

**Éviter les deadlocks**

1. **Ordre cohérent** : Toujours verrouiller dans le même ordre
2. **Transactions courtes** : Minimiser le temps de verrouillage
3. **Index appropriés** : Réduire les verrous de plage
4. **NOWAIT/SKIP LOCKED** : Gérer les conflits explicitement

---

**Exemple : Ordre cohérent**

```sql
-- ❌ MAUVAIS : Ordre variable
BEGIN;
UPDATE comptes SET solde = solde - 100 WHERE id = $random_id1;
UPDATE comptes SET solde = solde + 100 WHERE id = $random_id2;
COMMIT;

-- ✅ BON : Toujours ordre croissant des ID
BEGIN;
UPDATE comptes SET solde = solde - 100 WHERE id = LEAST($id1, $id2);
UPDATE comptes SET solde = solde + 100 WHERE id = GREATEST($id1, $id2);
COMMIT;
```

---

# Savepoints : Points de sauvegarde 💾

**Rollback partiel dans une transaction**

```sql
BEGIN;

INSERT INTO commandes (client_id, montant) VALUES (1, 100);

SAVEPOINT avant_lignes;

INSERT INTO lignes_commande (commande_id, produit_id) VALUES (1, 10);
INSERT INTO lignes_commande (commande_id, produit_id) VALUES (1, 20);
-- Erreur ici

ROLLBACK TO SAVEPOINT avant_lignes;  -- Annule seulement les lignes
-- La commande est conservée

INSERT INTO lignes_commande (commande_id, produit_id) VALUES (1, 15);  -- Réessayer

COMMIT;  -- Valide tout
```

---

**Plusieurs savepoints**

```sql
BEGIN;

INSERT INTO log (message) VALUES ('Début');

SAVEPOINT sp1;
UPDATE produits SET prix = prix * 1.1;

SAVEPOINT sp2;
DELETE FROM produits WHERE stock = 0;

-- Oups, pas DELETE
ROLLBACK TO sp2;

-- Garder l'UPDATE
COMMIT;
```

---

# Transactions préparées (2PC) 🔄

**Two-Phase Commit pour transactions distribuées**

```sql
-- Phase 1 : Préparer
BEGIN;
UPDATE comptes SET solde = solde - 100 WHERE id = 1;
PREPARE TRANSACTION 'tx_transfer_001';

-- Peut vérifier sur autre système

-- Phase 2 : Valider ou annuler
COMMIT PREPARED 'tx_transfer_001';
-- ou
ROLLBACK PREPARED 'tx_transfer_001';
```

---

# Surveiller les transactions 📊

**Voir les transactions actives**

```sql
SELECT 
    pid,
    usename,
    application_name,
    state,
    query,
    xact_start,
    state_change
FROM pg_stat_activity
WHERE state != 'idle'
ORDER BY xact_start;
```

---

**Transactions longues**

```sql
SELECT 
    pid,
    usename,
    NOW() - xact_start AS duration,
    query
FROM pg_stat_activity
WHERE state IN ('active', 'idle in transaction')
  AND xact_start < NOW() - INTERVAL '5 minutes'
ORDER BY xact_start;
```

---

**Voir les verrous**

```sql
SELECT 
    l.pid,
    l.mode,
    l.granted,
    a.usename,
    a.query
FROM pg_locks l
JOIN pg_stat_activity a ON l.pid = a.pid
WHERE NOT l.granted
ORDER BY l.pid;
```

---

**Terminer une transaction bloquée**

```sql
-- Terminer gentiment
SELECT pg_cancel_backend(pid);

-- Forcer la terminaison
SELECT pg_terminate_backend(pid);
```

---

# Paramètres de configuration ⚙️

**Timeouts**

```sql
-- Temps max pour une commande (milliseconds)
SET statement_timeout = 30000;  -- 30 secondes

-- Temps max pour un verrou
SET lock_timeout = 5000;  -- 5 secondes

-- Temps max pour transaction idle
SET idle_in_transaction_session_timeout = 600000;  -- 10 minutes
```

---

# Bonnes pratiques 👍

**1. Transactions courtes**

```sql
-- ✅ BON : Transaction rapide
BEGIN;
UPDATE stock SET quantite = quantite - 1 WHERE produit_id = 10;
INSERT INTO historique (produit_id, operation) VALUES (10, 'vente');
COMMIT;

-- ❌ MAUVAIS : Transaction longue
BEGIN;
-- Traitement complexe de 10 secondes
-- Verrous maintenus trop longtemps
COMMIT;
```

---

**2. Isoler uniquement si nécessaire**

```sql
-- Défaut (Read Committed) suffit souvent
BEGIN;
-- ...
COMMIT;

-- Repeatable Read si besoin de cohérence
BEGIN ISOLATION LEVEL REPEATABLE READ;
-- ...
COMMIT;

-- Serializable uniquement si critique
BEGIN ISOLATION LEVEL SERIALIZABLE;
-- ...
COMMIT;
```

---

**3. Gérer les erreurs**

```sql
-- PL/pgSQL
BEGIN
    BEGIN
        UPDATE comptes SET solde = solde - 100 WHERE id = 1;
        UPDATE comptes SET solde = solde + 100 WHERE id = 2;
    EXCEPTION
        WHEN others THEN
            RAISE NOTICE 'Erreur: %', SQLERRM;
            -- Transaction annulée automatiquement
    END;
END;
```

---

**4. Utiliser savepoints pour logique complexe**

```sql
BEGIN;

-- Opération principale
INSERT INTO commandes (...) VALUES (...);

-- Tentative optionnelle
SAVEPOINT before_bonus;
BEGIN
    INSERT INTO bonus (...) VALUES (...);
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK TO before_bonus;
END;

COMMIT;  -- Valide commande même si bonus échoue
```

---

**5. SKIP LOCKED pour files de travail**

```sql
-- Système de queue sans conflit
WITH tache AS (
    SELECT id
    FROM taches
    WHERE statut = 'en_attente'
    ORDER BY priorite DESC, date_creation
    LIMIT 1
    FOR UPDATE SKIP LOCKED
)
UPDATE taches t
SET statut = 'en_cours', worker_id = $worker_id
FROM tache
WHERE t.id = tache.id
RETURNING t.*;
```

---

# Exercice pratique 🎯

**Système de réservation de sièges**

1. Table `sieges` avec disponibilité
2. Empêcher double réservation
3. Gérer timeout
4. Éviter deadlocks

---

**Solution - Partie 1**

```sql
CREATE TABLE sieges (
    id SERIAL PRIMARY KEY,
    numero VARCHAR(10) UNIQUE NOT NULL,
    disponible BOOLEAN DEFAULT true,
    reservation_id INTEGER
);

CREATE TABLE reservations (
    id SERIAL PRIMARY KEY,
    client_email VARCHAR(255) NOT NULL,
    nb_sieges INTEGER NOT NULL,
    date_reservation TIMESTAMP DEFAULT NOW(),
    statut VARCHAR(50) DEFAULT 'en_cours'
);
```

---

```sql
CREATE TABLE sieges_reserves (
    reservation_id INTEGER REFERENCES reservations(id),
    siege_id INTEGER REFERENCES sieges(id),
    PRIMARY KEY (reservation_id, siege_id)
);
```

---

**Solution - Partie 2 : Réservation**

```sql
-- Fonction de réservation sécurisée
CREATE OR REPLACE FUNCTION reserver_sieges(
    p_email VARCHAR,
    p_nb_sieges INTEGER
) RETURNS INTEGER AS $$
DECLARE
    v_reservation_id INTEGER;
    v_siege RECORD;
BEGIN
    -- Démarrer transaction
    BEGIN
        -- Créer réservation
        INSERT INTO reservations (client_email, nb_sieges)
        VALUES (p_email, p_nb_sieges)
        RETURNING id INTO v_reservation_id;
        
        -- Sélectionner et verrouiller sièges
        FOR v_siege IN
            SELECT id
            FROM sieges
            WHERE disponible = true
            ORDER BY id
            LIMIT p_nb_sieges
            FOR UPDATE SKIP LOCKED
        LOOP
            -- Marquer comme réservé
            UPDATE sieges
            SET disponible = false, reservation_id = v_reservation_id
            WHERE id = v_siege.id;
            
            -- Enregistrer
            INSERT INTO sieges_reserves (reservation_id, siege_id)
            VALUES (v_reservation_id, v_siege.id);
        END LOOP;
        
        -- Vérifier qu'on a assez de sièges
        IF (SELECT COUNT(*) FROM sieges_reserves 
            WHERE reservation_id = v_reservation_id) < p_nb_sieges 
        THEN
            RAISE EXCEPTION 'Pas assez de sièges disponibles';
        END IF;
        
        -- Valider réservation
        UPDATE reservations
        SET statut = 'confirmee'
        WHERE id = v_reservation_id;
        
        RETURN v_reservation_id;
    EXCEPTION
        WHEN OTHERS THEN
            RAISE NOTICE 'Erreur: %', SQLERRM;
            RETURN NULL;
    END;
END;
$$ LANGUAGE plpgsql;
```

---

**Solution - Partie 3 : Utilisation**

```sql
-- Réserver 3 sièges
SELECT reserver_sieges('alice@example.com', 3);

-- Plusieurs clients simultanément (pas de deadlock grâce à SKIP LOCKED)
-- Client 1
SELECT reserver_sieges('bob@example.com', 2);

-- Client 2 (en parallèle)
SELECT reserver_sieges('charlie@example.com', 2);

-- Libérer après timeout (cron job)
UPDATE sieges s
SET disponible = true, reservation_id = NULL
FROM reservations r
WHERE s.reservation_id = r.id
  AND r.statut = 'en_cours'
  AND r.date_reservation < NOW() - INTERVAL '15 minutes';
```

---

# Résumé du module 📝

**Points clés à retenir**

✅ ACID : Atomicité, Cohérence, Isolation, Durabilité
✅ BEGIN, COMMIT, ROLLBACK : contrôle transactions
✅ Niveaux d'isolation : Read Committed, Repeatable Read, Serializable
✅ MVCC : pas de blocage lecteur/écrivain
✅ FOR UPDATE : verrous explicites
✅ SKIP LOCKED : files de travail sans conflit
✅ Savepoints : rollback partiel
✅ Éviter deadlocks : ordre cohérent, transactions courtes

---

# Questions ? 🙋

Des questions sur les transactions et la concurrence ?

**À suivre** : Fonctions et procédures stockées

