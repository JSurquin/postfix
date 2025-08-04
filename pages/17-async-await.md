---
layout: default
routeAlias: 'async-await'
---

# PROGRAMMATION ASYNCHRONE ⚡

### async/await et gestion des tâches concurrentes

---

# Concepts de Base

### async/await en Python

```python
import asyncio

async def hello_world():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

# Exécution
asyncio.run(hello_world())

# Fonction asynchrone avec valeur de retour
async def fetch_data():
    await asyncio.sleep(2)
    return {"data": "résultat"}

# Utilisation
result = asyncio.run(fetch_data())
print(result)
```

---

# Gestion des Tâches

### Exécution concurrente

```python
import asyncio
import aiohttp

async def fetch_url(session, url):
    async with session.get(url) as response:
        return await response.text()

async def fetch_multiple_urls():
    urls = [
        "https://httpbin.org/delay/1",
        "https://httpbin.org/delay/2",
        "https://httpbin.org/delay/3"
    ]
    
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
        return results

# Exécution
results = asyncio.run(fetch_multiple_urls())
```

---

# Gestion d'Erreurs

### try/except en asynchrone

```python
async def risky_operation():
    await asyncio.sleep(1)
    if True:  # Simulation d'erreur
        raise ValueError("Erreur simulée")
    return "succès"

async def safe_operation():
    try:
        result = await risky_operation()
        return result
    except ValueError as e:
        print(f"Erreur capturée: {e}")
        return "valeur par défaut"

# Exécution
result = asyncio.run(safe_operation())
```

---

# Context Managers Asynchrones

### Gestion de ressources asynchrones

```python
class AsyncDatabase:
    async def __aenter__(self):
        print("Connexion à la base de données")
        return self
    
    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("Fermeture de la connexion")
    
    async def query(self, sql):
        await asyncio.sleep(0.1)
        return f"Résultat de: {sql}"

async def main():
    async with AsyncDatabase() as db:
        result = await db.query("SELECT * FROM users")
        print(result)

asyncio.run(main())
```

---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez une fonction asynchrone qui simule le téléchargement de plusieurs fichiers.

**Exercice 2 :** Implémentez un rate limiter asynchrone qui limite le nombre de requêtes par seconde.

**Exercice 3 :** Créez un pool de workers asynchrones pour traiter une liste de tâches.

---

# Solutions des Exercices 💡

### Exercice 1 - Téléchargement Multiple

```python
import asyncio
import random

async def download_file(filename):
    delay = random.uniform(0.5, 2.0)
    await asyncio.sleep(delay)
    return f"Contenu de {filename}"

async def download_all_files():
    files = ["file1.txt", "file2.txt", "file3.txt", "file4.txt"]
    tasks = [download_file(f) for f in files]
    results = await asyncio.gather(*tasks)
    return results

# Test
results = asyncio.run(download_all_files())
print(results)
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Rate Limiter

```python
import asyncio
import time

class RateLimiter:
    def __init__(self, max_requests, time_window):
        self.max_requests = max_requests
        self.time_window = time_window
        self.requests = []
    
    async def acquire(self):
        now = time.time()
        # Nettoyer les anciennes requêtes
        self.requests = [req for req in self.requests if now - req < self.time_window]
        
        if len(self.requests) >= self.max_requests:
            # Attendre
            wait_time = self.requests[0] + self.time_window - now
            await asyncio.sleep(wait_time)
        
        self.requests.append(now)

# Utilisation
limiter = RateLimiter(max_requests=3, time_window=1.0)

async def limited_request(i):
    await limiter.acquire()
    print(f"Requête {i} exécutée")
    await asyncio.sleep(0.1)

async def test_rate_limiter():
    tasks = [limited_request(i) for i in range(10)]
    await asyncio.gather(*tasks)

asyncio.run(test_rate_limiter())
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Pool de Workers

```python
import asyncio
from asyncio import Queue

class WorkerPool:
    def __init__(self, num_workers):
        self.num_workers = num_workers
        self.queue = Queue()
        self.workers = []
    
    async def worker(self, worker_id):
        while True:
            try:
                task = await asyncio.wait_for(self.queue.get(), timeout=1.0)
                if task is None:  # Signal d'arrêt
                    break
                
                result = await task()
                print(f"Worker {worker_id}: {result}")
                self.queue.task_done()
            except asyncio.TimeoutError:
                continue
    
    async def start(self):
        self.workers = [
            asyncio.create_task(self.worker(i))
            for i in range(self.num_workers)
        ]
    
    async def stop(self):
        for _ in self.workers:
            await self.queue.put(None)
        await asyncio.gather(*self.workers)

# Utilisation
async def sample_task():
    await asyncio.sleep(0.5)
    return "Tâche terminée"

async def main():
    pool = WorkerPool(3)
    await pool.start()
    
    # Ajouter des tâches
    for i in range(10):
        await pool.queue.put(sample_task)
    
    await pool.queue.join()
    await pool.stop()

asyncio.run(main())
```

---

# Points Clés à Retenir 🎯

### Résumé de la programmation asynchrone

**1. Concepts :**
- `async def` pour les fonctions asynchrones
- `await` pour attendre les opérations
- `asyncio.run()` pour exécuter

**2. Avantages :**
- Gestion efficace des I/O
- Concurrence sans threads
- Performance améliorée

**3. Applications :**
- APIs web
- Base de données
- Téléchargements
- Traitement de données

**Prêt pour les tests !** 🚀 