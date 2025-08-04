---
layout: default
routeAlias: 'rappel-fondamentaux'
---

# RAPPEL DES FONDAMENTAUX 🔄

### Révision approfondie des concepts de base

---

# Pourquoi ce rappel ? 🤔

### Avant d'aborder les concepts avancés

**Objectifs de cette session :**
- Consolider les bases essentielles
- Identifier les points d'amélioration
- Préparer le terrain pour les concepts avancés
- Éviter les pièges courants

**Public cible :**
- Développeurs avec des bases Python
- Préparation aux concepts avancés
- Révision avant perfectionnement

---

# 1. Variables et Types - Niveau Avancé

### Compréhension approfondie des types Python

```python
# Types immutables vs mutables
x = 42          # int (immutable)
y = "hello"     # str (immutable)
z = [1, 2, 3]  # list (mutable)

# Références et copies
a = [1, 2, 3]
b = a           # Référence (même objet)
c = a.copy()    # Copie superficielle
d = a[:]        # Copie superficielle (slice)
e = list(a)     # Copie superficielle

# Copie profonde
import copy
f = copy.deepcopy(a)  # Copie profonde
```

---

# 2. Structures de Contrôle - Optimisations

### Techniques avancées de contrôle de flux

```python
# Optimisation des boucles
# ❌ Moins efficace
for i in range(len(items)):
    print(items[i])

# ✅ Plus efficace
for item in items:
    print(item)

# ✅ Avec index si nécessaire
for i, item in enumerate(items):
    print(f"{i}: {item}")

# List comprehensions avancées
squares = [x**2 for x in range(10) if x % 2 == 0]
matrix = [[i+j for j in range(3)] for i in range(3)]
```

---

# 3. Fonctions - Concepts Avancés

### Fonctionnalités avancées des fonctions

```python
# Fonctions avec annotations de type
def greet(name: str, age: int = 18) -> str:
    return f"Hello {name}, you are {age} years old"

# Fonctions avec *args et **kwargs
def flexible_func(*args, **kwargs):
    print(f"Positional args: {args}")
    print(f"Keyword args: {kwargs}")

# Fonctions lambda avancées
from functools import reduce
numbers = [1, 2, 3, 4, 5]
sum_squares = reduce(lambda x, y: x + y**2, numbers, 0)
```

---

# 4. Collections - Utilisation Avancée

### Techniques avancées avec les collections

```python
# Dictionnaires avancés
from collections import defaultdict, Counter

# defaultdict - évite les KeyError
dd = defaultdict(list)
dd['a'].append(1)  # Pas besoin de vérifier si 'a' existe

# Counter - comptage efficace
text = "hello world"
char_count = Counter(text)
most_common = char_count.most_common(3)

# Sets avancés
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}
union = set1 | set2
intersection = set1 & set2
difference = set1 - set2
```

---

# 5. Gestion d'Erreurs - Bonnes Pratiques

### Gestion d'erreurs robuste et efficace

```python
# Context managers personnalisés
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    yield
    end = time.time()
    print(f"Temps d'exécution: {end - start}")

# Utilisation
with timer():
    # Code à mesurer
    pass

# Gestion d'erreurs spécifique
try:
    result = risky_operation()
except (ValueError, TypeError) as e:
    logger.error(f"Erreur de type: {e}")
except Exception as e:
    logger.error(f"Erreur inattendue: {e}")
    raise  # Re-lève l'exception
```

---

# 6. Programmation Orientée Objet - Concepts Avancés

### POO avancée et bonnes pratiques

```python
# Propriétés et encapsulation
class BankAccount:
    def __init__(self, balance):
        self._balance = balance
    
    @property
    def balance(self):
        return self._balance
    
    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("Le solde ne peut pas être négatif")
        self._balance = value

# Méthodes de classe et statiques
class MathUtils:
    @staticmethod
    def add(x, y):
        return x + y
    
    @classmethod
    def from_string(cls, string):
        x, y = map(int, string.split(','))
        return cls(x, y)
```

---

# 7. Modules et Packages - Organisation

### Structure de projet professionnelle

```python
# Structure recommandée
my_project/
├── src/
│   └── my_package/
│       ├── __init__.py
│       ├── core.py
│       └── utils.py
├── tests/
│   └── test_core.py
├── docs/
├── requirements.txt
└── setup.py

# __init__.py avancé
from .core import main_function
from .utils import helper_function

__version__ = "1.0.0"
__all__ = ["main_function", "helper_function"]
```

---

# 8. Manipulation de Fichiers - Techniques Avancées

### Gestion de fichiers robuste

```python
# Pathlib - approche moderne
from pathlib import Path

# Création de structure
project_dir = Path("my_project")
project_dir.mkdir(exist_ok=True)
(project_dir / "data").mkdir(exist_ok=True)

# Lecture/écriture avec gestion d'erreurs
def safe_read_file(file_path):
    try:
        with open(file_path, 'r', encoding='utf-8') as f:
            return f.read()
    except FileNotFoundError:
        print(f"Fichier {file_path} non trouvé")
        return None
    except UnicodeDecodeError:
        print(f"Erreur d'encodage pour {file_path}")
        return None
```

---

# 9. Bonnes Pratiques - Code Professionnel

### Standards de qualité du code

```python
# PEP 8 - Style de code
def calculate_average(numbers: list[float]) -> float:
    """Calcule la moyenne d'une liste de nombres.
    
    Args:
        numbers: Liste de nombres flottants
        
    Returns:
        float: La moyenne calculée
        
    Raises:
        ValueError: Si la liste est vide
    """
    if not numbers:
        raise ValueError("La liste ne peut pas être vide")
    
    return sum(numbers) / len(numbers)

# Docstrings et annotations
from typing import Optional, List, Dict, Any
```

---

# 10. Performance et Optimisation - Bases

### Premiers concepts d'optimisation

```python
# Profiling simple
import time
import cProfile

def profile_function(func):
    def wrapper(*args, **kwargs):
        profiler = cProfile.Profile()
        profiler.enable()
        result = func(*args, **kwargs)
        profiler.disable()
        profiler.print_stats(sort='cumulative')
        return result
    return wrapper

# Optimisation des boucles
# ❌ Moins efficace
result = []
for i in range(1000):
    result.append(i * 2)

# ✅ Plus efficace
result = [i * 2 for i in range(1000)]
```

---

# Exercices de Révision 🎯

### Testez vos connaissances

**Exercice 1 :** Écrivez une fonction qui prend une liste de nombres et retourne un dictionnaire avec les statistiques (min, max, moyenne, médiane).

**Exercice 2 :** Créez une classe `Logger` qui utilise un context manager pour logger automatiquement les entrées/sorties de fonctions.

**Exercice 3 :** Implémentez un décorateur `@retry` qui relance une fonction jusqu'à 3 fois en cas d'échec.

---

# Solutions des Exercices 💡

### Exercice 1 - Statistiques

```python
from statistics import mean, median
from typing import Dict, List

def get_statistics(numbers: List[float]) -> Dict[str, float]:
    if not numbers:
        raise ValueError("La liste ne peut pas être vide")
    
    return {
        'min': min(numbers),
        'max': max(numbers),
        'mean': mean(numbers),
        'median': median(numbers)
    }

# Test
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
stats = get_statistics(numbers)
print(stats)
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Logger avec Context Manager

```python
import logging
from contextlib import contextmanager
from typing import Callable, Any

class Logger:
    def __init__(self, name: str):
        self.logger = logging.getLogger(name)
        self.logger.setLevel(logging.INFO)
    
    @contextmanager
    def log_function(self, func_name: str):
        self.logger.info(f"Entrée dans {func_name}")
        try:
            yield
            self.logger.info(f"Sortie normale de {func_name}")
        except Exception as e:
            self.logger.error(f"Erreur dans {func_name}: {e}")
            raise

# Utilisation
logger = Logger("my_app")
with logger.log_function("ma_fonction"):
    # Code de la fonction
    pass
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Décorateur Retry

```python
import time
from functools import wraps
from typing import Callable, Any

def retry(max_attempts: int = 3, delay: float = 1.0):
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        def wrapper(*args, **kwargs) -> Any:
            last_exception = None
            
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    last_exception = e
                    if attempt < max_attempts - 1:
                        print(f"Tentative {attempt + 1} échouée: {e}")
                        time.sleep(delay)
            
            raise last_exception
        return wrapper
    return decorator

# Utilisation
@retry(max_attempts=3, delay=0.5)
def risky_function():
    import random
    if random.random() < 0.7:
        raise ValueError("Échec aléatoire")
    return "Succès!"
```

---

# Points Clés à Retenir 🎯

### Résumé des fondamentaux avancés

**1. Types et Mutabilité :**
- Comprendre la différence entre types mutables/immutables
- Maîtriser les références et copies

**2. Optimisation :**
- Utiliser les bonnes structures de données
- Éviter les anti-patterns de performance

**3. Bonnes Pratiques :**
- Suivre PEP 8
- Écrire du code lisible et maintenable
- Documenter correctement

**4. Gestion d'Erreurs :**
- Utiliser les context managers
- Gérer les exceptions de manière appropriée

---

# Préparation au Perfectionnement 🚀

### Vous êtes prêt pour les concepts avancés !

**Prochaines étapes :**
- Décorateurs et générateurs
- Métaclasses et introspection
- Programmation asynchrone
- Design patterns
- Optimisation avancée

**Compétences consolidées :**
✅ Maîtrise des types Python
✅ Optimisation des structures de contrôle
✅ Fonctions avancées
✅ Collections spécialisées
✅ Gestion d'erreurs robuste
✅ POO avancée
✅ Bonnes pratiques de code

**Prêt pour la suite !** 🎉 