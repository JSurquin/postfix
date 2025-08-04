---
layout: intro
routeAlias: 'projet-final-initiation'
---

# Projet Final - Initiation 🎯

### Application complète intégrant tous les concepts

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Créons notre projet final <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Système de Gestion de Contacts 📞

### Projet final intégrateur

**Objectifs du projet :**
- Créer une application de gestion de contacts
- Intégrer tous les concepts appris
- Interface en ligne de commande
- Persistance des données
- Validation et gestion d'erreurs

**Fonctionnalités requises :**
1. Ajouter/supprimer/modifier des contacts
2. Rechercher des contacts
3. Sauvegarder/charger depuis un fichier
4. Validation des données
5. Interface utilisateur intuitive

---
layout: default
---

# Architecture du projet 🏗️

### Structure des fichiers

```
gestionnaire_contacts/
├── main.py              # Point d'entrée
├── models/
│   ├── __init__.py
│   └── contact.py       # Classe Contact
├── services/
│   ├── __init__.py
│   ├── gestionnaire.py  # Logique métier
│   └── validation.py    # Validation des données
├── utils/
│   ├── __init__.py
│   └── fichier.py       # Gestion des fichiers
├── data/
│   └── contacts.json    # Données persistantes
└── README.md
```

---
layout: default
---

# Classe Contact 📋

### Modèle de données

```python
# models/contact.py
from datetime import datetime
import re

class Contact:
    def __init__(self, nom, telephone, email=None, adresse=None):
        self.nom = nom
        self.telephone = telephone
        self.email = email
        self.adresse = adresse
        self.date_creation = datetime.now()
    
    def __str__(self):
        return f"{self.nom} - {self.telephone}"
    
    def to_dict(self):
        """Convertit le contact en dictionnaire pour JSON."""
        return {
            'nom': self.nom,
            'telephone': self.telephone,
            'email': self.email,
            'adresse': self.adresse,
            'date_creation': self.date_creation.isoformat()
        }
    
    @classmethod
    def from_dict(cls, data):
        """Crée un contact depuis un dictionnaire."""
        contact = cls(data['nom'], data['telephone'])
        contact.email = data.get('email')
        contact.adresse = data.get('adresse')
        contact.date_creation = datetime.fromisoformat(data['date_creation'])
        return contact
```

---
layout: default
---

# Service de validation ✅

### Validation des données

```python
# services/validation.py
import re

class ValidateurContact:
    @staticmethod
    def valider_nom(nom):
        """Valide un nom de contact."""
        if not nom or len(nom.strip()) < 2:
            return False, "Le nom doit contenir au moins 2 caractères"
        if not nom.replace(" ", "").isalpha():
            return False, "Le nom ne doit contenir que des lettres"
        return True, nom.strip()
    
    @staticmethod
    def valider_telephone(telephone):
        """Valide un numéro de téléphone français."""
        pattern = r'^(?:(?:\+|00)33|0)\s*[1-9](?:[\s.-]*\d{2}){4}$'
        if not re.match(pattern, telephone):
            return False, "Format de téléphone invalide"
        return True, telephone
    
    @staticmethod
    def valider_email(email):
        """Valide une adresse email."""
        if not email:  # Email optionnel
            return True, None
        pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
        if not re.match(pattern, email):
            return False, "Format d'email invalide"
        return True, email
    
    @classmethod
    def valider_contact(cls, nom, telephone, email=None, adresse=None):
        """Valide toutes les données d'un contact."""
        # Validation du nom
        valide, resultat = cls.valider_nom(nom)
        if not valide:
            return False, resultat
        
        # Validation du téléphone
        valide, resultat = cls.valider_telephone(telephone)
        if not valide:
            return False, resultat
        
        # Validation de l'email
        valide, resultat = cls.valider_email(email)
        if not valide:
            return False, resultat
        
        return True, {
            'nom': nom.strip(),
            'telephone': telephone,
            'email': resultat,
            'adresse': adresse.strip() if adresse else None
        }
```

---
layout: default
---

# Gestionnaire de contacts 🔧

### Logique métier

```python
# services/gestionnaire.py
from models.contact import Contact
from services.validation import ValidateurContact
import json
from pathlib import Path

class GestionnaireContacts:
    def __init__(self, fichier_donnees="data/contacts.json"):
        self.fichier_donnees = Path(fichier_donnees)
        self.contacts = []
        self.charger_contacts()
    
    def ajouter_contact(self, nom, telephone, email=None, adresse=None):
        """Ajoute un nouveau contact."""
        # Validation des données
        valide, resultat = ValidateurContact.valider_contact(nom, telephone, email, adresse)
        if not valide:
            return False, resultat
        
        # Création du contact
        contact = Contact(
            resultat['nom'],
            resultat['telephone'],
            resultat['email'],
            resultat['adresse']
        )
        
        # Vérification de doublon
        if self.contact_existe(contact.telephone):
            return False, "Un contact avec ce numéro existe déjà"
        
        self.contacts.append(contact)
        self.sauvegarder_contacts()
        return True, f"Contact {contact.nom} ajouté avec succès"
    
    def supprimer_contact(self, telephone):
        """Supprime un contact par son téléphone."""
        for i, contact in enumerate(self.contacts):
            if contact.telephone == telephone:
                nom = contact.nom
                del self.contacts[i]
                self.sauvegarder_contacts()
                return True, f"Contact {nom} supprimé"
        return False, "Contact non trouvé"
    
    def rechercher_contacts(self, terme):
        """Recherche des contacts par nom ou téléphone."""
        terme = terme.lower()
        resultats = []
        
        for contact in self.contacts:
            if (terme in contact.nom.lower() or 
                terme in contact.telephone or
                (contact.email and terme in contact.email.lower())):
                resultats.append(contact)
        
        return resultats
    
    def contact_existe(self, telephone):
        """Vérifie si un contact existe déjà."""
        return any(c.telephone == telephone for c in self.contacts)
    
    def lister_contacts(self):
        """Retourne tous les contacts."""
        return self.contacts.copy()
```

---
layout: default
---

# Gestion des fichiers 📁

### Persistance des données

```python
# utils/fichier.py
import json
from pathlib import Path

class GestionnaireFichiers:
    @staticmethod
    def sauvegarder_json(donnees, fichier):
        """Sauvegarde des données en JSON."""
        try:
            # Créer le dossier parent si nécessaire
            Path(fichier).parent.mkdir(parents=True, exist_ok=True)
            
            with open(fichier, 'w', encoding='utf-8') as f:
                json.dump(donnees, f, indent=2, ensure_ascii=False)
            return True, "Sauvegarde réussie"
        except Exception as e:
            return False, f"Erreur de sauvegarde : {e}"
    
    @staticmethod
    def charger_json(fichier):
        """Charge des données depuis un fichier JSON."""
        try:
            if not Path(fichier).exists():
                return True, []
            
            with open(fichier, 'r', encoding='utf-8') as f:
                donnees = json.load(f)
            return True, donnees
        except Exception as e:
            return False, f"Erreur de chargement : {e}"

# Méthodes dans GestionnaireContacts
def sauvegarder_contacts(self):
    """Sauvegarde les contacts dans le fichier."""
    donnees = [contact.to_dict() for contact in self.contacts]
    succes, message = GestionnaireFichiers.sauvegarder_json(
        donnees, self.fichier_donnees
    )
    if not succes:
        print(f"Erreur de sauvegarde : {message}")

def charger_contacts(self):
    """Charge les contacts depuis le fichier."""
    succes, donnees = GestionnaireFichiers.charger_json(self.fichier_donnees)
    if succes:
        self.contacts = [Contact.from_dict(contact_data) for contact_data in donnees]
    else:
        print(f"Erreur de chargement : {donnees}")
        self.contacts = []
```

---
layout: default
---

# Interface utilisateur 🖥️

### Interface en ligne de commande

```python
# main.py
from services.gestionnaire import GestionnaireContacts

class InterfaceUtilisateur:
    def __init__(self):
        self.gestionnaire = GestionnaireContacts()
    
    def afficher_menu(self):
        """Affiche le menu principal."""
        print("\n=== GESTIONNAIRE DE CONTACTS ===")
        print("1. Ajouter un contact")
        print("2. Rechercher des contacts")
        print("3. Lister tous les contacts")
        print("4. Supprimer un contact")
        print("5. Quitter")
        print("================================")
    
    def ajouter_contact(self):
        """Interface pour ajouter un contact."""
        print("\n--- Ajouter un contact ---")
        
        nom = input("Nom : ").strip()
        telephone = input("Téléphone : ").strip()
        email = input("Email (optionnel) : ").strip() or None
        adresse = input("Adresse (optionnel) : ").strip() or None
        
        succes, message = self.gestionnaire.ajouter_contact(nom, telephone, email, adresse)
        print(f"\n{message}")
    
    def rechercher_contacts(self):
        """Interface pour rechercher des contacts."""
        print("\n--- Rechercher des contacts ---")
        terme = input("Terme de recherche : ").strip()
        
        resultats = self.gestionnaire.rechercher_contacts(terme)
        
        if resultats:
            print(f"\n{len(resultats)} contact(s) trouvé(s) :")
            for contact in resultats:
                print(f"  - {contact}")
        else:
            print("Aucun contact trouvé")
    
    def lister_contacts(self):
        """Interface pour lister tous les contacts."""
        contacts = self.gestionnaire.lister_contacts()
        
        if contacts:
            print(f"\n{len(contacts)} contact(s) :")
            for contact in contacts:
                print(f"  - {contact}")
        else:
            print("Aucun contact enregistré")
    
    def supprimer_contact(self):
        """Interface pour supprimer un contact."""
        print("\n--- Supprimer un contact ---")
        telephone = input("Téléphone du contact à supprimer : ").strip()
        
        succes, message = self.gestionnaire.supprimer_contact(telephone)
        print(f"\n{message}")
    
    def executer(self):
        """Boucle principale de l'application."""
        while True:
            self.afficher_menu()
            choix = input("Votre choix (1-5) : ").strip()
            
            if choix == "1":
                self.ajouter_contact()
            elif choix == "2":
                self.rechercher_contacts()
            elif choix == "3":
                self.lister_contacts()
            elif choix == "4":
                self.supprimer_contact()
            elif choix == "5":
                print("Au revoir !")
                break
            else:
                print("Choix invalide")

if __name__ == "__main__":
    app = InterfaceUtilisateur()
    app.executer()
```

---
layout: default
---

# Tests et validation 🧪

### Tests unitaires pour le projet

```python
# tests/test_gestionnaire.py
import unittest
from services.gestionnaire import GestionnaireContacts
from services.validation import ValidateurContact

class TestGestionnaireContacts(unittest.TestCase):
    def setUp(self):
        """Initialisation avant chaque test."""
        self.gestionnaire = GestionnaireContacts("test_contacts.json")
    
    def test_ajouter_contact_valide(self):
        """Test d'ajout d'un contact valide."""
        succes, message = self.gestionnaire.ajouter_contact(
            "Alice", "0123456789", "alice@example.com"
        )
        self.assertTrue(succes)
        self.assertEqual(len(self.gestionnaire.contacts), 1)
    
    def test_ajouter_contact_invalide(self):
        """Test d'ajout d'un contact invalide."""
        succes, message = self.gestionnaire.ajouter_contact(
            "A", "123", "email_invalide"
        )
        self.assertFalse(succes)
        self.assertEqual(len(self.gestionnaire.contacts), 0)
    
    def test_rechercher_contacts(self):
        """Test de recherche de contacts."""
        # Ajouter des contacts de test
        self.gestionnaire.ajouter_contact("Alice", "0123456789")
        self.gestionnaire.ajouter_contact("Bob", "0987654321")
        
        # Rechercher
        resultats = self.gestionnaire.rechercher_contacts("Alice")
        self.assertEqual(len(resultats), 1)
        self.assertEqual(resultats[0].nom, "Alice")
    
    def test_supprimer_contact(self):
        """Test de suppression d'un contact."""
        # Ajouter un contact
        self.gestionnaire.ajouter_contact("Alice", "0123456789")
        
        # Supprimer
        succes, message = self.gestionnaire.supprimer_contact("0123456789")
        self.assertTrue(succes)
        self.assertEqual(len(self.gestionnaire.contacts), 0)

class TestValidateurContact(unittest.TestCase):
    def test_valider_nom_valide(self):
        """Test de validation d'un nom valide."""
        valide, resultat = ValidateurContact.valider_nom("Alice")
        self.assertTrue(valide)
    
    def test_valider_nom_invalide(self):
        """Test de validation d'un nom invalide."""
        valide, resultat = ValidateurContact.valider_nom("A")
        self.assertFalse(valide)
    
    def test_valider_telephone_valide(self):
        """Test de validation d'un téléphone valide."""
        valide, resultat = ValidateurContact.valider_telephone("0123456789")
        self.assertTrue(valide)
    
    def test_valider_telephone_invalide(self):
        """Test de validation d'un téléphone invalide."""
        valide, resultat = ValidateurContact.valider_telephone("123")
        self.assertFalse(valide)

if __name__ == "__main__":
    unittest.main()
```

---
layout: default
---

# Améliorations possibles 🚀

### Extensions du projet

**Fonctionnalités avancées :**
1. **Interface graphique** avec tkinter ou PyQt
2. **Base de données** SQLite au lieu de JSON
3. **Export/Import** CSV, Excel
4. **Catégorisation** des contacts (famille, travail, etc.)
5. **Historique** des modifications
6. **Sauvegarde automatique** et versioning
7. **Recherche avancée** avec filtres multiples
8. **Synchronisation** avec des services cloud

**Améliorations techniques :**
1. **Logging** des opérations
2. **Tests unitaires** complets
3. **Documentation** avec docstrings
4. **Configuration** externalisée
5. **Gestion des erreurs** robuste
6. **Performance** optimisée
7. **Sécurité** des données

---
layout: default
---

# Déploiement et distribution 📦

### Préparation pour la production

```python
# setup.py
from setuptools import setup, find_packages

setup(
    name="gestionnaire-contacts",
    version="1.0.0",
    description="Gestionnaire de contacts en ligne de commande",
    author="Votre Nom",
    author_email="votre.email@example.com",
    packages=find_packages(),
    install_requires=[
        "pathlib",
    ],
    entry_points={
        'console_scripts': [
            'gestionnaire-contacts=main:main',
        ],
    },
    classifiers=[
        "Development Status :: 4 - Beta",
        "Intended Audience :: End Users/Desktop",
        "License :: OSI Approved :: MIT License",
        "Programming Language :: Python :: 3",
        "Programming Language :: Python :: 3.8",
        "Programming Language :: Python :: 3.9",
        "Programming Language :: Python :: 3.10",
        "Programming Language :: Python :: 3.11",
        "Programming Language :: Python :: 3.12",
    ],
    python_requires=">=3.8",
)
```

---
layout: default
---

# Documentation du projet 📚

### README.md complet

```markdown
# Gestionnaire de Contacts

Application de gestion de contacts en ligne de commande développée en Python.

## Fonctionnalités

- ✅ Ajouter/supprimer/modifier des contacts
- ✅ Rechercher des contacts par nom, téléphone ou email
- ✅ Validation des données (nom, téléphone, email)
- ✅ Persistance des données en JSON
- ✅ Interface utilisateur intuitive
- ✅ Gestion d'erreurs robuste

## Installation

```bash
git clone <repository>
cd gestionnaire-contacts
python -m pip install -e .
```

## Utilisation

```bash
python main.py
```

Ou après installation :

```bash
gestionnaire-contacts
```

## Structure du projet

```
gestionnaire_contacts/
├── main.py              # Point d'entrée
├── models/              # Modèles de données
├── services/            # Logique métier
├── utils/               # Utilitaires
├── tests/               # Tests unitaires
└── data/                # Données persistantes
```

## Tests

```bash
python -m unittest discover tests
```

## Licence

MIT License
```

---
layout: default
---

# Évaluation du projet 📊

### Critères d'évaluation

**Fonctionnalités (40%) :**
- ✅ Toutes les fonctionnalités de base implémentées
- ✅ Interface utilisateur fonctionnelle
- ✅ Validation des données
- ✅ Persistance des données

**Code qualité (30%) :**
- ✅ Structure modulaire
- ✅ Gestion d'erreurs
- ✅ Documentation
- ✅ Tests unitaires

**Architecture (20%) :**
- ✅ Séparation des responsabilités
- ✅ Réutilisabilité
- ✅ Extensibilité
- ✅ Maintenabilité

**Présentation (10%) :**
- ✅ Documentation claire
- ✅ Instructions d'installation
- ✅ Exemples d'utilisation

---
layout: default
---

# Prochaines étapes 🎯

### Après le projet final

1. **QCM de validation** des connaissances
2. **Débriefing** et retours
3. **Perspectives** d'évolution
4. **Ressources** pour continuer

**Préparation :**
- Finalisez votre projet
- Testez toutes les fonctionnalités
- Préparez-vous pour le QCM

---
src: './pages/12-qcm-initiation.md'
--- 