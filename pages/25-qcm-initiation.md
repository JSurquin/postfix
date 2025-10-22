---
layout: question
routeAlias: 'qcm-initiation'
---

<a name="qcm-initiation" id="qcm-initiation"></a>

# QCM Initiation PostgreSQL ✅

**Testez vos connaissances ! 🧠**

**Ce QCM c'est pour quoi ?**
- ✅ Vérifier ce que vous avez retenu
- ✅ Identifier les points à revoir
- ✅ Gagner en confiance !

💡 **Comment utiliser ce QCM ?**
1. Lisez bien chaque question
2. Répondez spontanément (pas de triche !)
3. Comptez vos bonnes réponses
4. Si < 70% → Relisez le module concerné

🎯 **Scoring** :
- 18-20 : Expert ! 🏆
- 15-17 : Très bien ! ⭐
- 12-14 : Bien, continuez ! 💪
- < 12 : Revoyez les bases ! 📚

**C'est parti !** 🚀

---

# Question 1 📝

**Quelle commande permet de lister toutes les bases de données ?**

A) `SHOW DATABASES;` ← MySQL, pas PostgreSQL !  
B) `\l` ← **CORRECT !** ✅  
C) `LIST DATABASES;` ← N'existe pas  
D) `SELECT * FROM databases;` ← Pas de table "databases"

**Réponse : B**

💡 **Explication** : `\l` est une commande psql (pas SQL) qui liste toutes les bases

---

# Question 2 📝

**Quel type utiliser pour un identifiant auto-incrémenté ?**

A) INTEGER AUTO  
B) SERIAL  
C) AUTOINCREMENT  
D) IDENTITY

**Réponse : B**

---

# Question 3 📝

**Comment créer un index sur la colonne `email` ?**

A) `CREATE INDEX ON users(email);`  
B) `CREATE INDEX idx_email ON users(email);`  
C) `ADD INDEX email ON users;`  
D) `INDEX users(email);`

**Réponse : B**

---

# Question 4 📝

**Quelle contrainte garantit qu'une valeur est unique ?**

A) PRIMARY KEY  
B) UNIQUE  
C) CHECK  
D) NOT NULL

**Réponse : B (ou A, mais PRIMARY KEY implique UNIQUE + NOT NULL)**

---

# Question 5 📝

**Comment démarrer une transaction ?**

A) `START;`  
B) `BEGIN;`  
C) `TRANSACTION;`  
D) `START TRANS;`

**Réponse : B**

---

# Question 6 📝

**Quelle fonction compte le nombre de lignes ?**

A) `SIZE()`  
B) `COUNT(*)`  
C) `LENGTH()`  
D) `ROWS()`

**Réponse : B**

---

# Question 7 📝

**Comment filtrer les lignes dans SELECT ?**

A) `FILTER`  
B) `HAVING`  
C) `WHERE`  
D) `IF`

**Réponse : C**

---

# Question 8 📝

**Quel JOIN retourne toutes les lignes de la table de gauche ?**

A) INNER JOIN  
B) LEFT JOIN  
C) RIGHT JOIN  
D) FULL JOIN

**Réponse : B**

---

# Question 9 📝

**Comment trier les résultats par ordre décroissant ?**

A) `ORDER BY col ASC`  
B) `ORDER BY col DESC`  
C) `SORT BY col DESC`  
D) `ORDER col DESC`

**Réponse : B**

---

# Question 10 📝

**Quelle commande sauvegarde une base de données ?**

A) `BACKUP DATABASE`  
B) `pg_dump`  
C) `EXPORT DATABASE`  
D) `SAVE DATABASE`

**Réponse : B**

---

# Score 📊

**8-10 correctes** : Excellent ! 🎉  
**6-7 correctes** : Bien, continuez ! 👍  
**4-5 correctes** : À revoir 📚  
**< 4 correctes** : Reprenez les bases 📖

---

# Questions ? 🙋

**À suivre** : QCM Perfectionnement

