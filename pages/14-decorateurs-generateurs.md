---
layout: default
routeAlias: 'decorateurs-generateurs'
---

# DÉCORATEURS ET GÉNÉRATEURS 🎭

### Concepts avancés de programmation fonctionnelle

---

# Décorateurs Avancés

### Au-delà des bases

**Concepts avancés :**
- Décorateurs avec paramètres
- Décorateurs de classe
- Décorateurs de méthodes
- Décorateurs de validation

---

# Décorateur avec Paramètres

```python
# Décorateur avec paramètres
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello {name}")
```

---

# Utilisation du Décorateur avec Paramètres

```python
# Utilisation
greet("John")
```

---

# Décorateur de Classe

```python
# Décorateur de classe
def singleton(cls):
    instances = {}
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class Database:
    def __init__(self):
        print("Initialisation de la base de données")
```

---

# Test du Décorateur de Classe

```python
# Test
db1 = Database()
db2 = Database()
print(db1 is db2)  # True
```

---

# Générateurs et Itérateurs

### Gestion efficace de la mémoire

**Avantages des générateurs :**
- Économie de mémoire
- Évaluation lazy
- Traitement de grandes séquences
- Pipelines de données

---

# Générateur Simple

```python
# Générateur simple
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b
```

---

# Utilisation du Générateur Simple

```python
# Utilisation
fib = fibonacci()
for _ in range(10):
    print(next(fib))
```

---

# Générateur avec Paramètres

```python
# Générateur avec paramètres
def count_up_to(n):
    i = 0
    while i < n:
        yield i
        i += 1
```

---

# Expression Génératrice

```python
# Expression génératrice
squares = (x**2 for x in range(1000000))  # Pas de liste en mémoire
```

---

# Utilisation du Générateur avec Paramètres

```python
# Utilisation
for num in count_up_to(5):
    print(num)
```

---

# Décorateurs de Méthodes

### Décorateurs pour les classes

**Types de décorateurs :**
- `@property` : propriétés
- `@classmethod` : méthodes de classe
- `@staticmethod` : méthodes statiques
- Décorateurs personnalisés

---

# Décorateur de Propriété

```python
# Décorateur de propriété
class Circle:
    def __init__(self, radius):
        self._radius = radius
    
    @property
    def radius(self):
        return self._radius
    
    @radius.setter
    def radius(self, value):
        if value < 0:
            raise ValueError("Le rayon ne peut pas être négatif")
        self._radius = value
```

---

# Propriétés Calculées

```python
# Propriétés calculées
class Circle:
    def __init__(self, radius):
        self._radius = radius
    
    @property
    def area(self):
        import math
        return math.pi * self._radius ** 2
    
    @property
    def circumference(self):
        import math
        return 2 * math.pi * self._radius
```

---

# Utilisation des Propriétés

```python
# Utilisation
circle = Circle(5)
print(f"Rayon: {circle.radius}")
print(f"Aire: {circle.area}")
print(f"Circonférence: {circle.circumference}")
```

---

# Générateurs Avancés

### Patterns avancés avec les générateurs

**Applications avancées :**
- Pipelines de traitement
- Filtrage de données
- Transformation de séquences
- Traitement de fichiers

---

# Pipeline de Générateurs

```python
# Pipeline de générateurs
def read_numbers(filename):
    with open(filename) as f:
        for line in f:
            yield int(line.strip())

def filter_even(numbers):
    for num in numbers:
        if num % 2 == 0:
            yield num

def square(numbers):
    for num in numbers:
        yield num ** 2
```

---

# Pipeline Complet

```python
# Pipeline complet
numbers = read_numbers('numbers.txt')
even_numbers = filter_even(numbers)
squared_even = square(even_numbers)

for result in squared_even:
    print(result)
```

---

# Décorateurs de Validation

### Validation automatique des données

**Types de validation :**
- Validation de types
- Validation de valeurs
- Validation de paramètres
- Validation de retour

---

# Validation de Types

```python
def validate_types(**type_hints):
    def decorator(func):
        def wrapper(*args, **kwargs):
            # Validation des arguments
            for i, (name, expected_type) in enumerate(type_hints.items()):
                if i < len(args):
                    if not isinstance(args[i], expected_type):
                        raise TypeError(f"Argument {name} doit être de type {expected_type}")
            
            result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@validate_types(x=int, y=str)
def process_data(x, y):
    return f"Processed: {x} and {y}"
```

---

# Test de Validation

```python
# Test
try:
    process_data("5", 123)  # Erreur de type
except TypeError as e:
    print(f"Erreur: {e}")
```

---

# Succès de Validation

```python
# Succès
result = process_data(5, "hello")
print(result)
```

---

# Générateurs Infinis

### Générateurs sans limite

**Applications :**
- Séquences mathématiques
- Flux de données
- Simulation de processus
- Génération de données

---

# Générateur de Nombres Premiers

```python
# Générateur de nombres premiers
def primes():
    """Générateur infini de nombres premiers"""
    def is_prime(n):
        if n < 2:
            return False
        for i in range(2, int(n ** 0.5) + 1):
            if n % i == 0:
                return False
        return True
    
    n = 2
    while True:
        if is_prime(n):
            yield n
        n += 1
```

---

# Utilisation du Générateur Infini

```python
# Utilisation
prime_gen = primes()
first_10_primes = [next(prime_gen) for _ in range(10)]
print(first_10_primes)
```

---

# Utilisation avec itertools

```python
# Ou avec itertools
from itertools import islice
first_10 = list(islice(primes(), 10))
print(first_10)
```

---

# Décorateurs de Cache

### Optimisation avec mise en cache

**Types de cache :**
- Cache simple
- Cache avec TTL
- Cache avec limite de taille
- Cache avec politique d'éviction

---

# Cache avec TTL

```python
import functools
import time

def cache_with_ttl(ttl_seconds=300):
    def decorator(func):
        cache = {}
        
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            key = str(args) + str(sorted(kwargs.items()))
            current_time = time.time()
            
            if key in cache:
                result, timestamp = cache[key]
                if current_time - timestamp < ttl_seconds:
                    return result
            
            result = func(*args, **kwargs)
            cache[key] = (result, current_time)
            return result
        
        return wrapper
    return decorator
```

---

# Utilisation du Cache

```python
@cache_with_ttl(ttl_seconds=60)
def expensive_calculation(n):
    time.sleep(1)  # Simulation d'un calcul coûteux
    return n * n
```

---

# Test du Cache

```python
# Test
print(expensive_calculation(5))  # Premier appel - lent
print(expensive_calculation(5))  # Deuxième appel - rapide (cache)
```

---

# Générateurs de Coroutines

### Introduction aux coroutines

**Concepts :**
- Générateurs qui reçoivent des valeurs
- Communication bidirectionnelle
- Pipelines de traitement
- Contrôle de flux

---

# Coroutine Simple

```python
def coroutine_example():
    """Générateur qui peut recevoir des valeurs"""
    while True:
        x = yield
        if x is None:
            break
        yield x * 2
```

---

# Utilisation de la Coroutine

```python
# Utilisation
gen = coroutine_example()
next(gen)  # Démarrage du générateur

print(gen.send(5))  # Envoie 5, reçoit 10
next(gen)  # Prépare pour le prochain envoi
print(gen.send(10))  # Envoie 10, reçoit 20
```

---

# Décorateurs de Logging

### Logging automatique des fonctions

**Fonctionnalités :**
- Log des entrées/sorties
- Mesure du temps d'exécution
- Log des erreurs
- Traçabilité

---

# Décorateur de Logging

```python
import logging
import functools
from datetime import datetime

def log_function_calls(logger_name="app"):
    def decorator(func):
        logger = logging.getLogger(logger_name)
        
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            start_time = datetime.now()
            logger.info(f"Appel de {func.__name__} avec args={args}, kwargs={kwargs}")
            
            try:
                result = func(*args, **kwargs)
                end_time = datetime.now()
                duration = (end_time - start_time).total_seconds()
                logger.info(f"{func.__name__} terminé en {duration:.4f}s, résultat={result}")
                return result
            except Exception as e:
                logger.error(f"Erreur dans {func.__name__}: {e}")
                raise
        
        return wrapper
    return decorator
```

---

# Utilisation du Logging

```python
@log_function_calls()
def slow_function(n):
    import time
    time.sleep(0.1)
    return n * 2
```

---

# Test du Logging

```python
# Test
logging.basicConfig(level=logging.INFO)
result = slow_function(5)
print(f"Résultat: {result}")
```

---

# Exercices Avancés 🎯

### Testez vos compétences

**Exercice 1 :** Créez un décorateur `@memoize` qui met en cache les résultats de fonctions récursives.

**Exercice 2 :** Implémentez un générateur `fibonacci_lazy` qui génère les nombres de Fibonacci de manière lazy.

**Exercice 3 :** Créez un décorateur `@timeout` qui interrompt une fonction si elle prend trop de temps.

---

# Solutions des Exercices 💡

### Exercice 1 - Décorateur Memoize

```python
def memoize(func):
    cache = {}
    
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    
    return wrapper

@memoize
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

---

# Test du Memoize

```python
# Test - beaucoup plus rapide avec memoization
print(fibonacci(35))  # Sans memoize: très lent, avec memoize: instantané
```

---

# Vérification du Cache

```python
# Vérification du cache
print(f"Cache size: {len(fibonacci.__closure__[0].cell_contents)}")
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Générateur Fibonacci Lazy

```python
def fibonacci_lazy():
    """Générateur infini de nombres de Fibonacci"""
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b
```

---

# Utilisation du Générateur Fibonacci

```python
# Utilisation
fib_gen = fibonacci_lazy()
first_20 = [next(fib_gen) for _ in range(20)]
print(first_20)
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Décorateur Timeout

```python
import signal
import functools

def timeout(seconds):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            def timeout_handler(signum, frame):
                raise TimeoutError(f"Fonction {func.__name__} a dépassé le délai de {seconds}s")
            
            # Configuration du signal (Unix seulement)
            old_handler = signal.signal(signal.SIGALRM, timeout_handler)
            signal.alarm(seconds)
            
            try:
                result = func(*args, **kwargs)
                return result
            finally:
                signal.alarm(0)  # Désactive l'alarme
                signal.signal(signal.SIGALRM, old_handler)
        
        return wrapper
    return decorator
```

---

# Test du Timeout

```python
@timeout(5)
def slow_function():
    import time
    time.sleep(10)  # Cette fonction sera interrompue après 5 secondes
    return "Terminé"
```

---

# Exécution du Test Timeout

```python
# Test
try:
    result = slow_function()
    print(result)
except TimeoutError as e:
    print(f"Timeout: {e}")
```

---

# Points Clés à Retenir 🎯

### Résumé des concepts avancés

**1. Décorateurs :**
- Permettent de modifier le comportement des fonctions
- Utiles pour la validation, le cache, le logging
- Peuvent prendre des paramètres

**2. Générateurs :**
- Économisent la mémoire pour les grandes séquences
- Permettent le traitement lazy des données
- Créent des pipelines de traitement

**3. Applications Pratiques :**
- Cache et optimisation
- Validation automatique
- Logging et monitoring
- Traitement de données en streaming

**Prêt pour les context managers !** 🚀 