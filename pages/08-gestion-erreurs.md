---
layout: intro
routeAlias: 'gestion-erreurs'
---

# Gestion des Erreurs ⚠️

### Exceptions et gestion d'erreurs avancées

<div class="pt-12">
  <span @click="next" class="px-2 p-3 rounded cursor-pointer hover:bg-white hover:bg-opacity-10 neon-border">
    Maîtrisons la gestion d'erreurs <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: default
---

# Types d'exceptions 🚨

### Les exceptions intégrées de Python

```python
# Exceptions courantes
try:
    resultat = 10 / 0
except ZeroDivisionError:
    print("Division par zéro impossible")

try:
    nombre = int("abc")
except ValueError:
    print("Impossible de convertir en nombre")

try:
    liste = [1, 2, 3]
    print(liste[10])
except IndexError:
    print("Index hors limites")

try:
    dictionnaire = {"a": 1}
    print(dictionnaire["b"])
except KeyError:
    print("Clé inexistante")

try:
    fichier = open("fichier_inexistant.txt")
except FileNotFoundError:
    print("Fichier non trouvé")
```

---
layout: default
---

# Structure try/except/finally 🔧

### Gestion complète des exceptions

```python
# Structure de base
try:
    # Code qui peut générer une exception
    nombre = int(input("Entrez un nombre : "))
    resultat = 100 / nombre
    print(f"Résultat : {resultat}")
except ValueError:
    print("Erreur : Veuillez entrer un nombre valide")
except ZeroDivisionError:
    print("Erreur : Division par zéro impossible")
except Exception as e:
    print(f"Erreur inattendue : {e}")
finally:
    print("Ce code s'exécute toujours")

# Exemple avec gestion de fichier
try:
    fichier = open("donnees.txt", "r")
    contenu = fichier.read()
    print(contenu)
except FileNotFoundError:
    print("Fichier non trouvé")
except PermissionError:
    print("Pas de permission pour lire le fichier")
finally:
    if 'fichier' in locals():
        fichier.close()
```

---
layout: default
---

# Gestion d'erreurs avancées 🎯

### Techniques de gestion d'erreurs sophistiquées

```python
# Gestion avec else
try:
    nombre = int(input("Entrez un nombre : "))
except ValueError:
    print("Erreur : Nombre invalide")
else:
    # Ce code s'exécute seulement si aucune exception n'est levée
    print(f"Nombre saisi : {nombre}")
    resultat = nombre ** 2
    print(f"Carré : {resultat}")

# Gestion avec plusieurs exceptions
try:
    # Code qui peut générer plusieurs types d'erreurs
    pass
except (ValueError, TypeError) as e:
    print(f"Erreur de type : {e}")
except (FileNotFoundError, PermissionError) as e:
    print(f"Erreur de fichier : {e}")

# Gestion avec récupération
def lire_nombre():
    while True:
        try:
            return int(input("Entrez un nombre : "))
        except ValueError:
            print("Erreur : Veuillez entrer un nombre valide")
            continue
```

---
layout: default
---

# Lever des exceptions 🚀

### Création et levée d'exceptions personnalisées

```python
# Lever une exception simple
def diviser(a, b):
    if b == 0:
        raise ValueError("Division par zéro impossible")
    return a / b

# Lever une exception avec contexte
def ouvrir_fichier(nom_fichier):
    try:
        return open(nom_fichier, "r")
    except FileNotFoundError:
        raise FileNotFoundError(f"Le fichier '{nom_fichier}' n'existe pas")

# Lever une exception depuis une exception
def traiter_donnees(donnees):
    try:
        return int(donnees)
    except ValueError as e:
        raise ValueError(f"Données invalides : {donnees}") from e

# Exemple d'utilisation
try:
    resultat = diviser(10, 0)
except ValueError as e:
    print(f"Erreur : {e}")

try:
    fichier = ouvrir_fichier("inexistant.txt")
except FileNotFoundError as e:
    print(f"Erreur : {e}")
```

---
layout: default
---

# Exceptions personnalisées 🎭

### Création de classes d'exception

```python
# Exception personnalisée simple
class ErreurValidation(Exception):
    """Exception levée lors d'une erreur de validation."""
    pass

class ErreurAge(Exception):
    """Exception pour les erreurs d'âge."""
    def __init__(self, age, message="Âge invalide"):
        self.age = age
        self.message = message
        super().__init__(self.message)

# Exceptions avec attributs
class ErreurConnexion(Exception):
    """Exception pour les erreurs de connexion."""
    def __init__(self, serveur, port, message="Erreur de connexion"):
        self.serveur = serveur
        self.port = port
        self.message = message
        super().__init__(self.message)

# Utilisation des exceptions personnalisées
def valider_age(age):
    if not isinstance(age, int):
        raise ErreurValidation("L'âge doit être un entier")
    if age < 0 or age > 150:
        raise ErreurAge(age, f"Âge {age} invalide")
    return True

def connecter_serveur(serveur, port):
    # Simulation d'une erreur de connexion
    raise ErreurConnexion(serveur, port, "Serveur indisponible")
```

---
layout: default
---

# Context managers 🔄

### Gestion automatique des ressources

```python
# Context manager avec with
with open("fichier.txt", "r") as fichier:
    contenu = fichier.read()
    print(contenu)
# Le fichier est automatiquement fermé

# Context manager personnalisé
class GestionnaireFichier:
    def __init__(self, nom_fichier, mode="r"):
        self.nom_fichier = nom_fichier
        self.mode = mode
        self.fichier = None
    
    def __enter__(self):
        self.fichier = open(self.nom_fichier, self.mode)
        return self.fichier
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.fichier:
            self.fichier.close()
        # Retourner False pour propager l'exception
        return False

# Utilisation du context manager personnalisé
with GestionnaireFichier("test.txt", "w") as f:
    f.write("Test de context manager")
```

---
layout: default
---

# Context managers avec contextlib 🛠️

### Utilisation de contextlib pour créer des context managers

```python
from contextlib import contextmanager, closing
import time

# Context manager avec décorateur
@contextmanager
def timer(nom_operation):
    debut = time.time()
    try:
        yield
    finally:
        fin = time.time()
        print(f"{nom_operation} : {fin - debut:.4f} secondes")

# Context manager pour gestion d'erreurs
@contextmanager
def gestion_erreur_graceful():
    try:
        yield
    except Exception as e:
        print(f"Erreur gérée gracieusement : {e}")
        # Ne pas propager l'exception

# Utilisation
with timer("Calcul complexe"):
    # Simulation d'un calcul
    time.sleep(1)

with gestion_erreur_graceful():
    resultat = 10 / 0  # Erreur gérée gracieusement

# Context manager pour ressources
@contextmanager
def connexion_base_donnees():
    print("Connexion à la base de données...")
    try:
        yield "connexion_objet"
    finally:
        print("Fermeture de la connexion...")

with connexion_base_donnees() as conn:
    print(f"Utilisation de {conn}")
```

---
layout: default
---

# Assertions et debugging 🐛

### Vérifications et débogage

```python
# Assertions pour vérifications
def calculer_moyenne(nombres):
    assert len(nombres) > 0, "La liste ne peut pas être vide"
    assert all(isinstance(x, (int, float)) for x in nombres), "Tous les éléments doivent être des nombres"
    return sum(nombres) / len(nombres)

# Assertions avec messages personnalisés
def valider_age(age):
    assert isinstance(age, int), f"L'âge doit être un entier, reçu : {type(age)}"
    assert 0 <= age <= 150, f"L'âge doit être entre 0 et 150, reçu : {age}"
    return True

# Désactiver les assertions en production
# python -O script.py  # Désactive les assertions

# Debugging avec pdb
import pdb

def fonction_complexe():
    x = 10
    y = 20
    pdb.set_trace()  # Point d'arrêt
    resultat = x + y
    return resultat

# Commandes pdb utiles :
# n (next) - ligne suivante
# s (step) - entrer dans la fonction
# c (continue) - continuer l'exécution
# p variable - afficher une variable
# l (list) - afficher le code autour
```

---
layout: default
---

# Logging et monitoring 📊

### Enregistrement des erreurs et événements

```python
import logging
import traceback

# Configuration du logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('app.log'),
        logging.StreamHandler()
    ]
)

logger = logging.getLogger(__name__)

# Logging des erreurs
def fonction_avec_logging():
    try:
        resultat = 10 / 0
    except ZeroDivisionError as e:
        logger.error(f"Erreur de division : {e}")
        logger.error(f"Traceback complet : {traceback.format_exc()}")
        raise

# Logging avec contexte
def traiter_donnees(donnees):
    logger.info(f"Début du traitement de {len(donnees)} éléments")
    
    try:
        resultat = [int(x) for x in donnees]
        logger.info(f"Traitement réussi : {len(resultat)} éléments")
        return resultat
    except ValueError as e:
        logger.error(f"Erreur de conversion : {e}")
        raise
    except Exception as e:
        logger.critical(f"Erreur inattendue : {e}")
        raise

# Logging avec niveaux
logger.debug("Message de debug")
logger.info("Message d'information")
logger.warning("Avertissement")
logger.error("Erreur")
logger.critical("Erreur critique")
```

---
layout: default
---

# Gestion d'erreurs en production 🏭

### Bonnes pratiques pour la production

```python
import logging
import sys
from typing import Optional, Any

class GestionnaireErreurs:
    def __init__(self):
        self.logger = logging.getLogger(__name__)
        self.setup_logging()
    
    def setup_logging(self):
        """Configure le logging pour la production."""
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler('production.log'),
                logging.StreamHandler(sys.stdout)
            ]
        )
    
    def executer_securise(self, fonction, *args, **kwargs) -> Optional[Any]:
        """Exécute une fonction avec gestion d'erreurs complète."""
        try:
            return fonction(*args, **kwargs)
        except ValueError as e:
            self.logger.warning(f"Erreur de validation : {e}")
            return None
        except FileNotFoundError as e:
            self.logger.error(f"Fichier non trouvé : {e}")
            return None
        except PermissionError as e:
            self.logger.error(f"Erreur de permission : {e}")
            return None
        except Exception as e:
            self.logger.critical(f"Erreur inattendue : {e}")
            self.logger.critical(f"Traceback : {traceback.format_exc()}")
            return None
    
    def valider_donnees(self, donnees):
        """Valide les données avec gestion d'erreurs."""
        if not donnees:
            raise ValueError("Données vides")
        
        if not isinstance(donnees, (list, tuple)):
            raise TypeError("Données doivent être une liste ou un tuple")
        
        return True

# Utilisation
gestionnaire = GestionnaireErreurs()

def fonction_risquee(nombre):
    return 100 / nombre

resultat = gestionnaire.executer_securise(fonction_risquee, 0)
if resultat is None:
    print("Fonction a échoué, gestion d'erreur en place")
```

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 1 : Validateur de données robuste

Créez un système de validation qui :
1. Valide différents types de données (email, téléphone, âge)
2. Lève des exceptions personnalisées
3. Gère les erreurs de manière élégante
4. Enregistre les erreurs dans un fichier de log

**Fonctionnalités :**
- Validation d'email avec regex
- Validation de téléphone français
- Validation d'âge avec plages
- Logging des erreurs de validation

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 2 : Gestionnaire de fichiers sécurisé

Créez un gestionnaire de fichiers qui :
1. Gère les erreurs de lecture/écriture
2. Utilise des context managers
3. Valide les types de fichiers
4. Effectue des sauvegardes automatiques

**Fonctionnalités :**
- Lecture/écriture sécurisée
- Validation des extensions
- Sauvegarde avant modification
- Gestion des permissions

---
layout: default
---

# Exercices pratiques 🎯

### Exercice 3 : Système de monitoring

Créez un système de monitoring qui :
1. Surveille l'exécution de fonctions
2. Enregistre les performances
3. Détecte les erreurs récurrentes
4. Génère des rapports

**Fonctionnalités :**
- Décorateur de monitoring
- Métriques de performance
- Détection d'anomalies
- Rapports automatiques

---
layout: default
---

# Solutions des exercices 💡

### Exercice 1 : Validateur de données robuste

```python
import re
import logging
from typing import Optional

# Configuration du logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('validation.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

# Exceptions personnalisées
class ErreurValidation(Exception):
    """Exception de base pour les erreurs de validation."""
    pass

class ErreurEmail(ErreurValidation):
    """Exception pour les erreurs d'email."""
    pass

class ErreurTelephone(ErreurValidation):
    """Exception pour les erreurs de téléphone."""
    pass

class ErreurAge(ErreurValidation):
    """Exception pour les erreurs d'âge."""
    pass

class ValidateurDonnees:
    def __init__(self):
        # Patterns regex
        self.email_pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
        self.telephone_pattern = r'^(?:(?:\+|00)33|0)\s*[1-9](?:[\s.-]*\d{2}){4}$'
    
    def valider_email(self, email: str) -> bool:
        """Valide une adresse email."""
        if not email:
            raise ErreurEmail("Email vide")
        
        if not re.match(self.email_pattern, email):
            raise ErreurEmail(f"Format d'email invalide : {email}")
        
        logger.info(f"Email validé : {email}")
        return True
    
    def valider_telephone(self, telephone: str) -> bool:
        """Valide un numéro de téléphone français."""
        if not telephone:
            raise ErreurTelephone("Numéro de téléphone vide")
        
        if not re.match(self.telephone_pattern, telephone):
            raise ErreurTelephone(f"Format de téléphone invalide : {telephone}")
        
        logger.info(f"Téléphone validé : {telephone}")
        return True
    
    def valider_age(self, age: int) -> bool:
        """Valide un âge."""
        if not isinstance(age, int):
            raise ErreurAge(f"L'âge doit être un entier, reçu : {type(age)}")
        
        if age < 0 or age > 150:
            raise ErreurAge(f"L'âge doit être entre 0 et 150, reçu : {age}")
        
        logger.info(f"Âge validé : {age}")
        return True
    
    def valider_utilisateur(self, nom: str, email: str, telephone: str, age: int) -> bool:
        """Valide toutes les données d'un utilisateur."""
        try:
            self.valider_email(email)
            self.valider_telephone(telephone)
            self.valider_age(age)
            
            if not nom or len(nom.strip()) < 2:
                raise ErreurValidation("Le nom doit contenir au moins 2 caractères")
            
            logger.info(f"Utilisateur validé : {nom}")
            return True
            
        except ErreurValidation as e:
            logger.error(f"Erreur de validation : {e}")
            raise

# Utilisation
if __name__ == "__main__":
    validateur = ValidateurDonnees()
    
    # Tests de validation
    donnees_test = [
        ("Alice", "alice@example.com", "0123456789", 25),
        ("Bob", "email_invalide", "0123456789", 25),
        ("Charlie", "charlie@example.com", "123", 25),
        ("David", "david@example.com", "0123456789", 200),
    ]
    
    for nom, email, telephone, age in donnees_test:
        try:
            validateur.valider_utilisateur(nom, email, telephone, age)
            print(f"✅ {nom} : Validation réussie")
        except ErreurValidation as e:
            print(f"❌ {nom} : {e}")
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 2 : Gestionnaire de fichiers sécurisé

```python
import os
import shutil
import logging
from pathlib import Path
from contextlib import contextmanager
from typing import Optional, List

# Configuration du logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('fichiers.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

class ErreurFichier(Exception):
    """Exception pour les erreurs de fichiers."""
    pass

class GestionnaireFichiers:
    def __init__(self, dossier_backup: str = "backups"):
        self.dossier_backup = Path(dossier_backup)
        self.dossier_backup.mkdir(exist_ok=True)
        self.extensions_autorisees = {'.txt', '.csv', '.json', '.xml'}
    
    def valider_fichier(self, chemin: str) -> bool:
        """Valide un fichier."""
        fichier = Path(chemin)
        
        if not fichier.exists():
            raise ErreurFichier(f"Fichier inexistant : {chemin}")
        
        if not fichier.is_file():
            raise ErreurFichier(f"Le chemin ne correspond pas à un fichier : {chemin}")
        
        extension = fichier.suffix.lower()
        if extension not in self.extensions_autorisees:
            raise ErreurFichier(f"Extension non autorisée : {extension}")
        
        return True
    
    def creer_backup(self, chemin: str) -> str:
        """Crée une sauvegarde du fichier."""
        fichier = Path(chemin)
        timestamp = int(time.time())
        nom_backup = f"{fichier.stem}_{timestamp}{fichier.suffix}"
        chemin_backup = self.dossier_backup / nom_backup
        
        try:
            shutil.copy2(fichier, chemin_backup)
            logger.info(f"Backup créé : {chemin_backup}")
            return str(chemin_backup)
        except Exception as e:
            logger.error(f"Erreur lors de la création du backup : {e}")
            raise ErreurFichier(f"Impossible de créer le backup : {e}")
    
    @contextmanager
    def lire_fichier_securise(self, chemin: str):
        """Context manager pour lire un fichier de manière sécurisée."""
        fichier = None
        try:
            self.valider_fichier(chemin)
            fichier = open(chemin, 'r', encoding='utf-8')
            logger.info(f"Fichier ouvert en lecture : {chemin}")
            yield fichier
        except Exception as e:
            logger.error(f"Erreur lors de la lecture : {e}")
            raise ErreurFichier(f"Erreur de lecture : {e}")
        finally:
            if fichier:
                fichier.close()
                logger.info(f"Fichier fermé : {chemin}")
    
    @contextmanager
    def ecrire_fichier_securise(self, chemin: str, creer_backup: bool = True):
        """Context manager pour écrire un fichier de manière sécurisée."""
        fichier = None
        backup_chemin = None
        
        try:
            # Valider le chemin
            fichier_path = Path(chemin)
            fichier_path.parent.mkdir(parents=True, exist_ok=True)
            
            # Créer un backup si le fichier existe
            if creer_backup and fichier_path.exists():
                backup_chemin = self.creer_backup(chemin)
            
            fichier = open(chemin, 'w', encoding='utf-8')
            logger.info(f"Fichier ouvert en écriture : {chemin}")
            yield fichier
            
        except Exception as e:
            logger.error(f"Erreur lors de l'écriture : {e}")
            # Restaurer le backup en cas d'erreur
            if backup_chemin and Path(backup_chemin).exists():
                shutil.copy2(backup_chemin, chemin)
                logger.info(f"Backup restauré : {backup_chemin}")
            raise ErreurFichier(f"Erreur d'écriture : {e}")
        finally:
            if fichier:
                fichier.close()
                logger.info(f"Fichier fermé : {chemin}")
    
    def lister_fichiers(self, dossier: str, extension: Optional[str] = None) -> List[str]:
        """Liste les fichiers d'un dossier."""
        try:
            dossier_path = Path(dossier)
            if not dossier_path.exists():
                raise ErreurFichier(f"Dossier inexistant : {dossier}")
            
            fichiers = []
            for fichier in dossier_path.iterdir():
                if fichier.is_file():
                    if extension is None or fichier.suffix.lower() == extension.lower():
                        fichiers.append(str(fichier))
            
            logger.info(f"Liste des fichiers trouvés : {len(fichiers)}")
            return fichiers
            
        except Exception as e:
            logger.error(f"Erreur lors de la liste des fichiers : {e}")
            raise ErreurFichier(f"Erreur de liste : {e}")

# Utilisation
if __name__ == "__main__":
    gestionnaire = GestionnaireFichiers()
    
    # Test de lecture sécurisée
    try:
        with gestionnaire.lire_fichier_securise("test.txt") as f:
            contenu = f.read()
            print(f"Contenu lu : {contenu}")
    except ErreurFichier as e:
        print(f"Erreur : {e}")
    
    # Test d'écriture sécurisée
    try:
        with gestionnaire.ecrire_fichier_securise("test.txt") as f:
            f.write("Test de gestionnaire de fichiers sécurisé")
        print("Écriture réussie")
    except ErreurFichier as e:
        print(f"Erreur : {e}")
```

---
layout: default
---

# Solutions des exercices 💡

### Exercice 3 : Système de monitoring

```python
import time
import logging
import functools
from typing import Dict, List, Any, Optional
from collections import defaultdict
from datetime import datetime, timedelta

# Configuration du logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('monitoring.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

class MetriquesMonitoring:
    def __init__(self):
        self.appels = defaultdict(int)
        self.temps_execution = defaultdict(list)
        self.erreurs = defaultdict(list)
        self.debut_monitoring = datetime.now()
    
    def ajouter_appel(self, nom_fonction: str, temps: float, erreur: Optional[Exception] = None):
        """Ajoute une métrique d'appel."""
        self.appels[nom_fonction] += 1
        self.temps_execution[nom_fonction].append(temps)
        
        if erreur:
            self.erreurs[nom_fonction].append({
                'erreur': str(erreur),
                'timestamp': datetime.now(),
                'temps_execution': temps
            })
    
    def obtenir_statistiques(self, nom_fonction: str) -> Dict[str, Any]:
        """Retourne les statistiques d'une fonction."""
        temps_list = self.temps_execution[nom_fonction]
        
        if not temps_list:
            return {}
        
        return {
            'appels_total': self.appels[nom_fonction],
            'temps_moyen': sum(temps_list) / len(temps_list),
            'temps_min': min(temps_list),
            'temps_max': max(temps_list),
            'erreurs_total': len(self.erreurs[nom_fonction]),
            'taux_erreur': len(self.erreurs[nom_fonction]) / self.appels[nom_fonction] * 100
        }
    
    def obtenir_rapport_complet(self) -> Dict[str, Any]:
        """Génère un rapport complet."""
        rapport = {
            'debut_monitoring': self.debut_monitoring,
            'duree_monitoring': datetime.now() - self.debut_monitoring,
            'fonctions_surveillees': list(self.appels.keys()),
            'statistiques_par_fonction': {},
            'erreurs_recentes': []
        }
        
        # Statistiques par fonction
        for fonction in self.appels.keys():
            rapport['statistiques_par_fonction'][fonction] = self.obtenir_statistiques(fonction)
        
        # Erreurs récentes (dernières 24h)
        limite = datetime.now() - timedelta(hours=24)
        for fonction, erreurs in self.erreurs.items():
            for erreur in erreurs:
                if erreur['timestamp'] > limite:
                    rapport['erreurs_recentes'].append({
                        'fonction': fonction,
                        'erreur': erreur['erreur'],
                        'timestamp': erreur['timestamp']
                    })
        
        return rapport

class SystemeMonitoring:
    def __init__(self):
        self.metriques = MetriquesMonitoring()
        self.seuils_alerte = {
            'temps_execution_max': 5.0,  # secondes
            'taux_erreur_max': 10.0,     # pourcentage
            'appels_min': 100            # nombre minimum d'appels
        }
    
    def monitorer(self, seuil_temps: Optional[float] = None):
        """Décorateur pour monitorer une fonction."""
        def decorateur(func):
            @functools.wraps(func)
            def wrapper(*args, **kwargs):
                debut = time.time()
                erreur = None
                
                try:
                    resultat = func(*args, **kwargs)
                    return resultat
                except Exception as e:
                    erreur = e
                    raise
                finally:
                    fin = time.time()
                    temps_execution = fin - debut
                    
                    # Ajouter les métriques
                    self.metriques.ajouter_appel(
                        func.__name__, 
                        temps_execution, 
                        erreur
                    )
                    
                    # Vérifier les seuils d'alerte
                    self._verifier_alertes(func.__name__, temps_execution)
            
            return wrapper
        return decorateur
    
    def _verifier_alertes(self, nom_fonction: str, temps_execution: float):
        """Vérifie les seuils d'alerte."""
        stats = self.metriques.obtenir_statistiques(nom_fonction)
        
        if not stats:
            return
        
        # Alerte temps d'exécution
        if temps_execution > self.seuils_alerte['temps_execution_max']:
            logger.warning(f"ALERTE : {nom_fonction} a pris {temps_execution:.2f}s")
        
        # Alerte taux d'erreur
        if stats['taux_erreur'] > self.seuils_alerte['taux_erreur_max']:
            logger.error(f"ALERTE : {nom_fonction} a un taux d'erreur de {stats['taux_erreur']:.1f}%")
        
        # Alerte nombre d'appels
        if stats['appels_total'] > self.seuils_alerte['appels_min']:
            logger.info(f"INFO : {nom_fonction} a été appelée {stats['appels_total']} fois")
    
    def generer_rapport(self) -> str:
        """Génère un rapport de monitoring."""
        rapport = self.metriques.obtenir_rapport_complet()
        
        rapport_str = "=== RAPPORT DE MONITORING ===\n"
        rapport_str += f"Période : {rapport['duree_monitoring']}\n"
        rapport_str += f"Fonctions surveillées : {len(rapport['fonctions_surveillees'])}\n\n"
        
        for fonction, stats in rapport['statistiques_par_fonction'].items():
            rapport_str += f"--- {fonction} ---\n"
            rapport_str += f"  Appels total : {stats['appels_total']}\n"
            rapport_str += f"  Temps moyen : {stats['temps_moyen']:.4f}s\n"
            rapport_str += f"  Temps min/max : {stats['temps_min']:.4f}s / {stats['temps_max']:.4f}s\n"
            rapport_str += f"  Taux d'erreur : {stats['taux_erreur']:.1f}%\n"
            rapport_str += f"  Erreurs total : {stats['erreurs_total']}\n\n"
        
        if rapport['erreurs_recentes']:
            rapport_str += "=== ERREURS RÉCENTES ===\n"
            for erreur in rapport['erreurs_recentes'][:5]:  # 5 dernières erreurs
                rapport_str += f"{erreur['timestamp']} - {erreur['fonction']}: {erreur['erreur']}\n"
        
        return rapport_str

# Utilisation
monitoring = SystemeMonitoring()

@monitoring.monitorer()
def fonction_test():
    """Fonction de test pour le monitoring."""
    time.sleep(0.1)  # Simulation d'un traitement
    if time.time() % 10 < 2:  # 20% de chance d'erreur
        raise ValueError("Erreur simulée")
    return "Succès"

# Tests
if __name__ == "__main__":
    for i in range(50):
        try:
            resultat = fonction_test()
            print(f"Appel {i+1}: {resultat}")
        except Exception as e:
            print(f"Appel {i+1}: Erreur - {e}")
    
    # Générer le rapport
    rapport = monitoring.generer_rapport()
    print(rapport)
```

---
layout: default
---

# Prochaines étapes 🎯

### Ce qui nous attend

1. **Programmation orientée objet** (classes, héritage)
2. **Manipulation de fichiers** (I/O avancé)
3. **Projet final intégrateur**
4. **QCM de validation**
5. **Débriefing et perspectives**

**Préparation :**
- Maîtrisez la gestion d'erreurs
- Pratiquez les context managers
- Testez les exercices proposés

---
src: './pages/09-programmation-objet.md'
--- 