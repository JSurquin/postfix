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
layout: default
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
layout: default
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
```

---

# Utilisation d'enumerate

```python
# ❌ Moins efficace
for i in range(len(items)):
    print(f"{i}: {items[i]}")

# ✅ Plus efficace
for i, item in enumerate(items):
    print(f"{i}: {item}")
```

---

# List Comprehensions Optimisées

```python
# ❌ Moins efficace
resultat = []
for i in range(10):
    if i % 2 == 0:
        resultat.append(i * 2)

# ✅ Plus efficace
resultat = [i * 2 for i in range(10) if i % 2 == 0]
```

---
layout: default
---

# 3. Fonctions - Concepts Avancés

### Fonctions de niveau expert

**Concepts avancés :**
- Fonctions d'ordre supérieur
- Closures et scope
- Décorateurs de base
- Fonctions génératrices

---

# Fonctions d'Ordre Supérieur

```python
def appliquer_fonction(fonction, liste):
    """Applique une fonction à chaque élément d'une liste"""
    return [fonction(x) for x in liste]

def carre(x):
    return x ** 2

def cube(x):
    return x ** 3

# Utilisation
nombres = [1, 2, 3, 4, 5]
carres = appliquer_fonction(carre, nombres)
cubes = appliquer_fonction(cube, nombres)
```

---

# Closures

```python
def creer_multiplicateur(facteur):
    """Crée une fonction qui multiplie par le facteur"""
    def multiplier(x):
        return x * facteur
    return multiplier

# Utilisation
double = creer_multiplicateur(2)
triple = creer_multiplicateur(3)

print(double(5))  # 10
print(triple(5))  # 15
```

---
layout: default
---

# 4. Collections - Utilisation Avancée

### Techniques avancées avec les collections

**Optimisations :**
- Choix de la bonne collection
- Méthodes spécialisées
- Performance des opérations
- Patterns d'utilisation

---

# Choix de la Bonne Collection

```python
# List : ordre important, doublons autorisés
liste = [1, 2, 2, 3, 4, 4, 5]

# Set : ordre non important, doublons interdits
ensemble = {1, 2, 3, 4, 5}

# Dict : paires clé-valeur
dictionnaire = {"a": 1, "b": 2, "c": 3}

# Tuple : immuable, ordre important
tuple_immutable = (1, 2, 3)
```

---

# Méthodes Spécialisées

```python
from collections import defaultdict, Counter, deque

# defaultdict : dictionnaire avec valeur par défaut
dd = defaultdict(list)
dd["a"].append(1)  # Pas besoin de vérifier si la clé existe

# Counter : compteur d'éléments
compteur = Counter("hello")
print(compteur)  # Counter({'l': 2, 'h': 1, 'e': 1, 'o': 1})

# deque : file double-ended
file = deque([1, 2, 3])
file.append(4)      # Ajoute à droite
file.appendleft(0)  # Ajoute à gauche
```

---
layout: default
---

# 5. Gestion d'Erreurs - Patterns Avancés

### Gestion d'erreurs sophistiquée

**Patterns avancés :**
- Gestion hiérarchique des exceptions
- Context managers personnalisés
- Exceptions personnalisées
- Logging avancé

---

# Gestion Hiérarchique des Exceptions

```python
def traiter_donnees(donnees):
    """Traite des données avec gestion d'erreurs hiérarchique"""
    try:
        # Conversion en nombres
        nombres = [float(x) for x in donnees]
        
        # Calculs
        moyenne = sum(nombres) / len(nombres)
        return moyenne
        
    except ValueError as e:
        print(f"Erreur de conversion : {e}")
        return None
        
    except ZeroDivisionError:
        print("Liste vide")
        return None
        
    except Exception as e:
        print(f"Erreur inattendue : {e}")
        return None
```

---

# Context Manager Personnalisé

```python
class GestionnaireRessource:
    def __init__(self, nom):
        self.nom = nom
        self.ressource = None
    
    def __enter__(self):
        print(f"Ouverture de {self.nom}")
        self.ressource = f"ressource_{self.nom}"
        return self.ressource
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        print(f"Fermeture de {self.nom}")
        if exc_type:
            print(f"Erreur lors de l'utilisation : {exc_val}")
        return False  # Propager l'exception

# Utilisation
with GestionnaireRessource("fichier") as ressource:
    print(f"Utilisation de {ressource}")
```

---
layout: default
---

# 6. Programmation Orientée Objet - Avancé

### Concepts POO avancés

**Concepts avancés :**
- Méthodes spéciales
- Propriétés et descripteurs
- Héritage multiple
- Classes abstraites

---

# Méthodes Spéciales Avancées

```python
class Vecteur:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __add__(self, autre):
        return Vecteur(self.x + autre.x, self.y + autre.y)
    
    def __sub__(self, autre):
        return Vecteur(self.x - autre.x, self.y - autre.y)
    
    def __mul__(self, scalaire):
        return Vecteur(self.x * scalaire, self.y * scalaire)
    
    def __eq__(self, autre):
        return self.x == autre.x and self.y == autre.y
    
    def __str__(self):
        return f"Vecteur({self.x}, {self.y})"
```

---

# Propriétés et Descripteurs

```python
class Rectangle:
    def __init__(self, largeur, hauteur):
        self._largeur = largeur
        self._hauteur = hauteur
    
    @property
    def largeur(self):
        return self._largeur
    
    @largeur.setter
    def largeur(self, valeur):
        if valeur <= 0:
            raise ValueError("La largeur doit être positive")
        self._largeur = valeur
    
    @property
    def aire(self):
        return self._largeur * self._hauteur
```

---
layout: default
---

# 7. Modules et Packages - Organisation

### Organisation avancée du code

**Concepts avancés :**
- Structure de packages
- Imports relatifs vs absolus
- `__init__.py` avancé
- Namespace packages

---

# Structure de Package

```python
# mypackage/
# ├── __init__.py
# ├── core/
# │   ├── __init__.py
# │   ├── utils.py
# │   └── validators.py
# ├── models/
# │   ├── __init__.py
# │   └── user.py
# └── tests/
#     ├── __init__.py
#     └── test_core.py

# __init__.py principal
from .core.utils import helper_function
from .models.user import User

__version__ = "1.0.0"
__all__ = ["helper_function", "User"]
```

---

# Imports Relatifs vs Absolus

```python
# Import absolu (recommandé)
from mypackage.core.utils import helper_function
from mypackage.models.user import User

# Import relatif (dans le package)
from .core.utils import helper_function
from ..models.user import User

# Import avec alias
import mypackage.core.utils as utils
from mypackage.models import user as user_model
```

---
layout: default
---

# 8. Manipulation de Fichiers - Avancé

### Techniques avancées de manipulation de fichiers

**Concepts avancés :**
- Gestion de gros fichiers
- Formats spécialisés
- Compression et chiffrement
- Monitoring de fichiers

---

# Lecture de Gros Fichiers

```python
def traiter_gros_fichier(chemin, taille_buffer=8192):
    """Traite un gros fichier par chunks"""
    with open(chemin, 'r', encoding='utf-8') as fichier:
        while True:
            chunk = fichier.read(taille_buffer)
            if not chunk:
                break
            yield chunk

# Utilisation
for chunk in traiter_gros_fichier("gros_fichier.txt"):
    # Traitement du chunk
    print(f"Traité {len(chunk)} caractères")
```

---

# Formats Spécialisés

```python
import json
import csv
import pickle

# JSON avec gestion d'erreurs
def lire_json_securise(chemin):
    try:
        with open(chemin, 'r', encoding='utf-8') as f:
            return json.load(f)
    except json.JSONDecodeError as e:
        print(f"Erreur JSON : {e}")
        return None
    except FileNotFoundError:
        print("Fichier non trouvé")
        return None

# CSV avec validation
def lire_csv_valide(chemin):
    with open(chemin, 'r', encoding='utf-8') as f:
        reader = csv.DictReader(f)
        for ligne in reader:
            # Validation de chaque ligne
            if valider_ligne(ligne):
                yield ligne
```

---
layout: default
---

# 9. Performance et Optimisation - Base

### Premiers pas en optimisation

**Concepts de base :**
- Profiling simple
- Optimisation des boucles
- Choix de structures de données
- Éviter les pièges courants

---

# Profiling Simple

```python
import time
import cProfile
import pstats

def fonction_lente():
    """Fonction à profiler"""
    resultat = 0
    for i in range(1000000):
        resultat += i
    return resultat

# Profiling avec time
debut = time.time()
resultat = fonction_lente()
fin = time.time()
print(f"Temps d'exécution : {fin - debut:.4f} secondes")

# Profiling avec cProfile
profiler = cProfile.Profile()
profiler.enable()
fonction_lente()
profiler.disable()
stats = pstats.Stats(profiler)
stats.sort_stats('cumulative')
stats.print_stats(10)
```

---

# Optimisation des Structures de Données

```python
# ❌ Moins efficace
def rechercher_inefficace(liste, valeur):
    return valeur in liste  # O(n)

# ✅ Plus efficace avec set
def rechercher_efficace(ensemble, valeur):
    return valeur in ensemble  # O(1)

# Comparaison
import time

liste_grande = list(range(100000))
set_grand = set(range(100000))
valeur_recherche = 99999

# Test avec liste
debut = time.time()
rechercher_inefficace(liste_grande, valeur_recherche)
fin = time.time()
temps_liste = fin - debut

# Test avec set
debut = time.time()
rechercher_efficace(set_grand, valeur_recherche)
fin = time.time()
temps_set = fin - debut

print(f"Liste : {temps_liste:.6f}s")
print(f"Set : {temps_set:.6f}s")
```

---
layout: default
---

# 10. Bonnes Pratiques - Niveau Avancé

### Écrire du code professionnel

**Bonnes pratiques :**
- Style de code (PEP 8)
- Documentation
- Tests unitaires
- Gestion des erreurs

---

# Style de Code (PEP 8)

```python
# ✅ Bon style
def calculer_moyenne(nombres):
    """Calcule la moyenne d'une liste de nombres.
    
    Args:
        nombres (list): Liste de nombres
        
    Returns:
        float: Moyenne des nombres
        
    Raises:
        ValueError: Si la liste est vide
    """
    if not nombres:
        raise ValueError("La liste ne peut pas être vide")
    
    return sum(nombres) / len(nombres)

# ❌ Mauvais style
def calc_moy(n):
    if len(n)==0:raise ValueError("vide")
    return sum(n)/len(n)
```

---

# Tests Unitaires de Base

```python
import unittest

class TestCalculerMoyenne(unittest.TestCase):
    def test_moyenne_normale(self):
        """Test avec des nombres normaux"""
        resultat = calculer_moyenne([1, 2, 3, 4, 5])
        self.assertEqual(resultat, 3.0)
    
    def test_moyenne_zero(self):
        """Test avec des zéros"""
        resultat = calculer_moyenne([0, 0, 0])
        self.assertEqual(resultat, 0.0)
    
    def test_liste_vide(self):
        """Test avec une liste vide"""
        with self.assertRaises(ValueError):
            calculer_moyenne([])

if __name__ == "__main__":
    unittest.main()
```

---
layout: default
---

# Points Clés à Retenir 🎯

### Résumé des fondamentaux avancés

**1. Types et références :**
- Compréhension immutables vs mutables
- Gestion des copies et références
- Optimisation des structures de données

**2. Structures de contrôle :**
- Optimisation des boucles
- Utilisation d'enumerate
- List comprehensions efficaces

**3. Fonctions avancées :**
- Fonctions d'ordre supérieur
- Closures et scope
- Patterns fonctionnels

**4. Collections spécialisées :**
- Choix de la bonne collection
- Méthodes spécialisées (defaultdict, Counter)
- Performance des opérations

**5. Gestion d'erreurs :**
- Patterns hiérarchiques
- Context managers personnalisés
- Logging approprié

**6. POO avancée :**
- Méthodes spéciales
- Propriétés et descripteurs
- Héritage multiple

**7. Organisation :**
- Structure de packages
- Imports relatifs vs absolus
- Bonnes pratiques

**8. Performance :**
- Profiling de base
- Optimisation des structures
- Éviter les pièges courants

**Prêt pour les décorateurs et générateurs !** 🚀 