---
layout: default
routeAlias: 'context-managers'
---

# CONTEXT MANAGERS 🔧

### Gestion automatique des ressources

---

# Context Managers de Base

### Le mot-clé `with`

**Avantages des context managers :**
- Gestion automatique des ressources
- Code plus propre et lisible
- Gestion d'erreurs centralisée
- Réutilisabilité

---

# Classe Context Manager

```python
# Context manager basique
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None
    
    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.file:
            self.file.close()
```

---

# Utilisation du Context Manager

```python
# Utilisation
with FileManager('test.txt', 'w') as f:
    f.write('Hello World')
```

---
layout: default
---

# Context Managers avec `contextlib`

### Approche fonctionnelle

**Avantages de `@contextmanager` :**
- Syntaxe plus simple
- Moins de code boilerplate
- Plus lisible
- Gestion automatique des exceptions

---

# Context Manager avec Décorateur

```python
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    yield
    end = time.time()
    print(f"Temps d'exécution: {end - start}")
```

---

# Utilisation du Timer

```python
# Utilisation
with timer():
    # Code à mesurer
    import time
    time.sleep(1)
```

---

# Context Manager avec Valeur

```python
# Context manager avec valeur
@contextmanager
def temporary_file():
    import tempfile
    import os
    
    temp = tempfile.NamedTemporaryFile(delete=False)
    try:
        yield temp.name
    finally:
        os.unlink(temp.name)
```

---
layout: default
---

# Context Managers Avancés

### Gestion d'erreurs et ressources

**Fonctionnalités avancées :**
- Gestion d'erreurs personnalisée
- Ressources complexes
- Rollback automatique
- Logging intégré

---

# Context Manager de Base de Données

```python
class DatabaseConnection:
    def __init__(self, host, port):
        self.host = host
        self.port = port
        self.connection = None
    
    def __enter__(self):
        print(f"Connexion à {self.host}:{self.port}")
        self.connection = "connection_established"
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Fermeture de la connexion")
        self.connection = None
        if exc_type:
            print(f"Erreur: {exc_val}")
        return False  # Re-lève l'exception
```

---

# Utilisation avec Gestion d'Erreur

```python
# Utilisation avec gestion d'erreur
try:
    with DatabaseConnection("localhost", 5432) as db:
        print("Utilisation de la base de données")
        # Simulation d'une erreur
        raise ValueError("Erreur de base de données")
except ValueError as e:
    print(f"Erreur capturée : {e}")
```

---
layout: default
---

# Context Managers pour Threading

### Gestion des threads et locks

**Applications :**
- Gestion automatique des locks
- Synchronisation de threads
- Timeout automatique
- Nettoyage des ressources

---

# Context Manager pour Lock

```python
import threading
import time

class ThreadSafeCounter:
    def __init__(self):
        self._value = 0
        self._lock = threading.Lock()
    
    def increment(self):
        with self._lock:
            current = self._value
            time.sleep(0.1)  # Simulation d'un travail
            self._value = current + 1
    
    @property
    def value(self):
        with self._lock:
            return self._value
```

---

# Utilisation du Counter Thread-Safe

```python
# Utilisation
counter = ThreadSafeCounter()

def worker():
    for _ in range(5):
        counter.increment()

# Création de threads
threads = []
for _ in range(3):
    t = threading.Thread(target=worker)
    threads.append(t)
    t.start()

# Attendre la fin des threads
for t in threads:
    t.join()

print(f"Valeur finale : {counter.value}")
```

---
layout: default
---

# Context Managers pour Transactions

### Gestion des transactions de base de données

**Fonctionnalités :**
- Commit automatique en cas de succès
- Rollback automatique en cas d'erreur
- Gestion des connexions
- Logging des opérations

---

# Context Manager de Transaction

```python
class DatabaseTransaction:
    def __init__(self, connection):
        self.connection = connection
        self.committed = False
    
    def __enter__(self):
        print("Début de transaction")
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is None:
            # Aucune exception - commit
            print("Commit de la transaction")
            self.committed = True
        else:
            # Exception - rollback
            print("Rollback de la transaction")
            self.committed = False
        return False  # Propager l'exception
```

---

# Utilisation de la Transaction

```python
# Simulation d'une connexion
class MockConnection:
    def __init__(self):
        self.data = []
    
    def execute(self, query):
        print(f"Exécution : {query}")
        self.data.append(query)

# Utilisation
connection = MockConnection()

try:
    with DatabaseTransaction(connection) as tx:
        connection.execute("INSERT INTO users (name) VALUES ('Alice')")
        connection.execute("UPDATE users SET name = 'Bob' WHERE id = 1")
        # Simulation d'une erreur
        raise ValueError("Erreur de base de données")
except ValueError as e:
    print(f"Erreur : {e}")
```

---
layout: default
---

# Context Managers pour Profiling

### Mesure de performance automatique

**Applications :**
- Profiling de fonctions
- Mesure de temps d'exécution
- Monitoring de mémoire
- Traçabilité des performances

---

# Context Manager de Profiling

```python
import time
import cProfile
import pstats
from io import StringIO

class Profiler:
    def __init__(self, name="function"):
        self.name = name
        self.profiler = None
        self.stats = None
    
    def __enter__(self):
        self.profiler = cProfile.Profile()
        self.profiler.enable()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.profiler.disable()
        
        # Capture des statistiques
        s = StringIO()
        stats = pstats.Stats(self.profiler, stream=s)
        stats.sort_stats('cumulative')
        stats.print_stats(10)
        
        print(f"=== Profiling de {self.name} ===")
        print(s.getvalue())
```

---

# Utilisation du Profiler

```python
# Fonction à profiler
def fonction_lente():
    resultat = 0
    for i in range(100000):
        resultat += i
    return resultat

# Utilisation du profiler
with Profiler("fonction_lente"):
    resultat = fonction_lente()
    print(f"Résultat : {resultat}")
```

---
layout: default
---

# Context Managers pour Logging

### Logging automatique des opérations

**Fonctionnalités :**
- Log automatique des entrées/sorties
- Mesure du temps d'exécution
- Capture des erreurs
- Contextualisation des logs

---

# Context Manager de Logging

```python
import logging
import time
from contextlib import contextmanager

@contextmanager
def log_operation(operation_name, logger=None):
    if logger is None:
        logger = logging.getLogger(__name__)
    
    start_time = time.time()
    logger.info(f"Début de l'opération : {operation_name}")
    
    try:
        yield
        end_time = time.time()
        duration = end_time - start_time
        logger.info(f"Fin de l'opération : {operation_name} (durée: {duration:.4f}s)")
    except Exception as e:
        end_time = time.time()
        duration = end_time - start_time
        logger.error(f"Erreur dans l'opération : {operation_name} (durée: {duration:.4f}s) - {e}")
        raise
```

---

# Utilisation du Logging

```python
# Configuration du logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Utilisation
def traitement_complexe():
    time.sleep(0.5)  # Simulation d'un traitement
    if time.time() % 2 < 1:  # 50% de chance d'erreur
        raise ValueError("Erreur simulée")

# Test avec succès
with log_operation("traitement_succes"):
    time.sleep(0.1)

# Test avec erreur
try:
    with log_operation("traitement_erreur"):
        traitement_complexe()
except ValueError:
    pass
```

---
layout: default
---

# Context Managers pour Testing

### Tests avec setup/teardown automatique

**Applications :**
- Setup automatique des tests
- Nettoyage automatique
- Isolation des tests
- Mocking automatique

---

# Context Manager de Test

```python
import tempfile
import os
import shutil

class TestEnvironment:
    def __init__(self, test_name):
        self.test_name = test_name
        self.temp_dir = None
        self.original_cwd = None
    
    def __enter__(self):
        # Sauvegarder le répertoire de travail
        self.original_cwd = os.getcwd()
        
        # Créer un répertoire temporaire
        self.temp_dir = tempfile.mkdtemp(prefix=f"test_{self.test_name}_")
        
        # Changer vers le répertoire temporaire
        os.chdir(self.temp_dir)
        
        print(f"Environnement de test créé : {self.temp_dir}")
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        # Retourner au répertoire original
        os.chdir(self.original_cwd)
        
        # Nettoyer le répertoire temporaire
        shutil.rmtree(self.temp_dir)
        
        print(f"Environnement de test nettoyé : {self.temp_dir}")
```

---

# Utilisation de l'Environnement de Test

```python
# Test avec environnement isolé
with TestEnvironment("test_fichiers"):
    # Créer des fichiers de test
    with open("test.txt", "w") as f:
        f.write("Contenu de test")
    
    # Vérifier que le fichier existe
    assert os.path.exists("test.txt")
    
    # Lire le contenu
    with open("test.txt", "r") as f:
        contenu = f.read()
    
    assert contenu == "Contenu de test"
    print("Test réussi !")
```

---
layout: default
---

# Context Managers Combinés

### Combinaison de plusieurs context managers

**Techniques :**
- Context managers imbriqués
- Context managers multiples
- Context managers conditionnels
- Context managers avec paramètres

---

# Context Managers Imbriqués

```python
@contextmanager
def database_transaction(connection):
    print("Début de transaction")
    try:
        yield connection
        print("Commit de transaction")
    except Exception as e:
        print(f"Rollback de transaction : {e}")
        raise

@contextmanager
def log_operation(operation_name):
    print(f"Début : {operation_name}")
    try:
        yield
        print(f"Succès : {operation_name}")
    except Exception as e:
        print(f"Erreur : {operation_name} - {e}")
        raise

# Utilisation combinée
with log_operation("opération_complexe"):
    with database_transaction("connection"):
        print("Exécution de l'opération")
        # Simulation d'une erreur
        raise ValueError("Erreur simulée")
```

---

# Context Manager avec Paramètres

```python
@contextmanager
def timeout(seconds):
    import signal
    
    def timeout_handler(signum, frame):
        raise TimeoutError(f"Timeout après {seconds} secondes")
    
    # Configuration du signal (Unix seulement)
    old_handler = signal.signal(signal.SIGALRM, timeout_handler)
    signal.alarm(seconds)
    
    try:
        yield
    finally:
        signal.alarm(0)  # Désactiver l'alarme
        signal.signal(signal.SIGALRM, old_handler)

# Utilisation avec timeout
try:
    with timeout(5):
        import time
        time.sleep(10)  # Sera interrompu après 5 secondes
except TimeoutError as e:
    print(f"Timeout : {e}")
```

---
layout: default
---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez un context manager `@retry` qui relance une fonction jusqu'à N fois en cas d'échec.

**Exercice 2 :** Implémentez un context manager `@cache` qui met en cache les résultats de fonctions.

**Exercice 3 :** Créez un context manager `@benchmark` qui mesure les performances d'une fonction.

---

# Solutions des Exercices 💡

### Exercice 1 - Context Manager Retry

```python
import time
from contextlib import contextmanager

@contextmanager
def retry(max_attempts=3, delay=1.0):
    """Context manager qui relance une opération en cas d'échec"""
    last_exception = None
    
    for attempt in range(max_attempts):
        try:
            yield
            return  # Succès
        except Exception as e:
            last_exception = e
            if attempt < max_attempts - 1:
                print(f"Tentative {attempt + 1} échouée : {e}")
                time.sleep(delay)
    
    # Toutes les tentatives ont échoué
    raise last_exception

# Utilisation
def fonction_risquee():
    import random
    if random.random() < 0.7:
        raise ValueError("Échec aléatoire")
    return "Succès !"

try:
    with retry(max_attempts=5, delay=0.5):
        resultat = fonction_risquee()
        print(resultat)
except ValueError as e:
    print(f"Échec final : {e}")
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Context Manager Cache

```python
from contextlib import contextmanager
from functools import wraps

@contextmanager
def cache():
    """Context manager pour mettre en cache les résultats"""
    cache_dict = {}
    
    def cached_function(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # Créer une clé unique pour les arguments
            key = str(args) + str(sorted(kwargs.items()))
            
            if key not in cache_dict:
                cache_dict[key] = func(*args, **kwargs)
            
            return cache_dict[key]
        return wrapper
    
    yield cached_function

# Utilisation
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

with cache() as cached:
    # Décorer la fonction avec le cache
    fib_cached = cached(fibonacci)
    
    # Test avec cache
    print(fib_cached(35))  # Beaucoup plus rapide avec cache
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Context Manager Benchmark

```python
import time
import cProfile
import pstats
from contextlib import contextmanager

@contextmanager
def benchmark(name="function"):
    """Context manager pour benchmarker une fonction"""
    start_time = time.time()
    profiler = cProfile.Profile()
    profiler.enable()
    
    try:
        yield
    finally:
        profiler.disable()
        end_time = time.time()
        
        # Statistiques de performance
        duration = end_time - start_time
        print(f"=== Benchmark : {name} ===")
        print(f"Temps d'exécution : {duration:.6f} secondes")
        
        # Statistiques détaillées
        stats = pstats.Stats(profiler)
        stats.sort_stats('cumulative')
        stats.print_stats(5)

# Utilisation
def fonction_a_benchmarker():
    resultat = 0
    for i in range(1000000):
        resultat += i
    return resultat

with benchmark("fonction_a_benchmarker"):
    resultat = fonction_a_benchmarker()
    print(f"Résultat : {resultat}")
```

---

# Points Clés à Retenir 🎯

### Résumé des context managers

**1. Context managers de base :**
- Gestion automatique des ressources
- Syntaxe `with` claire et lisible
- Gestion d'erreurs centralisée

**2. Context managers avec `contextlib` :**
- Décorateur `@contextmanager`
- Syntaxe plus simple
- Moins de code boilerplate

**3. Applications avancées :**
- Threading et synchronisation
- Transactions de base de données
- Profiling et benchmarking
- Logging automatique

**4. Bonnes pratiques :**
- Toujours nettoyer les ressources
- Gérer les exceptions appropriément
- Documenter le comportement
- Tester les context managers

**5. Patterns courants :**
- Setup/teardown automatique
- Rollback en cas d'erreur
- Mesure de performance
- Isolation de tests

**Prêt pour les métaclasses !** 🚀 