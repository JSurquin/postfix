---
layout: default
routeAlias: 'testing'
---

# TESTS ET TDD 🧪

### Tests unitaires et développement piloté par les tests

---

# Pourquoi les Tests ?

### Importance des tests en développement

**Avantages des tests :**
- Détection précoce des bugs
- Documentation vivante du code
- Refactoring sécurisé
- Confiance dans le code

**Types de tests :**
- Tests unitaires
- Tests d'intégration
- Tests fonctionnels
- Tests de performance

---

# Tests Unitaires avec unittest

### Framework de tests standard Python

```python
import unittest

class TestCalculator(unittest.TestCase):
    def setUp(self):
        """Configuration avant chaque test"""
        self.calc = Calculator()
    
    def test_addition(self):
        """Test de l'addition"""
        result = self.calc.add(2, 3)
        self.assertEqual(result, 5)
    
    def test_division_by_zero(self):
        """Test de division par zéro"""
        with self.assertRaises(ValueError):
            self.calc.divide(10, 0)
```

---

# Assertions Principales

### Méthodes d'assertion unittest

```python
# Assertions de base
self.assertEqual(a, b)      # a == b
self.assertNotEqual(a, b)   # a != b
self.assertTrue(x)          # bool(x) is True
self.assertFalse(x)         # bool(x) is False
self.assertIs(a, b)         # a is b
self.assertIsNone(x)        # x is None
self.assertIn(a, b)         # a in b
self.assertRaises(exception) # Vérifie qu'une exception est levée
```

---

# Tests avec pytest

### Framework moderne et populaire

```python
import pytest

def test_addition():
    """Test simple avec pytest"""
    assert 2 + 2 == 4

def test_string_operations():
    """Test sur les chaînes"""
    text = "hello world"
    assert "hello" in text
    assert text.upper() == "HELLO WORLD"
```

---

# Fixtures avec pytest

### Configuration réutilisable

```python
import pytest

@pytest.fixture
def sample_data():
    """Fixture pour des données de test"""
    return [1, 2, 3, 4, 5]

def test_with_fixture(sample_data):
    """Test utilisant une fixture"""
    assert len(sample_data) == 5
    assert sum(sample_data) == 15

@pytest.fixture
def database_connection():
    """Fixture pour connexion DB"""
    # Setup
    connection = create_test_db()
    yield connection
    # Teardown
    connection.close()
```

---

# Paramétrisation de Tests

### Tests avec plusieurs jeux de données

```python
import pytest

@pytest.mark.parametrize("input,expected", [
    (2, 4),
    (3, 9),
    (4, 16),
    (5, 25)
])
def test_square(input, expected):
    """Test de la fonction carré avec plusieurs valeurs"""
    assert input ** 2 == expected

@pytest.mark.parametrize("a,b,expected", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
    (10, 20, 30)
])
def test_addition(a, b, expected):
    """Test d'addition avec plusieurs cas"""
    assert a + b == expected
```

---

# Mocks et Stubs

### Simulation d'objets pour les tests

```python
from unittest.mock import Mock, patch

def test_with_mock():
    """Test utilisant un mock"""
    # Création d'un mock
    mock_service = Mock()
    mock_service.get_data.return_value = {"key": "value"}
    
    # Utilisation du mock
    result = process_data(mock_service)
    assert result == "processed_value"
    
    # Vérification des appels
    mock_service.get_data.assert_called_once()
```

---

# Patch avec unittest.mock

### Remplacement temporaire d'objets

```python
from unittest.mock import patch

def test_with_patch():
    """Test avec patch"""
    with patch('module.function') as mock_func:
        mock_func.return_value = "mocked_result"
        
        result = call_function()
        assert result == "mocked_result"
        mock_func.assert_called_once()

# Patch comme décorateur
@patch('module.function')
def test_with_decorator(mock_func):
    """Test avec patch comme décorateur"""
    mock_func.return_value = "mocked"
    result = call_function()
    assert result == "mocked"
```

---

# Tests de Performance

### Mesure des performances

```python
import time
import pytest

def test_performance():
    """Test de performance"""
    start_time = time.time()
    
    # Code à tester
    result = expensive_operation()
    
    end_time = time.time()
    execution_time = end_time - start_time
    
    # Assertions sur le temps
    assert execution_time < 1.0  # Doit prendre moins d'1 seconde
    assert result is not None
```

---

# Tests d'Intégration

### Tests de composants ensemble

```python
import pytest
from myapp.database import Database
from myapp.service import UserService

class TestUserIntegration:
    """Tests d'intégration pour les utilisateurs"""
    
    def setup_method(self):
        """Setup avant chaque test"""
        self.db = Database(":memory:")
        self.service = UserService(self.db)
    
    def test_create_and_get_user(self):
        """Test complet de création et récupération"""
        # Création
        user = self.service.create_user("john", "john@example.com")
        assert user.id is not None
        
        # Récupération
        retrieved = self.service.get_user(user.id)
        assert retrieved.name == "john"
        assert retrieved.email == "john@example.com"
```

---

# TDD - Test Driven Development

### Développement piloté par les tests

**Cycle TDD (Red-Green-Refactor) :**
1. **Red** : Écrire un test qui échoue
2. **Green** : Écrire le code minimal pour passer le test
3. **Refactor** : Améliorer le code sans casser les tests

---

# Exemple TDD - Calculatrice

### Étape 1 : Test qui échoue

```python
import unittest

class TestCalculator(unittest.TestCase):
    def test_addition(self):
        """Test d'addition - étape Red"""
        calc = Calculator()
        result = calc.add(2, 3)
        self.assertEqual(result, 5)

# Ce test va échouer car Calculator n'existe pas encore
```

---

# Étape 2 : Code minimal

```python
class Calculator:
    def add(self, a, b):
        """Implémentation minimale pour passer le test"""
        return a + b

# Maintenant le test passe (Green)
```

---

# Étape 3 : Refactoring

```python
class Calculator:
    def add(self, a, b):
        """Version améliorée avec validation"""
        if not isinstance(a, (int, float)) or not isinstance(b, (int, float)):
            raise TypeError("Les arguments doivent être numériques")
        return a + b

# Le test passe toujours, mais le code est plus robuste
```

---

# Tests de Couverture

### Mesure de la couverture de code

```python
# Installation : pip install coverage

# Commande pour mesurer la couverture
# coverage run -m pytest tests/
# coverage report
# coverage html  # Génère un rapport HTML

# Dans le code
import coverage

def measure_coverage():
    """Mesure de la couverture"""
    cov = coverage.Coverage()
    cov.start()
    
    # Code à tester
    run_tests()
    
    cov.stop()
    cov.save()
    
    # Rapport
    cov.report()
```

---

# Tests de Sécurité

### Tests pour la sécurité

```python
import pytest

def test_sql_injection_prevention():
    """Test de prévention injection SQL"""
    user_input = "'; DROP TABLE users; --"
    
    # Le code doit échapper correctement l'entrée
    result = safe_query(user_input)
    
    # Vérification que la requête est sécurisée
    assert "DROP TABLE" not in result
    assert result.count("'") == 0

def test_xss_prevention():
    """Test de prévention XSS"""
    user_input = "<script>alert('xss')</script>"
    
    result = sanitize_html(user_input)
    
    # Vérification que le script est échappé
    assert "<script>" not in result
    assert "&lt;script&gt;" in result
```

---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Écrivez des tests pour une classe `StringUtils` avec des méthodes `reverse()`, `palindrome()`, et `count_vowels()`.

**Exercice 2 :** Implémentez une fonction `validate_email()` avec des tests complets.

**Exercice 3 :** Créez des tests pour une API REST simple avec des mocks.

---

# Solutions des Exercices 💡

### Exercice 1 - Tests StringUtils

```python
import unittest

class TestStringUtils(unittest.TestCase):
    def test_reverse(self):
        """Test de la méthode reverse"""
        utils = StringUtils()
        self.assertEqual(utils.reverse("hello"), "olleh")
        self.assertEqual(utils.reverse(""), "")
        self.assertEqual(utils.reverse("a"), "a")
    
    def test_palindrome(self):
        """Test de la méthode palindrome"""
        utils = StringUtils()
        self.assertTrue(utils.palindrome("radar"))
        self.assertTrue(utils.palindrome(""))
        self.assertFalse(utils.palindrome("hello"))
    
    def test_count_vowels(self):
        """Test de la méthode count_vowels"""
        utils = StringUtils()
        self.assertEqual(utils.count_vowels("hello"), 2)
        self.assertEqual(utils.count_vowels("xyz"), 0)
        self.assertEqual(utils.count_vowels("aeiou"), 5)
```

---

# Implémentation StringUtils

```python
class StringUtils:
    def reverse(self, text):
        """Inverse une chaîne"""
        return text[::-1]
    
    def palindrome(self, text):
        """Vérifie si une chaîne est un palindrome"""
        return text == text[::-1]
    
    def count_vowels(self, text):
        """Compte les voyelles dans une chaîne"""
        vowels = "aeiouAEIOU"
        return sum(1 for char in text if char in vowels)
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Validation Email

```python
import re
import unittest

def validate_email(email):
    """Valide une adresse email"""
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(pattern, email))

class TestEmailValidation(unittest.TestCase):
    def test_valid_emails(self):
        """Test d'emails valides"""
        valid_emails = [
            "user@example.com",
            "user.name@domain.co.uk",
            "user+tag@example.org"
        ]
        for email in valid_emails:
            with self.subTest(email=email):
                self.assertTrue(validate_email(email))
    
    def test_invalid_emails(self):
        """Test d'emails invalides"""
        invalid_emails = [
            "invalid",
            "@example.com",
            "user@",
            "user@.com"
        ]
        for email in invalid_emails:
            with self.subTest(email=email):
                self.assertFalse(validate_email(email))
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Tests API REST

```python
from unittest.mock import Mock, patch
import unittest

class TestUserAPI(unittest.TestCase):
    def setUp(self):
        """Setup pour les tests API"""
        self.mock_db = Mock()
        self.api = UserAPI(self.mock_db)
    
    def test_get_user_success(self):
        """Test de récupération d'utilisateur"""
        # Mock de la base de données
        self.mock_db.get_user.return_value = {
            "id": 1,
            "name": "John",
            "email": "john@example.com"
        }
        
        # Test de l'API
        result = self.api.get_user(1)
        
        # Vérifications
        self.assertEqual(result["name"], "John")
        self.mock_db.get_user.assert_called_once_with(1)
    
    def test_get_user_not_found(self):
        """Test utilisateur non trouvé"""
        self.mock_db.get_user.return_value = None
        
        with self.assertRaises(ValueError):
            self.api.get_user(999)
```

---

# Points Clés à Retenir 🎯

### Résumé des tests et TDD

**1. Tests Unitaires :**
- Tests isolés et rapides
- Couverture de code
- Détection précoce des bugs

**2. TDD :**
- Cycle Red-Green-Refactor
- Code testé dès le début
- Documentation vivante

**3. Bonnes Pratiques :**
- Tests indépendants
- Noms de tests explicites
- Mocks pour les dépendances
- Couverture de code

**Prêt pour les design patterns !** 🚀 