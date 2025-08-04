---
layout: intro
routeAlias: 'premiers-pas'
---

# Premiers pas avec Python 🚀

### Découverte de l'interpréteur interactif

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Commençons à programmer <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# L'interpréteur interactif 🐍

### Python en mode interactif

**Lancement de l'interpréteur :**
```bash
python
# ou
python3
```

**Interface de l'interpréteur :**
```python
Python 3.12.0 (main, Oct 15 2024, 10:00:00)
[GCC 9.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

**Commandes utiles :**
- `help()` : Aide interactive
- `exit()` ou `quit()` : Quitter l'interpréteur
- `Ctrl+D` (Linux/macOS) ou `Ctrl+Z` (Windows) : Quitter

---
layout: default
---

# Premiers calculs 🧮

### Opérations mathématiques de base

```python
# Addition
>>> 5 + 3
8

# Soustraction
>>> 10 - 4
6

# Multiplication
>>> 6 * 7
42

# Division
>>> 15 / 3
5.0

# Division entière
>>> 15 // 3
5

# Modulo (reste de la division)
>>> 17 % 5
2

# Puissance
>>> 2 ** 3
8
```

---
layout: default
---

# Variables et assignation 📝

### Stockage de valeurs en mémoire

```python
# Assignation simple
>>> nom = "Alice"
>>> age = 25
>>> taille = 1.75

# Affichage des variables
>>> print(nom)
Alice

>>> print(age)
25

# Utilisation dans des calculs
>>> annee_naissance = 2025 - age
>>> print(f"{nom} est né(e) en {annee_naissance}")
Alice est né(e) en 2000
```

---
layout: default
---

# Types de données de base 📊

### Les types fondamentaux de Python

```python
# Entiers (int)
>>> nombre = 42
>>> type(nombre)
<class 'int'>

# Nombres à virgule flottante (float)
>>> decimal = 3.14
>>> type(decimal)
<class 'float'>

# Chaînes de caractères (str)
>>> texte = "Hello World"
>>> type(texte)
<class 'str'>

# Booléens (bool)
>>> vrai = True
>>> faux = False
>>> type(vrai)
<class 'bool'>
```

---
layout: default
---

# Chaînes de caractères 📝

### Manipulation du texte

```python
# Création de chaînes
>>> nom = "Python"
>>> version = "3.12"

# Concaténation
>>> message = nom + " " + version
>>> print(message)
Python 3.12

# Formatage avec f-strings (Python 3.6+)
>>> message = f"J'utilise {nom} version {version}"
>>> print(message)
J'utilise Python version 3.12

# Méthodes sur les chaînes
>>> texte = "  hello world  "
>>> texte.strip()  # Supprime les espaces
'hello world'
>>> texte.upper()  # Majuscules
'  HELLO WORLD  '
```

---
layout: default
---

# Entrées utilisateur 👤

### Récupération de données depuis l'utilisateur

```python
# Fonction input() pour récupérer une entrée
>>> nom = input("Quel est votre nom ? ")
Quel est votre nom ? Alice

>>> age = input("Quel est votre âge ? ")
Quel est votre âge ? 25

# Conversion de types
>>> age_entier = int(age)
>>> print(f"Bonjour {nom}, vous avez {age_entier} ans")
Bonjour Alice, vous avez 25 ans

# Conversion avec gestion d'erreur
>>> try:
...     age = int(input("Votre âge : "))
... except ValueError:
...     print("Veuillez entrer un nombre valide")
```

---
layout: default
---

# Premier programme complet 🎯

### Script Python autonome

**Fichier : `premier_programme.py`**
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

"""
Mon premier programme Python
Auteur : [Votre nom]
Date : 2025
"""

def main():
    """Fonction principale du programme."""
    print("=== Mon premier programme Python ===")
    
    # Récupération des données utilisateur
    nom = input("Quel est votre nom ? ")
    age = input("Quel est votre âge ? ")
    
    # Conversion et calculs
    try:
        age_entier = int(age)
        annee_naissance = 2025 - age_entier
        
        # Affichage des résultats
        print(f"\nBonjour {nom} !")
        print(f"Vous avez {age_entier} ans")
        print(f"Vous êtes né(e) vers {annee_naissance}")
        
    except ValueError:
        print("Erreur : Veuillez entrer un âge valide")

if __name__ == "__main__":
    main()
```

---
layout: default
---

# Exécution du programme 🚀

### Lancement du script

**Exécution en ligne de commande :**
```bash
python premier_programme.py
```

**Exemple d'exécution :**
```
=== Mon premier programme Python ===
Quel est votre nom ? Alice
Quel est votre âge ? 25

Bonjour Alice !
Vous avez 25 ans
Vous êtes né(e) vers 2000
```

**Exécution avec erreur :**
```
=== Mon premier programme Python ===
Quel est votre nom ? Bob
Quel est votre âge ? abc
Erreur : Veuillez entrer un âge valide
```

---
layout: default
---

# Commentaires et documentation 📚

### Annoter votre code

```python
# Ceci est un commentaire sur une ligne

"""
Ceci est un commentaire
sur plusieurs lignes
(docstring)
"""

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

# Gestion des erreurs ⚠️

### Traitement des exceptions

```python
# Gestion d'erreur basique
try:
    nombre = int(input("Entrez un nombre : "))
    resultat = 100 / nombre
    print(f"100 / {nombre} = {resultat}")
except ValueError:
    print("Erreur : Veuillez entrer un nombre valide")
except ZeroDivisionError:
    print("Erreur : Division par zéro impossible")
except Exception as e:
    print(f"Erreur inattendue : {e}")
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Calculateur simple

Créez un programme qui :
1. Demande deux nombres à l'utilisateur
2. Propose les opérations (+, -, *, /)
3. Affiche le résultat
4. Gère les erreurs de division par zéro

**Structure suggérée :**
```python
def calculateur():
    # Votre code ici
    pass

if __name__ == "__main__":
    calculateur()
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Convertisseur de température

Créez un programme qui :
1. Demande une température en Celsius
2. Convertit en Fahrenheit (F = C × 9/5 + 32)
3. Convertit en Kelvin (K = C + 273.15)
4. Affiche les trois valeurs

**Formule :**
- Fahrenheit = Celsius × 9/5 + 32
- Kelvin = Celsius + 273.15

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Calculateur d'IMC

Créez un programme qui :
1. Demande le poids (kg) et la taille (m)
2. Calcule l'IMC (poids / taille²)
3. Affiche la catégorie :
   - < 18.5 : Insuffisance pondérale
   - 18.5-25 : Normal
   - 25-30 : Surpoids
   - > 30 : Obésité

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Calculateur simple

```python
def calculateur():
    try:
        a = float(input("Premier nombre : "))
        b = float(input("Deuxième nombre : "))
        operation = input("Opération (+, -, *, /) : ")
        
        if operation == "+":
            resultat = a + b
        elif operation == "-":
            resultat = a - b
        elif operation == "*":
            resultat = a * b
        elif operation == "/":
            if b == 0:
                print("Erreur : Division par zéro")
                return
            resultat = a / b
        else:
            print("Opération non reconnue")
            return
            
        print(f"{a} {operation} {b} = {resultat}")
        
    except ValueError:
        print("Erreur : Veuillez entrer des nombres valides")

if __name__ == "__main__":
    calculateur()
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Convertisseur de température

```python
def convertisseur_temperature():
    try:
        celsius = float(input("Température en Celsius : "))
        
        fahrenheit = celsius * 9/5 + 32
        kelvin = celsius + 273.15
        
        print(f"\nRésultats :")
        print(f"Celsius : {celsius}°C")
        print(f"Fahrenheit : {fahrenheit:.2f}°F")
        print(f"Kelvin : {kelvin:.2f}K")
        
    except ValueError:
        print("Erreur : Veuillez entrer une température valide")

if __name__ == "__main__":
    convertisseur_temperature()
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Calculateur d'IMC

```python
def calculateur_imc():
    try:
        poids = float(input("Poids en kg : "))
        taille = float(input("Taille en m : "))
        
        if poids <= 0 or taille <= 0:
            print("Erreur : Poids et taille doivent être positifs")
            return
            
        imc = poids / (taille ** 2)
        
        print(f"\nVotre IMC : {imc:.1f}")
        
        if imc < 18.5:
            categorie = "Insuffisance pondérale"
        elif imc < 25:
            categorie = "Normal"
        elif imc < 30:
            categorie = "Surpoids"
        else:
            categorie = "Obésité"
            
        print(f"Catégorie : {categorie}")
        
    except ValueError:
        print("Erreur : Veuillez entrer des valeurs valides")

if __name__ == "__main__":
    calculateur_imc()
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Variables et types** de données avancés
2. **Structures de contrôle** (conditions, boucles)
3. **Fonctions et modules**
4. **Collections** (listes, dictionnaires)
5. **Programmation orientée objet**

**Préparation :**
- Pratiquez avec l'interpréteur interactif
- Créez vos premiers scripts
- Testez les exercices proposés

---
src: './pages/04-variables-types.md'
--- 