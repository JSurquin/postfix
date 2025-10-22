---
layout: new-section
routeAlias: 'projet-final'
---

<a name="projet-final" id="projet-final"></a>

# Projet Final 🎉

**LE GRAND PROJET ! 🚀**

**Félicitations d'être arrivé jusqu'ici !** 🎊

Vous avez appris :
- ✅ Les bases de PostgreSQL
- ✅ Les requêtes complexes
- ✅ L'optimisation
- ✅ La sécurité
- ✅ Les fonctionnalités avancées

**Maintenant : TOUT ASSEMBLER !** 💪

Ce projet final va utiliser **TOUTES** les compétences apprises :
- Tables et relations
- Triggers et fonctions
- Vues et index
- Sécurité et permissions
- Optimisation

💡 **C'est un projet RÉEL** que vous pourriez avoir en entreprise !

⏱️ **Durée estimée** : 4-6 heures (prenez votre temps !)

🎯 **Objectif** : Avoir un projet portfolio à montrer !

---

# Cahier des charges 📋

**Mission : Créer une plateforme e-commerce complète ! 🛒**

**Imaginez** : Vous êtes embauché comme développeur backend pour créer le système de base de données d'un nouveau site e-commerce.

**Fonctionnalités à implémenter** :

1️⃣ **Gestion produits** 📦
   - Produits avec catégories
   - Stock en temps réel
   - Images et descriptions

2️⃣ **Utilisateurs** 👥
   - Inscription / Authentification
   - Profils clients
   - Adresses de livraison

3️⃣ **Shopping** 🛍️
   - Panier d'achat
   - Commandes et historique
   - États de commande (en cours, livrée, etc.)

4️⃣ **Paiement** 💳
   - Facturation
   - Moyens de paiement

5️⃣ **Social** ⭐
   - Avis clients
   - Notes sur les produits

6️⃣ **Analytics** 📊
   - Tableau de bord vendeur
   - Statistiques de ventes
   - Produits populaires

💡 **C'est ambitieux MAIS vous avez toutes les compétences nécessaires !**

---

# Architecture 🏗️

**Schémas** :
- `public` : Tables principales
- `auth` : Authentification
- `analytics` : Statistiques
- `audit` : Logs

**Tables principales** : ~15 tables

---

# Exigences techniques ⚙️

1. **Modélisation** : Relations normalisées
2. **Contraintes** : PRIMARY KEY, FOREIGN KEY, CHECK, UNIQUE
3. **Index** : Stratégiques pour performance
4. **Vues** : 3+ vues métier
5. **Vues matérialisées** : 2+ pour analytics
6. **Fonctions** : 5+ fonctions métier
7. **Triggers** : Audit, calculs automatiques
8. **Sécurité** : RLS, utilisateurs, permissions
9. **Performance** : EXPLAIN optimisé
10. **Sauvegarde** : Script de backup

---

# Tables à créer 📊

```sql
-- Utilisateurs et auth
CREATE SCHEMA auth;
CREATE TABLE auth.users (...);
CREATE TABLE auth.sessions (...);

-- Catalogue
CREATE TABLE categories (...);
CREATE TABLE produits (...);
CREATE TABLE images_produits (...);

-- Commerce
CREATE TABLE paniers (...);
CREATE TABLE commandes (...);
CREATE TABLE lignes_commande (...);
CREATE TABLE paiements (...);

-- Social
CREATE TABLE avis (...);
CREATE TABLE favoris (...);

-- Analytics
CREATE SCHEMA analytics;
CREATE MATERIALIZED VIEW analytics.ventes_quotidiennes (...);
```

---

# Fonctions métier 🔧

```sql
-- 1. Calculer total panier
CREATE FUNCTION calculer_total_panier(p_user_id INTEGER)
RETURNS NUMERIC;

-- 2. Passer commande
CREATE FUNCTION passer_commande(p_user_id INTEGER)
RETURNS INTEGER;

-- 3. Vérifier stock
CREATE FUNCTION verifier_stock_disponible(...)
RETURNS BOOLEAN;

-- 4. Calculer note moyenne produit
CREATE FUNCTION note_moyenne_produit(p_produit_id INTEGER)
RETURNS NUMERIC;

-- 5. Recherche produits
CREATE FUNCTION rechercher_produits(p_query TEXT)
RETURNS TABLE(...);
```

---

# Triggers importants ⚡

```sql
-- 1. Audit des modifications
CREATE TRIGGER trg_audit_produits
AFTER INSERT OR UPDATE OR DELETE ON produits
FOR EACH ROW EXECUTE FUNCTION audit_log();

-- 2. Mettre à jour stock
CREATE TRIGGER trg_update_stock
AFTER INSERT ON lignes_commande
FOR EACH ROW EXECUTE FUNCTION diminuer_stock();

-- 3. Calculer total commande
CREATE TRIGGER trg_calc_total
AFTER INSERT OR UPDATE ON lignes_commande
FOR EACH ROW EXECUTE FUNCTION calculer_total();

-- 4. Timestamp automatique
CREATE TRIGGER trg_updated_at
BEFORE UPDATE ON produits
FOR EACH ROW EXECUTE FUNCTION update_timestamp();
```

---

# Vues métier 👁️

```sql
-- 1. Produits avec stock et notes
CREATE VIEW produits_complets AS
SELECT 
    p.*,
    c.nom AS categorie,
    AVG(a.note) AS note_moyenne,
    COUNT(a.id) AS nb_avis
FROM produits p
LEFT JOIN categories c ON p.categorie_id = c.id
LEFT JOIN avis a ON p.id = a.produit_id
GROUP BY p.id, c.nom;

-- 2. Commandes détaillées
CREATE VIEW commandes_detaillees AS ...;

-- 3. Top produits
CREATE VIEW top_produits AS ...;
```

---

# Analytics (vues matérialisées) 📈

```sql
-- Dashboard ventes
CREATE MATERIALIZED VIEW analytics.dashboard_ventes AS
SELECT 
    DATE_TRUNC('day', date_commande) AS jour,
    COUNT(*) AS nb_commandes,
    SUM(montant_total) AS ca,
    AVG(montant_total) AS panier_moyen,
    COUNT(DISTINCT user_id) AS clients_uniques
FROM commandes
WHERE date_commande >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY DATE_TRUNC('day', date_commande);

CREATE INDEX idx_dashboard_jour 
ON analytics.dashboard_ventes(jour);

-- Refresh horaire via cron
```

---

# Sécurité 🔒

```sql
-- Utilisateurs
CREATE USER app_user WITH PASSWORD 'secure123';
CREATE USER readonly WITH PASSWORD 'read123';

-- Permissions
GRANT CONNECT ON DATABASE ecommerce TO app_user;
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;

-- RLS : voir ses propres commandes
ALTER TABLE commandes ENABLE ROW LEVEL SECURITY;

CREATE POLICY user_commandes ON commandes
FOR SELECT USING (user_id = current_setting('app.user_id')::INTEGER);
```

---

# Performance 🚀

```sql
-- Index stratégiques
CREATE INDEX idx_produits_categorie ON produits(categorie_id);
CREATE INDEX idx_produits_nom ON produits USING GIN(to_tsvector('french', nom));
CREATE INDEX idx_commandes_user ON commandes(user_id);
CREATE INDEX idx_commandes_date ON commandes(date_commande DESC);

-- Index partiel
CREATE INDEX idx_produits_actifs 
ON produits(nom, prix) 
WHERE actif = true;

-- Index covering
CREATE INDEX idx_produits_liste 
ON produits(categorie_id, nom, prix, stock);
```

---

# Tests et validation ✅

**À vérifier** :
1. ✅ Toutes les contraintes fonctionnent
2. ✅ Triggers s'exécutent correctement
3. ✅ Index utilisés (EXPLAIN ANALYZE)
4. ✅ RLS empêche accès non autorisé
5. ✅ Fonctions retournent résultats attendus
6. ✅ Vues matérialisées rafraîchissent
7. ✅ Audit enregistre toutes modifications
8. ✅ Backup/Restore fonctionne

---

# Script de backup 💾

```bash
#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/ecommerce"

# Backup complet
pg_dump -Fc ecommerce > "$BACKUP_DIR/ecommerce_$DATE.dump"

# Backup schéma uniquement
pg_dump -s ecommerce > "$BACKUP_DIR/schema_$DATE.sql"

# Rotation : garder 7 jours
find $BACKUP_DIR -name "*.dump" -mtime +7 -delete

echo "Backup terminé : ecommerce_$DATE.dump"
```

---

# Documentation 📚

**README.md à créer** :

1. Architecture de la base
2. Schéma des tables (ERD)
3. Procédures d'installation
4. Exemples de requêtes
5. API des fonctions
6. Guide de maintenance
7. Procédure de backup/restore
8. Troubleshooting

---

# Évaluation 📊

**Critères** (sur 100 points) :

- Modélisation (20 pts)
- Contraintes et intégrité (15 pts)
- Fonctions et triggers (20 pts)
- Vues et optimisation (15 pts)
- Sécurité (10 pts)
- Performance (10 pts)
- Documentation (10 pts)

---

# Livrables 📦

1. **Scripts SQL** : Création complète de la base
2. **Données de test** : Script d'insertion
3. **Documentation** : README complet
4. **Tests** : Scripts de validation
5. **Backup** : Procédure automatisée
6. **Démo** : Requêtes d'exemple

---

# Bonus 🌟

**Points supplémentaires pour** :

- Partitionnement de tables
- Réplication mise en place
- Monitoring avec pg_stat_statements
- Interface web (optionnel)
- Tests de charge
- CI/CD pour migrations
- Docker Compose complet

---

# Félicitations ! 🎓

**Vous avez complété la formation PostgreSQL !**

Vous maîtrisez maintenant :
- ✅ SQL avancé
- ✅ Modélisation de données
- ✅ Performance et optimisation
- ✅ Sécurité
- ✅ Administration
- ✅ Haute disponibilité

**Continuez à pratiquer et explorez les extensions !**

---

# Ressources 📚

**Pour aller plus loin** :

- Documentation officielle : postgresql.org/docs
- Planet PostgreSQL : planet.postgresql.org
- Postgres Weekly : newsletter hebdomadaire
- PostgreSQL Wiki : wiki.postgresql.org
- GitHub : github.com/postgres/postgres

**Communauté** :
- Reddit : r/PostgreSQL
- Stack Overflow : tag [postgresql]
- Slack : PostgreSQL Slack

---

# Merci ! 🙏

**Questions ? Contact**

Formation créée par Andromed  
https://www.andromed.fr

**Bon courage pour vos projets PostgreSQL ! 🐘**

