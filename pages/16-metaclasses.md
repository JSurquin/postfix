---
layout: default
routeAlias: 'metaclasses'
---

# MÉTACLASSES ET INTROSPECTION 🏗️

### Métaprogrammation avancée en Python

---

# Qu'est-ce qu'une Métaclasse ?

### La classe des classes

```python
# Métaclasse basique
class Meta(type):
    def __new__(cls, name, bases, attrs):
        print(f"Création de la classe: {name}")
        return super().__new__(cls, name, bases, attrs)

class MaClasse(metaclass=Meta):
    pass

# Métaclasse avec validation
class ValidatedMeta(type):
    def __new__(cls, name, bases, attrs):
        # Validation des attributs
        if 'required_field' not in attrs:
            raise TypeError(f"La classe {name} doit avoir un attribut 'required_field'")
        return super().__new__(cls, name, bases, attrs)

class ValidatedClass(metaclass=ValidatedMeta):
    required_field = "valeur"
```

---

# Métaclasses pour l'Enregistrement

### Enregistrement automatique des classes

```python
class RegistryMeta(type):
    def __new__(cls, name, bases, attrs):
        # Enregistrement automatique
        if 'register' not in attrs:
            attrs['register'] = True
            if not hasattr(cls, '_registry'):
                cls._registry = {}
            cls._registry[name] = attrs
        return super().__new__(cls, name, bases, attrs)

class Plugin(metaclass=RegistryMeta):
    pass

class PluginA(Plugin):
    def execute(self):
        return "Plugin A"

class PluginB(Plugin):
    def execute(self):
        return "Plugin B"

# Accès au registre
print(Plugin._registry)
```

---

# Introspection Avancée

### Exploration des objets Python

```python
import inspect

class Example:
    def method(self, param):
        pass

# Introspection de classe
print(inspect.getmembers(Example))
print(inspect.signature(Example.method))

# Introspection d'objet
obj = Example()
print(inspect.getmembers(obj))

# Vérification de types
print(isinstance(obj, Example))
print(type(obj))
print(obj.__class__)
```

---

# Métaclasses pour les Singletons

### Pattern Singleton avec métaclasse

```python
class SingletonMeta(type):
    _instances = {}
    
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class Database(metaclass=SingletonMeta):
    def __init__(self):
        print("Initialisation de la base de données")

# Test
db1 = Database()
db2 = Database()
print(db1 is db2)  # True
```

---

# Métaclasses pour la Validation

### Validation automatique des attributs

```python
class ValidatedMeta(type):
    def __new__(cls, name, bases, attrs):
        # Validation des types d'attributs
        for attr_name, attr_value in attrs.items():
            if attr_name.startswith('_'):
                continue
            if hasattr(attr_value, '__annotations__'):
                # Validation des annotations de type
                pass
        return super().__new__(cls, name, bases, attrs)

class Model(metaclass=ValidatedMeta):
    def __init__(self, **kwargs):
        for key, value in kwargs.items():
            setattr(self, key, value)

class User(Model):
    name: str
    age: int

# Utilisation
user = User(name="John", age=30)
```

---

# Introspection des Modules

### Exploration des modules

```python
import sys
import inspect

def explore_module(module_name):
    module = sys.modules[module_name]
    
    print(f"Module: {module_name}")
    print(f"Fichier: {getattr(module, '__file__', 'N/A')}")
    
    # Fonctions et classes
    for name, obj in inspect.getmembers(module):
        if inspect.isfunction(obj):
            print(f"Fonction: {name}")
        elif inspect.isclass(obj):
            print(f"Classe: {name}")

# Exemple
explore_module('os')
```

---

# Métaclasses pour les Décorateurs

### Décorateurs automatiques avec métaclasses

```python
class AutoDecoratedMeta(type):
    def __new__(cls, name, bases, attrs):
        # Application automatique de décorateurs
        for attr_name, attr_value in attrs.items():
            if callable(attr_value) and not attr_name.startswith('_'):
                # Application d'un décorateur de logging
                attrs[attr_name] = cls.log_method(attr_value)
        return super().__new__(cls, name, bases, attrs)
    
    @staticmethod
    def log_method(method):
        def wrapper(*args, **kwargs):
            print(f"Appel de {method.__name__}")
            result = method(*args, **kwargs)
            print(f"Fin de {method.__name__}")
            return result
        return wrapper

class LoggedClass(metaclass=AutoDecoratedMeta):
    def method1(self):
        return "Résultat 1"
    
    def method2(self):
        return "Résultat 2"

# Test
obj = LoggedClass()
obj.method1()
```

---

# Exercices Avancés 🎯

### Testez vos compétences

**Exercice 1 :** Créez une métaclasse qui ajoute automatiquement des propriétés pour tous les attributs commençant par `_`.

**Exercice 2 :** Implémentez une métaclasse qui valide que toutes les méthodes publiques ont des docstrings.

**Exercice 3 :** Créez une métaclasse qui génère automatiquement des méthodes `__repr__` et `__eq__`.

---

# Solutions des Exercices 💡

### Exercice 1 - Propriétés Automatiques

```python
class PropertyMeta(type):
    def __new__(cls, name, bases, attrs):
        # Ajout de propriétés pour les attributs privés
        for attr_name in list(attrs.keys()):
            if attr_name.startswith('_') and not attr_name.startswith('__'):
                public_name = attr_name[1:]
                
                # Propriété getter
                def make_getter(attr):
                    def getter(self):
                        return getattr(self, attr)
                    return getter
                
                # Propriété setter
                def make_setter(attr):
                    def setter(self, value):
                        setattr(self, attr, value)
                    return setter
                
                attrs[public_name] = property(
                    make_getter(attr_name),
                    make_setter(attr_name)
                )
        
        return super().__new__(cls, name, bases, attrs)

class TestClass(metaclass=PropertyMeta):
    def __init__(self):
        self._value = 42

# Test
obj = TestClass()
print(obj.value)  # 42
obj.value = 100
print(obj._value)  # 100
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Validation des Docstrings

```python
class DocstringMeta(type):
    def __new__(cls, name, bases, attrs):
        for attr_name, attr_value in attrs.items():
            if (callable(attr_value) and 
                not attr_name.startswith('_') and
                not attr_value.__doc__):
                raise TypeError(
                    f"La méthode {attr_name} dans {name} doit avoir une docstring"
                )
        return super().__new__(cls, name, bases, attrs)

class ValidatedClass(metaclass=DocstringMeta):
    def method_with_doc(self):
        """Cette méthode a une docstring."""
        pass
    
    def method_without_doc(self):
        pass  # Erreur: pas de docstring
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Génération Automatique

```python
class AutoMethodsMeta(type):
    def __new__(cls, name, bases, attrs):
        # Génération de __repr__
        if '__repr__' not in attrs:
            def __repr__(self):
                attrs_str = ', '.join(f"{k}={v}" for k, v in self.__dict__.items())
                return f"{name}({attrs_str})"
            attrs['__repr__'] = __repr__
        
        # Génération de __eq__
        if '__eq__' not in attrs:
            def __eq__(self, other):
                if not isinstance(other, type(self)):
                    return False
                return self.__dict__ == other.__dict__
            attrs['__eq__'] = __eq__
        
        return super().__new__(cls, name, bases, attrs)

class AutoClass(metaclass=AutoMethodsMeta):
    def __init__(self, name, value):
        self.name = name
        self.value = value

# Test
obj1 = AutoClass("test", 42)
obj2 = AutoClass("test", 42)
print(repr(obj1))  # AutoClass(name=test, value=42)
print(obj1 == obj2)  # True
```

---

# Points Clés à Retenir 🎯

### Résumé des métaclasses et introspection

**1. Métaclasses :**
- Permettent de modifier la création de classes
- Utiles pour les patterns de design avancés
- Puissantes mais complexes

**2. Introspection :**
- Permet d'explorer les objets à l'exécution
- Utile pour les frameworks et outils
- Module `inspect` pour l'introspection avancée

**3. Applications :**
- Frameworks ORM
- Validation automatique
- Génération de code
- Patterns de design

**Prêt pour la programmation asynchrone !** 🚀 