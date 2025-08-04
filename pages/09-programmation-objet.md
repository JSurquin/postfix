---
layout: intro
routeAlias: 'programmation-objet'
---

# Programmation Orientée Objet 🏗️

### Classes, objets et héritage en Python

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Maîtrisons la POO <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Classes et objets 🎯

### Création de classes en Python

**Concepts de base :**
- Classes : modèles pour créer des objets
- Objets : instances de classes
- Attributs : données des objets
- Méthodes : comportements des objets

---

# Définition d'une Classe

```python
# Définition d'une classe simple
class Personne:
    def __init__(self, nom, age):
        self.nom = nom
        self.age = age
    
    def se_presenter(self):
        return f"Je m'appelle {self.nom} et j'ai {self.age} ans"
    
    def avoir_anniversaire(self):
        self.age += 1
        return f"Joyeux anniversaire ! J'ai maintenant {self.age} ans"
```

---

# Création d'Objets

```python
# Création d'objets
alice = Personne("Alice", 25)
bob = Personne("Bob", 30)

print(alice.se_presenter())  # Je m'appelle Alice et j'ai 25 ans
print(bob.se_presenter())    # Je m'appelle Bob et j'ai 30 ans
```

---

# Utilisation des Méthodes

```python
alice.avoir_anniversaire()
print(alice.se_presenter())  # Je m'appelle Alice et j'ai 26 ans
```

---
layout: default
---

# Méthodes spéciales 🎭

### Méthodes magiques de Python

**Méthodes spéciales principales :**
- `__init__` : constructeur
- `__str__` : représentation string
- `__repr__` : représentation technique
- `__eq__` : égalité
- `__add__` : addition
- `__len__` : longueur

---

# Méthodes Spéciales de Base

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __str__(self):
        return f"Point({self.x}, {self.y})"
    
    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"
```

---

# Méthodes Spéciales Avancées

```python
    def __eq__(self, autre):
        if not isinstance(autre, Point):
            return False
        return self.x == autre.x and self.y == autre.y
    
    def __add__(self, autre):
        return Point(self.x + autre.x, self.y + autre.y)
    
    def __len__(self):
        return int((self.x**2 + self.y**2)**0.5)
```

---

# Utilisation des Méthodes Spéciales

```python
# Utilisation
p1 = Point(3, 4)
p2 = Point(1, 2)

print(p1)           # Point(3, 4)
print(p1 == p2)     # False
print(p1 + p2)      # Point(4, 6)
print(len(p1))      # 5 (distance depuis l'origine)
```

---
layout: default
---

# Héritage et polymorphisme 🔄

### Création de hiérarchies de classes

**Concepts d'héritage :**
- Classe parent (base)
- Classe enfant (dérivée)
- Réutilisation de code
- Polymorphisme

---

# Classe Parent

```python
class Animal:
    def __init__(self, nom):
        self.nom = nom
    
    def parler(self):
        return "L'animal fait un son"
    
    def se_deplacer(self):
        return "L'animal se déplace"
```

---

# Classes Enfants

```python
class Chien(Animal):
    def parler(self):
        return f"{self.nom} dit : Woof !"
    
    def se_deplacer(self):
        return f"{self.nom} court"

class Chat(Animal):
    def parler(self):
        return f"{self.nom} dit : Miaou !"
    
    def se_deplacer(self):
        return f"{self.nom} marche silencieusement"
```

---

# Utilisation de l'Héritage

```python
# Utilisation
chien = Chien("Rex")
chat = Chat("Minou")

print(chien.parler())      # Rex dit : Woof !
print(chat.parler())       # Minou dit : Miaou !
print(chien.se_deplacer()) # Rex court
print(chat.se_deplacer())  # Minou marche silencieusement
```

---
layout: default
---

# Encapsulation 🔒

### Protection des données

**Niveaux d'encapsulation :**
- Public : accessible partout
- Protected : accessible dans la classe et les enfants
- Private : accessible seulement dans la classe

---

# Attributs Publics

```python
class Compte:
    def __init__(self, titulaire, solde=0):
        self.titulaire = titulaire  # Public
        self.solde = solde          # Public
    
    def deposer(self, montant):
        self.solde += montant
        return f"Dépôt de {montant}€ effectué"
    
    def retirer(self, montant):
        if montant <= self.solde:
            self.solde -= montant
            return f"Retrait de {montant}€ effectué"
        else:
            return "Solde insuffisant"
```

---

# Attributs Protected

```python
class CompteSecurise:
    def __init__(self, titulaire, solde=0):
        self.titulaire = titulaire
        self._solde = solde  # Protected (convention)
    
    def deposer(self, montant):
        if montant > 0:
            self._solde += montant
            return f"Dépôt de {montant}€ effectué"
        return "Montant invalide"
    
    def get_solde(self):
        return self._solde
```

---

# Attributs Private

```python
class ComptePrive:
    def __init__(self, titulaire, solde=0):
        self.titulaire = titulaire
        self.__solde = solde  # Private
    
    def deposer(self, montant):
        if montant > 0:
            self.__solde += montant
            return f"Dépôt de {montant}€ effectué"
        return "Montant invalide"
    
    def get_solde(self):
        return self.__solde
    
    def set_solde(self, nouveau_solde):
        if nouveau_solde >= 0:
            self.__solde = nouveau_solde
```

---
layout: default
---

# Propriétés 🎯

### Contrôle d'accès aux attributs

**Avantages des propriétés :**
- Contrôle d'accès
- Validation automatique
- Interface propre
- Calculs dynamiques

---

# Propriété Simple

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
        if valeur > 0:
            self._largeur = valeur
        else:
            raise ValueError("La largeur doit être positive")
```

---

# Propriété Calculée

```python
    @property
    def aire(self):
        return self._largeur * self._hauteur
    
    @property
    def perimetre(self):
        return 2 * (self._largeur + self._hauteur)
```

---

# Utilisation des Propriétés

```python
# Utilisation
rect = Rectangle(5, 3)
print(f"Largeur: {rect.largeur}")
print(f"Aire: {rect.aire}")
print(f"Périmètre: {rect.perimetre}")

# Modification
rect.largeur = 6
print(f"Nouvelle aire: {rect.aire}")
```

---
layout: default
---

# Méthodes de classe et statiques 🏭

### Méthodes au niveau de la classe

**Types de méthodes :**
- Méthodes d'instance : `self`
- Méthodes de classe : `@classmethod`
- Méthodes statiques : `@staticmethod`

---

# Méthodes de Classe

```python
class Date:
    def __init__(self, jour, mois, annee):
        self.jour = jour
        self.mois = mois
        self.annee = annee
    
    @classmethod
    def from_string(cls, date_str):
        """Crée une date à partir d'une chaîne 'JJ/MM/AAAA'"""
        jour, mois, annee = map(int, date_str.split('/'))
        return cls(jour, mois, annee)
    
    @classmethod
    def aujourd_hui(cls):
        """Crée une date pour aujourd'hui"""
        from datetime import date
        aujourd_hui = date.today()
        return cls(aujourd_hui.day, aujourd_hui.month, aujourd_hui.year)
```

---

# Méthodes Statiques

```python
    @staticmethod
    def est_valide(jour, mois, annee):
        """Vérifie si une date est valide"""
        if mois < 1 or mois > 12:
            return False
        if jour < 1 or jour > 31:
            return False
        return True
    
    @staticmethod
    def est_bissextile(annee):
        """Vérifie si une année est bissextile"""
        return annee % 4 == 0 and (annee % 100 != 0 or annee % 400 == 0)
```

---

# Utilisation des Méthodes

```python
# Utilisation
date1 = Date.from_string("15/03/2024")
date2 = Date.aujourd_hui()

print(f"Date1: {date1.jour}/{date1.mois}/{date1.annee}")
print(f"Date2: {date2.jour}/{date2.mois}/{date2.annee}")

# Méthodes statiques
print(Date.est_valide(32, 13, 2024))  # False
print(Date.est_bissextile(2024))       # True
```

---
layout: default
---

# Héritage multiple 🔀

### Héritage de plusieurs classes

**Avantages :**
- Réutilisation de code
- Flexibilité
- Composition de comportements

**Attention :**
- Complexité
- Conflits de noms
- Ordre de résolution

---

# Classes de Base

```python
class Volant:
    def voler(self):
        return "Je vole dans les airs"

class Nageant:
    def nager(self):
        return "Je nage dans l'eau"

class Marcheur:
    def marcher(self):
        return "Je marche sur terre"
```

---

# Héritage Multiple

```python
class Canard(Marcheur, Nageant, Volant):
    def __init__(self, nom):
        self.nom = nom
    
    def se_presenter(self):
        return f"Je suis {self.nom}, un canard"
    
    def faire_tout(self):
        return f"{self.se_presenter()}. {self.marcher()}. {self.nager()}. {self.voler()}"
```

---

# Utilisation de l'Héritage Multiple

```python
# Utilisation
donald = Canard("Donald")
print(donald.faire_tout())

# Ordre de résolution des méthodes (MRO)
print(Canard.__mro__)
```

---
layout: default
---

# Classes abstraites 🎭

### Interfaces et classes de base

**Utilisation :**
- Définir des interfaces
- Forcer l'implémentation
- Créer des contrats
- Polymorphisme garanti

---

# Classe Abstraite

```python
from abc import ABC, abstractmethod

class Forme(ABC):
    @abstractmethod
    def calculer_aire(self):
        pass
    
    @abstractmethod
    def calculer_perimetre(self):
        pass
    
    def afficher_info(self):
        return f"Aire: {self.calculer_aire()}, Périmètre: {self.calculer_perimetre()}"
```

---

# Implémentation des Classes Abstraites

```python
class Cercle(Forme):
    def __init__(self, rayon):
        self.rayon = rayon
    
    def calculer_aire(self):
        import math
        return math.pi * self.rayon ** 2
    
    def calculer_perimetre(self):
        import math
        return 2 * math.pi * self.rayon

class Rectangle(Forme):
    def __init__(self, largeur, hauteur):
        self.largeur = largeur
        self.hauteur = hauteur
    
    def calculer_aire(self):
        return self.largeur * self.hauteur
    
    def calculer_perimetre(self):
        return 2 * (self.largeur + self.hauteur)
```

---

# Utilisation des Classes Abstraites

```python
# Utilisation
cercle = Cercle(5)
rectangle = Rectangle(4, 6)

print(cercle.afficher_info())
print(rectangle.afficher_info())

# Polymorphisme
formes = [cercle, rectangle]
for forme in formes:
    print(f"Forme: {forme.afficher_info()}")
```

---
layout: default
---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez une hiérarchie de classes pour gérer différents types de véhicules (Voiture, Moto, Vélo) avec des méthodes communes et spécifiques.

**Exercice 2 :** Implémentez une classe `Banque` avec des comptes sécurisés utilisant l'encapsulation et les propriétés.

**Exercice 3 :** Créez une classe abstraite `Animal` avec des sous-classes concrètes et utilisez le polymorphisme.

---

# Solutions des Exercices 💡

### Exercice 1 - Hiérarchie de Véhicules

```python
class Vehicule:
    def __init__(self, marque, modele, annee):
        self.marque = marque
        self.modele = modele
        self.annee = annee
    
    def demarrer(self):
        return "Le véhicule démarre"
    
    def arreter(self):
        return "Le véhicule s'arrête"
    
    def __str__(self):
        return f"{self.marque} {self.modele} ({self.annee})"

class Voiture(Vehicule):
    def __init__(self, marque, modele, annee, portes=4):
        super().__init__(marque, modele, annee)
        self.portes = portes
    
    def demarrer(self):
        return f"{super().demarrer()} en tournant la clé"
    
    def klaxonner(self):
        return "Tut tut !"

class Moto(Vehicule):
    def demarrer(self):
        return f"{super().demarrer()} en poussant le bouton"
    
    def faire_wheelie(self):
        return "Wheeeelie !"
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Classe Banque

```python
class CompteBancaire:
    def __init__(self, titulaire, solde_initial=0):
        self._titulaire = titulaire
        self._solde = solde_initial
        self._historique = []
    
    @property
    def solde(self):
        return self._solde
    
    @property
    def titulaire(self):
        return self._titulaire
    
    def deposer(self, montant):
        if montant > 0:
            self._solde += montant
            self._historique.append(f"Dépôt: +{montant}€")
            return f"Dépôt de {montant}€ effectué"
        return "Montant invalide"
    
    def retirer(self, montant):
        if montant > 0 and montant <= self._solde:
            self._solde -= montant
            self._historique.append(f"Retrait: -{montant}€")
            return f"Retrait de {montant}€ effectué"
        return "Montant invalide ou solde insuffisant"
    
    def get_historique(self):
        return self._historique.copy()

class Banque:
    def __init__(self, nom):
        self.nom = nom
        self._comptes = {}
    
    def creer_compte(self, titulaire, solde_initial=0):
        compte = CompteBancaire(titulaire, solde_initial)
        self._comptes[titulaire] = compte
        return compte
    
    def get_compte(self, titulaire):
        return self._comptes.get(titulaire)
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Classes Abstraites Animal

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    def __init__(self, nom, age):
        self.nom = nom
        self.age = age
    
    @abstractmethod
    def faire_son(self):
        pass
    
    @abstractmethod
    def se_deplacer(self):
        pass
    
    def se_presenter(self):
        return f"Je suis {self.nom}, j'ai {self.age} ans"

class Chien(Animal):
    def faire_son(self):
        return "Woof !"
    
    def se_deplacer(self):
        return "Je cours"

class Chat(Animal):
    def faire_son(self):
        return "Miaou !"
    
    def se_deplacer(self):
        return "Je marche silencieusement"

class Oiseau(Animal):
    def faire_son(self):
        return "Cui cui !"
    
    def se_deplacer(self):
        return "Je vole"
```

---

# Test du Polymorphisme

```python
# Test du polymorphisme
animaux = [
    Chien("Rex", 3),
    Chat("Minou", 2),
    Oiseau("Titi", 1)
]

for animal in animaux:
    print(f"{animal.se_presenter()}")
    print(f"  Son: {animal.faire_son()}")
    print(f"  Déplacement: {animal.se_deplacer()}")
    print()
```

---

# Points Clés à Retenir 🎯

### Résumé de la programmation orientée objet

**1. Concepts de base :**
- Classes et objets
- Attributs et méthodes
- Constructeur `__init__`

**2. Héritage et polymorphisme :**
- Réutilisation de code
- Hiérarchies de classes
- Polymorphisme

**3. Encapsulation :**
- Protection des données
- Propriétés
- Méthodes de classe et statiques

**4. Concepts avancés :**
- Héritage multiple
- Classes abstraites
- Méthodes spéciales

**5. Applications pratiques :**
- Modélisation du monde réel
- Réutilisation de code
- Maintenance facilitée

**Prêt pour la manipulation de fichiers !** 🚀 