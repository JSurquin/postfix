---
layout: intro
routeAlias: 'structures-controle'
---

# Structures de Contrôle 🔄

### Conditions et boucles en Python

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Maîtrisons les structures de contrôle <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Opérateurs de comparaison 🔍

### Comparaison de valeurs

```python
# Opérateurs de comparaison
>>> a = 10
>>> b = 5

>>> a == b    # Égalité
False

>>> a != b    # Inégalité
True

>>> a > b     # Plus grand que
True

>>> a < b     # Plus petit que
False

>>> a >= b    # Plus grand ou égal
True

>>> a <= b    # Plus petit ou égal
False

# Comparaison de chaînes
>>> "abc" < "def"    # Ordre alphabétique
True

>>> "abc" == "ABC"   # Sensible à la casse
False
```

---
layout: default
---

# Opérateurs logiques 🧠

### Combinaison de conditions

```python
# Opérateurs logiques
>>> a = True
>>> b = False

>>> a and b    # ET logique
False

>>> a or b     # OU logique
True

>>> not a      # NON logique
False

>>> not b      # NON logique
True

# Exemples pratiques
>>> age = 25
>>> permis = True

>>> peut_conduire = age >= 18 and permis
True

>>> est_jeune = age < 30 or age > 60
True
```

---
layout: default
---

# Instructions conditionnelles if/elif/else 🎯

### Contrôle du flux d'exécution

```python
# Structure if simple
age = 18

if age >= 18:
    print("Vous êtes majeur")
else:
    print("Vous êtes mineur")

# Structure if/elif/else
note = 85

if note >= 90:
    print("Excellent")
elif note >= 80:
    print("Très bien")
elif note >= 70:
    print("Bien")
elif note >= 60:
    print("Assez bien")
else:
    print("Insuffisant")
```

---
layout: default
---

# Conditions imbriquées 🏗️

### Conditions à plusieurs niveaux

```python
# Conditions imbriquées
age = 25
permis = True
voiture = False

if age >= 18:
    if permis:
        if voiture:
            print("Vous pouvez conduire votre voiture")
        else:
            print("Vous avez le permis mais pas de voiture")
    else:
        print("Vous êtes majeur mais n'avez pas le permis")
else:
    print("Vous êtes mineur")

# Équivalent avec and/or
if age >= 18 and permis and voiture:
    print("Vous pouvez conduire votre voiture")
elif age >= 18 and permis:
    print("Vous avez le permis mais pas de voiture")
elif age >= 18:
    print("Vous êtes majeur mais n'avez pas le permis")
else:
    print("Vous êtes mineur")
```

---
layout: default
---

# Opérateur ternaire ⚡

### Condition en une ligne

```python
# Syntaxe traditionnelle
age = 18
if age >= 18:
    statut = "majeur"
else:
    statut = "mineur"

# Opérateur ternaire
statut = "majeur" if age >= 18 else "mineur"

# Autres exemples
note = 85
resultat = "Réussi" if note >= 60 else "Échoué"

temperature = 25
message = "Chaud" if temperature > 20 else "Froid"

# Opérateur ternaire imbriqué
age = 25
permis = True
statut = "Peut conduire" if age >= 18 and permis else "Ne peut pas conduire"
```

---
layout: default
---

# Boucle while 🔄

### Répétition conditionnelle

```python
# Boucle while simple
compteur = 0
while compteur < 5:
    print(f"Compteur : {compteur}")
    compteur += 1

# Boucle avec condition complexe
mot_de_passe = "secret123"
tentatives = 0
max_tentatives = 3

while tentatives < max_tentatives:
    saisie = input("Mot de passe : ")
    if saisie == mot_de_passe:
        print("Accès autorisé")
        break
    else:
        tentatives += 1
        print(f"Tentative {tentatives}/{max_tentatives}")
else:
    print("Accès refusé")
```

---
layout: default
---

# Boucle for avec range() 📊

### Itération sur des séquences numériques

```python
# Boucle for avec range()
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# Range avec début et fin
for i in range(1, 6):
    print(i)  # 1, 2, 3, 4, 5

# Range avec pas
for i in range(0, 10, 2):
    print(i)  # 0, 2, 4, 6, 8

# Range décroissant
for i in range(5, 0, -1):
    print(i)  # 5, 4, 3, 2, 1

# Utilisation avec enumerate
for index, valeur in enumerate(range(1, 4)):
    print(f"Index {index}: {valeur}")
```

---
layout: default
---

# Boucle for avec séquences 📝

### Itération sur des listes et chaînes

```python
# Itération sur une chaîne
for lettre in "Python":
    print(lettre)

# Itération sur une liste
fruits = ["pomme", "banane", "orange"]
for fruit in fruits:
    print(f"J'aime les {fruit}")

# Itération avec index
for index, fruit in enumerate(fruits):
    print(f"{index + 1}. {fruit}")

# Itération sur un dictionnaire
personne = {"nom": "Alice", "age": 25, "ville": "Paris"}
for cle, valeur in personne.items():
    print(f"{cle}: {valeur}")

# Itération sur les clés
for cle in personne.keys():
    print(cle)

# Itération sur les valeurs
for valeur in personne.values():
    print(valeur)
```

---
layout: default
---

# Instructions break et continue 🛑

### Contrôle du flux dans les boucles

```python
# Instruction break
for i in range(10):
    if i == 5:
        break  # Sort de la boucle
    print(i)

# Instruction continue
for i in range(10):
    if i % 2 == 0:
        continue  # Passe à l'itération suivante
    print(i)  # Affiche seulement les impairs

# Exemple pratique
nombres = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
somme = 0

for nombre in nombres:
    if nombre > 7:
        break  # Arrête si on dépasse 7
    if nombre % 2 == 0:
        continue  # Ignore les nombres pairs
    somme += nombre

print(f"Somme des impairs jusqu'à 7 : {somme}")
```

---
layout: default
---

# Boucles imbriquées 🔗

### Boucles à plusieurs niveaux

```python
# Table de multiplication
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i} x {j} = {i * j}")
    print()  # Ligne vide entre les tables

# Motif en étoiles
for ligne in range(5):
    for colonne in range(ligne + 1):
        print("*", end="")
    print()  # Nouvelle ligne

# Résultat :
# *
# **
# ***
# ****
# *****
```

---
layout: default
---

# List comprehensions 📋

### Création de listes de manière concise

```python
# List comprehension simple
nombres = [1, 2, 3, 4, 5]
carres = [x**2 for x in nombres]
print(carres)  # [1, 4, 9, 16, 25]

# List comprehension avec condition
pairs = [x for x in nombres if x % 2 == 0]
print(pairs)  # [2, 4]

# List comprehension avec transformation
mots = ["hello", "world", "python"]
longueurs = [len(mot) for mot in mots]
print(longueurs)  # [5, 5, 6]

# List comprehension imbriquée
matrice = [[i + j for j in range(3)] for i in range(3)]
print(matrice)  # [[0, 1, 2], [1, 2, 3], [2, 3, 4]]
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Jeu de devinettes

Créez un programme qui :
1. Génère un nombre aléatoire entre 1 et 100
2. Demande à l'utilisateur de deviner
3. Donne des indices (plus grand/plus petit)
4. Compte le nombre de tentatives
5. Propose de rejouer

**Fonctionnalités :**
- Gestion des erreurs de saisie
- Limitation du nombre de tentatives
- Statistiques de jeu

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Calculateur de factorielle

Créez un programme qui :
1. Demande un nombre entier positif
2. Calcule sa factorielle (n!)
3. Affiche le résultat
4. Gère les erreurs

**Rappel :**
- 0! = 1
- n! = n × (n-1) × (n-2) × ... × 1
- Exemple : 5! = 5 × 4 × 3 × 2 × 1 = 120

**Contraintes :**
- Utiliser une boucle for
- Gérer les nombres négatifs
- Limiter la taille pour éviter l'overflow

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Générateur de table de multiplication

Créez un programme qui :
1. Demande un nombre entre 1 et 12
2. Génère sa table de multiplication
3. Affiche la table de manière formatée
4. Propose d'afficher plusieurs tables

**Format d'affichage :**
```
Table de multiplication de 7 :
7 x 1 = 7
7 x 2 = 14
...
7 x 12 = 84
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Jeu de devinettes

```python
import random

def jeu_devinettes():
    """Jeu de devinettes avec nombre aléatoire."""
    print("=== Jeu de devinettes ===")
    print("Devinez le nombre entre 1 et 100")
    
    nombre_secret = random.randint(1, 100)
    tentatives = 0
    max_tentatives = 10
    
    while tentatives < max_tentatives:
        try:
            guess = int(input(f"Tentative {tentatives + 1}/{max_tentatives} : "))
            tentatives += 1
            
            if guess < nombre_secret:
                print("Plus grand !")
            elif guess > nombre_secret:
                print("Plus petit !")
            else:
                print(f"Bravo ! Vous avez trouvé en {tentatives} tentatives")
                return True
                
        except ValueError:
            print("Erreur : Veuillez entrer un nombre valide")
            tentatives -= 1  # Ne pas compter cette tentative
    
    print(f"Perdu ! Le nombre était {nombre_secret}")
    return False

def main():
    while True:
        jeu_devinettes()
        rejouer = input("Voulez-vous rejouer ? (o/n) : ")
        if rejouer.lower() != 'o':
            break

if __name__ == "__main__":
    main()
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Calculateur de factorielle

```python
def calculer_factorielle(n):
    """Calcule la factorielle de n."""
    if n < 0:
        raise ValueError("La factorielle n'est pas définie pour les nombres négatifs")
    
    if n == 0:
        return 1
    
    resultat = 1
    for i in range(1, n + 1):
        resultat *= i
    
    return resultat

def calculateur_factorielle():
    print("=== Calculateur de factorielle ===")
    
    while True:
        try:
            n = int(input("Entrez un nombre entier positif : "))
            
            if n < 0:
                print("Erreur : Le nombre doit être positif")
                continue
            
            if n > 20:
                print("Attention : Les factorielles de grands nombres peuvent être très grandes")
                confirmer = input("Continuer ? (o/n) : ")
                if confirmer.lower() != 'o':
                    continue
            
            resultat = calculer_factorielle(n)
            print(f"{n}! = {resultat}")
            
        except ValueError:
            print("Erreur : Veuillez entrer un nombre entier valide")
        except OverflowError:
            print("Erreur : Le résultat est trop grand")
        
        continuer = input("Calculer une autre factorielle ? (o/n) : ")
        if continuer.lower() != 'o':
            break

if __name__ == "__main__":
    calculateur_factorielle()
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Générateur de table de multiplication

```python
def afficher_table_multiplication(nombre):
    """Affiche la table de multiplication d'un nombre."""
    print(f"\n=== Table de multiplication de {nombre} ===")
    
    for i in range(1, 13):
        resultat = nombre * i
        print(f"{nombre} x {i:2d} = {resultat:3d}")

def generateur_tables():
    print("=== Générateur de tables de multiplication ===")
    
    while True:
        try:
            nombre = int(input("Entrez un nombre entre 1 et 12 : "))
            
            if 1 <= nombre <= 12:
                afficher_table_multiplication(nombre)
            else:
                print("Erreur : Le nombre doit être entre 1 et 12")
                continue
                
        except ValueError:
            print("Erreur : Veuillez entrer un nombre entier valide")
            continue
        
        # Demander si on veut afficher plusieurs tables
        choix = input("\nVoulez-vous :\n1. Afficher une autre table\n2. Afficher toutes les tables\n3. Quitter\nVotre choix : ")
        
        if choix == "1":
            continue
        elif choix == "2":
            print("\n=== Toutes les tables de multiplication ===")
            for i in range(1, 13):
                afficher_table_multiplication(i)
            break
        elif choix == "3":
            break
        else:
            print("Choix invalide")

if __name__ == "__main__":
    generateur_tables()
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Fonctions et modules** (organisation du code)
2. **Collections** (listes, dictionnaires)
3. **Programmation orientée objet**
4. **Gestion des erreurs**
5. **Manipulation de fichiers**

**Préparation :**
- Maîtrisez les conditions et boucles
- Pratiquez les list comprehensions
- Testez les exercices proposés

---
src: './pages/06-fonctions.md'
--- 