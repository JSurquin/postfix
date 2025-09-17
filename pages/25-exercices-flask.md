---
layout: default
routeAlias: 'exercices-flask'
---

# EXERCICES FLASK 🔧

### Pratique avec le micro-framework Flask

---

# Structure des Exercices 📋

### Progression pédagogique

**4 niveaux de difficulté :**
- ⚡ **Débutant** - Applications simples
- 🌊 **Intermédiaire** - Fonctionnalités avancées
- 🔥 **Avancé** - Applications complexes
- 💎 **Expert** - Production et optimisation

**Types d'exercices :**
- Routes et templates
- Formulaires et validation
- Base de données et ORM
- API REST et authentification

---
layout: default
---

# Exercice 1 - Application de Notes ⚡

### Créer une app de prise de notes

**Objectifs :**
- Setup Flask avec application factory
- Modèles SQLAlchemy
- CRUD complet
- Templates Bootstrap

**Temps estimé :** 2 heures

---

# Exercice 1 - Structure Initial

```bash
# 1. Setup du projet
mkdir notes_app
cd notes_app
python -m venv venv
source venv/bin/activate  # Linux/Mac
pip install Flask Flask-SQLAlchemy Flask-WTF

# 2. Structure attendue
notes_app/
├── app/
│   ├── __init__.py
│   ├── models.py
│   ├── routes.py
│   ├── forms.py
│   └── templates/
│       ├── base.html
│       ├── index.html
│       ├── create.html
│       └── edit.html
├── config.py
└── run.py
```

---

# Exercice 1 - Configuration

```python
# config.py - À implémenter
import os

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'dev-secret-key'
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'sqlite:///notes.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False

# app/__init__.py - À compléter
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

def create_app():
    app = Flask(__name__)
    app.config.from_object('config.Config')
    
    # Initialiser les extensions
    db.init_app(app)
    
    # Enregistrer les routes
    from app.routes import main_bp
    app.register_blueprint(main_bp)
    
    return app
```

---

# Exercice 1 - Modèle Note

```python
# app/models.py - À implémenter
from app import db
from datetime import datetime

class Note(db.Model):
    # Champs à ajouter :
    # - id (Integer, primary_key)
    # - titre (String(100), nullable=False)
    # - contenu (Text)
    # - date_creation (DateTime, default=datetime.utcnow)
    # - date_modification (DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
    # - priorite (String(10), default='normale') # basse, normale, haute
    # - terminee (Boolean, default=False)
    
    def __repr__(self):
        return f'<Note {self.titre}>'
    
    def to_dict(self):
        # Méthode pour convertir en dictionnaire (utile pour l'API)
        pass
```

---

# Exercice 1 - Formulaires

```python
# app/forms.py - À implémenter
from flask_wtf import FlaskForm
from wtforms import StringField, TextAreaField, SelectField, BooleanField, SubmitField
from wtforms.validators import DataRequired, Length

class NoteForm(FlaskForm):
    titre = StringField('Titre', validators=[
        DataRequired(),
        Length(min=1, max=100)
    ])
    
    contenu = TextAreaField('Contenu', validators=[Length(max=1000)])
    
    priorite = SelectField('Priorité', choices=[
        ('basse', 'Basse'),
        ('normale', 'Normale'),
        ('haute', 'Haute')
    ], default='normale')
    
    terminee = BooleanField('Terminée')
    
    submit = SubmitField('Sauvegarder')
    
    def validate_titre(self, titre):
        # Validation personnalisée : pas de titre "TODO"
        if titre.data.lower() == 'todo':
            raise ValidationError('Le titre ne peut pas être "TODO"')
```

---

# Exercice 1 - Routes

```python
# app/routes.py - À implémenter
from flask import Blueprint, render_template, request, redirect, url_for, flash, jsonify
from app import db
from app.models import Note
from app.forms import NoteForm

main_bp = Blueprint('main', __name__)

@main_bp.route('/')
def index():
    # Récupérer toutes les notes, ordonner par priorité puis par date
    # Séparer les notes terminées et non terminées
    # Rendre le template index.html
    pass

@main_bp.route('/create', methods=['GET', 'POST'])
def create_note():
    # GET : afficher le formulaire
    # POST : traiter et sauvegarder la note
    # Rediriger vers index avec message de succès
    pass

@main_bp.route('/edit/<int:note_id>', methods=['GET', 'POST'])
def edit_note(note_id):
    # Récupérer la note (404 si pas trouvée)
    # GET : pré-remplir le formulaire
    # POST : mettre à jour la note
    pass

@main_bp.route('/delete/<int:note_id>', methods=['POST'])
def delete_note(note_id):
    # Supprimer la note et rediriger
    pass

@main_bp.route('/toggle/<int:note_id>', methods=['POST'])
def toggle_note(note_id):
    # Basculer le statut terminé/non terminé
    pass
```

---

# Exercice 1 - Templates

```html
<!-- app/templates/base.html - À créer -->
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Notes App{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
        <div class="container">
            <a class="navbar-brand" href="{{ url_for('main.index') }}">📝 Mes Notes</a>
            <a class="btn btn-light" href="{{ url_for('main.create_note') }}">Nouvelle Note</a>
        </div>
    </nav>
    
    <main class="container mt-4">
        <!-- Messages flash -->
        {% with messages = get_flashed_messages(with_categories=true) %}
            {% if messages %}
                {% for category, message in messages %}
                    <div class="alert alert-{{ 'danger' if category == 'error' else 'success' }} alert-dismissible">
                        {{ message }}
                        <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                    </div>
                {% endfor %}
            {% endif %}
        {% endwith %}
        
        {% block content %}{% endblock %}
    </main>
</body>
</html>

<!-- app/templates/index.html - Template pour lister les notes -->
<!-- app/templates/create.html - Template pour créer/modifier une note -->
```

---

# Exercice 1 - Point d'Entrée

```python
# run.py - À créer
from app import create_app, db

app = create_app()

@app.before_first_request
def create_tables():
    db.create_all()

if __name__ == '__main__':
    app.run(debug=True)
```

---
layout: default
---

# Exercice 2 - Système de Tâches 🌊

### Application de gestion de tâches

**Objectifs :**
- Authentification utilisateur
- Relations entre modèles
- Filtres et recherche
- Ajax pour interactions

**Temps estimé :** 3 heures

---

# Exercice 2 - Modèles Étendus

```python
# app/models.py - Ajouter ces modèles
from flask_login import UserMixin
from werkzeug.security import generate_password_hash, check_password_hash

class User(UserMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128))
    date_creation = db.Column(db.DateTime, default=datetime.utcnow)
    
    # Relation avec les tâches
    taches = db.relationship('Tache', backref='proprietaire', lazy=True)
    
    def set_password(self, password):
        # Implémenter le hachage du mot de passe
        pass
    
    def check_password(self, password):
        # Vérifier le mot de passe
        pass

class Projet(db.Model):
    # Modèle pour organiser les tâches en projets
    # Champs : id, nom, description, couleur, user_id
    pass

class Tache(db.Model):
    # Extension du modèle Note avec :
    # - Relation vers User et Projet
    # - Date d'échéance
    # - Statut (nouvelle, en_cours, terminee)
    # - Tags (relation Many-to-Many)
    pass

class Tag(db.Model):
    # Modèle pour les tags
    # Table d'association tache_tags
    pass
```

---

# Exercice 2 - Authentification

```python
# app/auth.py - Nouveau blueprint
from flask import Blueprint, render_template, request, redirect, url_for, flash
from flask_login import login_user, logout_user, login_required, current_user
from app import db
from app.models import User
from app.forms import LoginForm, RegisterForm

auth_bp = Blueprint('auth', __name__, url_prefix='/auth')

@auth_bp.route('/login', methods=['GET', 'POST'])
def login():
    # Implémenter la connexion
    pass

@auth_bp.route('/register', methods=['GET', 'POST'])
def register():
    # Implémenter l'inscription
    pass

@auth_bp.route('/logout')
@login_required
def logout():
    # Déconnexion
    pass

# Formulaires dans app/forms.py
class LoginForm(FlaskForm):
    username = StringField('Nom d\'utilisateur', validators=[DataRequired()])
    password = PasswordField('Mot de passe', validators=[DataRequired()])
    remember_me = BooleanField('Se souvenir de moi')
    submit = SubmitField('Se connecter')

class RegisterForm(FlaskForm):
    # Formulaire d'inscription avec validation
    pass
```

---

# Exercice 2 - Vues Protégées

```python
# app/routes.py - Modifier pour l'authentification
from flask_login import login_required, current_user

@main_bp.route('/')
@login_required
def dashboard():
    # Tableau de bord personnalisé
    # Statistiques des tâches de l'utilisateur
    # Tâches en retard, à faire aujourd'hui, etc.
    pass

@main_bp.route('/projets')
@login_required
def projets():
    # Liste des projets de l'utilisateur
    pass

@main_bp.route('/taches')
@login_required
def taches():
    # Liste des tâches avec filtres
    # Paramètres GET : statut, projet, tag, recherche
    pass

@main_bp.route('/api/taches/<int:tache_id>/toggle', methods=['POST'])
@login_required
def api_toggle_tache(tache_id):
    # API pour basculer le statut d'une tâche (Ajax)
    # Vérifier que la tâche appartient à l'utilisateur
    # Retourner JSON
    pass
```

---

# Exercice 2 - Templates Avancés

```html
<!-- app/templates/dashboard.html -->
{% extends "base.html" %}

{% block content %}
<div class="row">
    <div class="col-md-3">
        <!-- Sidebar avec statistiques -->
        <div class="card">
            <div class="card-header">
                <h5>📊 Statistiques</h5>
            </div>
            <div class="card-body">
                <p>Tâches en cours: <span class="badge bg-primary">{{ stats.en_cours }}</span></p>
                <p>Terminées: <span class="badge bg-success">{{ stats.terminees }}</span></p>
                <p>En retard: <span class="badge bg-danger">{{ stats.en_retard }}</span></p>
            </div>
        </div>
    </div>
    
    <div class="col-md-9">
        <!-- Liste des tâches importantes -->
        <h3>🎯 Tâches importantes</h3>
        <!-- Implement task list with Ajax toggle -->
    </div>
</div>

<script>
// JavaScript pour les interactions Ajax
function toggleTache(tacheId) {
    // Envoyer requête AJAX pour basculer le statut
    fetch(`/api/taches/${tacheId}/toggle`, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'X-CSRFToken': '{{ csrf_token() }}'
        }
    })
    .then(response => response.json())
    .then(data => {
        // Mettre à jour l'interface
        location.reload();
    });
}
</script>
{% endblock %}
```

---
layout: default
---

# Exercice 3 - API de Blog 🔥

### Blog avec API REST complète

**Objectifs :**
- API REST avec Flask-RESTful
- Authentification JWT
- Validation avec Marshmallow
- Tests automatisés

**Temps estimé :** 4 heures

---

# Exercice 3 - Setup API

```python
# app/__init__.py - Configuration API
from flask_restful import Api
from flask_jwt_extended import JWTManager
from flask_marshmallow import Marshmallow

api = Api()
jwt = JWTManager()
ma = Marshmallow()

def create_app():
    app = Flask(__name__)
    app.config.from_object(Config)
    
    # Extensions
    db.init_app(app)
    api.init_app(app)
    jwt.init_app(app)
    ma.init_app(app)
    
    # Blueprints
    from app.api.resources import api_bp
    app.register_blueprint(api_bp, url_prefix='/api/v1')
    
    return app

# Installation des dépendances
# pip install Flask-RESTful Flask-JWT-Extended marshmallow
```

---

# Exercice 3 - Modèles Blog

```python
# app/models.py - Modèles pour le blog
class User(UserMixin, db.Model):
    # Modèle utilisateur étendu avec rôles
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128))
    role = db.Column(db.String(20), default='user')  # user, admin
    articles = db.relationship('Article', backref='auteur', lazy=True)

class Categorie(db.Model):
    # Catégories d'articles
    pass

class Article(db.Model):
    # Articles de blog
    id = db.Column(db.Integer, primary_key=True)
    titre = db.Column(db.String(200), nullable=False)
    contenu = db.Column(db.Text, nullable=False)
    resume = db.Column(db.String(300))
    date_creation = db.Column(db.DateTime, default=datetime.utcnow)
    date_publication = db.Column(db.DateTime)
    publie = db.Column(db.Boolean, default=False)
    vues = db.Column(db.Integer, default=0)
    
    # Relations
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    categorie_id = db.Column(db.Integer, db.ForeignKey('categorie.id'))

class Commentaire(db.Model):
    # Commentaires sur les articles
    pass
```

---

# Exercice 3 - Schémas Marshmallow

```python
# app/schemas.py - À créer
from marshmallow import Schema, fields, validate, post_load
from app.models import User, Article, Categorie

class UserSchema(ma.SQLAlchemyAutoSchema):
    class Meta:
        model = User
        load_instance = True
        exclude = ('password_hash',)
    
    password = fields.Str(write_only=True, validate=validate.Length(min=6))
    
    @post_load
    def hash_password(self, data, **kwargs):
        # Hasher le mot de passe avant sauvegarde
        if 'password' in data:
            user = User(**{k: v for k, v in data.items() if k != 'password'})
            user.set_password(data['password'])
            return user
        return User(**data)

class ArticleSchema(ma.SQLAlchemyAutoSchema):
    class Meta:
        model = Article
        load_instance = True
        include_fk = True
    
    auteur = fields.Nested(UserSchema, only=['id', 'username'], dump_only=True)
    date_creation = fields.DateTime(dump_only=True)
    
    def validate_titre(self, titre):
        # Validation personnalisée
        if len(titre) < 10:
            raise ValidationError('Le titre doit contenir au moins 10 caractères')

# Instances des schémas
user_schema = UserSchema()
users_schema = UserSchema(many=True)
article_schema = ArticleSchema()
articles_schema = ArticleSchema(many=True)
```

---

# Exercice 3 - Ressources API

```python
# app/api/resources.py - À créer
from flask_restful import Resource, request
from flask_jwt_extended import jwt_required, get_jwt_identity, create_access_token
from app.models import User, Article
from app.schemas import user_schema, article_schema, articles_schema
from app import db

class AuthResource(Resource):
    def post(self):
        # Endpoint de connexion
        # Vérifier les identifiants
        # Retourner le token JWT
        pass

class ArticleListResource(Resource):
    def get(self):
        # Liste des articles avec pagination et filtres
        # Paramètres : page, per_page, auteur, categorie, recherche
        page = request.args.get('page', 1, type=int)
        per_page = request.args.get('per_page', 10, type=int)
        
        # Implémenter la pagination et les filtres
        pass
    
    @jwt_required()
    def post(self):
        # Créer un nouvel article
        # Valider les données avec le schéma
        # Assigner l'auteur à l'utilisateur connecté
        pass

class ArticleResource(Resource):
    def get(self, article_id):
        # Récupérer un article par ID
        # Incrémenter le compteur de vues
        pass
    
    @jwt_required()
    def put(self, article_id):
        # Mettre à jour un article (auteur ou admin uniquement)
        pass
    
    @jwt_required()
    def delete(self, article_id):
        # Supprimer un article (auteur ou admin uniquement)
        pass

class StatsResource(Resource):
    def get(self):
        # Statistiques générales du blog
        # Nombre d'articles, utilisateurs, articles par catégorie
        pass
```

---

# Exercice 3 - Tests API

```python
# tests/test_api.py - À créer
import pytest
import json
from app import create_app, db
from app.models import User, Article

@pytest.fixture
def app():
    app = create_app()
    app.config['TESTING'] = True
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///:memory:'
    
    with app.app_context():
        db.create_all()
        yield app
        db.drop_all()

@pytest.fixture
def client(app):
    return app.test_client()

@pytest.fixture
def auth_headers(client, app):
    # Créer un utilisateur et retourner les headers d'auth
    with app.app_context():
        user = User(username='testuser', email='test@example.com')
        user.set_password('testpass')
        db.session.add(user)
        db.session.commit()
    
    # Se connecter et récupérer le token
    response = client.post('/api/v1/auth', json={
        'username': 'testuser',
        'password': 'testpass'
    })
    token = json.loads(response.data)['access_token']
    return {'Authorization': f'Bearer {token}'}

def test_get_articles(client):
    # Test récupération liste d'articles
    response = client.get('/api/v1/articles')
    assert response.status_code == 200
    
def test_create_article(client, auth_headers):
    # Test création d'article
    article_data = {
        'titre': 'Test Article',
        'contenu': 'Contenu de test pour l\'article'
    }
    response = client.post('/api/v1/articles', 
                          json=article_data, 
                          headers=auth_headers)
    assert response.status_code == 201

def test_update_article_unauthorized(client):
    # Test modification sans authentification
    response = client.put('/api/v1/articles/1', json={'titre': 'Nouveau titre'})
    assert response.status_code == 401
```

---
layout: default
---

# Exercice 4 - Marketplace 💎

### Plateforme de vente en ligne

**Objectifs :**
- Architecture complexe avec modules
- Paiements et commandes
- Upload de fichiers
- Performance et cache

**Temps estimé :** 8 heures

---

# Exercice 4 - Architecture Modulaire

```python
# Structure avancée du projet
marketplace/
├── app/
│   ├── __init__.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── product.py
│   │   └── order.py
│   ├── blueprints/
│   │   ├── main/
│   │   ├── auth/
│   │   ├── shop/
│   │   ├── admin/
│   │   └── api/
│   ├── services/
│   │   ├── __init__.py
│   │   ├── payment.py
│   │   ├── email.py
│   │   └── upload.py
│   ├── utils/
│   └── static/
├── migrations/
├── tests/
├── config.py
└── run.py
```

---

# Exercice 4 - Modèles Complexes

```python
# app/models/user.py
class User(UserMixin, db.Model):
    # Utilisateur avec profil vendeur/acheteur
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128))
    role = db.Column(db.String(20), default='buyer')  # buyer, seller, admin
    
    # Profil
    profile = db.relationship('UserProfile', backref='user', uselist=False)
    
    # Relations vendeur
    products = db.relationship('Product', backref='seller', lazy=True)
    
    # Relations acheteur
    orders = db.relationship('Order', backref='buyer', lazy=True)

class UserProfile(db.Model):
    # Profil étendu de l'utilisateur
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    first_name = db.Column(db.String(50))
    last_name = db.Column(db.String(50))
    phone = db.Column(db.String(20))
    address = db.Column(db.Text)
    avatar = db.Column(db.String(200))

# app/models/product.py
class Category(db.Model):
    # Catégories hiérarchiques
    pass

class Product(db.Model):
    # Produits avec variantes et images
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(200), nullable=False)
    description = db.Column(db.Text)
    price = db.Column(db.Numeric(10, 2), nullable=False)
    stock = db.Column(db.Integer, default=0)
    status = db.Column(db.String(20), default='draft')  # draft, active, inactive
    
    # Relations
    seller_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    category_id = db.Column(db.Integer, db.ForeignKey('category.id'))
    
    # Images et variantes
    images = db.relationship('ProductImage', backref='product', lazy=True)
    variants = db.relationship('ProductVariant', backref='product', lazy=True)

class ProductImage(db.Model):
    # Images de produits
    pass

class ProductVariant(db.Model):
    # Variantes de produits (taille, couleur, etc.)
    pass

# app/models/order.py
class Order(db.Model):
    # Commandes avec statuts
    pass

class OrderItem(db.Model):
    # Articles de commande
    pass

class Payment(db.Model):
    # Paiements
    pass
```

---

# Exercice 4 - Services

```python
# app/services/payment.py
class PaymentService:
    """Service de gestion des paiements"""
    
    def __init__(self):
        self.stripe_api_key = current_app.config.get('STRIPE_SECRET_KEY')
    
    def create_payment_intent(self, order):
        """Créer une intention de paiement Stripe"""
        try:
            import stripe
            stripe.api_key = self.stripe_api_key
            
            intent = stripe.PaymentIntent.create(
                amount=int(order.total * 100),  # En centimes
                currency='eur',
                metadata={'order_id': order.id}
            )
            return intent
        except Exception as e:
            current_app.logger.error(f'Erreur paiement: {e}')
            return None
    
    def confirm_payment(self, payment_intent_id):
        """Confirmer le paiement et mettre à jour la commande"""
        pass

# app/services/upload.py
class UploadService:
    """Service de gestion des uploads"""
    
    def __init__(self):
        self.upload_folder = current_app.config.get('UPLOAD_FOLDER')
        self.allowed_extensions = {'png', 'jpg', 'jpeg', 'gif'}
    
    def upload_product_image(self, file, product_id):
        """Upload et redimensionnement d'image produit"""
        if file and self.allowed_file(file.filename):
            # Sécuriser le nom de fichier
            filename = secure_filename(file.filename)
            filename = f"{product_id}_{uuid.uuid4().hex}_{filename}"
            
            # Sauvegarder et redimensionner
            file_path = os.path.join(self.upload_folder, filename)
            file.save(file_path)
            
            # Redimensionner avec Pillow
            self.resize_image(file_path, (800, 600))
            
            return filename
        return None
    
    def resize_image(self, file_path, size):
        """Redimensionner une image"""
        from PIL import Image
        with Image.open(file_path) as img:
            img.thumbnail(size, Image.Resampling.LANCZOS)
            img.save(file_path, optimize=True, quality=85)
```

---

# Exercice 4 - Blueprint Shop

```python
# app/blueprints/shop/routes.py
from flask import Blueprint, render_template, request, jsonify
from flask_login import login_required, current_user
from app.models import Product, Category, Order
from app.services.payment import PaymentService

shop_bp = Blueprint('shop', __name__, url_prefix='/shop')

@shop_bp.route('/')
def catalog():
    # Catalogue avec filtres avancés
    # Implémenter recherche, tri, pagination
    # Faceted search par catégorie, prix, vendeur
    pass

@shop_bp.route('/product/<int:product_id>')
def product_detail(product_id):
    # Détail produit avec variantes et images
    # Recommandations basées sur la catégorie
    pass

@shop_bp.route('/cart')
@login_required
def cart():
    # Panier en session ou base de données
    pass

@shop_bp.route('/api/cart/add', methods=['POST'])
@login_required
def add_to_cart():
    # API pour ajouter au panier (Ajax)
    product_id = request.json.get('product_id')
    quantity = request.json.get('quantity', 1)
    variant_id = request.json.get('variant_id')
    
    # Vérifier stock et ajouter au panier
    # Retourner JSON avec nouveau total
    pass

@shop_bp.route('/checkout')
@login_required
def checkout():
    # Processus de commande
    # Récapitulatif, adresse, mode de paiement
    pass

@shop_bp.route('/api/payment/create-intent', methods=['POST'])
@login_required
def create_payment_intent():
    # Créer intention de paiement Stripe
    payment_service = PaymentService()
    # Implémenter la logique
    pass
```

---

# Exercice 4 - Performance et Cache

```python
# app/utils/cache.py
from flask_caching import Cache
from functools import wraps

cache = Cache()

def cached_view(timeout=300):
    """Décorateur pour mettre en cache les vues"""
    def decorator(f):
        @wraps(f)
        def decorated_function(*args, **kwargs):
            cache_key = f"{f.__name__}_{hash(str(args))}{hash(str(kwargs))}"
            result = cache.get(cache_key)
            if result is None:
                result = f(*args, **kwargs)
                cache.set(cache_key, result, timeout=timeout)
            return result
        return decorated_function
    return decorator

# Utilisation
@shop_bp.route('/api/products/popular')
@cached_view(timeout=3600)  # Cache 1 heure
def popular_products():
    # Produits populaires (requête coûteuse)
    pass

# app/utils/database.py
class DatabaseOptimizer:
    """Optimisations base de données"""
    
    @staticmethod
    def get_products_with_stats():
        """Requête optimisée avec statistiques"""
        from sqlalchemy import func
        
        return db.session.query(
            Product,
            func.avg(Review.rating).label('avg_rating'),
            func.count(OrderItem.id).label('sales_count')
        ).outerjoin(Review)\
         .outerjoin(OrderItem)\
         .group_by(Product.id)\
         .options(
             db.joinedload(Product.category),
             db.joinedload(Product.seller)
         ).all()
```

---

# Exercice 4 - Tests d'Intégration

```python
# tests/test_marketplace.py
import pytest
from app import create_app, db
from app.models import User, Product, Order

class TestMarketplace:
    
    @pytest.fixture(autouse=True)
    def setup(self):
        self.app = create_app('testing')
        self.app_context = self.app.app_context()
        self.app_context.push()
        db.create_all()
        yield
        db.session.remove()
        db.drop_all()
        self.app_context.pop()
    
    def test_complete_purchase_flow(self):
        """Test du flux complet d'achat"""
        # 1. Créer vendeur et produit
        seller = User(username='seller', email='seller@test.com', role='seller')
        seller.set_password('password')
        db.session.add(seller)
        
        product = Product(
            name='Test Product',
            price=29.99,
            stock=10,
            seller=seller,
            status='active'
        )
        db.session.add(product)
        
        # 2. Créer acheteur
        buyer = User(username='buyer', email='buyer@test.com', role='buyer')
        buyer.set_password('password')
        db.session.add(buyer)
        db.session.commit()
        
        # 3. Simuler l'achat
        with self.app.test_client() as client:
            # Connexion
            response = client.post('/auth/login', data={
                'username': 'buyer',
                'password': 'password'
            })
            assert response.status_code == 302
            
            # Ajouter au panier
            response = client.post('/shop/api/cart/add', json={
                'product_id': product.id,
                'quantity': 2
            })
            assert response.status_code == 200
            
            # Checkout
            response = client.get('/shop/checkout')
            assert response.status_code == 200
    
    def test_inventory_management(self):
        """Test gestion du stock"""
        # Tester réduction du stock après commande
        # Tester produit en rupture
        pass
    
    def test_seller_dashboard(self):
        """Test tableau de bord vendeur"""
        # Tester statistiques vendeur
        # Tester gestion des produits
        pass
```

---
layout: default
---

# Solutions et Corrections 💡

### Exercice 1 - Application de Notes

**Points clés de la solution :**
- Application factory pattern
- Modèles SQLAlchemy avec contraintes
- Formulaires WTF avec validation
- Templates Bootstrap responsifs

---

# Solution Exercice 1 - Modèle

```python
# app/models.py - Solution complète
from app import db
from datetime import datetime

class Note(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    titre = db.Column(db.String(100), nullable=False)
    contenu = db.Column(db.Text)
    date_creation = db.Column(db.DateTime, default=datetime.utcnow)
    date_modification = db.Column(db.DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
    priorite = db.Column(db.String(10), default='normale')
    terminee = db.Column(db.Boolean, default=False)
    
    def __repr__(self):
        return f'<Note {self.titre}>'
    
    def to_dict(self):
        return {
            'id': self.id,
            'titre': self.titre,
            'contenu': self.contenu,
            'date_creation': self.date_creation.isoformat() if self.date_creation else None,
            'date_modification': self.date_modification.isoformat() if self.date_modification else None,
            'priorite': self.priorite,
            'terminee': self.terminee
        }
    
    @property
    def priorite_color(self):
        colors = {
            'basse': 'success',
            'normale': 'primary',
            'haute': 'danger'
        }
        return colors.get(self.priorite, 'secondary')
```

---

# Solution Exercice 1 - Routes

```python
# app/routes.py - Solution complète
from flask import Blueprint, render_template, request, redirect, url_for, flash, jsonify
from app import db
from app.models import Note
from app.forms import NoteForm

main_bp = Blueprint('main', __name__)

@main_bp.route('/')
def index():
    # Récupérer et organiser les notes
    notes_actives = Note.query.filter_by(terminee=False)\
                             .order_by(
                                 db.case(
                                     (Note.priorite == 'haute', 1),
                                     (Note.priorite == 'normale', 2),
                                     (Note.priorite == 'basse', 3)
                                 ),
                                 Note.date_creation.desc()
                             ).all()
    
    notes_terminees = Note.query.filter_by(terminee=True)\
                                .order_by(Note.date_modification.desc())\
                                .limit(5).all()
    
    # Statistiques
    stats = {
        'total': Note.query.count(),
        'actives': len(notes_actives),
        'terminees': Note.query.filter_by(terminee=True).count(),
        'haute_priorite': Note.query.filter_by(priorite='haute', terminee=False).count()
    }
    
    return render_template('index.html', 
                         notes_actives=notes_actives,
                         notes_terminees=notes_terminees,
                         stats=stats)

@main_bp.route('/create', methods=['GET', 'POST'])
def create_note():
    form = NoteForm()
    if form.validate_on_submit():
        note = Note(
            titre=form.titre.data,
            contenu=form.contenu.data,
            priorite=form.priorite.data,
            terminee=form.terminee.data
        )
        db.session.add(note)
        db.session.commit()
        flash(f'Note "{note.titre}" créée avec succès!', 'success')
        return redirect(url_for('main.index'))
    
    return render_template('create.html', form=form, title='Nouvelle Note')

@main_bp.route('/edit/<int:note_id>', methods=['GET', 'POST'])
def edit_note(note_id):
    note = Note.query.get_or_404(note_id)
    form = NoteForm(obj=note)
    
    if form.validate_on_submit():
        form.populate_obj(note)
        note.date_modification = datetime.utcnow()
        db.session.commit()
        flash(f'Note "{note.titre}" modifiée avec succès!', 'success')
        return redirect(url_for('main.index'))
    
    return render_template('create.html', form=form, note=note, title='Modifier Note')

@main_bp.route('/delete/<int:note_id>', methods=['POST'])
def delete_note(note_id):
    note = Note.query.get_or_404(note_id)
    titre = note.titre
    db.session.delete(note)
    db.session.commit()
    flash(f'Note "{titre}" supprimée.', 'info')
    return redirect(url_for('main.index'))

@main_bp.route('/toggle/<int:note_id>', methods=['POST'])
def toggle_note(note_id):
    note = Note.query.get_or_404(note_id)
    note.terminee = not note.terminee
    note.date_modification = datetime.utcnow()
    db.session.commit()
    
    status = 'terminée' if note.terminee else 'réactivée'
    flash(f'Note "{note.titre}" {status}.', 'success')
    return redirect(url_for('main.index'))

# API pour Ajax
@main_bp.route('/api/notes/<int:note_id>/toggle', methods=['POST'])
def api_toggle_note(note_id):
    note = Note.query.get_or_404(note_id)
    note.terminee = not note.terminee
    note.date_modification = datetime.utcnow()
    db.session.commit()
    
    return jsonify({
        'success': True,
        'terminee': note.terminee,
        'message': f'Note {note.titre} {"terminée" if note.terminee else "réactivée"}'
    })
```

---

# Grille d'Évaluation 📊

### Critères de notation

**Pour chaque exercice :**

| Critère | ⚡ Débutant | 🌊 Inter. | 🔥 Avancé | 💎 Expert |
|---------|-------------|-----------|-----------|-----------|
| **Fonctionnalité** | 5 pts | 8 pts | 12 pts | 20 pts |
| **Architecture** | 3 pts | 5 pts | 8 pts | 15 pts |
| **Sécurité** | 2 pts | 3 pts | 5 pts | 10 pts |
| **Tests** | 0 pts | 2 pts | 5 pts | 10 pts |
| **Performance** | 0 pts | 2 pts | 5 pts | 10 pts |

**Barème total : 65 points**

**Seuils de réussite :**
- ✅ **Validé** : 50+ points
- 🎯 **Bien** : 55+ points  
- 🏆 **Excellent** : 60+ points

---

# Projets Complémentaires 🚀

### Pour approfondir Flask

**Idées de projets avancés :**

1. **Système de Chat** - WebSockets avec Flask-SocketIO
2. **API GraphQL** - Avec Graphene-Python
3. **Microservices** - Architecture distribuée
4. **Analytics Dashboard** - Visualisation de données
5. **CMS Headless** - API pour frontend React/Vue

**Technologies à explorer :**
- Celery pour les tâches asynchrones
- Redis pour le cache et sessions
- Docker pour le déploiement
- ElasticSearch pour la recherche

**Maîtrisez Flask par la pratique !** ⚡
