---
layout: intro
routeAlias: 'installation-environnement'
---

# Installation et Environnement ⚙️

### Configuration de votre environnement Python

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Configurons votre environnement <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Installation de Python 🐍

### Méthodes d'installation selon votre OS

**Windows :**
- Téléchargement depuis python.org
- Installation avec l'option "Add to PATH"
- Vérification : `python --version`

**macOS :**
- Homebrew : `brew install python`
- Ou téléchargement depuis python.org
- Vérification : `python3 --version`

**Linux :**
- Ubuntu/Debian : `sudo apt install python3`
- CentOS/RHEL : `sudo yum install python3`
- Vérification : `python3 --version`

---
layout: default
---

# Vérification de l'installation ✅

### Test de votre installation Python

```bash
# Vérifier la version
python --version
# ou
python3 --version

# Vérifier l'emplacement
which python
# ou
which python3

# Tester l'interpréteur
python
```

**Sortie attendue :**
```
Python 3.12.0 (main, Oct 15 2024, 10:00:00)
[GCC 9.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

---
layout: default
---

# Environnements virtuels 🌍

### Isolation des projets Python

**Pourquoi utiliser des environnements virtuels ?**

- **Isolation** : Éviter les conflits entre projets
- **Reproductibilité** : Même environnement partout
- **Gestion des dépendances** : Packages spécifiques par projet
- **Sécurité** : Éviter les conflits de versions

**Création d'un environnement virtuel :**
```bash
# Créer un environnement
python -m venv mon_projet

# Activer l'environnement
# Windows
mon_projet\Scripts\activate

# macOS/Linux
source mon_projet/bin/activate
```

---
layout: default
---

# Gestionnaire de packages pip 📦

### Installation et gestion des packages

**Vérification de pip :**
```bash
pip --version
# ou
pip3 --version
```

**Commandes pip essentielles :**
```bash
# Installer un package
pip install nom_du_package

# Installer depuis un fichier requirements.txt
pip install -r requirements.txt

# Lister les packages installés
pip list

# Désinstaller un package
pip uninstall nom_du_package
```

---
layout: default
---

# IDE et éditeurs de code 💻

### Environnements de développement recommandés

**VS Code (Recommandé pour débutants)**
- Gratuit et open source
- Extensions Python excellentes
- Intégration Git native
- Débogueur intégré

**PyCharm (JetBrains)**
- IDE complet et professionnel
- Version Community gratuite
- Analyse de code avancée
- Refactoring intelligent

**Jupyter Notebooks**
- Idéal pour data science
- Code interactif
- Documentation intégrée
- Visualisations

---
layout: default
---

# Configuration VS Code 🎨

### Extensions Python essentielles

**Extensions recommandées :**
- Python (Microsoft)
- Pylance (Microsoft)
- Python Indent
- Python Docstring Generator
- Python Test Explorer

**Configuration settings.json :**
```json
{
    "python.defaultInterpreterPath": "./venv/bin/python",
    "python.linting.enabled": true,
    "python.linting.pylintEnabled": true,
    "python.formatting.provider": "black"
}
```

---
layout: default
---

# Outils de développement 🛠️

### Outils essentiels pour Python

**Formateur de code :**
```bash
# Installation de black
pip install black

# Formater un fichier
black mon_fichier.py

# Formater tout un dossier
black .
```

**Linter (analyse statique) :**
```bash
# Installation de flake8
pip install flake8

# Analyser un fichier
flake8 mon_fichier.py
```

---
layout: default
---

# Structure de projet 📁

### Organisation recommandée

```
mon_projet/
├── venv/                 # Environnement virtuel
├── src/                  # Code source
│   ├── __init__.py
│   ├── main.py
│   └── utils.py
├── tests/                # Tests unitaires
│   ├── __init__.py
│   └── test_main.py
├── docs/                 # Documentation
├── requirements.txt       # Dépendances
├── README.md            # Documentation
└── .gitignore           # Fichiers à ignorer
```

---
layout: default
---

# Premier projet Python 🚀

### Création d'un projet complet

**Étapes de création :**
```bash
# 1. Créer le dossier du projet
mkdir mon_premier_projet
cd mon_premier_projet

# 2. Créer l'environnement virtuel
python -m venv venv

# 3. Activer l'environnement
source venv/bin/activate  # Linux/macOS
# ou
venv\Scripts\activate     # Windows

# 4. Créer la structure
mkdir src tests docs
touch src/__init__.py
touch requirements.txt
touch README.md
```

---
layout: default
---

# Fichier requirements.txt 📋

### Gestion des dépendances

**Création du fichier :**
```txt
# requirements.txt
requests==2.31.0
pandas==2.1.0
numpy==1.24.0
pytest==7.4.0
black==23.0.0
flake8==6.0.0
```

**Installation des dépendances :**
```bash
pip install -r requirements.txt
```

**Génération du fichier :**
```bash
pip freeze > requirements.txt
```

---
layout: default
---

# Test de l'environnement ✅

### Vérification complète

**Script de test :**
```python
# test_environment.py
import sys
import platform

print(f"Python version: {sys.version}")
print(f"Platform: {platform.system()} {platform.release()}")
print(f"Python executable: {sys.executable}")

# Test des imports courants
try:
    import requests
    print("✓ requests installé")
except ImportError:
    print("✗ requests non installé")

try:
    import pandas
    print("✓ pandas installé")
except ImportError:
    print("✗ pandas non installé")
```

---
layout: default
---

# Problèmes courants 🔧

### Solutions aux erreurs fréquentes

**"python: command not found"**
- Vérifier l'installation
- Ajouter Python au PATH
- Utiliser `python3` au lieu de `python`

**"pip: command not found"**
- Installer pip séparément
- Utiliser `pip3` au lieu de `pip`
- Vérifier l'installation de Python

**Erreurs de permissions**
- Utiliser `sudo` sur Linux/macOS
- Exécuter en tant qu'administrateur sur Windows
- Utiliser des environnements virtuels

---
layout: default
---

# Ressources utiles 📚

### Documentation et aide

**Documentation officielle :**
- docs.python.org
- pypi.org (packages)
- realpython.com (tutoriels)

**Communauté :**
- Stack Overflow
- Reddit r/Python
- Discord Python

**Outils en ligne :**
- repl.it (exécution en ligne)
- pythonanywhere.com (hébergement)
- jupyter.org (notebooks)

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Premiers pas** avec Python
2. **Variables et types** de données
3. **Structures de contrôle**
4. **Fonctions et modules**
5. **Collections** avancées

**Préparation :**
- Assurez-vous que Python est installé
- Configurez votre IDE
- Créez votre premier environnement virtuel

---
src: './pages/03-premiers-pas.md'
--- 