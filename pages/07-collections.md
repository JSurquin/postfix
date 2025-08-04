---
layout: intro
routeAlias: 'collections'
---

# Collections 📦

### Listes, dictionnaires et ensembles

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Explorons les structures de données <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Listes (list) 📋

### Collections ordonnées et modifiables

**Caractéristiques :**
- Collections ordonnées
- Modifiables (mutable)
- Peuvent contenir des éléments de types différents
- Indexées par position

---

# Création de Listes

```python
# Création de listes
nombres = [1, 2, 3, 4, 5]
fruits = ["pomme", "banane", "orange"]
melange = [1, "hello", 3.14, True]
```

---

# Accès aux Éléments

```python
# Accès aux éléments
print(nombres[0])      # 1
print(nombres[-1])     # 5 (dernier élément)
print(nombres[1:3])    # [2, 3] (slicing)
```

---

# Modification des Listes

```python
# Modification des listes
nombres[0] = 10        # [10, 2, 3, 4, 5]
nombres.append(6)      # [10, 2, 3, 4, 5, 6]
nombres.insert(1, 15)  # [10, 15, 2, 3, 4, 5, 6]
nombres.remove(3)      # Supprime la première occurrence de 3
del nombres[0]         # Supprime l'élément à l'index 0
```

---

# Opérations sur les Listes

```python
# Opérations sur les listes
longueur = len(nombres)
somme = sum(nombres)
maximum = max(nombres)
minimum = min(nombres)
```

---
layout: default
---

# Méthodes des listes 🔧

### Fonctions intégrées pour manipuler les listes

**Catégories de méthodes :**
- Méthodes d'ajout
- Méthodes de suppression
- Méthodes de recherche
- Méthodes de tri

---

# Méthodes d'Ajout

```python
# Méthodes d'ajout
liste = [1, 2, 3]
liste.append(4)           # [1, 2, 3, 4]
liste.extend([5, 6])      # [1, 2, 3, 4, 5, 6]
liste.insert(1, 10)       # [1, 10, 2, 3, 4, 5, 6]
```

---

# Méthodes de Suppression

```python
# Méthodes de suppression
liste.remove(10)          # Supprime la première occurrence
element = liste.pop()     # Supprime et retourne le dernier élément
element = liste.pop(1)    # Supprime et retourne l'élément à l'index 1
del liste[0]             # Supprime l'élément à l'index 0
```

---

# Méthodes de Recherche

```python
# Méthodes de recherche
index = liste.index(3)    # Retourne l'index de la première occurrence
compte = liste.count(2)   # Compte les occurrences de 2
```

---

# Méthodes de Tri

```python
# Méthodes de tri
liste.sort()              # Tri en place
liste.sort(reverse=True)  # Tri décroissant
liste.reverse()           # Inverse l'ordre
liste_triee = sorted(liste)  # Retourne une nouvelle liste triée
```

---
layout: default
---

# Slicing des listes 🔪

### Extraction de portions de listes

**Syntaxe :** `liste[début:fin:pas]`
- `début` : index de départ (inclus)
- `fin` : index de fin (exclus)
- `pas` : pas d'incrémentation (optionnel)

---

# Slicing de Base

```python
nombres = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Syntaxe de base : liste[début:fin:pas]
print(nombres[2:5])      # [2, 3, 4]
print(nombres[:3])       # [0, 1, 2] (du début à l'index 3)
print(nombres[7:])       # [7, 8, 9] (de l'index 7 à la fin)
print(nombres[:])        # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] (copie complète)
```

---

# Slicing avec Pas

```python
# Slicing avec pas
print(nombres[::2])      # [0, 2, 4, 6, 8] (tous les 2 éléments)
print(nombres[1::2])     # [1, 3, 5, 7, 9] (impairs)
```

---

# Slicing Négatif

```python
# Slicing négatif
print(nombres[-3:])      # [7, 8, 9] (3 derniers éléments)
print(nombres[:-3])      # [0, 1, 2, 3, 4, 5, 6] (tous sauf les 3 derniers)
print(nombres[::-1])     # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0] (inverse)
```

---
layout: default
---

# List comprehensions 🎯

### Création de listes de manière concise

**Avantages :**
- Syntaxe concise et lisible
- Plus performant que les boucles classiques
- Expression fonctionnelle

---

# List Comprehension de Base

```python
# List comprehension de base
nombres = [1, 2, 3, 4, 5]
carres = [x**2 for x in nombres]
print(carres)  # [1, 4, 9, 16, 25]
```

---

# List Comprehension avec Condition

```python
# List comprehension avec condition
nombres = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
pairs = [x for x in nombres if x % 2 == 0]
print(pairs)  # [2, 4, 6, 8, 10]
```

---

# List Comprehension Complexe

```python
# List comprehension complexe
nombres = [1, 2, 3, 4, 5]
resultat = [x**2 if x % 2 == 0 else x**3 for x in nombres]
print(resultat)  # [1, 4, 27, 16, 125]
```

---
layout: default
---

# Tuples (tuple) 📌

### Collections ordonnées et immuables

**Caractéristiques :**
- Collections ordonnées
- Immuables (immutable)
- Plus performants que les listes
- Utilisés pour les données qui ne changent pas

---

# Création de Tuples

```python
# Création de tuples
coordonnees = (10, 20)
point = (3.14, 2.71)
personne = ("Alice", 25, "Paris")

# Tuple vide
vide = ()

# Tuple avec un seul élément (attention à la virgule)
singleton = (42,)
```

---

# Accès aux Tuples

```python
# Accès aux tuples
print(coordonnees[0])    # 10
print(coordonnees[-1])   # 20
print(coordonnees[0:2])  # (10, 20)

# Déstructuration
x, y = coordonnees
print(f"x = {x}, y = {y}")
```

---

# Méthodes des Tuples

```python
# Méthodes des tuples
nombres = (1, 2, 2, 3, 2, 4, 5)

# count() - compte les occurrences
print(nombres.count(2))  # 3

# index() - retourne l'index de la première occurrence
print(nombres.index(2))  # 1
```

---
layout: default
---

# Dictionnaires (dict) 📚

### Collections de paires clé-valeur

**Caractéristiques :**
- Collections non ordonnées (avant Python 3.7)
- Modifiables (mutable)
- Clés uniques et immuables
- Valeurs de n'importe quel type

---

# Création de Dictionnaires

```python
# Création de dictionnaires
personne = {"nom": "Alice", "age": 25, "ville": "Paris"}

# Avec dict()
personne2 = dict(nom="Bob", age=30, ville="Lyon")

# Dictionnaire vide
vide = {}

# Avec zip()
cles = ["nom", "age", "ville"]
valeurs = ["Charlie", 35, "Marseille"]
personne3 = dict(zip(cles, valeurs))
```

---

# Accès aux Dictionnaires

```python
# Accès aux dictionnaires
personne = {"nom": "Alice", "age": 25, "ville": "Paris"}

# Accès direct
print(personne["nom"])      # Alice

# Accès sécurisé avec get()
print(personne.get("age"))  # 25
print(personne.get("email", "Non spécifié"))  # Valeur par défaut
```

---

# Modification des Dictionnaires

```python
# Modification des dictionnaires
personne = {"nom": "Alice", "age": 25}

# Ajout/Modification
personne["ville"] = "Paris"
personne["age"] = 26

# Suppression
del personne["age"]
valeur = personne.pop("nom", "Valeur par défaut")
```

---
layout: default
---

# Méthodes des Dictionnaires 🔧

### Fonctions intégrées pour manipuler les dictionnaires

**Méthodes principales :**
- `keys()`, `values()`, `items()`
- `get()`, `setdefault()`
- `update()`, `pop()`, `clear()`

---

# Méthodes d'Accès

```python
personne = {"nom": "Alice", "age": 25, "ville": "Paris"}

# keys() - retourne les clés
cles = list(personne.keys())
print(cles)  # ['nom', 'age', 'ville']

# values() - retourne les valeurs
valeurs = list(personne.values())
print(valeurs)  # ['Alice', 25, 'Paris']

# items() - retourne les paires clé-valeur
items = list(personne.items())
print(items)  # [('nom', 'Alice'), ('age', 25), ('ville', 'Paris')]
```

---

# Méthodes de Manipulation

```python
personne = {"nom": "Alice", "age": 25}

# get() - accès sécurisé
age = personne.get("age", 0)
email = personne.get("email", "Non spécifié")

# setdefault() - définit une valeur si la clé n'existe pas
ville = personne.setdefault("ville", "Inconnue")

# update() - fusionne des dictionnaires
personne.update({"email": "alice@example.com", "age": 26})
```

---
layout: default
---

# Sets (set) 🎯

### Collections d'éléments uniques et non ordonnés

**Caractéristiques :**
- Collections non ordonnées
- Éléments uniques
- Modifiables (mutable)
- Optimisés pour les tests d'appartenance

---

# Création de Sets

```python
# Création de sets
fruits = {"pomme", "banane", "orange"}
nombres = {1, 2, 3, 4, 5}

# Avec set()
lettres = set("hello")
print(lettres)  # {'h', 'e', 'l', 'o'}

# Set vide
vide = set()
```

---

# Opérations sur les Sets

```python
# Opérations sur les sets
fruits = {"pomme", "banane", "orange"}

# Ajout d'éléments
fruits.add("kiwi")
fruits.update(["ananas", "mangue"])

# Suppression d'éléments
fruits.remove("banane")  # Lève une exception si l'élément n'existe pas
fruits.discard("pomme")  # Ne lève pas d'exception
element = fruits.pop()    # Supprime et retourne un élément arbitraire
```

---

# Opérations Ensemblistes

```python
# Opérations ensemblistes
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# Union
union = set1 | set2
union = set1.union(set2)

# Intersection
intersection = set1 & set2
intersection = set1.intersection(set2)

# Différence
difference = set1 - set2
difference = set1.difference(set2)

# Différence symétrique
sym_diff = set1 ^ set2
sym_diff = set1.symmetric_difference(set2)
```

---
layout: default
---

# Collections Avancées 📚

### Modules spécialisés de la bibliothèque standard

**Modules disponibles :**
- `collections.defaultdict`
- `collections.Counter`
- `collections.deque`
- `collections.namedtuple`

---

# defaultdict

```python
from collections import defaultdict

# defaultdict avec list
dd = defaultdict(list)
dd["a"].append(1)
dd["a"].append(2)
dd["b"].append(3)

print(dd)  # defaultdict(<class 'list'>, {'a': [1, 2], 'b': [3]})

# defaultdict avec int
compteur = defaultdict(int)
mots = ["hello", "world", "hello", "python"]
for mot in mots:
    compteur[mot] += 1

print(compteur)  # defaultdict(<class 'int'>, {'hello': 2, 'world': 1, 'python': 1})
```

---

# Counter

```python
from collections import Counter

# Comptage d'éléments
mots = ["hello", "world", "hello", "python", "hello"]
compteur = Counter(mots)
print(compteur)  # Counter({'hello': 3, 'world': 1, 'python': 1})

# Méthodes utiles
print(compteur.most_common(2))  # [('hello', 3), ('world', 1)]
print(compteur["hello"])        # 3
print(compteur["unknown"])      # 0
```

---

# deque

```python
from collections import deque

# Création d'une deque
d = deque([1, 2, 3, 4, 5])

# Opérations aux deux extrémités
d.append(6)        # Ajoute à droite
d.appendleft(0)    # Ajoute à gauche
d.pop()            # Supprime à droite
d.popleft()        # Supprime à gauche

# Rotation
d.rotate(1)        # Rotation vers la droite
d.rotate(-1)       # Rotation vers la gauche
```

---
layout: default
---

# Itérateurs et Générateurs 🔄

### Traitement efficace des séquences

**Concepts :**
- Itérateurs : objets qui peuvent être parcourus
- Générateurs : fonctions qui produisent des valeurs
- Évaluation lazy : calcul à la demande

---

# Itérateurs

```python
# Itérateurs
nombres = [1, 2, 3, 4, 5]
iterateur = iter(nombres)

print(next(iterateur))  # 1
print(next(iterateur))  # 2
print(next(iterateur))  # 3

# Boucle for (utilise automatiquement l'itérateur)
for nombre in nombres:
    print(nombre)
```

---

# Générateurs

```python
# Générateur simple
def nombres_pairs(n):
    for i in range(n):
        if i % 2 == 0:
            yield i

# Utilisation
for nombre in nombres_pairs(10):
    print(nombre)  # 0, 2, 4, 6, 8
```

---

# Expression Génératrice

```python
# Expression génératrice
carres = (x**2 for x in range(5))

# Utilisation
for carre in carres:
    print(carre)  # 0, 1, 4, 9, 16

# Avantage : économie de mémoire
# Pas de liste complète en mémoire
```

---
layout: default
---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez une liste de nombres de 1 à 10, puis utilisez une list comprehension pour créer une liste des carrés des nombres pairs.

**Exercice 2 :** Créez un dictionnaire représentant un étudiant avec ses informations (nom, âge, notes), puis ajoutez une nouvelle note.

**Exercice 3 :** Créez deux sets de fruits et trouvez les fruits communs et les fruits uniques à chaque set.

---

# Solutions des Exercices 💡

### Exercice 1 - List Comprehension

```python
# Création de la liste
nombres = list(range(1, 11))
print(nombres)  # [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# List comprehension pour les carrés des nombres pairs
carres_pairs = [x**2 for x in nombres if x % 2 == 0]
print(carres_pairs)  # [4, 16, 36, 64, 100]
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Dictionnaire Étudiant

```python
# Création du dictionnaire étudiant
etudiant = {
    "nom": "Alice",
    "age": 20,
    "notes": [15, 18, 16, 14]
}

print(f"Étudiant: {etudiant['nom']}")
print(f"Âge: {etudiant['age']}")
print(f"Notes: {etudiant['notes']}")

# Ajout d'une nouvelle note
etudiant["notes"].append(17)
print(f"Nouvelles notes: {etudiant['notes']}")

# Calcul de la moyenne
moyenne = sum(etudiant["notes"]) / len(etudiant["notes"])
print(f"Moyenne: {moyenne:.2f}")
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Sets de Fruits

```python
# Création des sets
fruits1 = {"pomme", "banane", "orange", "kiwi"}
fruits2 = {"banane", "orange", "ananas", "mangue"}

# Fruits communs (intersection)
communs = fruits1 & fruits2
print(f"Fruits communs: {communs}")

# Fruits uniques à chaque set
uniques_fruits1 = fruits1 - fruits2
uniques_fruits2 = fruits2 - fruits1
print(f"Uniques à fruits1: {uniques_fruits1}")
print(f"Uniques à fruits2: {uniques_fruits2}")

# Tous les fruits (union)
tous = fruits1 | fruits2
print(f"Tous les fruits: {tous}")
```

---

# Points Clés à Retenir 🎯

### Résumé des collections Python

**1. Listes :**
- Collections ordonnées et modifiables
- Méthodes : append, extend, insert, remove, pop
- Slicing et list comprehensions

**2. Tuples :**
- Collections ordonnées et immuables
- Plus performants que les listes
- Utilisés pour les données constantes

**3. Dictionnaires :**
- Collections de paires clé-valeur
- Clés uniques et immuables
- Méthodes : keys, values, items, get, update

**4. Sets :**
- Collections d'éléments uniques
- Optimisés pour les tests d'appartenance
- Opérations ensemblistes

**5. Collections Avancées :**
- defaultdict, Counter, deque
- Itérateurs et générateurs
- Évaluation lazy

**Prêt pour la gestion d'erreurs !** 🚀 