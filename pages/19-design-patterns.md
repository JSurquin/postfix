---
layout: default
routeAlias: 'design-patterns'
---

# DESIGN PATTERNS 📐

### Patterns de conception en Python

---

# Qu'est-ce qu'un Design Pattern ?

### Solutions réutilisables aux problèmes courants

**Catégories principales :**
- Patterns de création (Factory, Singleton, Builder)
- Patterns structurels (Adapter, Decorator, Proxy)
- Patterns comportementaux (Observer, Strategy, Command)

**Avantages :**
- Code réutilisable et maintenable
- Solutions éprouvées
- Communication entre développeurs

---

# Singleton Pattern

### Garantit une seule instance

```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        if not hasattr(self, 'initialized'):
            self.initialized = True
            self.data = []

# Utilisation
s1 = Singleton()
s2 = Singleton()
print(s1 is s2)  # True
```

---

# Factory Pattern

### Création d'objets sans exposer la logique

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

class AnimalFactory:
    @staticmethod
    def create_animal(animal_type):
        if animal_type == "dog":
            return Dog()
        elif animal_type == "cat":
            return Cat()
        else:
            raise ValueError(f"Unknown animal type: {animal_type}")

# Utilisation
animal = AnimalFactory.create_animal("dog")
print(animal.speak())  # Woof!
```

---

# Observer Pattern

### Notification automatique des changements

```python
from abc import ABC, abstractmethod

class Observer(ABC):
    @abstractmethod
    def update(self, subject):
        pass

class Subject:
    def __init__(self):
        self._observers = []
        self._state = None
    
    def attach(self, observer):
        self._observers.append(observer)
    
    def detach(self, observer):
        self._observers.remove(observer)
    
    def notify(self):
        for observer in self._observers:
            observer.update(self)
    
    @property
    def state(self):
        return self._state
    
    @state.setter
    def state(self, value):
        self._state = value
        self.notify()
```

---

# Utilisation de l'Observer

```python
class ConcreteObserver(Observer):
    def __init__(self, name):
        self.name = name
    
    def update(self, subject):
        print(f"{self.name} notified: state = {subject.state}")

# Utilisation
subject = Subject()
observer1 = ConcreteObserver("Observer 1")
observer2 = ConcreteObserver("Observer 2")

subject.attach(observer1)
subject.attach(observer2)

subject.state = "New State"
# Output:
# Observer 1 notified: state = New State
# Observer 2 notified: state = New State
```

---

# Strategy Pattern

### Algorithmes interchangeables

```python
from abc import ABC, abstractmethod

class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

class CreditCardPayment(PaymentStrategy):
    def pay(self, amount):
        return f"Paid {amount} with Credit Card"

class PayPalPayment(PaymentStrategy):
    def pay(self, amount):
        return f"Paid {amount} with PayPal"

class CashPayment(PaymentStrategy):
    def pay(self, amount):
        return f"Paid {amount} with Cash"

class ShoppingCart:
    def __init__(self):
        self.payment_strategy = None
    
    def set_payment_strategy(self, strategy):
        self.payment_strategy = strategy
    
    def checkout(self, amount):
        if self.payment_strategy:
            return self.payment_strategy.pay(amount)
        else:
            raise ValueError("No payment strategy set")
```

---

# Utilisation du Strategy

```python
# Utilisation
cart = ShoppingCart()

# Paiement par carte
cart.set_payment_strategy(CreditCardPayment())
print(cart.checkout(100))

# Paiement par PayPal
cart.set_payment_strategy(PayPalPayment())
print(cart.checkout(50))

# Paiement en espèces
cart.set_payment_strategy(CashPayment())
print(cart.checkout(25))
```

---

# Decorator Pattern

### Ajout de fonctionnalités dynamiquement

```python
from abc import ABC, abstractmethod

class Coffee(ABC):
    @abstractmethod
    def cost(self):
        pass
    
    @abstractmethod
    def description(self):
        pass

class SimpleCoffee(Coffee):
    def cost(self):
        return 2.0
    
    def description(self):
        return "Simple coffee"

class CoffeeDecorator(Coffee):
    def __init__(self, coffee):
        self._coffee = coffee
    
    def cost(self):
        return self._coffee.cost()
    
    def description(self):
        return self._coffee.description()

class Milk(CoffeeDecorator):
    def cost(self):
        return self._coffee.cost() + 0.5
    
    def description(self):
        return self._coffee.description() + ", milk"

class Sugar(CoffeeDecorator):
    def cost(self):
        return self._coffee.cost() + 0.2
    
    def description(self):
        return self._coffee.description() + ", sugar"
```

---

# Utilisation du Decorator

```python
# Utilisation
coffee = SimpleCoffee()
print(f"{coffee.description()}: ${coffee.cost()}")

coffee_with_milk = Milk(coffee)
print(f"{coffee_with_milk.description()}: ${coffee_with_milk.cost()}")

coffee_with_milk_and_sugar = Sugar(coffee_with_milk)
print(f"{coffee_with_milk_and_sugar.description()}: ${coffee_with_milk_and_sugar.cost()}")
```

---

# Command Pattern

### Encapsulation des requêtes

```python
from abc import ABC, abstractmethod

class Command(ABC):
    @abstractmethod
    def execute(self):
        pass
    
    @abstractmethod
    def undo(self):
        pass

class Light:
    def turn_on(self):
        return "Light is ON"
    
    def turn_off(self):
        return "Light is OFF"

class LightOnCommand(Command):
    def __init__(self, light):
        self.light = light
    
    def execute(self):
        return self.light.turn_on()
    
    def undo(self):
        return self.light.turn_off()

class LightOffCommand(Command):
    def __init__(self, light):
        self.light = light
    
    def execute(self):
        return self.light.turn_off()
    
    def undo(self):
        return self.light.turn_on()
```

---

# Utilisation du Command

```python
class RemoteControl:
    def __init__(self):
        self.commands = {}
    
    def set_command(self, slot, command):
        self.commands[slot] = command
    
    def press_button(self, slot):
        if slot in self.commands:
            return self.commands[slot].execute()
        else:
            return "No command set for this slot"

# Utilisation
light = Light()
remote = RemoteControl()

remote.set_command(0, LightOnCommand(light))
remote.set_command(1, LightOffCommand(light))

print(remote.press_button(0))  # Light is ON
print(remote.press_button(1))  # Light is OFF
```

---

# Adapter Pattern

### Interface incompatible

```python
# Interface cible
class Target:
    def request(self):
        return "Target: The default target's behavior."

# Interface incompatible
class Adaptee:
    def specific_request(self):
        return ".eetpadA eht fo roivaheb laicepS"

# Adapter
class Adapter(Target):
    def __init__(self, adaptee):
        self.adaptee = adaptee
    
    def request(self):
        result = self.adaptee.specific_request()
        return f"Adapter: (TRANSLATED) {result[::-1]}"

# Utilisation
adaptee = Adaptee()
adapter = Adapter(adaptee)
print(adapter.request())
```

---

# Builder Pattern

### Construction d'objets complexes

```python
class Computer:
    def __init__(self):
        self.parts = []
    
    def add(self, part):
        self.parts.append(part)
    
    def list_parts(self):
        return f"Computer parts: {', '.join(self.parts)}"

class ComputerBuilder:
    def __init__(self):
        self.reset()
    
    def reset(self):
        self._computer = Computer()
    
    def build_cpu(self):
        self._computer.add("CPU")
        return self
    
    def build_memory(self):
        self._computer.add("Memory")
        return self
    
    def build_storage(self):
        self._computer.add("Storage")
        return self
    
    def get_result(self):
        result = self._computer
        self.reset()
        return result
```

---

# Utilisation du Builder

```python
# Utilisation
builder = ComputerBuilder()
computer = (builder
    .build_cpu()
    .build_memory()
    .build_storage()
    .get_result())

print(computer.list_parts())
# Output: Computer parts: CPU, Memory, Storage
```

---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Implémentez un pattern Observer pour un système de notifications.

**Exercice 2 :** Créez un pattern Strategy pour différents algorithmes de tri.

**Exercice 3 :** Implémentez un pattern Factory pour créer différents types de documents.

---

# Solutions des Exercices 💡

### Exercice 1 - Observer pour Notifications

```python
class NotificationSystem:
    def __init__(self):
        self._observers = []
    
    def add_observer(self, observer):
        self._observers.append(observer)
    
    def remove_observer(self, observer):
        self._observers.remove(observer)
    
    def notify_all(self, message):
        for observer in self._observers:
            observer.update(message)

class EmailNotifier:
    def update(self, message):
        print(f"Email sent: {message}")

class SMSNotifier:
    def update(self, message):
        print(f"SMS sent: {message}")

# Utilisation
system = NotificationSystem()
system.add_observer(EmailNotifier())
system.add_observer(SMSNotifier())
system.notify_all("System maintenance at 2 AM")
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Strategy pour le Tri

```python
from abc import ABC, abstractmethod

class SortStrategy(ABC):
    @abstractmethod
    def sort(self, data):
        pass

class BubbleSort(SortStrategy):
    def sort(self, data):
        return sorted(data)  # Simplifié

class QuickSort(SortStrategy):
    def sort(self, data):
        return sorted(data)  # Simplifié

class Sorter:
    def __init__(self, strategy=None):
        self.strategy = strategy or BubbleSort()
    
    def set_strategy(self, strategy):
        self.strategy = strategy
    
    def sort(self, data):
        return self.strategy.sort(data)

# Utilisation
sorter = Sorter()
data = [3, 1, 4, 1, 5, 9, 2, 6]
print(sorter.sort(data))

sorter.set_strategy(QuickSort())
print(sorter.sort(data))
```

---

# Solutions des Exercices (fin)

### Exercice 3 - Factory pour Documents

```python
from abc import ABC, abstractmethod

class Document(ABC):
    @abstractmethod
    def create(self):
        pass

class PDFDocument(Document):
    def create(self):
        return "PDF document created"

class WordDocument(Document):
    def create(self):
        return "Word document created"

class DocumentFactory:
    @staticmethod
    def create_document(doc_type):
        if doc_type == "pdf":
            return PDFDocument()
        elif doc_type == "word":
            return WordDocument()
        else:
            raise ValueError(f"Unknown document type: {doc_type}")

# Utilisation
factory = DocumentFactory()
pdf_doc = factory.create_document("pdf")
word_doc = factory.create_document("word")

print(pdf_doc.create())
print(word_doc.create())
```

---

# Points Clés à Retenir 🎯

### Résumé des design patterns

**1. Patterns de Création :**
- Factory : création d'objets
- Singleton : instance unique
- Builder : construction complexe

**2. Patterns Structurels :**
- Adapter : interface incompatible
- Decorator : fonctionnalités dynamiques
- Proxy : contrôle d'accès

**3. Patterns Comportementaux :**
- Observer : notifications
- Strategy : algorithmes interchangeables
- Command : encapsulation des requêtes

**Prêt pour l'optimisation !** 🚀 