---
layout: intro
routeAlias: 'fonctions'
---

# Fonctions et Modules 🔧

### Organisation et réutilisation du code

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Organisons notre code <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Définition de fonctions 📝

### Création de fonctions personnalisées

```python
# Fonction simple sans paramètres
def dire_bonjour():
    print("Bonjour !")

# Fonction avec paramètres
def saluer(nom):
    print(f"Bonjour {nom} !")

# Fonction avec valeur de retour
def additionner(a, b):
    return a + b

# Fonction avec documentation
def calculer_moyenne(nombres):
    """
    Calcule la moyenne d'une liste de nombres.
    
    Args:
        nombres (list): Liste de nombres
        
    Returns:
        float: La moyenne calculée
    """
    if not nombres:
        return 0
    return sum(nombres) / len(nombres)
```

---
layout: default
---

# Paramètres de fonctions 🎯

### Différents types de paramètres

```python
# Paramètres positionnels
def info_personne(nom, age, ville):
    print(f"Nom: {nom}, Âge: {age}, Ville: {ville}")

# Paramètres avec valeurs par défaut
def saluer(nom="Utilisateur", langue="français"):
    if langue == "français":
        print(f"Bonjour {nom} !")
    else:
        print(f"Hello {nom} !")

# Paramètres nommés
info_personne(nom="Alice", age=25, ville="Paris")
info_personne(age=30, nom="Bob", ville="Lyon")

# Mélange de paramètres
def creer_utilisateur(nom, age, email=None, actif=True):
    utilisateur = {"nom": nom, "age": age}
    if email:
        utilisateur["email"] = email
    utilisateur["actif"] = actif
    return utilisateur
```

---
layout: default
---

# Arguments variables 📦

### Fonctions avec nombre variable d'arguments

```python
# Arguments positionnels variables (*args)
def additionner_tous(*nombres):
    """Additionne tous les nombres passés en arguments."""
    return sum(nombres)

print(additionner_tous(1, 2, 3, 4, 5))  # 15
print(additionner_tous(10, 20))          # 30

# Arguments nommés variables (**kwargs)
def creer_profil(**informations):
    """Crée un profil avec des informations variables."""
    profil = {}
    for cle, valeur in informations.items():
        profil[cle] = valeur
    return profil

utilisateur = creer_profil(
    nom="Alice",
    age=25,
    ville="Paris",
    profession="Développeur"
)

# Mélange d'arguments
def fonction_complete(obligatoire, *args, optionnel="défaut", **kwargs):
    print(f"Obligatoire: {obligatoire}")
    print(f"Args: {args}")
    print(f"Optionnel: {optionnel}")
    print(f"Kwargs: {kwargs}")
```

---
layout: default
---

# Valeurs de retour 🔄

### Retour de données depuis les fonctions

```python
# Retour simple
def carre(nombre):
    return nombre ** 2

# Retour multiple (tuple)
def diviser_et_reste(a, b):
    quotient = a // b
    reste = a % b
    return quotient, reste

# Retour conditionnel
def evaluer_note(note):
    if note >= 90:
        return "Excellent"
    elif note >= 80:
        return "Très bien"
    elif note >= 70:
        return "Bien"
    elif note >= 60:
        return "Assez bien"
    else:
        return "Insuffisant"

# Retour None (implicite)
def afficher_message(message):
    print(message)
    # Retourne None automatiquement

# Utilisation des retours
resultat = carre(5)  # 25
quotient, reste = diviser_et_reste(17, 5)  # 3, 2
evaluation = evaluer_note(85)  # "Très bien"
```

---
layout: default
---

# Portée des variables 📦

### Visibilité des variables dans les fonctions

```python
# Variable globale
variable_globale = "Je suis globale"

def fonction_test():
    # Variable locale
    variable_locale = "Je suis locale"
    print(f"Locale: {variable_locale}")
    print(f"Globale: {variable_globale}")

def fonction_modifier_globale():
    global variable_globale  # Déclaration globale
    variable_globale = "Modifiée par la fonction"
    print(f"Globale modifiée: {variable_globale}")

def fonction_avec_nonlocal():
    variable_enclos = "Variable d'enclos"
    
    def fonction_interne():
        nonlocal variable_enclos  # Accès à la variable d'enclos
        variable_enclos = "Modifiée par la fonction interne"
        print(f"Variable d'enclos: {variable_enclos}")
    
    fonction_interne()
    print(f"Après modification: {variable_enclos}")
```

---
layout: default
---

# Fonctions lambda ⚡

### Fonctions anonymes en une ligne

```python
# Fonction lambda simple
carre = lambda x: x ** 2
print(carre(5))  # 25

# Lambda avec plusieurs paramètres
additionner = lambda a, b: a + b
print(additionner(3, 4))  # 7

# Lambda avec condition
est_pair = lambda x: x % 2 == 0
print(est_pair(4))  # True
print(est_pair(7))  # False

# Utilisation avec map()
nombres = [1, 2, 3, 4, 5]
carres = list(map(lambda x: x**2, nombres))
print(carres)  # [1, 4, 9, 16, 25]

# Utilisation avec filter()
pairs = list(filter(lambda x: x % 2 == 0, nombres))
print(pairs)  # [2, 4]

# Utilisation avec sorted()
personnes = [{"nom": "Alice", "age": 25}, {"nom": "Bob", "age": 30}]
triees = sorted(personnes, key=lambda p: p["age"])
print(triees)
```

---
layout: default
---

# Décorateurs 🎭

### Modification de comportement des fonctions

```python
# Décorateur simple
def timer(func):
    """Décorateur pour mesurer le temps d'exécution."""
    import time
    
    def wrapper(*args, **kwargs):
        debut = time.time()
        resultat = func(*args, **kwargs)
        fin = time.time()
        print(f"Fonction {func.__name__} exécutée en {fin - debut:.4f} secondes")
        return resultat
    
    return wrapper

# Utilisation du décorateur
@timer
def fonction_lente():
    import time
    time.sleep(1)
    return "Terminé"

# Décorateur avec paramètres
def retry(max_tentatives=3):
    def decorateur(func):
        def wrapper(*args, **kwargs):
            for tentative in range(max_tentatives):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if tentative == max_tentatives - 1:
                        raise e
                    print(f"Tentative {tentative + 1} échouée, nouvelle tentative...")
            return None
        return wrapper
    return decorateur

@retry(max_tentatives=3)
def fonction_instable():
    import random
    if random.random() < 0.7:
        raise ValueError("Erreur aléatoire")
    return "Succès"
```

---
layout: default
---

# Modules et imports 📚

### Organisation du code en modules

```python
# Import simple
import math
print(math.pi)  # 3.141592653589793
print(math.sqrt(16))  # 4.0

# Import avec alias
import numpy as np
array = np.array([1, 2, 3, 4, 5])

# Import spécifique
from math import pi, sqrt
print(pi)  # 3.141592653589793
print(sqrt(25))  # 5.0

# Import avec alias pour fonction
from math import sqrt as racine_carree
print(racine_carree(9))  # 3.0

# Import de tout (déconseillé)
from math import *
print(cos(0))  # 1.0

# Import conditionnel
try:
    import pandas as pd
    print("Pandas disponible")
except ImportError:
    print("Pandas non installé")
```

---
layout: default
---

# Création de modules 📁

### Organisation de votre code

**Structure de projet :**
```
mon_projet/
├── main.py
├── utils/
│   ├── __init__.py
│   ├── calculs.py
│   └── validation.py
└── tests/
    ├── __init__.py
    └── test_calculs.py
```

**utils/calculs.py :**
```python
"""Module pour les calculs mathématiques."""

def additionner(a, b):
    """Additionne deux nombres."""
    return a + b

def multiplier(a, b):
    """Multiplie deux nombres."""
    return a * b

def calculer_moyenne(nombres):
    """Calcule la moyenne d'une liste."""
    if not nombres:
        return 0
    return sum(nombres) / len(nombres)

# Variables du module
PI = 3.14159
VERSION = "1.0.0"
```

---
layout: default
---

# Utilisation de modules 🔧

### Import et utilisation de vos modules

**main.py :**
```python
# Import du module complet
import utils.calculs as calc

resultat = calc.additionner(5, 3)
print(resultat)  # 8

moyenne = calc.calculer_moyenne([1, 2, 3, 4, 5])
print(moyenne)  # 3.0

print(calc.PI)  # 3.14159

# Import spécifique
from utils.calculs import additionner, PI

resultat = additionner(10, 20)
print(resultat)  # 30
print(PI)  # 3.14159

# Import avec alias
from utils.calculs import calculer_moyenne as moyenne

nombres = [10, 20, 30, 40, 50]
resultat = moyenne(nombres)
print(resultat)  # 30.0
```

---
layout: default
---

# Package __init__.py 📦

### Configuration des packages

**utils/__init__.py :**
```python
"""
Package utils pour les utilitaires.
"""

# Import des fonctions principales
from .calculs import additionner, multiplier, calculer_moyenne
from .validation import valider_nombre, valider_email

# Version du package
__version__ = "1.0.0"

# Fonctions disponibles
__all__ = [
    'additionner',
    'multiplier', 
    'calculer_moyenne',
    'valider_nombre',
    'valider_email'
]

# Configuration du package
def get_info():
    """Retourne les informations du package."""
    return {
        "nom": "utils",
        "version": __version__,
        "description": "Utilitaires pour les calculs et validations"
    }
```

---
layout: default
---

# Fonctions built-in utiles 🛠️

### Fonctions intégrées de Python

```python
# Fonctions mathématiques
print(abs(-5))        # 5
print(round(3.14159, 2))  # 3.14
print(max(1, 2, 3, 4, 5))  # 5
print(min(1, 2, 3, 4, 5))  # 1
print(sum([1, 2, 3, 4, 5]))  # 15

# Fonctions de type
print(len("Hello"))    # 5
print(type(42))       # <class 'int'>
print(isinstance(42, int))  # True

# Fonctions de conversion
print(str(42))        # "42"
print(int("42"))      # 42
print(float("3.14"))  # 3.14
print(list("Hello"))  # ['H', 'e', 'l', 'l', 'o']

# Fonctions de séquence
nombres = [1, 2, 3, 4, 5]
print(all(x > 0 for x in nombres))  # True
print(any(x > 3 for x in nombres))  # True
print(list(enumerate(nombres)))  # [(0, 1), (1, 2), ...]
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Calculatrice modulaire

Créez un module `calculatrice.py` avec :
1. Fonctions pour les opérations de base (+, -, *, /)
2. Fonctions pour les opérations avancées (puissance, racine)
3. Fonction de validation des entrées
4. Tests unitaires

**Structure :**
```python
# calculatrice.py
def additionner(a, b):
    pass

def soustraire(a, b):
    pass

# etc.
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Gestionnaire de contacts

Créez un système de gestion de contacts avec :
1. Module `contact.py` avec classe Contact
2. Module `gestionnaire.py` pour les opérations CRUD
3. Module `validation.py` pour valider les données
4. Interface en ligne de commande

**Fonctionnalités :**
- Ajouter/supprimer/modifier des contacts
- Rechercher par nom ou téléphone
- Sauvegarder/charger depuis un fichier
- Validation des données (email, téléphone)

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Décorateurs personnalisés

Créez des décorateurs pour :
1. **@logger** : Enregistrer les appels de fonctions
2. **@cache** : Mettre en cache les résultats
3. **@validate** : Valider les paramètres d'entrée
4. **@retry** : Réessayer en cas d'échec

**Exemples d'utilisation :**
```python
@logger
@cache
def fibonacci(n):
    pass

@validate
def creer_utilisateur(nom, age, email):
    pass
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Calculatrice modulaire

```python
# calculatrice.py
"""Module de calculatrice avec opérations mathématiques."""

import math

def additionner(a, b):
    """Additionne deux nombres."""
    return a + b

def soustraire(a, b):
    """Soustrait deux nombres."""
    return a - b

def multiplier(a, b):
    """Multiplie deux nombres."""
    return a * b

def diviser(a, b):
    """Divise deux nombres."""
    if b == 0:
        raise ValueError("Division par zéro impossible")
    return a / b

def puissance(base, exposant):
    """Élève un nombre à une puissance."""
    return base ** exposant

def racine_carree(nombre):
    """Calcule la racine carrée d'un nombre."""
    if nombre < 0:
        raise ValueError("Racine carrée d'un nombre négatif impossible")
    return math.sqrt(nombre)

def valider_nombre(valeur):
    """Valide qu'une valeur est un nombre."""
    try:
        float(valeur)
        return True
    except (ValueError, TypeError):
        return False

# Tests unitaires
if __name__ == "__main__":
    # Test des opérations
    assert additionner(5, 3) == 8
    assert soustraire(10, 4) == 6
    assert multiplier(6, 7) == 42
    assert diviser(15, 3) == 5.0
    assert puissance(2, 3) == 8
    assert racine_carree(16) == 4.0
    
    print("Tous les tests sont passés !")
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Gestionnaire de contacts

```python
# contact.py
"""Module pour la gestion des contacts."""

import re
from datetime import datetime

class Contact:
    def __init__(self, nom, telephone, email=None, adresse=None):
        self.nom = nom
        self.telephone = telephone
        self.email = email
        self.adresse = adresse
        self.date_creation = datetime.now()
    
    def __str__(self):
        return f"Contact: {self.nom} - {self.telephone}"
    
    def to_dict(self):
        """Convertit le contact en dictionnaire."""
        return {
            'nom': self.nom,
            'telephone': self.telephone,
            'email': self.email,
            'adresse': self.adresse,
            'date_creation': self.date_creation.isoformat()
        }

# validation.py
"""Module de validation des données."""

import re

def valider_telephone(telephone):
    """Valide un numéro de téléphone français."""
    pattern = r'^(?:(?:\+|00)33|0)\s*[1-9](?:[\s.-]*\d{2}){4}$'
    return bool(re.match(pattern, telephone))

def valider_email(email):
    """Valide une adresse email."""
    if not email:
        return True  # Email optionnel
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(pattern, email))

def valider_nom(nom):
    """Valide un nom."""
    return len(nom.strip()) >= 2
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Décorateurs personnalisés

```python
# decorators.py
"""Module de décorateurs personnalisés."""

import functools
import time
import logging

# Configuration du logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def logger(func):
    """Décorateur pour enregistrer les appels de fonctions."""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        logger.info(f"Appel de {func.__name__} avec args={args}, kwargs={kwargs}")
        resultat = func(*args, **kwargs)
        logger.info(f"{func.__name__} a retourné {resultat}")
        return resultat
    return wrapper

def cache(func):
    """Décorateur pour mettre en cache les résultats."""
    cache_data = {}
    
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        # Créer une clé pour le cache
        cle = str(args) + str(sorted(kwargs.items()))
        
        if cle not in cache_data:
            cache_data[cle] = func(*args, **kwargs)
        
        return cache_data[cle]
    return wrapper

def validate(*validators):
    """Décorateur pour valider les paramètres."""
    def decorateur(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            # Validation des arguments
            for i, (arg, validator) in enumerate(zip(args, validators)):
                if not validator(arg):
                    raise ValueError(f"Argument {i} invalide: {arg}")
            
            return func(*args, **kwargs)
        return wrapper
    return decorateur

def retry(max_tentatives=3, delay=1):
    """Décorateur pour réessayer en cas d'échec."""
    def decorateur(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for tentative in range(max_tentatives):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if tentative == max_tentatives - 1:
                        raise e
                    logger.warning(f"Tentative {tentative + 1} échouée: {e}")
                    time.sleep(delay)
            return None
        return wrapper
    return decorateur

# Exemples d'utilisation
@logger
@cache
def fibonacci(n):
    """Calcule le n-ième nombre de Fibonacci."""
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

@validate(lambda x: isinstance(x, str), lambda x: isinstance(x, int))
def creer_utilisateur(nom, age):
    """Crée un utilisateur avec validation."""
    return {"nom": nom, "age": age}

@retry(max_tentatives=3)
def fonction_instable():
    """Fonction qui peut échouer."""
    import random
    if random.random() < 0.7:
        raise ValueError("Erreur aléatoire")
    return "Succès"
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Collections** (listes, dictionnaires, sets)
2. **Programmation orientée objet**
3. **Gestion des erreurs**
4. **Manipulation de fichiers**
5. **Projet final intégrateur**

**Préparation :**
- Maîtrisez les fonctions et modules
- Pratiquez les décorateurs
- Organisez votre code en modules

---
src: './pages/07-collections.md'
--- 