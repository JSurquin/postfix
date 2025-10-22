---
layout: new-section
routeAlias: 'json-types-avances'
---

<a name="json-types-avances" id="json-types-avances"></a>

# JSON et Types avancés 📦

Données semi-structurées dans PostgreSQL

---

# JSON vs JSONB 🤔

**PostgreSQL peut stocker du JSON ! 🎉**

*Analogie* : C'est comme stocker un objet JavaScript directement dans votre base !

**Pourquoi c'est génial ?** 💡
- Données flexibles (pas besoin de créer 50 colonnes)
- Parfait pour des données qui changent souvent de structure
- *Exemple* : Métadonnées de produits, configurations, logs

---

**JSON vs JSONB : Lequel choisir ?**

**JSON** = Texte simple 📝
- Stocké tel quel (comme du TEXT)
- Lent pour les recherches
- ❌ Rarement utilisé

**JSONB** = Binaire ⚡ ⭐ **UTILISEZ CELUI-LÀ !**
- Format binaire optimisé
- ULTRA-RAPIDE pour les recherches
- Peut être indexé
- ✅ **C'est le bon choix dans 99% des cas**

---

**Exemple concret** 📦 :
```sql
CREATE TABLE produits (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(200),
    metadata JSONB  -- ← Toutes les infos variables ici !
);
```

💡 **metadata** peut contenir :
- Pour un laptop : `{"marque": "Dell", "ram": "16GB", "ecran": "15 pouces"}`
- Pour une souris : `{"marque": "Logitech", "sans_fil": true, "dpi": 1600}`

🎯 **Avantage** : Pas besoin de colonnes différentes pour chaque type de produit !

---

# Insérer JSON 📝

```sql
INSERT INTO produits (nom, metadata) VALUES
    ('Laptop', '{"marque": "Dell", "ram": "16GB", "prix": 899.99}'),
    ('Souris', '{"marque": "Logitech", "sans_fil": true, "dpi": 1600}');
```

---

# Requêter JSON 🔍

```sql
-- Opérateur ->
SELECT metadata->'marque' FROM produits;

-- Opérateur ->> (retourne texte)
SELECT metadata->>'marque' FROM produits;

-- Imbriqué
SELECT metadata->'specs'->>'cpu' FROM produits;

-- Contient
SELECT * FROM produits
WHERE metadata @> '{"marque": "Dell"}';

-- Clé existe
SELECT * FROM produits
WHERE metadata ? 'ram';
```

---

# Index sur JSONB 📑

```sql
-- Index GIN
CREATE INDEX idx_metadata ON produits USING GIN (metadata);

-- Index sur chemin spécifique
CREATE INDEX idx_marque ON produits ((metadata->>'marque'));
```

---

# Fonctions JSON 🔧

```sql
-- jsonb_set : Modifier
UPDATE produits
SET metadata = jsonb_set(metadata, '{prix}', '999.99')
WHERE id = 1;

-- jsonb_insert : Insérer
UPDATE produits
SET metadata = jsonb_insert(metadata, '{couleur}', '"noir"')
WHERE id = 1;

-- Supprimer clé
UPDATE produits
SET metadata = metadata - 'ancien_champ'
WHERE id = 1;
```

---

# Agrégation JSON 📊

```sql
-- json_agg : Créer tableau JSON
SELECT 
    categorie,
    json_agg(json_build_object('nom', nom, 'prix', prix)) AS produits
FROM produits
GROUP BY categorie;

-- jsonb_object_agg : Créer objet
SELECT jsonb_object_agg(nom, prix) FROM produits;
```

---

# Questions ? 🙋

**À suivre** : Extensions PostgreSQL

