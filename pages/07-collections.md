---
layout: intro
routeAlias: 'collections'
---

# Collections 📦

### Listes, dictionnaires et ensembles

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Explorons les structures de données <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Listes (list) 📋

### Collections ordonnées et modifiables

```python
# Création de listes
nombres = [1, 2, 3, 4, 5]
fruits = ["pomme", "banane", "orange"]
melange = [1, "hello", 3.14, True]

# Accès aux éléments
print(nombres[0])      # 1
print(nombres[-1])     # 5 (dernier élément)
print(nombres[1:3])    # [2, 3] (slicing)

# Modification des listes
nombres[0] = 10        # [10, 2, 3, 4, 5]
nombres.append(6)      # [10, 2, 3, 4, 5, 6]
nombres.insert(1, 15)  # [10, 15, 2, 3, 4, 5, 6]
nombres.remove(3)      # Supprime la première occurrence de 3
del nombres[0]         # Supprime l'élément à l'index 0

# Opérations sur les listes
longueur = len(nombres)
somme = sum(nombres)
maximum = max(nombres)
minimum = min(nombres)
```

---
layout: default
---

# Méthodes des listes 🔧

### Fonctions intégrées pour manipuler les listes

```python
# Méthodes d'ajout
liste = [1, 2, 3]
liste.append(4)           # [1, 2, 3, 4]
liste.extend([5, 6])      # [1, 2, 3, 4, 5, 6]
liste.insert(1, 10)       # [1, 10, 2, 3, 4, 5, 6]

# Méthodes de suppression
liste.remove(10)          # Supprime la première occurrence
element = liste.pop()     # Supprime et retourne le dernier élément
element = liste.pop(1)    # Supprime et retourne l'élément à l'index 1
del liste[0]             # Supprime l'élément à l'index 0

# Méthodes de recherche
index = liste.index(3)    # Retourne l'index de la première occurrence
compte = liste.count(2)   # Compte les occurrences de 2

# Méthodes de tri
liste.sort()              # Tri en place
liste.sort(reverse=True)  # Tri décroissant
liste.reverse()           # Inverse l'ordre
liste_triee = sorted(liste)  # Retourne une nouvelle liste triée
```

---
layout: default
---

# Slicing des listes 🔪

### Extraction de portions de listes

```python
nombres = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Syntaxe de base : liste[début:fin:pas]
print(nombres[2:5])      # [2, 3, 4]
print(nombres[:3])       # [0, 1, 2] (du début à l'index 3)
print(nombres[7:])       # [7, 8, 9] (de l'index 7 à la fin)
print(nombres[:])        # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] (copie complète)

# Slicing avec pas
print(nombres[::2])      # [0, 2, 4, 6, 8] (tous les 2 éléments)
print(nombres[1::2])     # [1, 3, 5, 7, 9] (impairs)
print(nombres[::-1])     # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0] (inverse)

# Slicing négatif
print(nombres[-3:])      # [7, 8, 9] (3 derniers éléments)
print(nombres[:-3])      # [0, 1, 2, 3, 4, 5, 6] (sauf les 3 derniers)
print(nombres[-5:-2])    # [5, 6, 7] (de l'index -5 à -2)

# Modification par slicing
nombres[2:5] = [20, 30, 40]  # Remplace les éléments de 2 à 5
```

---
layout: default
---

# List comprehensions 📝

### Création de listes de manière concise

```python
# List comprehension simple
nombres = [1, 2, 3, 4, 5]
carres = [x**2 for x in nombres]
print(carres)  # [1, 4, 9, 16, 25]

# List comprehension avec condition
pairs = [x for x in nombres if x % 2 == 0]
print(pairs)  # [2, 4]

# List comprehension avec transformation conditionnelle
resultats = [x**2 if x % 2 == 0 else x**3 for x in nombres]
print(resultats)  # [1, 4, 27, 16, 125]

# List comprehension imbriquée
matrice = [[i + j for j in range(3)] for i in range(3)]
print(matrice)  # [[0, 1, 2], [1, 2, 3], [2, 3, 4]]

# List comprehension avec plusieurs variables
coordonnees = [(x, y) for x in range(3) for y in range(3)]
print(coordonnees)  # [(0,0), (0,1), (0,2), (1,0), (1,1), (1,2), (2,0), (2,1), (2,2)]
```

---
layout: default
---

# Tuples (tuple) 🔒

### Collections ordonnées et immuables

```python
# Création de tuples
point = (3, 4)
personne = ("Alice", 25, "Paris")
singleton = (42,)  # Notez la virgule pour un tuple d'un élément

# Accès aux éléments (comme les listes)
x, y = point  # Déstructuration
nom, age, ville = personne

# Tuples vs listes
liste = [1, 2, 3]
tuple_exemple = (1, 2, 3)

# liste[0] = 10  # ✅ Possible
# tuple_exemple[0] = 10  # ❌ Erreur : tuple immuable

# Méthodes des tuples
index = point.index(3)    # Retourne l'index de 3
compte = point.count(4)   # Compte les occurrences de 4

# Avantages des tuples
# - Plus rapides que les listes
# - Peuvent être utilisés comme clés de dictionnaire
# - Garantissent l'immutabilité
```

---
layout: default
---

# Dictionnaires (dict) 📚

### Collections de paires clé-valeur

```python
# Création de dictionnaires
personne = {
    "nom": "Alice",
    "age": 25,
    "ville": "Paris",
    "profession": "Développeur"
}

# Accès aux valeurs
print(personne["nom"])           # Alice
print(personne.get("age"))       # 25
print(personne.get("email", "Non renseigné"))  # Valeur par défaut

# Modification du dictionnaire
personne["age"] = 26
personne["email"] = "alice@example.com"
personne.update({"telephone": "0123456789", "age": 27})

# Suppression
del personne["profession"]
email = personne.pop("email", None)  # Supprime et retourne la valeur
personne.clear()  # Vide le dictionnaire
```

---
layout: default
---

# Méthodes des dictionnaires 🔧

### Fonctions pour manipuler les dictionnaires

```python
personne = {"nom": "Alice", "age": 25, "ville": "Paris"}

# Méthodes d'accès
cles = personne.keys()        # dict_keys(['nom', 'age', 'ville'])
valeurs = personne.values()   # dict_values(['Alice', 25, 'Paris'])
items = personne.items()      # dict_items([('nom', 'Alice'), ('age', 25), ('ville', 'Paris')])

# Itération sur les dictionnaires
for cle in personne:
    print(f"{cle}: {personne[cle]}")

for cle, valeur in personne.items():
    print(f"{cle}: {valeur}")

# Méthodes de recherche
"nom" in personne            # True
personne.get("age")          # 25
personne.get("email", "Non renseigné")  # Valeur par défaut

# Méthodes de modification
personne.setdefault("pays", "France")  # Ajoute seulement si la clé n'existe pas
nouveau = personne.copy()    # Copie superficielle
```

---
layout: default
---

# Sets (set) 🎯

### Collections d'éléments uniques et non ordonnés

```python
# Création de sets
nombres = {1, 2, 3, 4, 5}
fruits = {"pomme", "banane", "orange"}
vide = set()  # Set vide

# Propriétés des sets
# - Éléments uniques
# - Non ordonnés
# - Mutables (sauf frozenset)

# Opérations sur les sets
nombres.add(6)           # Ajoute un élément
nombres.remove(1)        # Supprime un élément (erreur si absent)
nombres.discard(10)      # Supprime un élément (pas d'erreur si absent)
element = nombres.pop()   # Supprime et retourne un élément aléatoire

# Opérations ensemblistes
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

union = set1 | set2              # {1, 2, 3, 4, 5, 6}
intersection = set1 & set2        # {3, 4}
difference = set1 - set2          # {1, 2}
difference_sym = set1 ^ set2      # {1, 2, 5, 6}
```

---
layout: default
---

# Frozenset 🔒

### Sets immuables

```python
# Création d'un frozenset
nombres = frozenset([1, 2, 3, 4, 5])

# Propriétés
# - Éléments uniques
# - Non ordonnés
# - Immuables
# - Peut être utilisé comme clé de dictionnaire

# Opérations autorisées
print(1 in nombres)      # True
print(len(nombres))      # 5

# Opérations ensemblistes
set1 = frozenset([1, 2, 3])
set2 = frozenset([3, 4, 5])

union = set1 | set2              # frozenset({1, 2, 3, 4, 5})
intersection = set1 & set2        # frozenset({3})

# Utilisation comme clé de dictionnaire
dico = {frozenset([1, 2, 3]): "ensemble"}
```

---
layout: default
---

# Collections avancées 📊

### Types de collections spécialisés

```python
from collections import defaultdict, Counter, deque, namedtuple

# defaultdict - dictionnaire avec valeur par défaut
dd = defaultdict(list)
dd["a"].append(1)  # Pas besoin de vérifier si la clé existe
dd["a"].append(2)
print(dd)  # defaultdict(<class 'list'>, {'a': [1, 2]})

# Counter - compteur d'éléments
texte = "hello world"
compteur = Counter(texte)
print(compteur)  # Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})
print(compteur.most_common(3))  # [('l', 3), ('o', 2), ('h', 1)]

# deque - file double-ended
file = deque([1, 2, 3])
file.append(4)      # Ajoute à la fin
file.appendleft(0)  # Ajoute au début
print(file)  # deque([0, 1, 2, 3, 4])

# namedtuple - tuple avec noms
Point = namedtuple('Point', ['x', 'y'])
p = Point(3, 4)
print(p.x, p.y)  # 3 4
```

---
layout: default
---

# Itérateurs et générateurs 🔄

### Création d'objets itérables

```python
# Itérateur simple
class Compteur:
    def __init__(self, debut, fin):
        self.debut = debut
        self.fin = fin
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.debut >= self.fin:
            raise StopIteration
        self.debut += 1
        return self.debut - 1

# Utilisation
for i in Compteur(0, 5):
    print(i)  # 0, 1, 2, 3, 4

# Générateur avec yield
def fibonacci(n):
    """Générateur pour les nombres de Fibonacci."""
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

# Utilisation du générateur
for fib in fibonacci(10):
    print(fib, end=" ")  # 0 1 1 2 3 5 8 13 21 34

# Expression génératrice
carres = (x**2 for x in range(5))
print(list(carres))  # [0, 1, 4, 9, 16]
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Gestionnaire de tâches

Créez un système de gestion de tâches avec :
1. Classe Task avec priorité, statut, date
2. Liste de tâches avec opérations CRUD
3. Filtrage par statut/priorité
4. Tri par date/priorité
5. Sauvegarde/chargement JSON

**Fonctionnalités :**
- Ajouter/supprimer/modifier des tâches
- Marquer comme terminée
- Rechercher par mot-clé
- Statistiques (tâches terminées, en cours)

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Analyse de texte

Créez un analyseur de texte qui :
1. Compte les mots, caractères, lignes
2. Trouve les mots les plus fréquents
3. Calcule la longueur moyenne des mots
4. Identifie les mots uniques
5. Génère des statistiques

**Fonctionnalités :**
- Lecture depuis un fichier
- Analyse en temps réel
- Export des résultats
- Interface en ligne de commande

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Système de cache

Créez un système de cache avec :
1. Cache LRU (Least Recently Used)
2. Taille maximale configurable
3. Expiration automatique
4. Statistiques d'utilisation
5. Persistance sur disque

**Fonctionnalités :**
- Décorateur @cache
- Gestion de la mémoire
- Métriques de performance
- Configuration flexible

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Gestionnaire de tâches

```python
import json
from datetime import datetime
from enum import Enum

class Statut(Enum):
    A_FAIRE = "À faire"
    EN_COURS = "En cours"
    TERMINEE = "Terminée"

class Priorite(Enum):
    BASSE = 1
    MOYENNE = 2
    HAUTE = 3

class Task:
    def __init__(self, titre, description="", priorite=Priorite.MOYENNE):
        self.titre = titre
        self.description = description
        self.priorite = priorite
        self.statut = Statut.A_FAIRE
        self.date_creation = datetime.now()
        self.date_modification = datetime.now()
    
    def to_dict(self):
        return {
            'titre': self.titre,
            'description': self.description,
            'priorite': self.priorite.value,
            'statut': self.statut.value,
            'date_creation': self.date_creation.isoformat(),
            'date_modification': self.date_modification.isoformat()
        }
    
    @classmethod
    def from_dict(cls, data):
        task = cls(data['titre'], data['description'])
        task.priorite = Priorite(data['priorite'])
        task.statut = Statut(data['statut'])
        task.date_creation = datetime.fromisoformat(data['date_creation'])
        task.date_modification = datetime.fromisoformat(data['date_modification'])
        return task

class GestionnaireTaches:
    def __init__(self):
        self.taches = []
    
    def ajouter_tache(self, titre, description="", priorite=Priorite.MOYENNE):
        tache = Task(titre, description, priorite)
        self.taches.append(tache)
        return tache
    
    def supprimer_tache(self, index):
        if 0 <= index < len(self.taches):
            return self.taches.pop(index)
        return None
    
    def modifier_statut(self, index, statut):
        if 0 <= index < len(self.taches):
            self.taches[index].statut = statut
            self.taches[index].date_modification = datetime.now()
            return True
        return False
    
    def filtrer_par_statut(self, statut):
        return [t for t in self.taches if t.statut == statut]
    
    def filtrer_par_priorite(self, priorite):
        return [t for t in self.taches if t.priorite == priorite]
    
    def rechercher(self, mot_cle):
        mot_cle = mot_cle.lower()
        return [t for t in self.taches 
                if mot_cle in t.titre.lower() or mot_cle in t.description.lower()]
    
    def statistiques(self):
        total = len(self.taches)
        terminees = len(self.filtrer_par_statut(Statut.TERMINEE))
        en_cours = len(self.filtrer_par_statut(Statut.EN_COURS))
        a_faire = len(self.filtrer_par_statut(Statut.A_FAIRE))
        
        return {
            'total': total,
            'terminees': terminees,
            'en_cours': en_cours,
            'a_faire': a_faire,
            'pourcentage_terminees': (terminees / total * 100) if total > 0 else 0
        }
    
    def sauvegarder(self, fichier):
        data = [tache.to_dict() for tache in self.taches]
        with open(fichier, 'w', encoding='utf-8') as f:
            json.dump(data, f, ensure_ascii=False, indent=2)
    
    def charger(self, fichier):
        with open(fichier, 'r', encoding='utf-8') as f:
            data = json.load(f)
        self.taches = [Task.from_dict(item) for item in data]
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Analyse de texte

```python
import re
from collections import Counter
from pathlib import Path

class AnalyseurTexte:
    def __init__(self, texte=""):
        self.texte = texte
        self._analyse_cache = None
    
    def charger_fichier(self, chemin):
        """Charge un fichier texte."""
        try:
            with open(chemin, 'r', encoding='utf-8') as f:
                self.texte = f.read()
            self._analyse_cache = None
            return True
        except FileNotFoundError:
            print(f"Fichier {chemin} non trouvé")
            return False
        except Exception as e:
            print(f"Erreur lors de la lecture: {e}")
            return False
    
    def _analyser(self):
        """Effectue l'analyse du texte."""
        if self._analyse_cache:
            return self._analyse_cache
        
        # Nettoyage du texte
        texte_propre = re.sub(r'[^\w\s]', '', self.texte.lower())
        
        # Découpage en mots
        mots = texte_propre.split()
        
        # Statistiques
        stats = {
            'caracteres': len(self.texte),
            'caracteres_sans_espaces': len(self.texte.replace(' ', '')),
            'mots': len(mots),
            'lignes': len(self.texte.splitlines()),
            'mots_uniques': len(set(mots)),
            'longueur_moyenne_mots': sum(len(mot) for mot in mots) / len(mots) if mots else 0,
            'mots_frequents': Counter(mots).most_common(10),
            'densite_mots': len(mots) / len(self.texte) if self.texte else 0
        }
        
        self._analyse_cache = stats
        return stats
    
    def obtenir_statistiques(self):
        """Retourne toutes les statistiques."""
        return self._analyser()
    
    def mots_plus_frequents(self, n=10):
        """Retourne les n mots les plus fréquents."""
        stats = self._analyser()
        return stats['mots_frequents'][:n]
    
    def mots_uniques(self):
        """Retourne la liste des mots uniques."""
        texte_propre = re.sub(r'[^\w\s]', '', self.texte.lower())
        mots = texte_propre.split()
        return sorted(set(mots))
    
    def rechercher_mot(self, mot):
        """Compte les occurrences d'un mot."""
        texte_propre = re.sub(r'[^\w\s]', '', self.texte.lower())
        mots = texte_propre.split()
        return mots.count(mot.lower())
    
    def exporter_statistiques(self, fichier):
        """Exporte les statistiques en JSON."""
        import json
        
        stats = self._analyser()
        # Conversion des tuples en listes pour JSON
        stats['mots_frequents'] = [list(item) for item in stats['mots_frequents']]
        
        with open(fichier, 'w', encoding='utf-8') as f:
            json.dump(stats, f, ensure_ascii=False, indent=2)
    
    def afficher_resume(self):
        """Affiche un résumé des statistiques."""
        stats = self._analyser()
        
        print("=== ANALYSE DE TEXTE ===")
        print(f"Caractères: {stats['caracteres']}")
        print(f"Caractères (sans espaces): {stats['caracteres_sans_espaces']}")
        print(f"Mots: {stats['mots']}")
        print(f"Lignes: {stats['lignes']}")
        print(f"Mots uniques: {stats['mots_uniques']}")
        print(f"Longueur moyenne des mots: {stats['longueur_moyenne_mots']:.2f}")
        print(f"Densité de mots: {stats['densite_mots']:.2f}")
        
        print("\nMots les plus fréquents:")
        for mot, compte in stats['mots_frequents']:
            print(f"  {mot}: {compte}")

# Utilisation
if __name__ == "__main__":
    analyseur = AnalyseurTexte()
    
    # Exemple avec un texte
    texte_exemple = """
    Python est un langage de programmation de haut niveau.
    Il est connu pour sa simplicité et sa lisibilité.
    Python est utilisé dans de nombreux domaines.
    """
    
    analyseur.texte = texte_exemple
    analyseur.afficher_resume()
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Système de cache

```python
import time
import json
import pickle
from collections import OrderedDict
from functools import wraps
from typing import Any, Optional

class CacheLRU:
    def __init__(self, capacite_max: int = 100, expiration: int = 3600):
        self.capacite_max = capacite_max
        self.expiration = expiration  # en secondes
        self.cache = OrderedDict()
        self.stats = {
            'hits': 0,
            'misses': 0,
            'evictions': 0,
            'expirations': 0
        }
    
    def get(self, cle: str) -> Optional[Any]:
        """Récupère une valeur du cache."""
        if cle in self.cache:
            valeur, timestamp = self.cache[cle]
            
            # Vérifier l'expiration
            if time.time() - timestamp > self.expiration:
                del self.cache[cle]
                self.stats['expirations'] += 1
                self.stats['misses'] += 1
                return None
            
            # Déplacer à la fin (LRU)
            del self.cache[cle]
            self.cache[cle] = (valeur, timestamp)
            self.stats['hits'] += 1
            return valeur
        
        self.stats['misses'] += 1
        return None
    
    def put(self, cle: str, valeur: Any) -> None:
        """Ajoute une valeur au cache."""
        timestamp = time.time()
        
        # Supprimer si la clé existe déjà
        if cle in self.cache:
            del self.cache[cle]
        
        # Éviction si nécessaire
        while len(self.cache) >= self.capacite_max:
            self.cache.popitem(last=False)  # Supprime le plus ancien
            self.stats['evictions'] += 1
        
        self.cache[cle] = (valeur, timestamp)
    
    def clear(self) -> None:
        """Vide le cache."""
        self.cache.clear()
    
    def get_stats(self) -> dict:
        """Retourne les statistiques du cache."""
        total_requests = self.stats['hits'] + self.stats['misses']
        hit_rate = (self.stats['hits'] / total_requests * 100) if total_requests > 0 else 0
        
        return {
            **self.stats,
            'taille_actuelle': len(self.cache),
            'capacite_max': self.capacite_max,
            'hit_rate': hit_rate,
            'total_requests': total_requests
        }
    
    def save(self, fichier: str) -> None:
        """Sauvegarde le cache sur disque."""
        data = {
            'cache': dict(self.cache),
            'stats': self.stats,
            'capacite_max': self.capacite_max,
            'expiration': self.expiration
        }
        with open(fichier, 'wb') as f:
            pickle.dump(data, f)
    
    def load(self, fichier: str) -> None:
        """Charge le cache depuis le disque."""
        try:
            with open(fichier, 'rb') as f:
                data = pickle.load(f)
            
            self.cache = OrderedDict(data['cache'])
            self.stats = data['stats']
            self.capacite_max = data['capacite_max']
            self.expiration = data['expiration']
        except FileNotFoundError:
            print(f"Fichier {fichier} non trouvé")

# Décorateur pour utiliser le cache
def cache_lru(capacite_max: int = 100, expiration: int = 3600):
    """Décorateur pour mettre en cache les résultats de fonctions."""
    cache = CacheLRU(capacite_max, expiration)
    
    def decorateur(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # Créer une clé unique pour les arguments
            cle = str(args) + str(sorted(kwargs.items()))
            
            # Essayer de récupérer depuis le cache
            resultat = cache.get(cle)
            if resultat is not None:
                return resultat
            
            # Calculer le résultat
            resultat = func(*args, **kwargs)
            
            # Mettre en cache
            cache.put(cle, resultat)
            
            return resultat
        
        # Ajouter des méthodes au wrapper
        wrapper.cache = cache
        wrapper.clear_cache = cache.clear
        wrapper.get_stats = cache.get_stats
        wrapper.save_cache = cache.save
        wrapper.load_cache = cache.load
        
        return wrapper
    return decorateur

# Exemple d'utilisation
@cache_lru(capacite_max=50, expiration=1800)  # 30 minutes
def fibonacci(n):
    """Calcul récursif de Fibonacci (lent sans cache)."""
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Test du cache
if __name__ == "__main__":
    # Premier appel (calcul)
    print(fibonacci(30))
    
    # Deuxième appel (depuis le cache)
    print(fibonacci(30))
    
    # Statistiques
    stats = fibonacci.get_stats()
    print(f"Hit rate: {stats['hit_rate']:.2f}%")
    print(f"Hits: {stats['hits']}, Misses: {stats['misses']}")
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Programmation orientée objet** (classes, héritage)
2. **Gestion des erreurs** (exceptions avancées)
3. **Manipulation de fichiers** (I/O avancé)
4. **Projet final intégrateur**
5. **QCM de validation**

**Préparation :**
- Maîtrisez les collections
- Pratiquez les list comprehensions
- Testez les exercices proposés

---
src: './pages/08-gestion-erreurs.md'
--- 