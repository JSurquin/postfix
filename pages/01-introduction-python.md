---
layout: intro
routeAlias: 'introduction-python'
---

# Introduction à Python 🐍

### Le langage de programmation le plus populaire au monde

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Commençons l'aventure Python <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Pourquoi Python ? 🤔

### Un langage polyvalent et accessible

- **Simplicité** : Syntaxe claire et lisible
- **Polyvalence** : Web, data science, IA, automation...
- **Écosystème riche** : Plus de 400 000 packages
- **Communauté active** : Support et documentation excellents
- **Adoption massive** : Google, Netflix, Instagram, Spotify...

---
layout: two-cols
---

# Caractéristiques de Python

### Points forts du langage

- **Langage interprété** : Exécution directe du code
- **Typage dynamique** : Les types sont inférés automatiquement
- **Gestion automatique de la mémoire** : Pas de gestion manuelle
- **Multi-paradigme** : Procédural, orienté objet, fonctionnel
- **Cross-platform** : Fonctionne sur Windows, macOS, Linux

::right::

# Versions Python en 2025

### Python 3.12+ est la référence

- **Python 3.12** : Performance améliorée
- **Python 3.13** : Nouvelles fonctionnalités
- **Python 3.14** : En développement
- **Python 2** : Déprécié depuis 2020
- **Recommandation** : Python 3.12+ pour les nouveaux projets

---
layout: default
---

# Écosystème Python 🌍

### Les domaines d'application principaux

**Développement Web**
- Django, Flask, FastAPI
- Frameworks modernes et performants

**Data Science & IA**
- Pandas, NumPy, Matplotlib
- TensorFlow, PyTorch, scikit-learn

**Automation & DevOps**
- Ansible, SaltStack
- Scripts d'automatisation

**Systèmes embarqués**
- MicroPython, CircuitPython
- IoT et objets connectés

---
layout: default
---

# Philosophie Python 🎯

### Les principes fondateurs

```python
import this
```

**The Zen of Python (PEP 20)**

- Beautiful is better than ugly
- Explicit is better than implicit
- Simple is better than complex
- Complex is better than complicated
- Readability counts
- Special cases aren't special enough to break the rules

---
layout: default
---

# Premier programme Python 🚀

### Votre premier "Hello World"

```python
print("Hello, World!")
```

**Exécution :**
```bash
python hello.py
```

**Résultat :**
```
Hello, World!
```

---
layout: default
---

# Structure d'un programme Python 📝

### Organisation du code

```python
# 1. Imports
import os
import sys

# 2. Variables globales
VERSION = "1.0.0"

# 3. Fonctions
def main():
    print("Programme principal")
    
# 4. Point d'entrée
if __name__ == "__main__":
    main()
```

---
layout: default
---

# Bonnes pratiques Python 📋

### Conventions et style

**PEP 8 - Style Guide**
- Indentation : 4 espaces (pas de tab)
- Longueur de ligne : 79 caractères max
- Nommage : snake_case pour variables/fonctions
- Classes : PascalCase
- Constantes : UPPER_CASE

**Exemple :**
```python
def calculer_moyenne(nombres):
    """Calcule la moyenne d'une liste de nombres."""
    if not nombres:
        return 0
    return sum(nombres) / len(nombres)
```

---
layout: default
---

# Outils de développement 🛠️

### Environnement de développement moderne

**IDE recommandés :**
- PyCharm (JetBrains)
- VS Code avec extensions Python
- Jupyter Notebooks
- Spyder (scientifique)

**Outils essentiels :**
- pip (gestionnaire de packages)
- venv (environnements virtuels)
- black (formateur de code)
- flake8 (linter)
- pytest (tests)

---
layout: default
---

# Prochaines étapes 🎯

### Ce que nous allons voir

1. **Installation et configuration** de l'environnement
2. **Variables et types** de données
3. **Structures de contrôle** (conditions, boucles)
4. **Fonctions et modules**
5. **Collections** (listes, dictionnaires)
6. **Programmation orientée objet**
7. **Gestion des erreurs**
8. **Manipulation de fichiers**

---
layout: default
---

# Questions ? 🤔

### Avant de passer à l'installation

- Avez-vous déjà programmé dans d'autres langages ?
- Quels sont vos objectifs avec Python ?
- Avez-vous des questions sur l'écosystème Python ?
- Connaissez-vous des frameworks ou domaines d'application ?

---
src: './pages/02-installation-environnement.md'
--- 