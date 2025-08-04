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

# Création d'objets
alice = Personne("Alice", 25)
bob = Personne("Bob", 30)

print(alice.se_presenter())  # Je m'appelle Alice et j'ai 25 ans
print(bob.se_presenter())    # Je m'appelle Bob et j'ai 30 ans

alice.avoir_anniversaire()
print(alice.se_presenter())  # Je m'appelle Alice et j'ai 26 ans
```

---
layout: default
---

# Méthodes spéciales 🎭

### Méthodes magiques de Python

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __str__(self):
        return f"Point({self.x}, {self.y})"
    
    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"
    
    def __eq__(self, autre):
        if not isinstance(autre, Point):
            return False
        return self.x == autre.x and self.y == autre.y
    
    def __add__(self, autre):
        return Point(self.x + autre.x, self.y + autre.y)
    
    def __len__(self):
        return int((self.x**2 + self.y**2)**0.5)

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

```python
class Animal:
    def __init__(self, nom):
        self.nom = nom
    
    def parler(self):
        return "L'animal fait un son"
    
    def se_deplacer(self):
        return "L'animal se déplace"

class Chien(Animal):
    def parler(self):
        return f"{self.nom} dit : Wouf !"
    
    def se_deplacer(self):
        return f"{self.nom} court"

class Chat(Animal):
    def parler(self):
        return f"{self.nom} dit : Miaou !"
    
    def se_deplacer(self):
        return f"{self.nom} marche silencieusement"

# Polymorphisme
animaux = [Chien("Rex"), Chat("Mimi"), Animal("Générique")]

for animal in animaux:
    print(f"{animal.nom} : {animal.parler()}")
    print(f"{animal.nom} : {animal.se_deplacer()}")
```

---
layout: default
---

# Encapsulation et propriétés 🔒

### Contrôle d'accès aux attributs

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
            self._historique.append(f"Dépôt: +{montant}")
            return True
        return False
    
    def retirer(self, montant):
        if 0 < montant <= self._solde:
            self._solde -= montant
            self._historique.append(f"Retrait: -{montant}")
            return True
        return False
    
    def obtenir_historique(self):
        return self._historique.copy()

# Utilisation
compte = CompteBancaire("Alice", 1000)
print(compte.solde)      # 1000
compte.deposer(500)      # True
compte.retirer(200)      # True
print(compte.solde)      # 1300
print(compte.obtenir_historique())
```

---
layout: default
---

# Méthodes de classe et statiques ⚡

### Différents types de méthodes

```python
class Mathematiques:
    PI = 3.14159
    
    def __init__(self, valeur):
        self.valeur = valeur
    
    def carre(self):
        return self.valeur ** 2
    
    @classmethod
    def creer_avec_pi(cls):
        return cls(cls.PI)
    
    @staticmethod
    def additionner(a, b):
        return a + b
    
    @staticmethod
    def factorielle(n):
        if n <= 1:
            return 1
        return n * Mathematiques.factorielle(n - 1)

# Utilisation
math = Mathematiques(5)
print(math.carre())                    # 25

math_pi = Mathematiques.creer_avec_pi()
print(math_pi.valeur)                  # 3.14159

resultat = Mathematiques.additionner(3, 4)  # 7
facto = Mathematiques.factorielle(5)        # 120
```

---
layout: default
---

# Héritage multiple 🔗

### Héritage de plusieurs classes

```python
class Volant:
    def voler(self):
        return "Je vole dans les airs"

class Nageur:
    def nager(self):
        return "Je nage dans l'eau"

class Canard(Volant, Nageur):
    def __init__(self, nom):
        self.nom = nom
    
    def parler(self):
        return f"{self.nom} dit : Coin coin !"
    
    def se_deplacer(self):
        return f"{self.nom} peut voler et nager"

# Utilisation
donald = Canard("Donald")
print(donald.voler())      # Je vole dans les airs
print(donald.nager())      # Je nage dans l'eau
print(donald.parler())     # Donald dit : Coin coin !

# Méthode Resolution Order (MRO)
print(Canard.__mro__)      # Ordre de résolution des méthodes
```

---
layout: default
---

# Classes abstraites 🎨

### Définition d'interfaces

```python
from abc import ABC, abstractmethod

class Forme(ABC):
    @abstractmethod
    def aire(self):
        pass
    
    @abstractmethod
    def perimetre(self):
        pass

class Rectangle(Forme):
    def __init__(self, largeur, hauteur):
        self.largeur = largeur
        self.hauteur = hauteur
    
    def aire(self):
        return self.largeur * self.hauteur
    
    def perimetre(self):
        return 2 * (self.largeur + self.hauteur)

class Cercle(Forme):
    def __init__(self, rayon):
        self.rayon = rayon
    
    def aire(self):
        return 3.14159 * self.rayon ** 2
    
    def perimetre(self):
        return 2 * 3.14159 * self.rayon

# Utilisation
formes = [Rectangle(5, 3), Cercle(4)]

for forme in formes:
    print(f"Aire: {forme.aire():.2f}")
    print(f"Périmètre: {forme.perimetre():.2f}")
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Système de gestion de bibliothèque

Créez un système de bibliothèque avec :
1. Classe Livre avec titre, auteur, ISBN
2. Classe Membre avec nom, ID, livres empruntés
3. Classe Bibliotheque pour gérer les livres et membres
4. Fonctionnalités d'emprunt/retour

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Système de géométrie

Créez un système de géométrie avec :
1. Classe abstraite Forme
2. Classes Rectangle, Cercle, Triangle
3. Calculs d'aire et périmètre
4. Comparaison de formes

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Système de gestion d'école

Créez un système de gestion d'école avec :
1. Classes Personne, Etudiant, Professeur
2. Gestion des cours et notes
3. Calcul de moyennes
4. Rapports de performance

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Système de gestion de bibliothèque

```python
from datetime import datetime, timedelta

class Livre:
    def __init__(self, titre, auteur, isbn):
        self.titre = titre
        self.auteur = auteur
        self.isbn = isbn
        self.disponible = True
        self.emprunte_par = None
        self.date_emprunt = None
    
    def __str__(self):
        statut = "Disponible" if self.disponible else f"Emprunté par {self.emprunte_par}"
        return f"{self.titre} par {self.auteur} ({self.isbn}) - {statut}"

class Membre:
    def __init__(self, nom, id_membre):
        self.nom = nom
        self.id_membre = id_membre
        self.livres_empruntes = []
    
    def emprunter_livre(self, livre):
        if livre.disponible:
            livre.disponible = False
            livre.emprunte_par = self.nom
            livre.date_emprunt = datetime.now()
            self.livres_empruntes.append(livre)
            return True
        return False
    
    def retourner_livre(self, livre):
        if livre in self.livres_empruntes:
            livre.disponible = True
            livre.emprunte_par = None
            livre.date_emprunt = None
            self.livres_empruntes.remove(livre)
            return True
        return False

class Bibliotheque:
    def __init__(self):
        self.livres = []
        self.membres = []
    
    def ajouter_livre(self, livre):
        self.livres.append(livre)
    
    def ajouter_membre(self, membre):
        self.membres.append(membre)
    
    def rechercher_livre(self, titre=None, auteur=None, isbn=None):
        resultats = []
        for livre in self.livres:
            if (titre is None or titre.lower() in livre.titre.lower()) and \
               (auteur is None or auteur.lower() in livre.auteur.lower()) and \
               (isbn is None or isbn == livre.isbn):
                resultats.append(livre)
        return resultats
    
    def livres_disponibles(self):
        return [livre for livre in self.livres if livre.disponible]
    
    def livres_empruntes(self):
        return [livre for livre in self.livres if not livre.disponible]
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Système de géométrie

```python
from abc import ABC, abstractmethod
import math

class Forme(ABC):
    @abstractmethod
    def aire(self):
        pass
    
    @abstractmethod
    def perimetre(self):
        pass
    
    def __lt__(self, autre):
        return self.aire() < autre.aire()
    
    def __eq__(self, autre):
        return abs(self.aire() - autre.aire()) < 0.001

class Rectangle(Forme):
    def __init__(self, largeur, hauteur):
        self.largeur = largeur
        self.hauteur = hauteur
    
    def aire(self):
        return self.largeur * self.hauteur
    
    def perimetre(self):
        return 2 * (self.largeur + self.hauteur)
    
    def __str__(self):
        return f"Rectangle({self.largeur}x{self.hauteur})"

class Cercle(Forme):
    def __init__(self, rayon):
        self.rayon = rayon
    
    def aire(self):
        return math.pi * self.rayon ** 2
    
    def perimetre(self):
        return 2 * math.pi * self.rayon
    
    def __str__(self):
        return f"Cercle(r={self.rayon})"

class Triangle(Forme):
    def __init__(self, a, b, c):
        self.a = a
        self.b = b
        self.c = c
    
    def aire(self):
        # Formule de Héron
        s = (self.a + self.b + self.c) / 2
        return math.sqrt(s * (s - self.a) * (s - self.b) * (s - self.c))
    
    def perimetre(self):
        return self.a + self.b + self.c
    
    def __str__(self):
        return f"Triangle({self.a}, {self.b}, {self.c})"

# Utilisation
formes = [
    Rectangle(5, 3),
    Cercle(4),
    Triangle(3, 4, 5)
]

# Tri par aire
formes_triees = sorted(formes)
for forme in formes_triees:
    print(f"{forme}: Aire={forme.aire():.2f}, Périmètre={forme.perimetre():.2f}")
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Système de gestion d'école

```python
from abc import ABC, abstractmethod
from typing import List, Dict

class Personne(ABC):
    def __init__(self, nom, age):
        self.nom = nom
        self.age = age
    
    @abstractmethod
    def role(self):
        pass

class Etudiant(Personne):
    def __init__(self, nom, age, id_etudiant):
        super().__init__(nom, age)
        self.id_etudiant = id_etudiant
        self.notes = {}
        self.cours_suivis = []
    
    def role(self):
        return "Étudiant"
    
    def ajouter_note(self, cours, note):
        if cours not in self.notes:
            self.notes[cours] = []
        self.notes[cours].append(note)
    
    def moyenne_cours(self, cours):
        if cours in self.notes and self.notes[cours]:
            return sum(self.notes[cours]) / len(self.notes[cours])
        return 0
    
    def moyenne_generale(self):
        if not self.notes:
            return 0
        moyennes = [self.moyenne_cours(cours) for cours in self.notes]
        return sum(moyennes) / len(moyennes)

class Professeur(Personne):
    def __init__(self, nom, age, specialite):
        super().__init__(nom, age)
        self.specialite = specialite
        self.cours_enseignes = []
    
    def role(self):
        return "Professeur"
    
    def ajouter_cours(self, cours):
        self.cours_enseignes.append(cours)

class Cours:
    def __init__(self, nom, professeur):
        self.nom = nom
        self.professeur = professeur
        self.etudiants = []
    
    def ajouter_etudiant(self, etudiant):
        if etudiant not in self.etudiants:
            self.etudiants.append(etudiant)
            etudiant.cours_suivis.append(self.nom)
    
    def moyenne_classe(self):
        if not self.etudiants:
            return 0
        moyennes = [etudiant.moyenne_cours(self.nom) for etudiant in self.etudiants]
        return sum(moyennes) / len(moyennes)

class Ecole:
    def __init__(self, nom):
        self.nom = nom
        self.etudiants = []
        self.professeurs = []
        self.cours = []
    
    def ajouter_etudiant(self, etudiant):
        self.etudiants.append(etudiant)
    
    def ajouter_professeur(self, professeur):
        self.professeurs.append(professeur)
    
    def ajouter_cours(self, cours):
        self.cours.append(cours)
    
    def rapport_performance(self):
        rapport = {
            'nombre_etudiants': len(self.etudiants),
            'nombre_professeurs': len(self.professeurs),
            'nombre_cours': len(self.cours),
            'moyenne_ecole': 0,
            'meilleurs_etudiants': [],
            'cours_performance': {}
        }
        
        if self.etudiants:
            moyennes = [etudiant.moyenne_generale() for etudiant in self.etudiants]
            rapport['moyenne_ecole'] = sum(moyennes) / len(moyennes)
            
            # Meilleurs étudiants
            etudiants_tries = sorted(self.etudiants, key=lambda e: e.moyenne_generale(), reverse=True)
            rapport['meilleurs_etudiants'] = [(e.nom, e.moyenne_generale()) for e in etudiants_tries[:3]]
        
        # Performance par cours
        for cours in self.cours:
            rapport['cours_performance'][cours.nom] = cours.moyenne_classe()
        
        return rapport
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Manipulation de fichiers** (I/O avancé)
2. **Projet final intégrateur**
3. **QCM de validation**
4. **Débriefing et perspectives**

**Préparation :**
- Maîtrisez la POO
- Pratiquez l'héritage et le polymorphisme
- Testez les exercices proposés

---
src: './pages/10-fichiers-io.md'
--- 