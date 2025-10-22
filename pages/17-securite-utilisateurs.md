---
layout: new-section
routeAlias: 'securite-utilisateurs'
---

<a name="securite-utilisateurs" id="securite-utilisateurs"></a>

# Sécurité et Utilisateurs 🔒

Protéger vos données et gérer les accès

---

# Créer des utilisateurs 👤

**Utilisateurs PostgreSQL = Les comptes d'accès à votre base ! 🔑**

*Analogie* : C'est comme créer des comptes sur votre ordinateur !
- Papa : Administrateur (peut tout faire)
- Maman : Utilisateur normal
- Enfants : Utilisateurs limités (pas de suppression)

**Créer un utilisateur simple** 🆕 :
```sql
CREATE USER alice WITH PASSWORD 'motdepasse123';
```
💡 Alice peut maintenant se connecter !

---

**Créer un utilisateur avec des pouvoirs** 🦸:
```sql
CREATE USER bob WITH 
    PASSWORD 'secure123'
    LOGIN                    -- Peut se connecter
    CREATEDB                 -- Peut créer des bases
    VALID UNTIL '2026-01-01';-- Expire le 1er janvier 2026
```

---

**Créer un super-administrateur** 👑 :
```sql
CREATE USER admin WITH SUPERUSER PASSWORD 'admin123';
```
⚠️ **ATTENTION** : SUPERUSER = Tous les pouvoirs ! À utiliser avec précaution !

---

# Rôles vs Utilisateurs 👥

**Rôle = Un groupe d'utilisateurs ! 👨‍👩‍👧‍👦**

*Analogie* : C'est comme les groupes Facebook !
- Groupe "Lecteurs" → Peut seulement lire
- Groupe "Éditeurs" → Peut lire + modifier
- Groupe "Admins" → Peut tout faire

**Créer un rôle (groupe)** 📋 :
```sql
CREATE ROLE lecteurs;  -- Le groupe "lecteurs"
```

**Ajouter des membres au groupe** 👥 :
```sql
GRANT lecteurs TO alice, bob;
```
💡 Alice et Bob font maintenant partie du groupe "lecteurs"

---

**Hiérarchie de rôles** 🎯 :
```sql
CREATE ROLE managers;
GRANT lecteurs TO managers;    -- Les managers ont les droits des lecteurs
GRANT managers TO charlie;     -- Charlie est manager
```

💡 **Résultat** : Charlie a les droits de "managers" ET de "lecteurs" !

🎯 **En pratique** : Au lieu de donner les permissions à 50 utilisateurs un par un, vous créez un rôle et vous y ajoutez tout le monde !

---

# Permissions GRANT 🔐

```sql
-- Lecture seule
GRANT SELECT ON produits TO alice;

-- Toutes permissions
GRANT ALL PRIVILEGES ON produits TO bob;

-- Base de données
GRANT CONNECT ON DATABASE mabase TO alice;

-- Schéma
GRANT USAGE ON SCHEMA public TO alice;
GRANT CREATE ON SCHEMA public TO bob;
```

---

# REVOKE : Retirer permissions 🚫

```sql
-- Retirer permissions
REVOKE SELECT ON produits FROM alice;

-- Tout retirer
REVOKE ALL PRIVILEGES ON produits FROM bob;
```

---

# Row Level Security (RLS) 🛡️

```sql
-- Activer RLS
ALTER TABLE commandes ENABLE ROW LEVEL SECURITY;

-- Politique : voir ses propres commandes
CREATE POLICY user_commands ON commandes
    FOR SELECT
    USING (client_email = current_user);

-- Politique UPDATE
CREATE POLICY user_update ON commandes
    FOR UPDATE
    USING (client_email = current_user);
```

---

# Chiffrement 🔐

```sql
-- Extension pgcrypto
CREATE EXTENSION pgcrypto;

-- Hasher un mot de passe
INSERT INTO users (email, password_hash)
VALUES ('alice@example.com', crypt('motdepasse', gen_salt('bf')));

-- Vérifier
SELECT * FROM users
WHERE email = 'alice@example.com'
  AND password_hash = crypt('motdepasse', password_hash);
```

---

# SSL/TLS 🔒

**postgresql.conf**

```bash
ssl = on
ssl_cert_file = 'server.crt'
ssl_key_file = 'server.key'
ssl_ca_file = 'root.crt'
```

**pg_hba.conf**

```bash
hostssl all all 0.0.0.0/0 scram-sha-256
```

---

# Audit avec pgAudit 📝

```sql
CREATE EXTENSION pgaudit;

-- Auditer tout
SET pgaudit.log = 'all';

-- Auditer DDL et écriture
SET pgaudit.log = 'ddl, write';
```

---

# Bonnes pratiques 👍

1. **Principe du moindre privilège**
2. **Mots de passe forts** (scram-sha-256)
3. **SSL** en production
4. **RLS** pour multi-tenant
5. **Chiffrement** des données sensibles
6. **Audit** des accès
7. **Sauvegardes** chiffrées

---

# Questions ? 🙋

**À suivre** : Sauvegarde et restauration

