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

```python
# Écriture de fichier
with open("test.txt", "w", encoding="utf-8") as fichier:
    fichier.write("Première ligne\n")
    fichier.write("Deuxième ligne\n")
    fichier.write("Troisième ligne\n")

# Lecture de fichier
with open("test.txt", "r", encoding="utf-8") as fichier:
    contenu = fichier.read()
    print(contenu)

# Lecture ligne par ligne
with open("test.txt", "r", encoding="utf-8") as fichier:
    for ligne in fichier:
        print(ligne.strip())

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

```python
# Mode lecture (par défaut)
with open("fichier.txt", "r") as f:
    contenu = f.read()

# Mode écriture (écrase le contenu)
with open("fichier.txt", "w") as f:
    f.write("Nouveau contenu")

# Mode ajout (ajoute à la fin)
with open("fichier.txt", "a") as f:
    f.write("\nLigne ajoutée")

# Mode lecture/écriture
with open("fichier.txt", "r+") as f:
    contenu = f.read()
    f.seek(0)  # Retour au début
    f.write("Début modifié\n" + contenu)

# Mode binaire
with open("image.jpg", "rb") as f:
    donnees = f.read()

# Mode texte avec encodage spécifique
with open("fichier.txt", "r", encoding="latin-1") as f:
    contenu = f.read()
```

---
layout: default
---

# Gestion des chemins de fichiers 🗂️

### Utilisation du module pathlib

```python
from pathlib import Path

# Création de chemins
chemin = Path("dossier/sous_dossier/fichier.txt")
chemin_absolu = Path.cwd() / "fichier.txt"

# Vérification d'existence
if chemin.exists():
    print("Le fichier existe")

# Création de dossiers
chemin_dossier = Path("nouveau_dossier")
chemin_dossier.mkdir(exist_ok=True)

# Création de dossiers parents
chemin_complet = Path("a/b/c/d/fichier.txt")
chemin_complet.parent.mkdir(parents=True, exist_ok=True)

# Navigation dans l'arborescence
for fichier in Path(".").glob("*.txt"):
    print(fichier)

# Recherche récursive
for fichier in Path(".").rglob("*.py"):
    print(fichier)

# Informations sur le fichier
chemin = Path("fichier.txt")
print(chemin.name)        # nom du fichier
print(chemin.suffix)      # extension
print(chemin.stem)        # nom sans extension
print(chemin.parent)      # dossier parent
```

---
layout: default
---

# Formats de fichiers courants 📊

### JSON, CSV, et autres formats

```python
import json
import csv
import pickle

# JSON
donnees = {
    "nom": "Alice",
    "age": 25,
    "ville": "Paris",
    "hobbies": ["lecture", "musique"]
}

# Écriture JSON
with open("donnees.json", "w", encoding="utf-8") as f:
    json.dump(donnees, f, indent=2, ensure_ascii=False)

# Lecture JSON
with open("donnees.json", "r", encoding="utf-8") as f:
    donnees_lues = json.load(f)

# CSV
personnes = [
    ["Nom", "Age", "Ville"],
    ["Alice", 25, "Paris"],
    ["Bob", 30, "Lyon"],
    ["Charlie", 35, "Marseille"]
]

# Écriture CSV
with open("personnes.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerows(personnes)

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

### Traitement des erreurs d'I/O

```python
import os
from pathlib import Path

def lire_fichier_securise(chemin):
    """Lit un fichier avec gestion d'erreurs."""
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
        print(f"Erreur d'encodage pour {chemin}")
        return None
    except Exception as e:
        print(f"Erreur inattendue : {e}")
        return None

def ecrire_fichier_securise(chemin, contenu):
    """Écrit un fichier avec gestion d'erreurs."""
    try:
        # Créer le dossier parent si nécessaire
        Path(chemin).parent.mkdir(parents=True, exist_ok=True)
        
        with open(chemin, "w", encoding="utf-8") as f:
            f.write(contenu)
        return True
    except PermissionError:
        print(f"Pas de permission pour écrire {chemin}")
        return False
    except Exception as e:
        print(f"Erreur d'écriture : {e}")
        return False

# Utilisation
contenu = lire_fichier_securise("fichier_inexistant.txt")
if contenu is None:
    print("Impossible de lire le fichier")

succes = ecrire_fichier_securise("nouveau/fichier.txt", "Contenu test")
if succes:
    print("Fichier écrit avec succès")
```

---
layout: default
---

# Context managers personnalisés 🔄

### Création de gestionnaires de ressources

```python
from contextlib import contextmanager
import time

@contextmanager
def gestionnaire_fichier(chemin, mode="r"):
    """Context manager pour la gestion de fichiers."""
    fichier = None
    try:
        fichier = open(chemin, mode, encoding="utf-8")
        yield fichier
    except Exception as e:
        print(f"Erreur lors de l'utilisation du fichier : {e}")
        raise
    finally:
        if fichier:
            fichier.close()

@contextmanager
def timer(nom_operation):
    """Context manager pour mesurer le temps."""
    debut = time.time()
    try:
        yield
    finally:
        fin = time.time()
        print(f"{nom_operation} : {fin - debut:.4f} secondes")

# Utilisation
with gestionnaire_fichier("test.txt", "w") as f:
    f.write("Test de context manager")

with timer("Lecture de fichier"):
    with gestionnaire_fichier("test.txt", "r") as f:
        contenu = f.read()
        print(contenu)
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Gestionnaire de logs

Créez un système de logging qui :
1. Écrit les logs dans des fichiers datés
2. Gère différents niveaux de log
3. Rotation automatique des fichiers
4. Formatage personnalisé

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Convertisseur de formats

Créez un convertisseur qui :
1. Lit des fichiers CSV
2. Convertit en JSON
3. Gère différents encodages
4. Validation des données

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Synchroniseur de fichiers

Créez un synchroniseur qui :
1. Compare deux dossiers
2. Copie les fichiers modifiés
3. Gère les conflits
4. Log des opérations

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Gestionnaire de logs

```python
import logging
from datetime import datetime
from pathlib import Path

class GestionnaireLogs:
    def __init__(self, dossier_logs="logs"):
        self.dossier_logs = Path(dossier_logs)
        self.dossier_logs.mkdir(exist_ok=True)
        self.setup_logging()
    
    def setup_logging(self):
        """Configure le système de logging."""
        # Créer le nom de fichier avec la date
        nom_fichier = f"app_{datetime.now().strftime('%Y%m%d')}.log"
        chemin_fichier = self.dossier_logs / nom_fichier
        
        # Configuration du logging
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler(chemin_fichier, encoding='utf-8'),
                logging.StreamHandler()
            ]
        )
    
    def log_info(self, message):
        """Log un message d'information."""
        logging.info(message)
    
    def log_error(self, message):
        """Log un message d'erreur."""
        logging.error(message)
    
    def log_warning(self, message):
        """Log un avertissement."""
        logging.warning(message)
    
    def nettoyer_anciens_logs(self, jours=30):
        """Supprime les logs plus anciens que X jours."""
        limite = datetime.now().timestamp() - (jours * 24 * 3600)
        
        for fichier in self.dossier_logs.glob("*.log"):
            if fichier.stat().st_mtime < limite:
                fichier.unlink()
                print(f"Supprimé : {fichier}")

# Utilisation
gestionnaire = GestionnaireLogs()
gestionnaire.log_info("Démarrage de l'application")
gestionnaire.log_warning("Attention : espace disque faible")
gestionnaire.log_error("Erreur de connexion à la base de données")
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Convertisseur de formats

```python
import csv
import json
import sys
from pathlib import Path

class ConvertisseurFormats:
    def __init__(self):
        self.encodages_supportes = ['utf-8', 'latin-1', 'cp1252']
    
    def detecter_encodage(self, fichier):
        """Détecte l'encodage d'un fichier."""
        for encodage in self.encodages_supportes:
            try:
                with open(fichier, 'r', encoding=encodage) as f:
                    f.read()
                return encodage
            except UnicodeDecodeError:
                continue
        return 'utf-8'  # Par défaut
    
    def csv_vers_json(self, fichier_csv, fichier_json):
        """Convertit un fichier CSV en JSON."""
        encodage = self.detecter_encodage(fichier_csv)
        
        try:
            with open(fichier_csv, 'r', encoding=encodage) as f:
                reader = csv.DictReader(f)
                donnees = list(reader)
            
            with open(fichier_json, 'w', encoding='utf-8') as f:
                json.dump(donnees, f, indent=2, ensure_ascii=False)
            
            print(f"Conversion réussie : {len(donnees)} enregistrements")
            return True
            
        except Exception as e:
            print(f"Erreur de conversion : {e}")
            return False
    
    def valider_csv(self, fichier_csv):
        """Valide la structure d'un fichier CSV."""
        try:
            with open(fichier_csv, 'r', encoding=self.detecter_encodage(fichier_csv)) as f:
                reader = csv.reader(f)
                en_tete = next(reader)
                
                if not en_tete:
                    return False, "Fichier vide"
                
                # Vérifier que toutes les lignes ont le même nombre de colonnes
                for i, ligne in enumerate(reader, 2):
                    if len(ligne) != len(en_tete):
                        return False, f"Ligne {i} : nombre de colonnes incorrect"
                
                return True, f"Fichier valide : {len(en_tete)} colonnes"
                
        except Exception as e:
            return False, f"Erreur de validation : {e}"

# Utilisation
convertisseur = ConvertisseurFormats()

# Validation
valide, message = convertisseur.valider_csv("donnees.csv")
print(message)

if valide:
    # Conversion
    succes = convertisseur.csv_vers_json("donnees.csv", "donnees.json")
    if succes:
        print("Conversion terminée")
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Synchroniseur de fichiers

```python
import shutil
import hashlib
from pathlib import Path
from datetime import datetime

class SynchroniseurFichiers:
    def __init__(self, source, destination):
        self.source = Path(source)
        self.destination = Path(destination)
        self.logs = []
    
    def calculer_hash(self, fichier):
        """Calcule le hash MD5 d'un fichier."""
        hash_md5 = hashlib.md5()
        with open(fichier, "rb") as f:
            for chunk in iter(lambda: f.read(4096), b""):
                hash_md5.update(chunk)
        return hash_md5.hexdigest()
    
    def fichier_modifie(self, source, dest):
        """Vérifie si un fichier a été modifié."""
        if not dest.exists():
            return True
        
        return self.calculer_hash(source) != self.calculer_hash(dest)
    
    def synchroniser(self):
        """Synchronise les dossiers."""
        if not self.source.exists():
            self.log("ERREUR", f"Dossier source inexistant : {self.source}")
            return False
        
        self.destination.mkdir(parents=True, exist_ok=True)
        
        fichiers_copies = 0
        erreurs = 0
        
        for fichier_source in self.source.rglob("*"):
            if fichier_source.is_file():
                # Calculer le chemin relatif
                chemin_relatif = fichier_source.relative_to(self.source)
                fichier_dest = self.destination / chemin_relatif
                
                try:
                    # Créer le dossier parent si nécessaire
                    fichier_dest.parent.mkdir(parents=True, exist_ok=True)
                    
                    # Vérifier si le fichier a été modifié
                    if self.fichier_modifie(fichier_source, fichier_dest):
                        shutil.copy2(fichier_source, fichier_dest)
                        self.log("COPIE", f"{chemin_relatif}")
                        fichiers_copies += 1
                    else:
                        self.log("IGNORE", f"{chemin_relatif} (inchangé)")
                        
                except Exception as e:
                    self.log("ERREUR", f"{chemin_relatif} : {e}")
                    erreurs += 1
        
        self.log("RESUME", f"Synchronisation terminée : {fichiers_copies} fichiers copiés, {erreurs} erreurs")
        return erreurs == 0
    
    def log(self, niveau, message):
        """Enregistre un message de log."""
        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        log_message = f"[{timestamp}] {niveau}: {message}"
        self.logs.append(log_message)
        print(log_message)
    
    def sauvegarder_logs(self, fichier_log):
        """Sauvegarde les logs dans un fichier."""
        with open(fichier_log, "w", encoding="utf-8") as f:
            for log in self.logs:
                f.write(log + "\n")

# Utilisation
synchroniseur = SynchroniseurFichiers("source", "destination")
succes = synchroniseur.synchroniser()

if succes:
    print("Synchronisation réussie")
else:
    print("Synchronisation avec erreurs")

synchroniseur.sauvegarder_logs("sync.log")
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Projet final intégrateur**
2. **QCM de validation**
3. **Débriefing et perspectives**

**Préparation :**
- Maîtrisez la manipulation de fichiers
- Pratiquez les context managers
- Testez les exercices proposés

---
src: './pages/11-projet-final.md'
--- 