---
layout: intro
routeAlias: 'fichiers-io'
---

# Manipulation de Fichiers 📁

### Entrées/Sorties avancées en Python

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Maîtrisons les I/O <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Lecture et écriture de fichiers 📝

### Opérations de base sur les fichiers

**Opérations principales :**
- Écriture de fichiers
- Lecture de fichiers
- Lecture ligne par ligne
- Lecture de toutes les lignes

---

# Écriture de Fichier

```python
# Écriture de fichier
with open("test.txt", "w", encoding="utf-8") as fichier:
    fichier.write("Première ligne\n")
    fichier.write("Deuxième ligne\n")
    fichier.write("Troisième ligne\n")
```

---

# Lecture de Fichier

```python
# Lecture de fichier
with open("test.txt", "r", encoding="utf-8") as fichier:
    contenu = fichier.read()
    print(contenu)
```

---

# Lecture Ligne par Ligne

```python
# Lecture ligne par ligne
with open("test.txt", "r", encoding="utf-8") as fichier:
    for ligne in fichier:
        print(ligne.strip())
```

---

# Lecture de Toutes les Lignes

```python
# Lecture de toutes les lignes
with open("test.txt", "r", encoding="utf-8") as fichier:
    lignes = fichier.readlines()
    print(lignes)
```

---
layout: default
---

# Modes d'ouverture de fichiers 🔧

### Différents modes d'accès

**Modes disponibles :**
- `r` : Lecture (par défaut)
- `w` : Écriture (écrase le contenu)
- `a` : Ajout (ajoute à la fin)
- `r+` : Lecture/écriture
- `rb` : Lecture binaire
- `wb` : Écriture binaire

---

# Mode Lecture

```python
# Mode lecture (par défaut)
with open("fichier.txt", "r") as f:
    contenu = f.read()
```

---

# Mode Écriture

```python
# Mode écriture (écrase le contenu)
with open("fichier.txt", "w") as f:
    f.write("Nouveau contenu")
```

---

# Mode Ajout

```python
# Mode ajout (ajoute à la fin)
with open("fichier.txt", "a") as f:
    f.write("\nLigne ajoutée")
```

---

# Mode Lecture/Écriture

```python
# Mode lecture/écriture
with open("fichier.txt", "r+") as f:
    contenu = f.read()
    f.seek(0)  # Retour au début
    f.write("Début modifié\n" + contenu)
```

---

# Mode Binaire

```python
# Mode binaire
with open("image.jpg", "rb") as f:
    donnees = f.read()
```

---

# Mode avec Encodage

```python
# Mode texte avec encodage spécifique
with open("fichier.txt", "r", encoding="latin-1") as f:
    contenu = f.read()
```

---
layout: default
---

# Gestion des chemins de fichiers 🗂️

### Utilisation du module pathlib

**Avantages de pathlib :**
- Interface orientée objet
- Gestion cross-platform
- Méthodes intuitives
- Manipulation de chemins

---

# Création de Chemins

```python
from pathlib import Path

# Création de chemins
chemin = Path("dossier/sous_dossier/fichier.txt")
chemin_absolu = Path.cwd() / "fichier.txt"
```

---

# Vérification d'Existence

```python
# Vérification d'existence
if chemin.exists():
    print("Le fichier existe")
```

---

# Manipulation de Chemins

```python
# Manipulation de chemins
chemin = Path("dossier/sous_dossier/fichier.txt")

# Propriétés du chemin
print(chemin.name)      # nom du fichier
print(chemin.suffix)    # extension
print(chemin.stem)      # nom sans extension
print(chemin.parent)    # dossier parent
print(chemin.parts)     # parties du chemin
```

---

# Création de Dossiers

```python
# Création de dossiers
nouveau_dossier = Path("nouveau_dossier")
nouveau_dossier.mkdir(exist_ok=True)

# Création de dossiers imbriqués
dossier_imbrique = Path("dossier1/dossier2/dossier3")
dossier_imbrique.mkdir(parents=True, exist_ok=True)
```

---
layout: default
---

# Globbing et recherche 🔍

### Recherche de fichiers avec des patterns

**Patterns de recherche :**
- `*` : N'importe quel caractère
- `**` : Recherche récursive
- `?` : Un caractère unique
- `[abc]` : Caractères spécifiques

---

# Recherche Simple

```python
from pathlib import Path

# Recherche de fichiers
dossier = Path(".")
fichiers_txt = list(dossier.glob("*.txt"))
print(fichiers_txt)

# Recherche avec pattern
fichiers_py = list(dossier.glob("*.py"))
print(fichiers_py)
```

---

# Recherche Récursive

```python
# Recherche récursive
tous_fichiers = list(dossier.rglob("*.txt"))
print(tous_fichiers)

# Recherche avec plusieurs patterns
fichiers_divers = list(dossier.glob("*.{txt,py,md}"))
print(fichiers_divers)
```

---
layout: default
---

# Formats de fichiers courants 📊

### Manipulation de formats spécifiques

**Formats supportés :**
- JSON : Données structurées
- CSV : Données tabulaires
- Pickle : Sérialisation Python
- XML : Données hiérarchiques

---

# Manipulation JSON

```python
import json

# Écriture JSON
donnees = {
    "nom": "Alice",
    "age": 25,
    "ville": "Paris",
    "hobbies": ["lecture", "musique"]
}

with open("donnees.json", "w", encoding="utf-8") as f:
    json.dump(donnees, f, indent=2, ensure_ascii=False)
```

---

# Lecture JSON

```python
# Lecture JSON
with open("donnees.json", "r", encoding="utf-8") as f:
    donnees_lues = json.load(f)

print(donnees_lues["nom"])
print(donnees_lues["hobbies"])
```

---

# Manipulation CSV

```python
import csv

# Écriture CSV
donnees = [
    ["nom", "age", "ville"],
    ["Alice", 25, "Paris"],
    ["Bob", 30, "Lyon"],
    ["Charlie", 35, "Marseille"]
]

with open("personnes.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerows(donnees)
```

---

# Lecture CSV

```python
# Lecture CSV
with open("personnes.csv", "r", encoding="utf-8") as f:
    reader = csv.reader(f)
    for ligne in reader:
        print(ligne)
```

---
layout: default
---

# Gestion d'erreurs pour les fichiers ⚠️

### Gestion robuste des opérations de fichiers

**Erreurs courantes :**
- `FileNotFoundError` : Fichier inexistant
- `PermissionError` : Permissions insuffisantes
- `UnicodeDecodeError` : Problème d'encodage
- `OSError` : Erreurs système

---

# Gestion d'Erreurs de Base

```python
# Gestion d'erreurs de base
try:
    with open("fichier_inexistant.txt", "r") as f:
        contenu = f.read()
except FileNotFoundError:
    print("Le fichier n'existe pas")
except PermissionError:
    print("Pas de permission pour lire le fichier")
except UnicodeDecodeError:
    print("Problème d'encodage")
except Exception as e:
    print(f"Erreur inattendue : {e}")
```

---

# Fonction de Lecture Sécurisée

```python
def lire_fichier_securise(chemin):
    """Lit un fichier avec gestion d'erreurs complète"""
    try:
        with open(chemin, "r", encoding="utf-8") as f:
            return f.read()
    except FileNotFoundError:
        print(f"Fichier {chemin} non trouvé")
        return None
    except PermissionError:
        print(f"Pas de permission pour lire {chemin}")
        return None
    except UnicodeDecodeError:
        print(f"Problème d'encodage pour {chemin}")
        return None
    except Exception as e:
        print(f"Erreur inattendue : {e}")
        return None
```

---
layout: default
---

# Context managers personnalisés 🔧

### Création de context managers pour les fichiers

**Avantages :**
- Gestion automatique des ressources
- Code plus propre
- Gestion d'erreurs centralisée
- Réutilisabilité

---

# Context Manager Simple

```python
class GestionnaireFichier:
    def __init__(self, nom_fichier, mode="r"):
        self.nom_fichier = nom_fichier
        self.mode = mode
        self.fichier = None
    
    def __enter__(self):
        self.fichier = open(self.nom_fichier, self.mode, encoding="utf-8")
        return self.fichier
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.fichier:
            self.fichier.close()
        return False  # Propager l'exception
```

---

# Utilisation du Context Manager

```python
# Utilisation
with GestionnaireFichier("test.txt", "w") as f:
    f.write("Test de context manager")

with GestionnaireFichier("test.txt", "r") as f:
    contenu = f.read()
    print(contenu)
```

---
layout: default
---

# Context Manager avec contextlib 🛠️

### Utilisation du décorateur contextmanager

**Avantages :**
- Syntaxe plus simple
- Moins de code boilerplate
- Plus lisible

---

# Context Manager avec Décorateur

```python
from contextlib import contextmanager

@contextmanager
def gestionnaire_fichier(nom_fichier, mode="r"):
    fichier = open(nom_fichier, mode, encoding="utf-8")
    try:
        yield fichier
    finally:
        fichier.close()
```

---

# Utilisation du Context Manager Décoré

```python
# Utilisation
with gestionnaire_fichier("test.txt", "w") as f:
    f.write("Test avec contextmanager")

with gestionnaire_fichier("test.txt", "r") as f:
    contenu = f.read()
    print(contenu)
```

---
layout: default
---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez une fonction qui lit un fichier CSV et retourne les données sous forme de liste de dictionnaires.

**Exercice 2 :** Implémentez un système de logging qui écrit les logs dans un fichier avec rotation automatique.

**Exercice 3 :** Créez un context manager personnalisé pour gérer les sauvegardes automatiques de fichiers.

---

# Solutions des Exercices 💡

### Exercice 1 - Lecture CSV en Dictionnaires

```python
import csv
from pathlib import Path

def lire_csv_en_dict(chemin_fichier):
    """Lit un fichier CSV et retourne une liste de dictionnaires"""
    if not Path(chemin_fichier).exists():
        return []
    
    with open(chemin_fichier, "r", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        return list(reader)

# Test
donnees = lire_csv_en_dict("personnes.csv")
for personne in donnees:
    print(f"{personne['nom']} a {personne['age']} ans")
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Système de Logging

```python
import logging
from pathlib import Path
from datetime import datetime

class LoggerFichier:
    def __init__(self, nom_fichier, niveau=logging.INFO):
        self.nom_fichier = nom_fichier
        self.logger = logging.getLogger(nom_fichier)
        self.logger.setLevel(niveau)
        
        # Handler pour fichier
        handler = logging.FileHandler(nom_fichier, encoding="utf-8")
        formatter = logging.Formatter(
            '%(asctime)s - %(levelname)s - %(message)s'
        )
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)
    
    def info(self, message):
        self.logger.info(message)
    
    def error(self, message):
        self.logger.error(message)
    
    def warning(self, message):
        self.logger.warning(message)

# Utilisation
logger = LoggerFichier("app.log")
logger.info("Application démarrée")
logger.error("Une erreur s'est produite")
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Context Manager de Sauvegarde

```python
import shutil
from pathlib import Path
from datetime import datetime

class SauvegardeFichier:
    def __init__(self, chemin_fichier):
        self.chemin_fichier = Path(chemin_fichier)
        self.chemin_backup = None
    
    def __enter__(self):
        # Créer une sauvegarde
        if self.chemin_fichier.exists():
            timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
            self.chemin_backup = self.chemin_fichier.with_suffix(f".backup_{timestamp}{self.chemin_fichier.suffix}")
            shutil.copy2(self.chemin_fichier, self.chemin_backup)
        return self.chemin_fichier
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        # En cas d'erreur, restaurer la sauvegarde
        if exc_type is not None and self.chemin_backup and self.chemin_backup.exists():
            shutil.copy2(self.chemin_backup, self.chemin_fichier)
            print(f"Sauvegarde restaurée : {self.chemin_backup}")
        return False

# Utilisation
with SauvegardeFichier("important.txt") as f:
    f.write_text("Nouveau contenu important")
```

---

# Points Clés à Retenir 🎯

### Résumé de la manipulation de fichiers

**1. Opérations de base :**
- Lecture et écriture de fichiers
- Modes d'ouverture appropriés
- Gestion de l'encodage

**2. Gestion des chemins :**
- Module `pathlib` pour les chemins
- Manipulation cross-platform
- Recherche avec globbing

**3. Formats de fichiers :**
- JSON pour les données structurées
- CSV pour les données tabulaires
- Gestion d'encodage appropriée

**4. Gestion d'erreurs :**
- Gestion robuste des exceptions
- Context managers personnalisés
- Sauvegardes automatiques

**5. Bonnes pratiques :**
- Utilisation de `with` pour les fichiers
- Gestion appropriée des encodages
- Context managers pour la réutilisabilité

**Prêt pour le projet final !** 🚀 