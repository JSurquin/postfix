---
layout: default
routeAlias: 'context-managers'
---

# CONTEXT MANAGERS 🔧

### Gestion automatique des ressources

---

# Context Managers de Base

### Le mot-clé `with`

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

# Utilisation
with FileManager('test.txt', 'w') as f:
    f.write('Hello World')
```

---

# Context Managers avec `contextlib`

### Approche fonctionnelle

```python
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
    import time
    time.sleep(1)

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

# Context Managers Avancés

### Gestion d'erreurs et ressources

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

# Utilisation avec gestion d'erreur
try:
    with DatabaseConnection("localhost", 5432) as db:
        print("Utilisation de la base de données")
        raise ValueError("Erreur simulée")
except ValueError as e:
    print(f"Erreur capturée: {e}")
```

---

# Context Managers pour le Threading

### Gestion des threads et locks

```python
import threading
from contextlib import contextmanager

@contextmanager
def thread_safe_operation(lock):
    lock.acquire()
    try:
        yield
    finally:
        lock.release()

# Utilisation
lock = threading.Lock()
shared_resource = []

def worker():
    with thread_safe_operation(lock):
        shared_resource.append(threading.current_thread().name)

# Test
threads = [threading.Thread(target=worker) for _ in range(5)]
for t in threads:
    t.start()
for t in threads:
    t.join()
print(shared_resource)
```

---

# Context Managers pour les Transactions

### Gestion des transactions de base de données

```python
class Transaction:
    def __init__(self, db):
        self.db = db
        self.committed = False
    
    def __enter__(self):
        print("Début de transaction")
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type:
            print("Rollback de la transaction")
            return False
        else:
            print("Commit de la transaction")
            self.committed = True
            return True

# Simulation
class MockDB:
    def transaction(self):
        return Transaction(self)

db = MockDB()
with db.transaction():
    print("Opérations de base de données")
    # Si une exception se produit ici, rollback automatique
```

---

# Context Managers pour le Profiling

### Mesure de performance automatique

```python
import time
import cProfile
import pstats
from contextlib import contextmanager

@contextmanager
def profile_function(name="function"):
    profiler = cProfile.Profile()
    profiler.enable()
    yield
    profiler.disable()
    
    stats = pstats.Stats(profiler)
    stats.sort_stats('cumulative')
    print(f"\nProfilage de {name}:")
    stats.print_stats(10)

# Utilisation
def slow_function():
    time.sleep(0.1)
    sum(range(10000))

with profile_function("slow_function"):
    slow_function()
```

---

# Context Managers pour les Tests

### Setup et teardown automatiques

```python
import tempfile
import os
from contextlib import contextmanager

@contextmanager
def temporary_directory():
    temp_dir = tempfile.mkdtemp()
    try:
        yield temp_dir
    finally:
        import shutil
        shutil.rmtree(temp_dir)

# Utilisation dans les tests
def test_file_operations():
    with temporary_directory() as temp_dir:
        test_file = os.path.join(temp_dir, "test.txt")
        with open(test_file, 'w') as f:
            f.write("test data")
        
        # Test avec le fichier
        assert os.path.exists(test_file)
        # Le répertoire sera automatiquement supprimé
```

---

# Context Managers pour la Logging

### Logging automatique des opérations

```python
import logging
from contextlib import contextmanager

@contextmanager
def log_operation(operation_name):
    logger = logging.getLogger(__name__)
    logger.info(f"Début de l'opération: {operation_name}")
    try:
        yield
        logger.info(f"Opération réussie: {operation_name}")
    except Exception as e:
        logger.error(f"Erreur dans l'opération {operation_name}: {e}")
        raise

# Utilisation
logging.basicConfig(level=logging.INFO)

with log_operation("traitement_données"):
    # Code de traitement
    print("Traitement en cours...")
```

---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez un context manager `@suppress_exceptions` qui ignore silencieusement les exceptions spécifiées.

**Exercice 2 :** Implémentez un context manager `@change_directory` qui change temporairement le répertoire de travail.

**Exercice 3 :** Créez un context manager `@benchmark` qui mesure le temps et la mémoire utilisés.

---

# Solutions des Exercices 💡

### Exercice 1 - Suppression d'Exceptions

```python
from contextlib import contextmanager

@contextmanager
def suppress_exceptions(*exceptions):
    try:
        yield
    except exceptions as e:
        print(f"Exception supprimée: {e}")

# Utilisation
with suppress_exceptions(ValueError, TypeError):
    result = int("abc")  # ValueError supprimée
    print("Continuation après erreur")
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Changement de Répertoire

```python
import os
from contextlib import contextmanager

@contextmanager
def change_directory(path):
    original_dir = os.getcwd()
    try:
        os.chdir(path)
        yield
    finally:
        os.chdir(original_dir)

# Utilisation
print(f"Répertoire actuel: {os.getcwd()}")
with change_directory("/tmp"):
    print(f"Répertoire temporaire: {os.getcwd()}")
print(f"Répertoire restauré: {os.getcwd()}")
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Benchmark

```python
import time
import psutil
import os
from contextlib import contextmanager

@contextmanager
def benchmark():
    process = psutil.Process(os.getpid())
    start_memory = process.memory_info().rss
    start_time = time.time()
    
    yield
    
    end_time = time.time()
    end_memory = process.memory_info().rss
    
    print(f"Temps: {end_time - start_time:.4f}s")
    print(f"Mémoire: {(end_memory - start_memory) / 1024:.2f} KB")

# Utilisation
with benchmark():
    # Code à mesurer
    data = [i**2 for i in range(100000)]
```

---

# Points Clés à Retenir 🎯

### Résumé des context managers

**1. Avantages :**
- Gestion automatique des ressources
- Code plus propre et lisible
- Gestion d'erreurs centralisée

**2. Cas d'usage :**
- Fichiers et connexions
- Threading et synchronisation
- Profiling et benchmarking
- Tests et setup/teardown

**3. Bonnes pratiques :**
- Toujours utiliser `finally` pour le nettoyage
- Gérer les exceptions appropriément
- Documenter le comportement attendu

**Prêt pour les métaclasses !** 🚀 