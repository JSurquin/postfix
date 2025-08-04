---
layout: default
routeAlias: 'qcm-initiation'
---

# QCM DE VALIDATION - INITIATION 🎯

### Testez vos connaissances acquises

---

# Question 1 - Variables et Types

**Quelle est la valeur de `x` après l'exécution de ce code ?**

```python
x = 5
y = "10"
x = x + int(y)
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! 5 + 10 = 15')">A) 15</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) 510</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) Error</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) 5</button>
</div>

---

# Question 2 - Structures de Contrôle

**Que fait ce code ?**

```python
nombres = [1, 2, 3, 4, 5]
resultat = [x * 2 for x in nombres if x % 2 == 0]
print(resultat)
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! [4, 8] - les nombres pairs multipliés par 2')">A) [4, 8]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) [2, 4, 6, 8, 10]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) [1, 3, 5]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) [2, 4]</button>
</div>

---

# Question 3 - Fonctions

**Quelle est la sortie de ce code ?**

```python
def calculer(a, b=2, c=3):
    return a + b + c

print(calculer(1, c=5))
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! 1 + 2 + 5 = 8')">A) 8</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) 6</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) 9</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) Error</button>
</div>

---

# Question 4 - Collections

**Que contient `resultat` après ce code ?**

```python
liste = [1, 2, 2, 3, 4, 4, 5]
resultat = list(set(liste))
print(resultat)
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! Les doublons sont supprimés')">A) [1, 2, 3, 4, 5]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) [1, 2, 2, 3, 4, 4, 5]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) [1, 2, 3, 4]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) Error</button>
</div>

---

# Question 5 - Gestion d'Erreurs

**Quelle exception est levée par ce code ?**

```python
try:
    x = int("abc")
except ValueError as e:
    print("Erreur de conversion")
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! ValueError est levée')">A) ValueError</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) TypeError</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) Aucune exception</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) RuntimeError</button>
</div>

---

# Question 6 - Programmation Orientée Objet

**Qu'est-ce que ce code affiche ?**

```python
class Animal:
    def __init__(self, nom):
        self.nom = nom
    
    def parler(self):
        return "Son inconnu"

class Chien(Animal):
    def parler(self):
        return "Woof!"

mon_chien = Chien("Rex")
print(mon_chien.parler())
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! La méthode de la classe enfant est utilisée')">A) Woof!</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) Son inconnu</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) Rex</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) Error</button>
</div>

---

# Question 7 - Manipulation de Fichiers

**Que fait ce code ?**

```python
with open("test.txt", "w") as f:
    f.write("Hello")
    f.write("World")
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! HelloWorld sans espace')">A) Écrit "HelloWorld" dans test.txt</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) Écrit "Hello World" dans test.txt</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) Écrit "Hello\nWorld" dans test.txt</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) Erreur de fichier</button>
</div>

---

# Question 8 - Modules et Imports

**Quel import est correct ?**

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct !')">A) from math import sqrt</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) import sqrt from math</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) include math.sqrt</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) using math.sqrt</button>
</div>

---

# Question 9 - Décorateurs

**Que fait ce décorateur ?**

```python
def timer(func):
    def wrapper(*args, **kwargs):
        import time
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"Temps d'exécution: {end - start}")
        return result
    return wrapper

@timer
def fonction_lente():
    import time
    time.sleep(1)
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct !')">A) Mesure le temps d'exécution</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) Cache le résultat</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) Valide les paramètres</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) Gère les erreurs</button>
</div>

---

# Question 10 - List Comprehensions

**Que fait cette list comprehension ?**

```python
nombres = [1, 2, 3, 4, 5, 6]
resultat = [x**2 for x in nombres if x % 2 == 0]
```

<div class="flex flex-col gap-2 mt-4">
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Correct ! [4, 16, 36] - carrés des nombres pairs')">A) [4, 16, 36]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">B) [1, 4, 9, 16, 25, 36]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">C) [2, 4, 6]</button>
<button class="p-3 text-left border rounded hover:bg-blue-50" onclick="alert('Incorrect')">D) [1, 9, 25]</button>
</div>

---

# RÉSULTATS DU QCM 📊

### Évaluation de vos connaissances

**Score obtenu :** [Calcul automatique]

**Niveau atteint :**
- **0-3 réponses correctes :** Débutant - Révision nécessaire
- **4-6 réponses correctes :** Intermédiaire - Bonnes bases
- **7-8 réponses correctes :** Avancé - Très bon niveau
- **9-10 réponses correctes :** Expert - Prêt pour le perfectionnement

---

# CORRECTIONS DÉTAILLÉES 📝

### Explications des bonnes réponses

**Question 1 :** `int(y)` convertit "10" en entier, donc 5 + 10 = 15

**Question 2 :** List comprehension avec condition - seuls les nombres pairs sont multipliés par 2

**Question 3 :** `b=2` par défaut, `c=5` explicitement, donc 1 + 2 + 5 = 8

**Question 4 :** `set()` supprime les doublons, `list()` reconvertit en liste

**Question 5 :** `int("abc")` lève `ValueError` car "abc" n'est pas un nombre

**Question 6 :** Polymorphisme - la méthode de la classe enfant remplace celle du parent

**Question 7 :** `write()` écrit sans espace ni retour à la ligne

**Question 8 :** Syntaxe correcte pour importer une fonction spécifique

**Question 9 :** Le décorateur mesure le temps avant et après l'exécution

**Question 10 :** Filtre les nombres pairs puis les élève au carré

---

# PROCHAINES ÉTAPES 🚀

### Continuez votre apprentissage

**Si vous avez moins de 7 bonnes réponses :**
- Révisiez les concepts de base
- Refaites les exercices pratiques
- Consultez la documentation Python

**Si vous avez 7 réponses ou plus :**
- Vous êtes prêt pour le module Perfectionnement
- Explorez les fonctionnalités avancées
- Pratiquez sur des projets plus complexes

**Ressources recommandées :**
- Documentation officielle Python
- Exercices sur LeetCode/HackerRank
- Projets open source
- Communautés Python (Discord, Reddit)

---

# FÉLICITATIONS ! 🎉

### Vous avez terminé le module Initiation Python

**Compétences acquises :**
✅ Variables et types de données
✅ Structures de contrôle
✅ Fonctions et modules
✅ Collections et structures de données
✅ Gestion des erreurs
✅ Programmation orientée objet
✅ Manipulation de fichiers
✅ Bonnes pratiques Python

**Prêt pour la suite ?**
Le module Perfectionnement vous attend avec des concepts avancés ! 