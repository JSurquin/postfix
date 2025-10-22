---
layout: exercices
routeAlias: 'exercices-debutant'
---

<a name="exercices-debutant" id="exercices-debutant"></a>

# Exercices Pratiques Débutant 🎯

**C'est l'heure de pratiquer ! 💪**

*La théorie c'est bien, la pratique c'est MIEUX !* 🚀

**Ces exercices sont faits pour vous** :
- ✅ Pas de piège
- ✅ Niveau progressif
- ✅ Solutions détaillées
- ✅ Cas concrets du quotidien

💡 **Conseil** : Essayez VRAIMENT avant de regarder la solution !
- Ouvrez PostgreSQL
- Créez les tables
- Tapez les requêtes
- **C'est en pratiquant qu'on apprend !**

⚠️ **Pas de panique** : Si vous bloquez, la solution est juste après ! 🎯

---

# Exercice 1 : Bibliothèque 📚

**Scénario réel** : Vous êtes développeur et on vous demande de créer une base pour une bibliothèque

**Mission** :
1. Créer une base de données `bibliotheque`
2. Créer une table `livres` avec :
   - Un identifiant automatique
   - Titre (obligatoire)
   - Auteur (obligatoire)
   - Année de publication
   - ISBN (unique !)
3. Insérer 10 livres de votre choix
4. Faire des requêtes pour :
   - Trouver les livres publiés après 2000
   - Trouver tous les livres d'un auteur
   - Compter combien de livres il y a

💡 **Essayez d'abord par vous-même !** La solution est sur la slide suivante.

---

# Solution Exercice 1

```sql
CREATE DATABASE bibliotheque;
\c bibliotheque

CREATE TABLE livres (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(300) NOT NULL,
    auteur VARCHAR(200) NOT NULL,
    annee_publication INTEGER,
    isbn VARCHAR(20) UNIQUE
);

INSERT INTO livres (titre, auteur, annee_publication, isbn) VALUES
    ('1984', 'George Orwell', 1949, '978-0451524935'),
    ('Le Seigneur des Anneaux', 'J.R.R. Tolkien', 1954, '978-0544003415'),
    ('Harry Potter', 'J.K. Rowling', 1997, '978-2070584628');

SELECT * FROM livres WHERE annee_publication > 2000;
SELECT * FROM livres WHERE auteur = 'J.K. Rowling';
```

---

# Exercice 2 : E-commerce simple 🛒

**Créer** :
- Table `clients` : id, nom, email, ville
- Table `produits` : id, nom, prix, stock
- Table `commandes` : id, client_id, date_commande, total

**Requêtes** :
- Clients de Paris
- Produits < 50€
- Commandes du dernier mois

---

# Exercice 3 : Films 🎬

**Créer** :
- Table `films` avec note /10
- Insérer 15 films
- Top 5 des meilleurs films
- Films par décennie
- Moyenne des notes

---

# Exercice 4 : Employés 👔

**Créer** :
- Table `employes` : id, nom, departement, salaire, date_embauche
- Table `departements` : id, nom
- Requêtes : salaire moyen par département, ancienneté

---

# Exercice 5 : Blog 📝

**Créer** :
- Table `auteurs`
- Table `articles` avec clé étrangère
- Insérer données
- Articles par auteur
- Statistiques

---

# Questions ? 🙋

**À suivre** : Exercices intermédiaires

