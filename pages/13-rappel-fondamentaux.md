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

**Types immutables vs mutables :**
- `int`, `str`, `tuple` : immutables
- `list`, `dict`, `set` : mutables

**Références et copies :**
- Référence : même objet en mémoire
- Copie superficielle : nouveaux objets, références partagées
- Copie profonde : objets complètement indépendants

---

# Types Immutables vs Mutables

```python
# Types immutables
x = 42          # int (immutable)
y = "hello"     # str (immutable)
t = (1, 2, 3)  # tuple (immutable)

# Types mutables
z = [1, 2, 3]  # list (mutable)
d = {"a": 1}   # dict (mutable)
s = {1, 2, 3}  # set (mutable)
```

---

# Références et Copies

```python
# Référence (même objet)
a = [1, 2, 3]
b = a           # Référence (même objet)

# Copies superficielles
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

**Optimisations principales :**
- Éviter `range(len())` quand possible
- Utiliser `enumerate()` pour l'index
- Préférer les list comprehensions
- Optimiser les boucles imbriquées

---

# Optimisation des Boucles

```python
# ❌ Moins efficace
for i in range(len(items)):
    print(items[i])

# ✅ Plus efficace
for item in items:
    print(item)

# ✅ Avec index si nécessaire
for i, item in enumerate(items):
    print(f"{i}: {item}")
```

---

# List Comprehensions Avancées

```python
# List comprehension simple
squares = [x**2 for x in range(10)]

# Avec condition
even_squares = [x**2 for x in range(10) if x % 2 == 0]

# List comprehension imbriquée
matrix = [[i+j for j in range(3)] for i in range(3)]

# Dict comprehension
square_dict = {x: x**2 for x in range(5)}
```

---

# 3. Fonctions - Concepts Avancés

### Fonctionnalités avancées des fonctions

**Concepts avancés :**
- Annotations de type
- Arguments variables (`*args`, `**kwargs`)
- Fonctions lambda avancées
- Fonctions d'ordre supérieur

---

# Fonctions avec Annotations de Type

```python
# Fonctions avec annotations de type
def greet(name: str, age: int = 18) -> str:
    return f"Hello {name}, you are {age} years old"

# Fonction avec types complexes
from typing import List, Dict, Optional

def process_data(items: List[int], config: Optional[Dict] = None) -> List[str]:
    if config is None:
        config = {}
    return [str(item) for item in items]
```

---

# Arguments Variables

```python
# Fonctions avec *args et **kwargs
def flexible_func(*args, **kwargs):
    print(f"Positional args: {args}")
    print(f"Keyword args: {kwargs}")

# Utilisation
flexible_func(1, 2, 3, name="John", age=30)

# Fonction avec paramètres mixtes
def mixed_func(required, *args, optional=None, **kwargs):
    print(f"Required: {required}")
    print(f"Args: {args}")
    print(f"Optional: {optional}")
    print(f"Kwargs: {kwargs}")
```

---

# Fonctions Lambda Avancées

```python
# Fonctions lambda avec reduce
from functools import reduce
numbers = [1, 2, 3, 4, 5]
sum_squares = reduce(lambda x, y: x + y**2, numbers, 0)

# Lambda avec filter et map
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
doubled = list(map(lambda x: x * 2, numbers))

# Lambda avec sorted
names = ["Alice", "Bob", "Charlie"]
sorted_names = sorted(names, key=lambda x: len(x))
```

---

# 4. Collections - Utilisation Avancée

### Techniques avancées avec les collections

**Collections spécialisées :**
- `defaultdict` : évite les KeyError
- `Counter` : comptage efficace
- `deque` : file double-ended
- `namedtuple` : tuples nommés

---

# Dictionnaires Avancés

```python
# defaultdict - évite les KeyError
from collections import defaultdict

dd = defaultdict(list)
dd['a'].append(1)  # Pas besoin de vérifier si 'a' existe

# Counter - comptage efficace
from collections import Counter

text = "hello world"
char_count = Counter(text)
most_common = char_count.most_common(3)
```

---

# Sets et Opérations Ensemblistes

```python
# Sets avancés
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# Opérations ensemblistes
union = set1 | set2
intersection = set1 & set2
difference = set1 - set2
symmetric_diff = set1 ^ set2

# Frozenset (immutable)
fs = frozenset([1, 2, 3])
```

---

# 5. Gestion d'Erreurs - Bonnes Pratiques

### Gestion d'erreurs robuste et efficace

**Bonnes pratiques :**
- Utiliser des context managers
- Gérer les exceptions spécifiques
- Éviter les `except: pass`
- Logger les erreurs appropriément

---

# Context Managers Personnalisés

```python
# Context manager avec contextlib
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
```

---

# Gestion d'Erreurs Spécifique

```python
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

**Concepts avancés :**
- Propriétés et encapsulation
- Méthodes de classe et statiques
- Héritage multiple
- Classes abstraites

---

# Propriétés et Encapsulation

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
```

---

# Méthodes de Classe et Statiques

```python
# Méthodes de classe et statiques
class MathUtils:
    @staticmethod
    def add(x, y):
        return x + y
    
    @classmethod
    def from_string(cls, string):
        x, y = map(int, string.split(','))
        return cls(x, y)

# Utilisation
result = MathUtils.add(5, 3)
obj = MathUtils.from_string("10,20")
```

---

# 7. Modules et Packages - Organisation

### Structure de projet professionnelle

**Structure recommandée :**
- Séparation claire des responsabilités
- Packages avec `__init__.py`
- Tests unitaires
- Documentation

---

# Structure de Projet

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
```

---

# __init__.py Avancé

```python
# __init__.py avancé
from .core import main_function
from .utils import helper_function

__version__ = "1.0.0"
__all__ = ["main_function", "helper_function"]

# Configuration du package
def setup_package():
    """Configuration initiale du package"""
    pass
```

---

# 8. Manipulation de Fichiers - Techniques Avancées

### Gestion de fichiers robuste

**Techniques avancées :**
- `pathlib` pour la gestion des chemins
- Gestion d'erreurs robuste
- Context managers personnalisés
- Encodage approprié

---

# Pathlib - Approche Moderne

```python
# Pathlib - approche moderne
from pathlib import Path

# Création de structure
project_dir = Path("my_project")
project_dir.mkdir(exist_ok=True)
(project_dir / "data").mkdir(exist_ok=True)

# Manipulation de chemins
file_path = Path("my_folder/my_file.txt")
parent_dir = file_path.parent
```

---

# Lecture/Écriture Robuste

```python
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

**Standards :**
- PEP 8 pour le style
- Docstrings appropriées
- Annotations de type
- Gestion d'erreurs

---

# PEP 8 - Style de Code

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
```

---

# Annotations de Type

```python
# Docstrings et annotations
from typing import Optional, List, Dict, Any

def process_data(
    items: List[int], 
    config: Optional[Dict[str, Any]] = None
) -> List[str]:
    """Traite une liste de données avec configuration optionnelle."""
    if config is None:
        config = {}
    return [str(item) for item in items]
```

---

# 10. Performance et Optimisation - Bases

### Premiers concepts d'optimisation

**Concepts de base :**
- Profiling simple
- Optimisation des boucles
- Choix des structures de données
- Éviter les anti-patterns

---

# Profiling Simple

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
```

---

# Optimisation des Boucles

```python
# Optimisation des boucles
# ❌ Moins efficace
result = []
for i in range(1000):
    result.append(i * 2)

# ✅ Plus efficace
result = [i * 2 for i in range(1000)]

# ✅ Avec générateur pour la mémoire
result = (i * 2 for i in range(1000000))
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
```

---

# Test des Statistiques

```python
# Test
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
stats = get_statistics(numbers)
print(stats)
# {'min': 1, 'max': 10, 'mean': 5.5, 'median': 5.5}
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
```

---

# Utilisation du Logger

```python
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
```

---

# Test du Décorateur Retry

```python
# Utilisation
@retry(max_attempts=3, delay=0.5)
def risky_function():
    import random
    if random.random() < 0.7:
        raise ValueError("Échec aléatoire")
    return "Succès!"

# Test
try:
    result = risky_function()
    print(result)
except ValueError as e:
    print(f"Échec final: {e}")
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