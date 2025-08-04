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

**Organisation modulaire :**
- Séparation des responsabilités
- Code réutilisable
- Maintenance facilitée
- Tests unitaires possibles

---

# Structure des Fichiers

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

**Attributs du contact :**
- Nom et téléphone (obligatoires)
- Email et adresse (optionnels)
- Date de création automatique
- Méthodes de sérialisation

---

# Définition de la Classe Contact

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
```

---

# Méthodes de la Classe Contact

```python
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
```

---

# Méthode de Désérialisation

```python
    @classmethod
    def from_dict(cls, data):
        """Crée un contact depuis un dictionnaire."""
        contact = cls(
            nom=data['nom'],
            telephone=data['telephone'],
            email=data.get('email'),
            adresse=data.get('adresse')
        )
        contact.date_creation = datetime.fromisoformat(data['date_creation'])
        return contact
```

---
layout: default
---

# Validateur de Contact 🔍

### Validation des données

**Règles de validation :**
- Nom non vide et alphabétique
- Téléphone au format français
- Email valide (optionnel)
- Adresse non vide (optionnelle)

---

# Classe ValidateurContact

```python
# services/validation.py
import re

class ValidateurContact:
    def __init__(self):
        self.pattern_telephone = re.compile(r'^(?:(?:\+|00)33|0)\s*[1-9](?:[\s.-]*\d{2}){4}$')
        self.pattern_email = re.compile(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$')
    
    def valider_nom(self, nom):
        """Valide le nom du contact."""
        if not nom or not nom.strip():
            return False, "Le nom ne peut pas être vide"
        if not nom.replace(' ', '').isalpha():
            return False, "Le nom ne doit contenir que des lettres"
        return True, ""
    
    def valider_telephone(self, telephone):
        """Valide le numéro de téléphone."""
        if not telephone:
            return False, "Le téléphone ne peut pas être vide"
        if not self.pattern_telephone.match(telephone):
            return False, "Format de téléphone invalide"
        return True, ""
```

---

# Validation Email et Adresse

```python
    def valider_email(self, email):
        """Valide l'adresse email (optionnelle)."""
        if not email:
            return True, ""  # Email optionnel
        if not self.pattern_email.match(email):
            return False, "Format d'email invalide"
        return True, ""
    
    def valider_adresse(self, adresse):
        """Valide l'adresse (optionnelle)."""
        if not adresse:
            return True, ""  # Adresse optionnelle
        if len(adresse.strip()) < 5:
            return False, "L'adresse doit contenir au moins 5 caractères"
        return True, ""
    
    def valider_contact(self, nom, telephone, email=None, adresse=None):
        """Valide toutes les données d'un contact."""
        erreurs = []
        
        # Validation du nom
        valide, message = self.valider_nom(nom)
        if not valide:
            erreurs.append(f"Nom: {message}")
        
        # Validation du téléphone
        valide, message = self.valider_telephone(telephone)
        if not valide:
            erreurs.append(f"Téléphone: {message}")
        
        # Validation de l'email
        valide, message = self.valider_email(email)
        if not valide:
            erreurs.append(f"Email: {message}")
        
        # Validation de l'adresse
        valide, message = self.valider_adresse(adresse)
        if not valide:
            erreurs.append(f"Adresse: {message}")
        
        return len(erreurs) == 0, erreurs
```

---
layout: default
---

# Gestionnaire de Contacts 🗂️

### Logique métier de l'application

**Fonctionnalités principales :**
- Ajout/suppression/modification de contacts
- Recherche de contacts
- Gestion des erreurs
- Interface avec le validateur

---

# Classe GestionnaireContacts

```python
# services/gestionnaire.py
from models.contact import Contact
from services.validation import ValidateurContact

class GestionnaireContacts:
    def __init__(self):
        self.contacts = []
        self.validateur = ValidateurContact()
    
    def ajouter_contact(self, nom, telephone, email=None, adresse=None):
        """Ajoute un nouveau contact."""
        # Validation des données
        valide, erreurs = self.validateur.valider_contact(nom, telephone, email, adresse)
        if not valide:
            return False, erreurs
        
        # Vérifier si le contact existe déjà
        if self.rechercher_par_telephone(telephone):
            return False, ["Un contact avec ce téléphone existe déjà"]
        
        # Créer et ajouter le contact
        contact = Contact(nom, telephone, email, adresse)
        self.contacts.append(contact)
        return True, ["Contact ajouté avec succès"]
```

---

# Méthodes de Recherche

```python
    def rechercher_par_nom(self, nom):
        """Recherche un contact par nom."""
        nom_lower = nom.lower()
        return [c for c in self.contacts if nom_lower in c.nom.lower()]
    
    def rechercher_par_telephone(self, telephone):
        """Recherche un contact par téléphone."""
        for contact in self.contacts:
            if contact.telephone == telephone:
                return contact
        return None
    
    def rechercher_par_email(self, email):
        """Recherche un contact par email."""
        email_lower = email.lower()
        return [c for c in self.contacts if c.email and email_lower in c.email.lower()]
```

---

# Méthodes de Modification

```python
    def modifier_contact(self, telephone, nouveau_nom=None, nouveau_telephone=None, 
                        nouveau_email=None, nouvelle_adresse=None):
        """Modifie un contact existant."""
        contact = self.rechercher_par_telephone(telephone)
        if not contact:
            return False, ["Contact non trouvé"]
        
        # Préparer les nouvelles valeurs
        nom = nouveau_nom if nouveau_nom is not None else contact.nom
        tel = nouveau_telephone if nouveau_telephone is not None else contact.telephone
        email = nouveau_email if nouveau_email is not None else contact.email
        adresse = nouvelle_adresse if nouvelle_adresse is not None else contact.adresse
        
        # Validation
        valide, erreurs = self.validateur.valider_contact(nom, tel, email, adresse)
        if not valide:
            return False, erreurs
        
        # Mise à jour
        contact.nom = nom
        contact.telephone = tel
        contact.email = email
        contact.adresse = adresse
        
        return True, ["Contact modifié avec succès"]
    
    def supprimer_contact(self, telephone):
        """Supprime un contact."""
        contact = self.rechercher_par_telephone(telephone)
        if not contact:
            return False, ["Contact non trouvé"]
        
        self.contacts.remove(contact)
        return True, ["Contact supprimé avec succès"]
```

---
layout: default
---

# Gestionnaire de Fichiers 💾

### Persistance des données

**Fonctionnalités :**
- Sauvegarde en JSON
- Chargement depuis JSON
- Gestion d'erreurs
- Validation des données chargées

---

# Classe GestionnaireFichiers

```python
# utils/fichier.py
import json
from pathlib import Path
from models.contact import Contact

class GestionnaireFichiers:
    def __init__(self, chemin_fichier="data/contacts.json"):
        self.chemin_fichier = Path(chemin_fichier)
        self.chemin_fichier.parent.mkdir(parents=True, exist_ok=True)
    
    def sauvegarder_contacts(self, contacts):
        """Sauvegarde les contacts dans un fichier JSON."""
        try:
            donnees = [contact.to_dict() for contact in contacts]
            with open(self.chemin_fichier, 'w', encoding='utf-8') as f:
                json.dump(donnees, f, indent=2, ensure_ascii=False)
            return True, "Contacts sauvegardés avec succès"
        except Exception as e:
            return False, f"Erreur lors de la sauvegarde : {e}"
```

---

# Chargement des Contacts

```python
    def charger_contacts(self):
        """Charge les contacts depuis le fichier JSON."""
        try:
            if not self.chemin_fichier.exists():
                return [], "Aucun fichier de contacts trouvé"
            
            with open(self.chemin_fichier, 'r', encoding='utf-8') as f:
                donnees = json.load(f)
            
            contacts = []
            for contact_data in donnees:
                try:
                    contact = Contact.from_dict(contact_data)
                    contacts.append(contact)
                except Exception as e:
                    print(f"Erreur lors du chargement d'un contact : {e}")
            
            return contacts, f"{len(contacts)} contacts chargés"
        except Exception as e:
            return [], f"Erreur lors du chargement : {e}"
```

---
layout: default
---

# Interface Utilisateur 🖥️

### Interface en ligne de commande

**Fonctionnalités de l'interface :**
- Menu interactif
- Saisie sécurisée
- Affichage formaté
- Gestion des erreurs

---

# Classe InterfaceUtilisateur

```python
# main.py
from services.gestionnaire import GestionnaireContacts
from utils.fichier import GestionnaireFichiers

class InterfaceUtilisateur:
    def __init__(self):
        self.gestionnaire = GestionnaireContacts()
        self.gestionnaire_fichiers = GestionnaireFichiers()
        self.charger_donnees()
    
    def charger_donnees(self):
        """Charge les données au démarrage."""
        contacts, message = self.gestionnaire_fichiers.charger_contacts()
        self.gestionnaire.contacts = contacts
        print(f"📁 {message}")
    
    def afficher_menu(self):
        """Affiche le menu principal."""
        print("\n" + "="*50)
        print("📞 GESTIONNAIRE DE CONTACTS")
        print("="*50)
        print("1. Ajouter un contact")
        print("2. Rechercher un contact")
        print("3. Modifier un contact")
        print("4. Supprimer un contact")
        print("5. Lister tous les contacts")
        print("6. Sauvegarder")
        print("0. Quitter")
        print("="*50)
```

---

# Méthodes de Saisie

```python
    def saisir_contact(self):
        """Saisit les données d'un nouveau contact."""
        print("\n--- AJOUT D'UN CONTACT ---")
        
        nom = input("Nom : ").strip()
        telephone = input("Téléphone : ").strip()
        email = input("Email (optionnel) : ").strip() or None
        adresse = input("Adresse (optionnelle) : ").strip() or None
        
        return nom, telephone, email, adresse
    
    def saisir_telephone(self):
        """Saisit un numéro de téléphone."""
        return input("Téléphone du contact : ").strip()
```

---

# Méthodes d'Affichage

```python
    def afficher_contact(self, contact):
        """Affiche un contact de manière formatée."""
        print(f"\n📋 {contact.nom}")
        print(f"📞 {contact.telephone}")
        if contact.email:
            print(f"📧 {contact.email}")
        if contact.adresse:
            print(f"📍 {contact.adresse}")
        print(f"📅 Créé le {contact.date_creation.strftime('%d/%m/%Y à %H:%M')}")
    
    def afficher_contacts(self, contacts):
        """Affiche une liste de contacts."""
        if not contacts:
            print("Aucun contact trouvé.")
            return
        
        print(f"\n📋 {len(contacts)} contact(s) trouvé(s) :")
        for i, contact in enumerate(contacts, 1):
            print(f"\n{i}. {contact}")
            if contact.email:
                print(f"   📧 {contact.email}")
            if contact.adresse:
                print(f"   📍 {contact.adresse}")
```

---
layout: default
---

# Méthodes d'Action 🎯

### Implémentation des actions du menu

**Actions principales :**
- Ajouter un contact
- Rechercher des contacts
- Modifier un contact
- Supprimer un contact
- Lister tous les contacts

---

# Action Ajouter Contact

```python
    def ajouter_contact(self):
        """Action d'ajout de contact."""
        nom, telephone, email, adresse = self.saisir_contact()
        
        succes, messages = self.gestionnaire.ajouter_contact(nom, telephone, email, adresse)
        
        if succes:
            print(f"✅ {messages[0]}")
            self.sauvegarder_automatique()
        else:
            print("❌ Erreurs de validation :")
            for message in messages:
                print(f"   - {message}")
```

---

# Action Rechercher Contact

```python
    def rechercher_contact(self):
        """Action de recherche de contact."""
        print("\n--- RECHERCHE DE CONTACT ---")
        print("1. Par nom")
        print("2. Par téléphone")
        print("3. Par email")
        
        choix = input("Votre choix : ").strip()
        
        if choix == "1":
            nom = input("Nom à rechercher : ").strip()
            contacts = self.gestionnaire.rechercher_par_nom(nom)
            self.afficher_contacts(contacts)
        
        elif choix == "2":
            telephone = self.saisir_telephone()
            contact = self.gestionnaire.rechercher_par_telephone(telephone)
            if contact:
                self.afficher_contact(contact)
            else:
                print("❌ Contact non trouvé")
        
        elif choix == "3":
            email = input("Email à rechercher : ").strip()
            contacts = self.gestionnaire.rechercher_par_email(email)
            self.afficher_contacts(contacts)
        
        else:
            print("❌ Choix invalide")
```

---

# Action Modifier Contact

```python
    def modifier_contact(self):
        """Action de modification de contact."""
        print("\n--- MODIFICATION DE CONTACT ---")
        telephone = self.saisir_telephone()
        
        contact = self.gestionnaire.rechercher_par_telephone(telephone)
        if not contact:
            print("❌ Contact non trouvé")
            return
        
        print("Laissez vide pour ne pas modifier")
        nouveau_nom = input(f"Nouveau nom ({contact.nom}) : ").strip() or None
        nouveau_telephone = input(f"Nouveau téléphone ({contact.telephone}) : ").strip() or None
        nouveau_email = input(f"Nouveau email ({contact.email or 'aucun'}) : ").strip() or None
        nouvelle_adresse = input(f"Nouvelle adresse ({contact.adresse or 'aucune'}) : ").strip() or None
        
        succes, messages = self.gestionnaire.modifier_contact(
            telephone, nouveau_nom, nouveau_telephone, nouveau_email, nouvelle_adresse
        )
        
        if succes:
            print(f"✅ {messages[0]}")
            self.sauvegarder_automatique()
        else:
            print("❌ Erreurs :")
            for message in messages:
                print(f"   - {message}")
```

---
layout: default
---

# Actions Supprimer et Lister 📋

### Actions de suppression et de listage

**Fonctionnalités :**
- Suppression sécurisée
- Liste complète des contacts
- Sauvegarde automatique

---

# Action Supprimer Contact

```python
    def supprimer_contact(self):
        """Action de suppression de contact."""
        print("\n--- SUPPRESSION DE CONTACT ---")
        telephone = self.saisir_telephone()
        
        contact = self.gestionnaire.rechercher_par_telephone(telephone)
        if not contact:
            print("❌ Contact non trouvé")
            return
        
        print(f"Contact à supprimer : {contact}")
        confirmation = input("Êtes-vous sûr ? (oui/non) : ").strip().lower()
        
        if confirmation == "oui":
            succes, messages = self.gestionnaire.supprimer_contact(telephone)
            if succes:
                print(f"✅ {messages[0]}")
                self.sauvegarder_automatique()
            else:
                print(f"❌ {messages[0]}")
        else:
            print("❌ Suppression annulée")
```

---

# Action Lister Contacts

```python
    def lister_contacts(self):
        """Action de listage de tous les contacts."""
        print("\n--- LISTE DE TOUS LES CONTACTS ---")
        
        if not self.gestionnaire.contacts:
            print("📭 Aucun contact enregistré")
            return
        
        for i, contact in enumerate(self.gestionnaire.contacts, 1):
            print(f"\n{i}. {contact}")
            if contact.email:
                print(f"   📧 {contact.email}")
            if contact.adresse:
                print(f"   📍 {contact.adresse}")
```

---

# Sauvegarde Automatique

```python
    def sauvegarder_automatique(self):
        """Sauvegarde automatique des données."""
        succes, message = self.gestionnaire_fichiers.sauvegarder_contacts(
            self.gestionnaire.contacts
        )
        if succes:
            print(f"💾 {message}")
        else:
            print(f"❌ {message}")
    
    def sauvegarder(self):
        """Action de sauvegarde manuelle."""
        self.sauvegarder_automatique()
```

---
layout: default
---

# Boucle Principale 🔄

### Gestion du menu et des actions

**Fonctionnalités :**
- Menu interactif
- Gestion des choix
- Boucle infinie
- Sortie propre

---

# Méthode Exécuter

```python
    def executer(self):
        """Exécute l'interface utilisateur."""
        print("🚀 Démarrage du gestionnaire de contacts...")
        
        while True:
            self.afficher_menu()
            choix = input("Votre choix : ").strip()
            
            if choix == "1":
                self.ajouter_contact()
            elif choix == "2":
                self.rechercher_contact()
            elif choix == "3":
                self.modifier_contact()
            elif choix == "4":
                self.supprimer_contact()
            elif choix == "5":
                self.lister_contacts()
            elif choix == "6":
                self.sauvegarder()
            elif choix == "0":
                print("👋 Au revoir !")
                break
            else:
                print("❌ Choix invalide")
            
            input("\nAppuyez sur Entrée pour continuer...")
```

---

# Point d'Entrée

```python
if __name__ == "__main__":
    interface = InterfaceUtilisateur()
    interface.executer()
```

---
layout: default
---

# Tests Unitaires 🧪

### Validation du code

**Tests à implémenter :**
- Tests de validation
- Tests du gestionnaire
- Tests de persistance
- Tests d'intégration

---

# Tests de Validation

```python
# tests/test_validation.py
import unittest
from services.validation import ValidateurContact

class TestValidateurContact(unittest.TestCase):
    def setUp(self):
        self.validateur = ValidateurContact()
    
    def test_valider_nom_valide(self):
        valide, message = self.validateur.valider_nom("Jean Dupont")
        self.assertTrue(valide)
        self.assertEqual(message, "")
    
    def test_valider_nom_vide(self):
        valide, message = self.validateur.valider_nom("")
        self.assertFalse(valide)
        self.assertIn("vide", message)
    
    def test_valider_telephone_valide(self):
        valide, message = self.validateur.valider_telephone("0123456789")
        self.assertTrue(valide)
        self.assertEqual(message, "")
    
    def test_valider_telephone_invalide(self):
        valide, message = self.validateur.valider_telephone("123")
        self.assertFalse(valide)
        self.assertIn("invalide", message)

if __name__ == "__main__":
    unittest.main()
```

---
layout: default
---

# Améliorations Possibles 🚀

### Extensions du projet

**Fonctionnalités avancées :**
- Interface graphique (tkinter)
- Base de données SQLite
- Export/import CSV
- Synchronisation cloud
- Notifications

**Optimisations :**
- Recherche indexée
- Cache des données
- Compression des fichiers
- Chiffrement des données

---
layout: default
---

# Déploiement 📦

### Distribution de l'application

**Options de déploiement :**
- Script Python standalone
- Package avec setuptools
- Application avec PyInstaller
- Container Docker

---

# Setup.py pour Distribution

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
    install_requires=[],
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
    ],
)
```

---
layout: default
---

# Documentation 📚

### README.md complet

**Sections de documentation :**
- Installation
- Utilisation
- Configuration
- API
- Exemples

---

# README.md

```markdown
# Gestionnaire de Contacts

Application de gestion de contacts en ligne de commande développée en Python.

## Fonctionnalités

- ✅ Ajout, modification, suppression de contacts
- ✅ Recherche par nom, téléphone, email
- ✅ Validation des données
- ✅ Persistance JSON
- ✅ Interface intuitive

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

## Structure du Projet

```
gestionnaire_contacts/
├── main.py              # Point d'entrée
├── models/              # Modèles de données
├── services/            # Logique métier
├── utils/               # Utilitaires
├── data/                # Données persistantes
└── tests/               # Tests unitaires
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

# Critères d'Évaluation 📊

### Grille d'évaluation du projet

**Critères techniques (60%) :**
- Architecture modulaire (15%)
- Gestion d'erreurs (15%)
- Validation des données (15%)
- Persistance des données (15%)

**Critères fonctionnels (40%) :**
- Fonctionnalités complètes (20%)
- Interface utilisateur (10%)
- Tests unitaires (10%)

---
layout: default
---

# Points Clés à Retenir 🎯

### Résumé du projet final

**Concepts intégrés :**
1. **Programmation orientée objet** : Classes Contact, ValidateurContact
2. **Gestion d'erreurs** : Validation, exceptions, messages d'erreur
3. **Manipulation de fichiers** : JSON, pathlib, context managers
4. **Collections** : Listes, dictionnaires, recherche
5. **Fonctions** : Méthodes, paramètres, retour de valeurs

**Compétences développées :**
- Architecture logicielle
- Développement modulaire
- Tests unitaires
- Documentation
- Interface utilisateur

**Prêt pour le QCM de validation !** 🚀 