---
layout: default
routeAlias: 'optimisation-performance'
---

# OPTIMISATION ET PERFORMANCE 🚀

### Techniques d'optimisation avancées

---

# Profiling et Mesure

### Outils de mesure de performance

```python
import cProfile
import pstats
import time

def profile_function(func):
    """Profile une fonction"""
    profiler = cProfile.Profile()
    profiler.enable()
    result = func()
    profiler.disable()
    
    stats = pstats.Stats(profiler)
    stats.sort_stats('cumulative')
    stats.print_stats(10)
    return result

# Utilisation
def slow_function():
    return sum(i**2 for i in range(1000000))

profile_function(slow_function)
```

---

# Optimisation des Boucles

### Techniques d'optimisation des boucles

```python
# ❌ Moins efficace
result = []
for i in range(1000):
    result.append(i * 2)

# ✅ Plus efficace
result = [i * 2 for i in range(1000)]

# ✅ Avec générateur pour la mémoire
result = (i * 2 for i in range(1000000))

# ✅ Avec map pour les grandes séquences
result = list(map(lambda x: x * 2, range(1000)))
```

---

# Optimisation des Structures de Données

### Choix des bonnes structures

```python
# Pour les recherches fréquentes
# ❌ Liste
items = [1, 2, 3, 4, 5]
if 3 in items:  # O(n)
    pass

# ✅ Set
items_set = {1, 2, 3, 4, 5}
if 3 in items_set:  # O(1)
    pass

# Pour les comptages
from collections import Counter
text = "hello world"
char_count = Counter(text)  # Plus efficace que dict manuel
```

---

# Cache et Mémoisation

### Optimisation avec cache

```python
import functools

@functools.lru_cache(maxsize=128)
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Cache personnalisé
def memoize(func):
    cache = {}
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper

@memoize
def expensive_calculation(n):
    # Simulation d'un calcul coûteux
    return n * n
```

---

# Optimisation des Imports

### Imports optimisés

```python
# ❌ Import de tout le module
import math
result = math.sqrt(16)

# ✅ Import spécifique
from math import sqrt
result = sqrt(16)

# ✅ Import avec alias
import numpy as np
array = np.array([1, 2, 3])

# ✅ Import conditionnel
try:
    import fast_library
except ImportError:
    import slow_library as fast_library
```

---

# Optimisation des Chaînes

### Manipulation efficace des chaînes

```python
# ❌ Concaténation inefficace
result = ""
for i in range(1000):
    result += str(i)

# ✅ Avec join
result = "".join(str(i) for i in range(1000))

# ✅ Avec list comprehension
result = "".join([str(i) for i in range(1000)])

# ✅ f-strings (Python 3.6+)
name = "John"
age = 30
message = f"Hello {name}, you are {age} years old"
```

---

# Optimisation des Listes

### Techniques d'optimisation des listes

```python
# Pré-allocation de listes
# ❌ Croissance dynamique
result = []
for i in range(1000):
    result.append(i)

# ✅ Pré-allocation
result = [0] * 1000
for i in range(1000):
    result[i] = i

# List comprehensions optimisées
squares = [x**2 for x in range(1000) if x % 2 == 0]

# Avec générateur pour la mémoire
squares_gen = (x**2 for x in range(1000000))
```

---

# Optimisation des Dictionnaires

### Techniques d'optimisation des dicts

```python
# Utilisation de defaultdict
from collections import defaultdict

# ❌ Vérification manuelle
d = {}
if 'key' not in d:
    d['key'] = []
d['key'].append(1)

# ✅ Avec defaultdict
dd = defaultdict(list)
dd['key'].append(1)  # Pas besoin de vérifier

# Counter pour les comptages
from collections import Counter
text = "hello world"
char_count = Counter(text)
most_common = char_count.most_common(3)
```

---

# Optimisation des Fonctions

### Techniques d'optimisation des fonctions

```python
# Fonctions locales pour les boucles
def optimized_function():
    # Définir les fonctions localement
    def local_helper(x):
        return x * 2
    
    return [local_helper(i) for i in range(1000)]

# Utilisation de __slots__ pour les classes
class OptimizedClass:
    __slots__ = ['x', 'y']  # Économise la mémoire
    
    def __init__(self, x, y):
        self.x = x
        self.y = y

# Fonctions avec cache
@functools.lru_cache(maxsize=1000)
def cached_function(n):
    return expensive_calculation(n)
```

---

# Optimisation de la Mémoire

### Gestion efficace de la mémoire

```python
import sys
import gc

# Mesure de la taille des objets
def get_size(obj):
    return sys.getsizeof(obj)

# Garbage collection manuel
def cleanup_memory():
    gc.collect()

# Utilisation de __slots__
class MemoryEfficient:
    __slots__ = ['name', 'age']
    
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Comparaison
class RegularClass:
    def __init__(self, name, age):
        self.name = name
        self.age = age

print(f"Slots: {get_size(MemoryEfficient('John', 30))}")
print(f"Regular: {get_size(RegularClass('John', 30))}")
```

---

# Optimisation des Algorithme

### Choix d'algorithmes optimaux

```python
# Recherche binaire vs linéaire
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

# Tri optimisé
def optimized_sort(data):
    # Utiliser le tri natif Python (Timsort)
    return sorted(data)

# Recherche dans un set
def fast_lookup(data_set, target):
    return target in data_set  # O(1) vs O(n) pour une liste
```

---

# Exercices d'Optimisation 🎯

### Testez vos compétences

**Exercice 1 :** Optimisez une fonction qui calcule la somme des carrés des nombres pairs.

**Exercice 2 :** Créez une version optimisée d'une fonction de recherche de doublons.

**Exercice 3 :** Optimisez une fonction qui traite une grande liste de chaînes.

---

# Solutions des Exercices 💡

### Exercice 1 - Optimisation Somme Carrés

```python
# ❌ Version non optimisée
def sum_squares_slow(n):
    result = 0
    for i in range(n):
        if i % 2 == 0:
            result += i ** 2
    return result

# ✅ Version optimisée
def sum_squares_fast(n):
    return sum(i**2 for i in range(0, n, 2))

# Test de performance
import time

start = time.time()
result1 = sum_squares_slow(1000000)
time1 = time.time() - start

start = time.time()
result2 = sum_squares_fast(1000000)
time2 = time.time() - start

print(f"Slow: {time1:.4f}s")
print(f"Fast: {time2:.4f}s")
print(f"Speedup: {time1/time2:.1f}x")
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Recherche de Doublons

```python
# ❌ Version non optimisée
def find_duplicates_slow(items):
    duplicates = []
    for i in range(len(items)):
        for j in range(i + 1, len(items)):
            if items[i] == items[j] and items[i] not in duplicates:
                duplicates.append(items[i])
    return duplicates

# ✅ Version optimisée
def find_duplicates_fast(items):
    seen = set()
    duplicates = set()
    for item in items:
        if item in seen:
            duplicates.add(item)
        else:
            seen.add(item)
    return list(duplicates)

# Test
data = [1, 2, 3, 2, 4, 1, 5, 3]
print(find_duplicates_fast(data))  # [1, 2, 3]
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Traitement de Chaînes

```python
# ❌ Version non optimisée
def process_strings_slow(strings):
    result = ""
    for s in strings:
        result += s.upper() + " "
    return result

# ✅ Version optimisée
def process_strings_fast(strings):
    return " ".join(s.upper() for s in strings)

# Test avec grande liste
large_list = ["hello"] * 10000

import time

start = time.time()
result1 = process_strings_slow(large_list)
time1 = time.time() - start

start = time.time()
result2 = process_strings_fast(large_list)
time2 = time.time() - start

print(f"Slow: {time1:.4f}s")
print(f"Fast: {time2:.4f}s")
print(f"Speedup: {time1/time2:.1f}x")
```

---

# Points Clés à Retenir 🎯

### Résumé de l'optimisation

**1. Mesure :**
- Profiler avant d'optimiser
- Identifier les goulots d'étranglement
- Mesurer les améliorations

**2. Structures de Données :**
- Choisir la bonne structure
- Utiliser les collections spécialisées
- Optimiser pour les cas d'usage

**3. Techniques :**
- Cache et mémoisation
- Générateurs pour la mémoire
- Imports optimisés
- Algorithmes appropriés

**Prêt pour les frameworks web !** 🚀 