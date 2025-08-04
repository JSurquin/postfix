---
layout: intro
routeAlias: 'gestion-erreurs'
---

# Gestion des Erreurs ⚠️

### Exceptions et gestion d'erreurs avancées

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Maîtrisons la gestion d'erreurs <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Types d'exceptions 🚨

### Les exceptions intégrées de Python

**Exceptions courantes :**
- `ZeroDivisionError` : Division par zéro
- `ValueError` : Valeur incorrecte
- `IndexError` : Index hors limites
- `KeyError` : Clé inexistante
- `FileNotFoundError` : Fichier non trouvé

---

# Exception ZeroDivisionError

```python
# Exceptions courantes
try:
    resultat = 10 / 0
except ZeroDivisionError:
    print("Division par zéro impossible")
```

---

# Exception ValueError

```python
try:
    nombre = int("abc")
except ValueError:
    print("Impossible de convertir en nombre")
```

---

# Exception IndexError

```python
try:
    liste = [1, 2, 3]
    print(liste[10])
except IndexError:
    print("Index hors limites")
```

---

# Exception KeyError

```python
try:
    dictionnaire = {"a": 1}
    print(dictionnaire["b"])
except KeyError:
    print("Clé inexistante")
```

---

# Exception FileNotFoundError

```python
try:
    fichier = open("fichier_inexistant.txt")
except FileNotFoundError:
    print("Fichier non trouvé")
```

---
layout: default
---

# Structure try/except/finally 🔧

### Gestion complète des exceptions

**Structure de base :**
- `try` : Code qui peut générer une exception
- `except` : Gestion des exceptions
- `finally` : Code qui s'exécute toujours
- `else` : Code qui s'exécute si aucune exception

---

# Structure de Base

```python
# Structure de base
try:
    # Code qui peut générer une exception
    nombre = int(input("Entrez un nombre : "))
    resultat = 100 / nombre
    print(f"Résultat : {resultat}")
except ValueError:
    print("Erreur : Veuillez entrer un nombre valide")
except ZeroDivisionError:
    print("Erreur : Division par zéro impossible")
except Exception as e:
    print(f"Erreur inattendue : {e}")
finally:
    print("Ce code s'exécute toujours")
```

---

# Gestion de Fichier

```python
# Exemple avec gestion de fichier
try:
    fichier = open("donnees.txt", "r")
    contenu = fichier.read()
    print(contenu)
except FileNotFoundError:
    print("Fichier non trouvé")
except PermissionError:
    print("Pas de permission pour lire le fichier")
finally:
    if 'fichier' in locals():
        fichier.close()
```

---
layout: default
---

# Gestion d'erreurs avancées 🎯

### Techniques de gestion d'erreurs sophistiquées

**Techniques avancées :**
- Gestion hiérarchique des exceptions
- Exceptions personnalisées
- Context managers
- Assertions

---

# Gestion Hiérarchique

```python
# Gestion hiérarchique des exceptions
try:
    nombre = int(input("Entrez un nombre : "))
    resultat = 100 / nombre
    print(f"Résultat : {resultat}")
except (ValueError, TypeError):
    print("Erreur de type ou de valeur")
except ZeroDivisionError:
    print("Division par zéro")
except Exception as e:
    print(f"Erreur inattendue : {e}")
else:
    print("Aucune exception n'a été levée")
finally:
    print("Toujours exécuté")
```

---

# Exceptions Personnalisées

```python
# Exceptions personnalisées
class AgeInvalideError(Exception):
    """Exception levée quand l'âge est invalide"""
    pass

class Personne:
    def __init__(self, nom, age):
        if age < 0 or age > 150:
            raise AgeInvalideError(f"Âge invalide : {age}")
        self.nom = nom
        self.age = age

# Utilisation
try:
    personne = Personne("Alice", 200)
except AgeInvalideError as e:
    print(f"Erreur : {e}")
```

---

# Context Managers

```python
# Context managers avec with
try:
    with open("fichier.txt", "r") as fichier:
        contenu = fichier.read()
        print(contenu)
except FileNotFoundError:
    print("Fichier non trouvé")
# Le fichier est automatiquement fermé
```

---
layout: default
---

# Assertions 🔍

### Vérifications de débogage

**Utilisation des assertions :**
- Vérification de conditions
- Débogage et tests
- Validation de données
- Documentation du code

---

# Assertions de Base

```python
# Assertions de base
def diviser(a, b):
    assert b != 0, "Le diviseur ne peut pas être zéro"
    return a / b

# Test
try:
    resultat = diviser(10, 0)
except AssertionError as e:
    print(f"Assertion échouée : {e}")
```

---

# Assertions Avancées

```python
# Assertions avancées
def calculer_moyenne(notes):
    assert len(notes) > 0, "La liste ne peut pas être vide"
    assert all(0 <= note <= 20 for note in notes), "Les notes doivent être entre 0 et 20"
    return sum(notes) / len(notes)

# Test
notes = [15, 18, 12, 20]
moyenne = calculer_moyenne(notes)
print(f"Moyenne : {moyenne}")
```

---
layout: default
---

# Logging 📝

### Enregistrement des événements

**Avantages du logging :**
- Traçabilité des erreurs
- Niveaux de log configurables
- Rotation des fichiers de log
- Format personnalisable

---

# Configuration du Logging

```python
import logging

# Configuration de base
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    filename='app.log'
)

# Utilisation
logging.info("Application démarrée")
logging.warning("Attention : valeur suspecte")
logging.error("Erreur critique détectée")
```

---

# Logging Avancé

```python
# Logger personnalisé
logger = logging.getLogger('mon_app')
logger.setLevel(logging.DEBUG)

# Handler pour fichier
file_handler = logging.FileHandler('debug.log')
file_handler.setLevel(logging.DEBUG)

# Handler pour console
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.WARNING)

# Formatter
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
file_handler.setFormatter(formatter)
console_handler.setFormatter(formatter)

# Ajout des handlers
logger.addHandler(file_handler)
logger.addHandler(console_handler)
```

---
layout: default
---

# Débogage avec pdb 🐛

### Débogueur intégré de Python

**Fonctionnalités de pdb :**
- Points d'arrêt
- Inspection des variables
- Exécution pas à pas
- Évaluation d'expressions

---

# Utilisation de pdb

```python
import pdb

def fonction_complexe(n):
    resultat = 0
    for i in range(n):
        resultat += i
        if i == 5:
            pdb.set_trace()  # Point d'arrêt
    return resultat

# Utilisation
resultat = fonction_complexe(10)
```

---

# Commandes pdb

```python
# Commandes principales de pdb
# n (next) : Ligne suivante
# s (step) : Entrer dans la fonction
# c (continue) : Continuer l'exécution
# l (list) : Afficher le code autour
# p variable : Afficher une variable
# w (where) : Afficher la pile d'appels
# q (quit) : Quitter le débogueur
```

---
layout: default
---

# Gestion d'erreurs en production 🏭

### Bonnes pratiques pour la production

**Principes :**
- Ne jamais exposer les détails d'erreurs
- Logger toutes les erreurs
- Gérer gracieusement les échecs
- Monitoring et alertes

---

# Gestion Sécurisée

```python
# Gestion sécurisée des erreurs
def fonction_critique():
    try:
        # Code critique
        resultat = operation_risquee()
        return resultat
    except Exception as e:
        # Logger l'erreur complète
        logger.error(f"Erreur critique : {e}", exc_info=True)
        # Retourner une réponse sécurisée
        return {"erreur": "Une erreur s'est produite", "code": "ERR_001"}
```

---

# Monitoring d'Erreurs

```python
# Monitoring d'erreurs
import time
from collections import defaultdict

class ErrorMonitor:
    def __init__(self):
        self.errors = defaultdict(int)
        self.last_error_time = {}
    
    def log_error(self, error_type, error_msg):
        self.errors[error_type] += 1
        self.last_error_time[error_type] = time.time()
        
        # Alerte si trop d'erreurs
        if self.errors[error_type] > 10:
            logger.critical(f"Trop d'erreurs {error_type}: {self.errors[error_type]}")
    
    def get_stats(self):
        return dict(self.errors)
```

---
layout: default
---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez une fonction qui lit un fichier et gère toutes les exceptions possibles (FileNotFoundError, PermissionError, UnicodeDecodeError).

**Exercice 2 :** Implémentez une classe `Validateur` qui valide des données avec des exceptions personnalisées.

**Exercice 3 :** Créez un système de logging qui enregistre les erreurs dans un fichier avec rotation.

---

# Solutions des Exercices 💡

### Exercice 1 - Lecture de Fichier Sécurisée

```python
def lire_fichier_securise(chemin):
    """Lit un fichier avec gestion complète des erreurs"""
    try:
        with open(chemin, 'r', encoding='utf-8') as fichier:
            contenu = fichier.read()
            return {"succes": True, "contenu": contenu}
    except FileNotFoundError:
        return {"succes": False, "erreur": "Fichier non trouvé"}
    except PermissionError:
        return {"succes": False, "erreur": "Permission refusée"}
    except UnicodeDecodeError:
        return {"succes": False, "erreur": "Erreur d'encodage"}
    except Exception as e:
        return {"succes": False, "erreur": f"Erreur inattendue : {e}"}

# Test
resultat = lire_fichier_securise("fichier_inexistant.txt")
print(resultat)
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Classe Validateur

```python
class ValidationError(Exception):
    """Exception pour les erreurs de validation"""
    pass

class Validateur:
    def valider_email(self, email):
        if '@' not in email or '.' not in email:
            raise ValidationError(f"Email invalide : {email}")
        return True
    
    def valider_age(self, age):
        if not isinstance(age, int):
            raise ValidationError("L'âge doit être un entier")
        if age < 0 or age > 150:
            raise ValidationError(f"Âge invalide : {age}")
        return True
    
    def valider_utilisateur(self, nom, email, age):
        try:
            self.valider_email(email)
            self.valider_age(age)
            return True
        except ValidationError as e:
            print(f"Erreur de validation : {e}")
            return False

# Test
validateur = Validateur()
validateur.valider_utilisateur("Alice", "alice@example.com", 25)
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Système de Logging

```python
import logging
from logging.handlers import RotatingFileHandler

def configurer_logging():
    """Configure un système de logging avec rotation"""
    logger = logging.getLogger('mon_app')
    logger.setLevel(logging.INFO)
    
    # Handler avec rotation (5 fichiers de 1MB max)
    file_handler = RotatingFileHandler(
        'app.log', 
        maxBytes=1024*1024,  # 1MB
        backupCount=5
    )
    
    # Formatter
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )
    file_handler.setFormatter(formatter)
    
    # Ajout du handler
    logger.addHandler(file_handler)
    
    return logger

# Utilisation
logger = configurer_logging()
logger.info("Application démarrée")
logger.error("Erreur test")
```

---

# Points Clés à Retenir 🎯

### Résumé de la gestion d'erreurs

**1. Exceptions de base :**
- `try/except/finally/else`
- Gestion hiérarchique des exceptions
- Exceptions personnalisées

**2. Outils avancés :**
- Context managers (`with`)
- Assertions pour la validation
- Logging pour la traçabilité
- Débogueur `pdb`

**3. Bonnes pratiques :**
- Gestion sécurisée en production
- Monitoring des erreurs
- Logging approprié
- Messages d'erreur informatifs

**4. Applications pratiques :**
- Validation de données
- Gestion de fichiers
- Monitoring d'applications
- Débogage efficace

**Prêt pour la programmation orientée objet !** 🚀 