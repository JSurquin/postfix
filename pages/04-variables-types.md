---
layout: intro
routeAlias: 'variables-types'
---

# Variables et Types de Données 📊

### Maîtrise des types fondamentaux de Python

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Explorons les types de données <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Types de données fondamentaux 🏗️

### Les types de base en Python

**Types numériques :**
- `int` : Entiers (positifs, négatifs, zéro)
- `float` : Nombres à virgule flottante
- `complex` : Nombres complexes

**Types textuels :**
- `str` : Chaînes de caractères

**Types logiques :**
- `bool` : Booléens (True/False)

**Types spéciaux :**
- `None` : Valeur nulle/absence de valeur

---
layout: default
---

# Entiers (int) 🔢

### Nombres entiers sans limite de taille

```python
# Entiers positifs
>>> a = 42
>>> b = 1000000
>>> c = 999999999999999999999999999999

# Entiers négatifs
>>> d = -17
>>> e = -1000

# Zéro
>>> f = 0

# Opérations sur les entiers
>>> resultat = a + b
>>> produit = a * d
>>> quotient = b // a  # Division entière
>>> reste = b % a      # Modulo

# Conversion
>>> float_vers_int = int(3.14)  # 3
>>> str_vers_int = int("42")    # 42
```

---
layout: default
---

# Nombres à virgule flottante (float) 📈

### Nombres décimaux avec précision limitée

```python
# Nombres décimaux
>>> pi = 3.14159
>>> e = 2.71828
>>> temperature = -5.5

# Notation scientifique
>>> grand_nombre = 1.23e6    # 1.23 × 10^6
>>> petit_nombre = 1.23e-6   # 1.23 × 10^-6

# Opérations
>>> somme = 3.14 + 2.86
>>> produit = 3.14 * 2.0
>>> division = 10.0 / 3.0

# Précision limitée
>>> 0.1 + 0.2
0.30000000000000004

# Conversion
>>> int_vers_float = float(42)     # 42.0
>>> str_vers_float = float("3.14") # 3.14
```

---
layout: default
---

# Chaînes de caractères (str) 📝

### Manipulation avancée du texte

```python
# Création de chaînes
>>> simple = "Hello"
>>> double = 'World'
>>> triple = """Multi-lignes
... chaîne de caractères"""

# Échappement
>>> avec_guillemets = "Il a dit \"Bonjour\""
>>> avec_apostrophe = 'C\'est un exemple'
>>> raw_string = r"C:\Users\Documents"  # Raw string

# Formatage
>>> nom = "Alice"
>>> age = 25
>>> message = f"Bonjour {nom}, vous avez {age} ans"
>>> message_old = "Bonjour {}, vous avez {} ans".format(nom, age)
```

---
layout: default
---

# Méthodes sur les chaînes 🔧

### Fonctions intégrées pour manipuler le texte

```python
texte = "  Hello World  "

# Suppression d'espaces
>>> texte.strip()      # "Hello World"
>>> texte.lstrip()     # "Hello World  "
>>> texte.rstrip()     # "  Hello World"

# Changement de casse
>>> texte.upper()      # "  HELLO WORLD  "
>>> texte.lower()      # "  hello world  "
>>> texte.title()      # "  Hello World  "
>>> texte.capitalize() # "  hello world  "

# Recherche et remplacement
>>> texte.find("World")    # 8
>>> texte.replace("World", "Python")
>>> texte.count("l")       # 3

# Vérification
>>> texte.startswith("  ") # True
>>> texte.endswith("  ")   # True
>>> texte.isalpha()        # False (contient des espaces)
```

---
layout: default
---

# Booléens (bool) ✅

### Valeurs logiques True/False

```python
# Valeurs booléennes
>>> vrai = True
>>> faux = False

# Conversion depuis d'autres types
>>> bool(0)        # False
>>> bool(1)        # True
>>> bool(42)       # True
>>> bool("")       # False
>>> bool("Hello")  # True
>>> bool([])       # False
>>> bool([1, 2])   # True

# Opérateurs logiques
>>> a = True
>>> b = False
>>> a and b        # False
>>> a or b         # True
>>> not a          # False
>>> not b          # True
```

---
layout: default
---

# Valeur None 🚫

### Représentation de l'absence de valeur

```python
# Valeur None
>>> rien = None
>>> print(rien)    # None

# Utilisation courante
def fonction_sans_retour():
    print("Je ne retourne rien")
    # Retourne None implicitement

def fonction_optionnelle(parametre=None):
    if parametre is None:
        parametre = "valeur par défaut"
    return parametre

# Comparaison avec None
>>> x = None
>>> x is None       # True
>>> x == None       # True (mais moins recommandé)
>>> x is not None   # False
```

---
layout: default
---

# Conversion de types 🔄

### Transformation entre types de données

```python
# Conversion explicite
>>> int(3.14)          # 3
>>> float(42)          # 42.0
>>> str(123)           # "123"
>>> bool(1)            # True
>>> bool(0)            # False

# Conversion de chaînes
>>> int("42")          # 42
>>> float("3.14")      # 3.14
>>> bool("True")       # True
>>> bool("False")      # True (chaîne non vide)
>>> bool("")           # False

# Conversion avec base
>>> int("1010", 2)     # 10 (binaire)
>>> int("FF", 16)      # 255 (hexadécimal)
>>> hex(255)           # "0xff"
>>> bin(10)            # "0b1010"
```

---
layout: default
---

# Vérification de types 🔍

### Fonction type() et isinstance()

```python
# Fonction type()
>>> x = 42
>>> type(x)                    # <class 'int'>
>>> type("Hello")              # <class 'str'>
>>> type(3.14)                 # <class 'float'>
>>> type(True)                 # <class 'bool'>

# Fonction isinstance()
>>> isinstance(42, int)        # True
>>> isinstance(42, str)        # False
>>> isinstance("Hello", str)   # True
>>> isinstance(3.14, (int, float))  # True

# Utilisation pratique
def traiter_nombre(valeur):
    if isinstance(valeur, (int, float)):
        return valeur * 2
    else:
        return "Ce n'est pas un nombre"
```

---
layout: default
---

# Variables et portée 📦

### Règles de nommage et portée des variables

```python
# Règles de nommage
nom_valide = "Alice"           # ✅ Valide
nom_valide_2 = "Bob"           # ✅ Valide
_nom_prive = "Charlie"         # ✅ Convention privée
NOM_CONSTANTE = "David"        # ✅ Convention constante

# 2nom = "Eve"                # ❌ Ne commence pas par un chiffre
# nom-valide = "Frank"        # ❌ Contient un tiret
# class = "Grace"             # ❌ Mot-clé réservé

# Portée des variables
variable_globale = "Je suis globale"

def ma_fonction():
    variable_locale = "Je suis locale"
    print(variable_globale)    # Accès à la globale
    print(variable_locale)     # Accès à la locale

# print(variable_locale)      # ❌ Erreur : hors portée
```

---
layout: default
---

# Déstructuration et assignation multiple 🔀

### Assignation de plusieurs variables

```python
# Assignation multiple
>>> a, b, c = 1, 2, 3
>>> print(a, b, c)  # 1 2 3

# Échange de valeurs
>>> x, y = 10, 20
>>> x, y = y, x
>>> print(x, y)  # 20 10

# Déstructuration de listes
>>> nombres = [1, 2, 3]
>>> premier, deuxieme, troisieme = nombres
>>> print(premier, deuxieme, troisieme)  # 1 2 3

# Déstructuration partielle
>>> liste = [1, 2, 3, 4, 5]
>>> premier, *reste = liste
>>> print(premier)  # 1
>>> print(reste)    # [2, 3, 4, 5]
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Convertisseur de types

Créez un programme qui :
1. Demande une valeur à l'utilisateur
2. Devine son type (int, float, str, bool)
3. Propose des conversions possibles
4. Affiche les résultats

**Exemple :**
```
Entrez une valeur : 42
Type détecté : int
Conversions possibles :
- float : 42.0
- str : "42"
- bool : True
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Validateur de données

Créez un programme qui :
1. Demande un nom (chaîne non vide)
2. Demande un âge (entier positif)
3. Demande une taille (float positif)
4. Valide chaque entrée
5. Affiche un résumé formaté

**Validation :**
- Nom : non vide, alphabétique
- Âge : entre 0 et 150
- Taille : entre 0.5 et 3.0 mètres

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Calculateur de statistiques

Créez un programme qui :
1. Demande plusieurs nombres
2. Calcule la moyenne, min, max
3. Affiche les statistiques avec formatage
4. Gère les erreurs de saisie

**Fonctionnalités :**
- Saisie de nombres jusqu'à "fin"
- Calculs statistiques
- Affichage formaté (2 décimales)
- Gestion des cas d'erreur

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Convertisseur de types

```python
def detecter_type(valeur):
    """Détecte le type d'une valeur et propose des conversions."""
    try:
        # Essayer de convertir en int
        int_val = int(valeur)
        if str(int_val) == valeur:
            return "int", int_val
    except ValueError:
        pass
    
    try:
        # Essayer de convertir en float
        float_val = float(valeur)
        return "float", float_val
    except ValueError:
        pass
    
    # Vérifier si c'est un booléen
    if valeur.lower() in ['true', 'false']:
        return "bool", valeur.lower() == 'true'
    
    # Sinon c'est une chaîne
    return "str", valeur

def convertisseur_types():
    valeur = input("Entrez une valeur : ")
    type_detecte, valeur_convertie = detecter_type(valeur)
    
    print(f"Type détecté : {type_detecte}")
    print(f"Valeur : {valeur_convertie}")
    
    # Proposer des conversions
    if type_detecte == "int":
        print(f"Conversions possibles :")
        print(f"- float : {float(valeur_convertie)}")
        print(f"- str : \"{str(valeur_convertie)}\"")
        print(f"- bool : {bool(valeur_convertie)}")
    elif type_detecte == "float":
        print(f"Conversions possibles :")
        print(f"- int : {int(valeur_convertie)}")
        print(f"- str : \"{str(valeur_convertie)}\"")
        print(f"- bool : {bool(valeur_convertie)}")

if __name__ == "__main__":
    convertisseur_types()
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Validateur de données

```python
def valider_nom(nom):
    """Valide un nom (non vide, alphabétique)."""
    if not nom or not nom.replace(" ", "").isalpha():
        return False, "Le nom doit être non vide et alphabétique"
    return True, nom

def valider_age(age_str):
    """Valide un âge (entier entre 0 et 150)."""
    try:
        age = int(age_str)
        if 0 <= age <= 150:
            return True, age
        else:
            return False, "L'âge doit être entre 0 et 150"
    except ValueError:
        return False, "L'âge doit être un nombre entier"

def valider_taille(taille_str):
    """Valide une taille (float entre 0.5 et 3.0)."""
    try:
        taille = float(taille_str)
        if 0.5 <= taille <= 3.0:
            return True, taille
        else:
            return False, "La taille doit être entre 0.5 et 3.0 mètres"
    except ValueError:
        return False, "La taille doit être un nombre"

def validateur_donnees():
    print("=== Validateur de données ===")
    
    # Validation du nom
    while True:
        nom = input("Nom : ")
        valide, resultat = valider_nom(nom)
        if valide:
            nom_valide = resultat
            break
        print(f"Erreur : {resultat}")
    
    # Validation de l'âge
    while True:
        age = input("Âge : ")
        valide, resultat = valider_age(age)
        if valide:
            age_valide = resultat
            break
        print(f"Erreur : {resultat}")
    
    # Validation de la taille
    while True:
        taille = input("Taille (en mètres) : ")
        valide, resultat = valider_taille(taille)
        if valide:
            taille_valide = resultat
            break
        print(f"Erreur : {resultat}")
    
    # Affichage du résumé
    print(f"\n=== Résumé ===")
    print(f"Nom : {nom_valide}")
    print(f"Âge : {age_valide} ans")
    print(f"Taille : {taille_valide:.2f} m")

if __name__ == "__main__":
    validateur_donnees()
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Calculateur de statistiques

```python
def calculer_statistiques(nombres):
    """Calcule les statistiques d'une liste de nombres."""
    if not nombres:
        return None
    
    return {
        'moyenne': sum(nombres) / len(nombres),
        'minimum': min(nombres),
        'maximum': max(nombres),
        'nombre': len(nombres)
    }

def calculateur_statistiques():
    print("=== Calculateur de statistiques ===")
    print("Entrez des nombres (tapez 'fin' pour terminer)")
    
    nombres = []
    
    while True:
        entree = input("Nombre : ")
        
        if entree.lower() == 'fin':
            break
        
        try:
            nombre = float(entree)
            nombres.append(nombre)
        except ValueError:
            print("Erreur : Veuillez entrer un nombre valide")
    
    if not nombres:
        print("Aucun nombre saisi.")
        return
    
    stats = calculer_statistiques(nombres)
    
    print(f"\n=== Statistiques ===")
    print(f"Nombre de valeurs : {stats['nombre']}")
    print(f"Moyenne : {stats['moyenne']:.2f}")
    print(f"Minimum : {stats['minimum']:.2f}")
    print(f"Maximum : {stats['maximum']:.2f}")

if __name__ == "__main__":
    calculateur_statistiques()
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Structures de contrôle** (conditions, boucles)
2. **Fonctions et modules**
3. **Collections** (listes, dictionnaires)
4. **Programmation orientée objet**
5. **Gestion des erreurs**

**Préparation :**
- Maîtrisez les types de données
- Pratiquez les conversions
- Testez les exercices proposés

---
src: './pages/05-structures-controle.md'
--- 