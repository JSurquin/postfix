---
layout: default
routeAlias: 'decorateurs-generateurs'
---

# DÉCORATEURS ET GÉNÉRATEURS 🎭

### Concepts avancés de programmation fonctionnelle

---

# Décorateurs Avancés

### Au-delà des bases

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

# Générateurs et Itérateurs

### Gestion efficace de la mémoire

```python
# Générateur simple
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Utilisation
fib = fibonacci()
for _ in range(10):
    print(next(fib))

# Générateur avec paramètres
def count_up_to(n):
    i = 0
    while i < n:
        yield i
        i += 1

# Expression génératrice
squares = (x**2 for x in range(1000000))  # Pas de liste en mémoire
```

---

# Décorateurs de Méthodes

### Décorateurs pour les classes

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
    
    @property
    def area(self):
        import math
        return math.pi * self._radius ** 2

# Décorateur de méthode de classe
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day
    
    @classmethod
    def from_string(cls, date_string):
        year, month, day = map(int, date_string.split('-'))
        return cls(year, month, day)
```

---

# Générateurs Avancés

### Patterns avancés avec les générateurs

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

# Test
try:
    process_data("5", 123)  # Erreur de type
except TypeError as e:
    print(f"Erreur: {e}")
```

---

# Générateurs Infinis

### Générateurs sans limite

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

# Utilisation
prime_gen = primes()
first_10_primes = [next(prime_gen) for _ in range(10)]
print(first_10_primes)
```

---

# Décorateurs de Cache

### Optimisation avec mise en cache

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

@cache_with_ttl(ttl_seconds=60)
def expensive_calculation(n):
    time.sleep(1)  # Simulation d'un calcul coûteux
    return n * n
```

---

# Générateurs de Coroutines

### Introduction aux coroutines

```python
def coroutine_example():
    """Générateur qui peut recevoir des valeurs"""
    while True:
        x = yield
        if x is None:
            break
        yield x * 2

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

@log_function_calls()
def slow_function(n):
    import time
    time.sleep(0.1)
    return n * 2
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

# Test - beaucoup plus rapide avec memoization
print(fibonacci(35))  # Sans memoize: très lent, avec memoize: instantané
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

# Utilisation
fib_gen = fibonacci_lazy()
first_20 = [next(fib_gen) for _ in range(20)]
print(first_20)

# Ou avec itertools
from itertools import islice
first_10 = list(islice(fibonacci_lazy(), 10))
print(first_10)
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

@timeout(5)
def slow_function():
    import time
    time.sleep(10)  # Cette fonction sera interrompue après 5 secondes
    return "Terminé"
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