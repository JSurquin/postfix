---
layout: default
routeAlias: 'flask-complet'
---

# FLASK - COURS COMPLET ⚡

### Le micro-framework web de Python

---

# Pourquoi Flask ? 🤔

### Avantages du micro-framework

**Philosophie Flask :**
- Simplicité et minimalisme
- Flexibilité maximale
- "Micro" mais extensible
- Apprentissage rapide

**Avantages principaux :**
- Démarrage ultra-rapide
- Structure libre
- Extensions nombreuses
- Documentation excellente
- Communauté active

---

# Installation et Premier Projet 🛠️

### Setup rapide de Flask

**Prérequis :**
- Python 3.7+ requis
- Environnement virtuel recommandé
- IDE avec support Python

---

# Installation Flask

```bash
# Création d'un environnement virtuel
python -m venv flask_env
source flask_env/bin/activate  # Linux/Mac
# flask_env\Scripts\activate   # Windows

# Installation de Flask
pip install Flask

# Vérification
python -c "import flask; print(flask.__version__)"
```

---

# Application Flask Minimale

```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return '<h1>Hello, Flask!</h1>'

@app.route('/user/<name>')
def show_user(name):
    return f'<h1>Bonjour {name}!</h1>'

if __name__ == '__main__':
    app.run(debug=True)
```

---

# Lancement de l'Application

```bash
# Méthode 1 : Exécution directe
python app.py

# Méthode 2 : Avec flask run
export FLASK_APP=app.py
export FLASK_ENV=development
flask run

# Méthode 3 : Avec options
flask run --host=0.0.0.0 --port=5000 --debug
```

---
layout: default
---

# Structure d'un Projet Flask 📁

### Organisation professionnelle

**Structure recommandée :**
- Application factory pattern
- Blueprints pour la modularité
- Configuration par environnement
- Séparation des responsabilités

---

# Structure de Projet

```
flask_project/
├── app/
│   ├── __init__.py          # Factory
│   ├── models.py            # Modèles SQLAlchemy
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── main.py          # Routes principales
│   │   ├── auth.py          # Authentification
│   │   └── api.py           # API REST
│   ├── templates/
│   │   ├── base.html
│   │   └── index.html
│   ├── static/
│   │   ├── css/
│   │   ├── js/
│   │   └── img/
│   └── utils.py
├── migrations/              # Base de données
├── tests/
├── config.py
├── requirements.txt
└── run.py
```

---

# Application Factory

```python
# app/__init__.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
from flask_login import LoginManager

db = SQLAlchemy()
migrate = Migrate()
login_manager = LoginManager()

def create_app(config_name='development'):
    app = Flask(__name__)
    
    # Configuration
    app.config.from_object(f'config.{config_name.title()}Config')
    
    # Initialisation des extensions
    db.init_app(app)
    migrate.init_app(app, db)
    login_manager.init_app(app)
    login_manager.login_view = 'auth.login'
    
    # Enregistrement des blueprints
    from app.routes.main import main_bp
    from app.routes.auth import auth_bp
    from app.routes.api import api_bp
    
    app.register_blueprint(main_bp)
    app.register_blueprint(auth_bp, url_prefix='/auth')
    app.register_blueprint(api_bp, url_prefix='/api')
    
    return app
```

---

# Configuration

```python
# config.py
import os
from datetime import timedelta

class BaseConfig:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'dev-secret-key'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    PERMANENT_SESSION_LIFETIME = timedelta(days=31)

class DevelopmentConfig(BaseConfig):
    DEBUG = True
    SQLALCHEMY_DATABASE_URI = os.environ.get('DEV_DATABASE_URL') or \
        'sqlite:///app_dev.db'

class ProductionConfig(BaseConfig):
    DEBUG = False
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \
        'postgresql://user:pass@localhost/app_prod'

class TestingConfig(BaseConfig):
    TESTING = True
    SQLALCHEMY_DATABASE_URI = 'sqlite:///:memory:'
    WTF_CSRF_ENABLED = False
```

---

# Point d'Entrée

```python
# run.py
import os
from app import create_app

app = create_app(os.environ.get('FLASK_CONFIG', 'development'))

if __name__ == '__main__':
    app.run()
```

---
layout: default
---

# Routage Flask 🛣️

### Gestion des URLs et méthodes HTTP

**Types de routes :**
- Routes statiques
- Routes avec paramètres
- Routes avec contraintes
- Méthodes HTTP multiples

---

# Routes de Base

```python
# app/routes/main.py
from flask import Blueprint, render_template, request, jsonify

main_bp = Blueprint('main', __name__)

@main_bp.route('/')
def index():
    return render_template('index.html')

@main_bp.route('/about')
def about():
    return render_template('about.html')

# Route avec paramètre
@main_bp.route('/user/<username>')
def profile(username):
    return f'Profil de {username}'

# Route avec paramètre typé
@main_bp.route('/post/<int:post_id>')
def show_post(post_id):
    return f'Article numéro {post_id}'
```

---

# Routes Avancées

```python
# Routes avec méthodes HTTP
@main_bp.route('/contact', methods=['GET', 'POST'])
def contact():
    if request.method == 'POST':
        name = request.form['name']
        email = request.form['email']
        message = request.form['message']
        # Traitement du formulaire
        return 'Message envoyé!'
    return render_template('contact.html')

# Route avec contraintes
@main_bp.route('/files/<path:filename>')
def download_file(filename):
    return f'Téléchargement de {filename}'

# Route avec paramètres optionnels
@main_bp.route('/posts/')
@main_bp.route('/posts/<int:page>')
def posts(page=1):
    return f'Page {page} des articles'
```

---

# Gestion des Erreurs

```python
# Gestionnaires d'erreurs
@main_bp.errorhandler(404)
def not_found_error(error):
    return render_template('errors/404.html'), 404

@main_bp.errorhandler(500)
def internal_error(error):
    return render_template('errors/500.html'), 500

# Validation de paramètres
@main_bp.route('/api/user/<int:user_id>')
def get_user(user_id):
    if user_id < 1:
        return jsonify({'error': 'ID utilisateur invalide'}), 400
    
    # Logique pour récupérer l'utilisateur
    user = {'id': user_id, 'name': 'John Doe'}
    return jsonify(user)
```

---
layout: default
---

# Templates Jinja2 🎨

### Système de templates puissant

**Fonctionnalités Jinja2 :**
- Héritage de templates
- Variables et expressions
- Filtres et fonctions
- Structures de contrôle
- Macros réutilisables

---

# Template de Base

```html
<!-- app/templates/base.html -->
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Mon App Flask{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
</head>
<body>
    <!-- Navigation -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
        <div class="container">
            <a class="navbar-brand" href="{{ url_for('main.index') }}">Mon App</a>
            
            <div class="navbar-nav ms-auto">
                {% if current_user.is_authenticated %}
                    <a class="nav-link" href="{{ url_for('main.profile') }}">Profil</a>
                    <a class="nav-link" href="{{ url_for('auth.logout') }}">Déconnexion</a>
                {% else %}
                    <a class="nav-link" href="{{ url_for('auth.login') }}">Connexion</a>
                    <a class="nav-link" href="{{ url_for('auth.register') }}">Inscription</a>
                {% endif %}
            </div>
        </div>
    </nav>

    <!-- Messages flash -->
    {% with messages = get_flashed_messages(with_categories=true) %}
        {% if messages %}
            {% for category, message in messages %}
                <div class="alert alert-{{ 'danger' if category == 'error' else category }} alert-dismissible">
                    {{ message }}
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>
            {% endfor %}
        {% endif %}
    {% endwith %}

    <!-- Contenu principal -->
    <main class="container my-4">
        {% block content %}{% endblock %}
    </main>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    {% block scripts %}{% endblock %}
</body>
</html>
```

---

# Template de Page

```html
<!-- app/templates/index.html -->
{% extends "base.html" %}

{% block title %}Accueil - {{ super() }}{% endblock %}

{% block content %}
<div class="row">
    <div class="col-md-8">
        <h1>Bienvenue sur Mon App Flask</h1>
        
        {% if posts %}
            {% for post in posts %}
                <div class="card mb-3">
                    <div class="card-body">
                        <h5 class="card-title">
                            <a href="{{ url_for('main.show_post', post_id=post.id) }}">
                                {{ post.title }}
                            </a>
                        </h5>
                        <p class="card-text">{{ post.content[:200] }}...</p>
                        <small class="text-muted">
                            Par {{ post.author.username }} - 
                            {{ post.date_posted.strftime('%d/%m/%Y') }}
                        </small>
                    </div>
                </div>
            {% endfor %}
        {% else %}
            <p>Aucun article disponible.</p>
        {% endif %}
    </div>
    
    <div class="col-md-4">
        <div class="card">
            <div class="card-header">
                <h5>Derniers articles</h5>
            </div>
            <div class="card-body">
                {% for post in recent_posts %}
                    <a href="{{ url_for('main.show_post', post_id=post.id) }}" class="d-block">
                        {{ post.title }}
                    </a>
                {% endfor %}
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

---

# Filtres et Macros

```html
<!-- app/templates/macros.html -->
{% macro render_field(field) %}
    <div class="mb-3">
        {{ field.label(class="form-label") }}
        {{ field(class="form-control") }}
        {% if field.errors %}
            {% for error in field.errors %}
                <div class="text-danger">{{ error }}</div>
            {% endfor %}
        {% endif %}
    </div>
{% endmacro %}

<!-- Utilisation dans un template -->
{% from 'macros.html' import render_field %}

<form method="POST">
    {{ form.hidden_tag() }}
    {{ render_field(form.username) }}
    {{ render_field(form.password) }}
    <button type="submit" class="btn btn-primary">Connexion</button>
</form>
```

---
layout: default
---

# Base de Données avec SQLAlchemy 💾

### ORM intégré à Flask

**Avantages SQLAlchemy :**
- ORM complet et flexible
- Support multi-SGBD
- Migrations automatiques
- Relations complexes
- Optimisation des requêtes

---

# Modèles de Base

```python
# app/models.py
from app import db, login_manager
from flask_login import UserMixin
from datetime import datetime
from werkzeug.security import generate_password_hash, check_password_hash

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

class User(UserMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128))
    posts = db.relationship('Post', backref='author', lazy=True)
    date_created = db.Column(db.DateTime, default=datetime.utcnow)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)

    def __repr__(self):
        return f'<User {self.username}>'
```

---

# Modèles avec Relations

```python
class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    content = db.Column(db.Text, nullable=False)
    date_posted = db.Column(db.DateTime, default=datetime.utcnow)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    category_id = db.Column(db.Integer, db.ForeignKey('category.id'))
    
    # Relation Many-to-Many avec les tags
    tags = db.relationship('Tag', secondary='post_tags', backref='posts')

class Category(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50), unique=True, nullable=False)
    posts = db.relationship('Post', backref='category', lazy=True)

class Tag(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(30), unique=True, nullable=False)

# Table d'association Many-to-Many
post_tags = db.Table('post_tags',
    db.Column('post_id', db.Integer, db.ForeignKey('post.id'), primary_key=True),
    db.Column('tag_id', db.Integer, db.ForeignKey('tag.id'), primary_key=True)
)
```

---

# Opérations CRUD

```python
# Créer
def create_user(username, email, password):
    user = User(username=username, email=email)
    user.set_password(password)
    db.session.add(user)
    db.session.commit()
    return user

# Lire
def get_user_by_username(username):
    return User.query.filter_by(username=username).first()

def get_posts_by_user(user_id, page=1, per_page=5):
    return Post.query.filter_by(user_id=user_id)\
                    .order_by(Post.date_posted.desc())\
                    .paginate(page=page, per_page=per_page)

# Mettre à jour
def update_user_email(user_id, new_email):
    user = User.query.get(user_id)
    if user:
        user.email = new_email
        db.session.commit()
    return user

# Supprimer
def delete_post(post_id):
    post = Post.query.get(post_id)
    if post:
        db.session.delete(post)
        db.session.commit()
        return True
    return False
```

---

# Migrations

```bash
# Initialiser les migrations
flask db init

# Créer une migration
flask db migrate -m "Ajout du modèle User"

# Appliquer les migrations
flask db upgrade

# Voir l'historique
flask db history

# Rollback
flask db downgrade
```

---
layout: default
---

# Formulaires avec WTForms 📝

### Validation et sécurité

**Avantages WTForms :**
- Validation côté serveur
- Protection CSRF automatique
- Rendu HTML automatique
- Validation personnalisée
- Integration Flask parfaite

---

# Installation et Configuration

```bash
# Installation
pip install Flask-WTF WTForms

# Configuration
# app/__init__.py
from flask_wtf.csrf import CSRFProtect

csrf = CSRFProtect()

def create_app():
    app = Flask(__name__)
    csrf.init_app(app)
    return app
```

---

# Formulaires de Base

```python
# app/forms.py
from flask_wtf import FlaskForm
from wtforms import StringField, TextAreaField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Length, Email, EqualTo, ValidationError
from app.models import User

class LoginForm(FlaskForm):
    username = StringField('Nom d\'utilisateur', 
                          validators=[DataRequired(), Length(min=4, max=20)])
    password = PasswordField('Mot de passe', 
                           validators=[DataRequired()])
    submit = SubmitField('Se connecter')

class RegistrationForm(FlaskForm):
    username = StringField('Nom d\'utilisateur',
                          validators=[DataRequired(), Length(min=4, max=20)])
    email = StringField('Email',
                       validators=[DataRequired(), Email()])
    password = PasswordField('Mot de passe',
                           validators=[DataRequired(), Length(min=8)])
    password2 = PasswordField('Confirmer le mot de passe',
                            validators=[DataRequired(), EqualTo('password')])
    submit = SubmitField('S\'inscrire')

    def validate_username(self, username):
        user = User.query.filter_by(username=username.data).first()
        if user:
            raise ValidationError('Ce nom d\'utilisateur est déjà pris.')

    def validate_email(self, email):
        user = User.query.filter_by(email=email.data).first()
        if user:
            raise ValidationError('Cette adresse email est déjà utilisée.')
```

---

# Formulaire d'Article

```python
class PostForm(FlaskForm):
    title = StringField('Titre', 
                       validators=[DataRequired(), Length(min=1, max=100)])
    content = TextAreaField('Contenu', 
                          validators=[DataRequired()])
    category = SelectField('Catégorie', coerce=int)
    tags = StringField('Tags (séparés par des virgules)')
    submit = SubmitField('Publier')

    def __init__(self, *args, **kwargs):
        super(PostForm, self).__init__(*args, **kwargs)
        self.category.choices = [(c.id, c.name) for c in Category.query.all()]
```

---

# Utilisation dans les Vues

```python
# app/routes/auth.py
from flask import Blueprint, render_template, redirect, url_for, flash
from flask_login import login_user, logout_user, login_required
from app.forms import LoginForm, RegistrationForm
from app.models import User
from app import db

auth_bp = Blueprint('auth', __name__)

@auth_bp.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        user = User.query.filter_by(username=form.username.data).first()
        if user and user.check_password(form.password.data):
            login_user(user)
            flash('Connexion réussie!', 'success')
            return redirect(url_for('main.index'))
        else:
            flash('Nom d\'utilisateur ou mot de passe incorrect.', 'error')
    return render_template('auth/login.html', form=form)

@auth_bp.route('/register', methods=['GET', 'POST'])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        user = User(username=form.username.data, email=form.email.data)
        user.set_password(form.password.data)
        db.session.add(user)
        db.session.commit()
        flash('Inscription réussie!', 'success')
        return redirect(url_for('auth.login'))
    return render_template('auth/register.html', form=form)
```

---
layout: default
---

# Authentification et Autorisation 🔐

### Gestion des utilisateurs

**Flask-Login fonctionnalités :**
- Sessions utilisateur
- Protection des routes
- Remember me
- Gestion des permissions
- Callbacks personnalisés

---

# Configuration Flask-Login

```python
# app/__init__.py
from flask_login import LoginManager

login_manager = LoginManager()

def create_app():
    # ...
    login_manager.init_app(app)
    login_manager.login_view = 'auth.login'
    login_manager.login_message = 'Veuillez vous connecter pour accéder à cette page.'
    login_manager.login_message_category = 'info'
    
    @login_manager.user_loader
    def load_user(user_id):
        return User.query.get(int(user_id))
    
    return app
```

---

# Protection des Routes

```python
# app/routes/main.py
from flask_login import login_required, current_user

@main_bp.route('/profile')
@login_required
def profile():
    return render_template('profile.html', user=current_user)

@main_bp.route('/create_post', methods=['GET', 'POST'])
@login_required
def create_post():
    form = PostForm()
    if form.validate_on_submit():
        post = Post(
            title=form.title.data,
            content=form.content.data,
            author=current_user
        )
        db.session.add(post)
        db.session.commit()
        flash('Article créé avec succès!', 'success')
        return redirect(url_for('main.index'))
    return render_template('create_post.html', form=form)
```

---

# Système de Rôles

```python
# app/models.py
from enum import Enum

class RoleEnum(Enum):
    USER = "user"
    MODERATOR = "moderator"
    ADMIN = "admin"

class User(UserMixin, db.Model):
    # ... autres champs
    role = db.Column(db.Enum(RoleEnum), default=RoleEnum.USER)
    
    def is_admin(self):
        return self.role == RoleEnum.ADMIN
    
    def is_moderator(self):
        return self.role in [RoleEnum.MODERATOR, RoleEnum.ADMIN]

# Décorateur pour les rôles
from functools import wraps
from flask import abort

def require_role(role):
    def decorator(f):
        @wraps(f)
        def decorated_function(*args, **kwargs):
            if not current_user.is_authenticated:
                return login_manager.unauthorized()
            
            if role == 'admin' and not current_user.is_admin():
                abort(403)
            elif role == 'moderator' and not current_user.is_moderator():
                abort(403)
                
            return f(*args, **kwargs)
        return decorated_function
    return decorator

# Utilisation
@main_bp.route('/admin')
@login_required
@require_role('admin')
def admin_panel():
    return render_template('admin.html')
```

---
layout: default
---

# API REST avec Flask 🔌

### Création d'APIs modernes

**Outils pour API Flask :**
- Flask-RESTful
- Marshmallow pour sérialisation
- JWT pour authentification
- CORS pour cross-origin
- Pagination et filtres

---

# Installation Extensions API

```bash
# Installation des dépendances
pip install Flask-RESTful
pip install marshmallow
pip install Flask-JWT-Extended
pip install Flask-CORS
```

---

# Configuration API

```python
# app/__init__.py
from flask_restful import Api
from flask_jwt_extended import JWTManager
from flask_cors import CORS
from flask_marshmallow import Marshmallow

api = Api()
jwt = JWTManager()
cors = CORS()
ma = Marshmallow()

def create_app():
    app = Flask(__name__)
    
    # Configuration JWT
    app.config['JWT_SECRET_KEY'] = 'super-secret'
    app.config['JWT_ACCESS_TOKEN_EXPIRES'] = timedelta(hours=24)
    
    # Initialisation
    api.init_app(app)
    jwt.init_app(app)
    cors.init_app(app)
    ma.init_app(app)
    
    return app
```

---

# Schémas Marshmallow

```python
# app/schemas.py
from marshmallow import Schema, fields, validate, post_load
from app.models import User, Post

class UserSchema(ma.SQLAlchemyAutoSchema):
    class Meta:
        model = User
        load_instance = True
        exclude = ('password_hash',)
    
    password = fields.Str(write_only=True, validate=validate.Length(min=8))
    
    @post_load
    def make_user(self, data, **kwargs):
        if 'password' in data:
            user = User(**{k: v for k, v in data.items() if k != 'password'})
            user.set_password(data['password'])
            return user
        return User(**data)

class PostSchema(ma.SQLAlchemyAutoSchema):
    class Meta:
        model = Post
        load_instance = True
        include_fk = True
    
    author = fields.Nested(UserSchema, only=['id', 'username'], dump_only=True)
    date_posted = fields.DateTime(dump_only=True)

# Instances des schémas
user_schema = UserSchema()
users_schema = UserSchema(many=True)
post_schema = PostSchema()
posts_schema = PostSchema(many=True)
```

---

# Ressources API

```python
# app/api/resources.py
from flask_restful import Resource, request
from flask_jwt_extended import jwt_required, get_jwt_identity, create_access_token
from app.models import User, Post
from app.schemas import user_schema, users_schema, post_schema, posts_schema
from app import db

class AuthResource(Resource):
    def post(self):
        data = request.get_json()
        username = data.get('username')
        password = data.get('password')
        
        user = User.query.filter_by(username=username).first()
        
        if user and user.check_password(password):
            access_token = create_access_token(identity=user.id)
            return {
                'access_token': access_token,
                'user': user_schema.dump(user)
            }
        
        return {'message': 'Identifiants invalides'}, 401

class UserListResource(Resource):
    def get(self):
        users = User.query.all()
        return users_schema.dump(users)
    
    def post(self):
        try:
            user = user_schema.load(request.get_json())
            db.session.add(user)
            db.session.commit()
            return user_schema.dump(user), 201
        except Exception as e:
            return {'message': str(e)}, 400

class UserResource(Resource):
    def get(self, user_id):
        user = User.query.get_or_404(user_id)
        return user_schema.dump(user)
    
    @jwt_required()
    def put(self, user_id):
        current_user_id = get_jwt_identity()
        if current_user_id != user_id:
            return {'message': 'Accès non autorisé'}, 403
        
        user = User.query.get_or_404(user_id)
        try:
            user_data = user_schema.load(request.get_json(), partial=True)
            for key, value in user_data.items():
                setattr(user, key, value)
            db.session.commit()
            return user_schema.dump(user)
        except Exception as e:
            return {'message': str(e)}, 400
```

---

# Ressources Posts

```python
class PostListResource(Resource):
    def get(self):
        page = request.args.get('page', 1, type=int)
        per_page = request.args.get('per_page', 10, type=int)
        
        posts = Post.query.order_by(Post.date_posted.desc())\
                         .paginate(page=page, per_page=per_page)
        
        return {
            'posts': posts_schema.dump(posts.items),
            'total': posts.total,
            'pages': posts.pages,
            'current_page': posts.page
        }
    
    @jwt_required()
    def post(self):
        current_user_id = get_jwt_identity()
        try:
            post_data = post_schema.load(request.get_json())
            post_data.user_id = current_user_id
            db.session.add(post_data)
            db.session.commit()
            return post_schema.dump(post_data), 201
        except Exception as e:
            return {'message': str(e)}, 400

class PostResource(Resource):
    def get(self, post_id):
        post = Post.query.get_or_404(post_id)
        return post_schema.dump(post)
    
    @jwt_required()
    def put(self, post_id):
        current_user_id = get_jwt_identity()
        post = Post.query.get_or_404(post_id)
        
        if post.user_id != current_user_id:
            return {'message': 'Accès non autorisé'}, 403
        
        try:
            post_data = post_schema.load(request.get_json(), partial=True)
            for key, value in post_data.items():
                setattr(post, key, value)
            db.session.commit()
            return post_schema.dump(post)
        except Exception as e:
            return {'message': str(e)}, 400
    
    @jwt_required()
    def delete(self, post_id):
        current_user_id = get_jwt_identity()
        post = Post.query.get_or_404(post_id)
        
        if post.user_id != current_user_id:
            return {'message': 'Accès non autorisé'}, 403
        
        db.session.delete(post)
        db.session.commit()
        return {'message': 'Article supprimé'}, 200
```

---

# Enregistrement des Routes API

```python
# app/api/__init__.py
from flask import Blueprint
from flask_restful import Api
from .resources import (
    AuthResource, UserListResource, UserResource,
    PostListResource, PostResource
)

api_bp = Blueprint('api', __name__)
api = Api(api_bp)

# Enregistrement des routes
api.add_resource(AuthResource, '/auth/login')
api.add_resource(UserListResource, '/users')
api.add_resource(UserResource, '/users/<int:user_id>')
api.add_resource(PostListResource, '/posts')
api.add_resource(PostResource, '/posts/<int:post_id>')
```

---
layout: default
---

# Tests Flask 🧪

### Tests automatisés complets

**Types de tests :**
- Tests unitaires
- Tests d'intégration
- Tests de l'API
- Tests des formulaires
- Tests end-to-end

---

# Configuration des Tests

```python
# tests/conftest.py
import pytest
from app import create_app, db
from app.models import User, Post

@pytest.fixture
def app():
    app = create_app('testing')
    
    with app.app_context():
        db.create_all()
        yield app
        db.drop_all()

@pytest.fixture
def client(app):
    return app.test_client()

@pytest.fixture
def runner(app):
    return app.test_cli_runner()

@pytest.fixture
def auth(client):
    return AuthActions(client)

class AuthActions:
    def __init__(self, client):
        self._client = client
    
    def login(self, username='testuser', password='testpass'):
        return self._client.post('/auth/login', data={
            'username': username,
            'password': password
        })
    
    def logout(self):
        return self._client.get('/auth/logout')
```

---

# Tests de Modèles

```python
# tests/test_models.py
import pytest
from app.models import User, Post

def test_user_creation():
    user = User(username='testuser', email='test@example.com')
    user.set_password('testpassword')
    
    assert user.username == 'testuser'
    assert user.email == 'test@example.com'
    assert user.check_password('testpassword')
    assert not user.check_password('wrongpassword')

def test_user_repr():
    user = User(username='testuser')
    assert repr(user) == '<User testuser>'

def test_post_creation():
    user = User(username='author', email='author@example.com')
    post = Post(title='Test Post', content='Test content', author=user)
    
    assert post.title == 'Test Post'
    assert post.content == 'Test content'
    assert post.author == user
```

---

# Tests de Vues

```python
# tests/test_routes.py
import pytest
from app.models import User, Post
from app import db

def test_index_page(client):
    response = client.get('/')
    assert response.status_code == 200
    assert b'Bienvenue' in response.data

def test_login_required(client):
    response = client.get('/profile')
    assert response.status_code == 302  # Redirection vers login

def test_login_logout(client, app):
    # Créer un utilisateur de test
    with app.app_context():
        user = User(username='testuser', email='test@example.com')
        user.set_password('testpass')
        db.session.add(user)
        db.session.commit()
    
    # Test de connexion
    response = client.post('/auth/login', data={
        'username': 'testuser',
        'password': 'testpass'
    })
    assert response.status_code == 302
    
    # Vérifier que l'utilisateur est connecté
    response = client.get('/profile')
    assert response.status_code == 200
    
    # Test de déconnexion
    response = client.get('/auth/logout')
    assert response.status_code == 302
    
    # Vérifier que l'utilisateur est déconnecté
    response = client.get('/profile')
    assert response.status_code == 302
```

---

# Tests API

```python
# tests/test_api.py
import json
import pytest
from app.models import User
from app import db

def test_api_auth(client, app):
    with app.app_context():
        user = User(username='apiuser', email='api@example.com')
        user.set_password('apipass')
        db.session.add(user)
        db.session.commit()
    
    # Test de connexion API
    response = client.post('/api/auth/login', 
                          json={'username': 'apiuser', 'password': 'apipass'})
    assert response.status_code == 200
    
    data = json.loads(response.data)
    assert 'access_token' in data
    assert 'user' in data
    
    token = data['access_token']
    
    # Test d'accès protégé
    headers = {'Authorization': f'Bearer {token}'}
    response = client.get('/api/users/1', headers=headers)
    assert response.status_code == 200

def test_api_posts(client, app):
    with app.app_context():
        user = User(username='postuser', email='post@example.com')
        user.set_password('postpass')
        db.session.add(user)
        db.session.commit()
    
    # Obtenir un token
    auth_response = client.post('/api/auth/login',
                               json={'username': 'postuser', 'password': 'postpass'})
    token = json.loads(auth_response.data)['access_token']
    headers = {'Authorization': f'Bearer {token}'}
    
    # Créer un post
    post_data = {
        'title': 'Test API Post',
        'content': 'Contenu du post de test'
    }
    response = client.post('/api/posts', json=post_data, headers=headers)
    assert response.status_code == 201
    
    # Récupérer les posts
    response = client.get('/api/posts')
    assert response.status_code == 200
    
    data = json.loads(response.data)
    assert 'posts' in data
    assert len(data['posts']) > 0
```

---
layout: default
---

# Déploiement Flask 🚀

### Mise en production

**Options de déploiement :**
- Serveurs traditionnels (Nginx + Gunicorn)
- Containers Docker
- Cloud providers (Heroku, AWS, etc.)
- Serverless functions

---

# Configuration Production

```python
# config.py - Configuration production
import os

class ProductionConfig(BaseConfig):
    DEBUG = False
    TESTING = False
    
    # Base de données
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL')
    
    # Sécurité
    SECRET_KEY = os.environ.get('SECRET_KEY')
    
    # Performance
    SQLALCHEMY_ENGINE_OPTIONS = {
        'pool_size': 10,
        'pool_recycle': 120,
        'pool_timeout': 20,
        'max_overflow': 0
    }
    
    # Logging
    LOG_LEVEL = os.environ.get('LOG_LEVEL', 'INFO')
```

---

# Serveur WSGI

```python
# wsgi.py
import os
from app import create_app

app = create_app(os.environ.get('FLASK_CONFIG', 'production'))

if __name__ == "__main__":
    app.run()
```

---

# Dockerfile Flask

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# Installation des dépendances système
RUN apt-get update && apt-get install -y \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

# Installation des dépendances Python
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copie du code
COPY . .

# Variables d'environnement
ENV FLASK_APP=wsgi.py
ENV FLASK_CONFIG=production

# Exposition du port
EXPOSE 5000

# Commande de démarrage
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "4", "wsgi:app"]
```

---

# Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      - FLASK_CONFIG=production
      - DATABASE_URL=postgresql://user:password@db:5432/flaskapp
      - SECRET_KEY=your-production-secret-key
    depends_on:
      - db
    volumes:
      - ./uploads:/app/uploads

  db:
    image: postgres:14
    environment:
      - POSTGRES_DB=flaskapp
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./static:/app/static
    depends_on:
      - web

volumes:
  postgres_data:
```

---

# Configuration Nginx

```nginx
# nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream flask_app {
        server web:5000;
    }

    server {
        listen 80;
        server_name localhost;

        # Fichiers statiques
        location /static/ {
            alias /app/static/;
            expires 1y;
            add_header Cache-Control "public, immutable";
        }

        # Application Flask
        location / {
            proxy_pass http://flask_app;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

---

# Déploiement Heroku

```bash
# Procfile
web: gunicorn wsgi:app

# runtime.txt
python-3.11.2

# requirements.txt
Flask==2.3.2
Flask-SQLAlchemy==3.0.5
Flask-Login==0.6.3
Flask-WTF==1.1.1
Flask-Migrate==4.0.4
psycopg2-binary==2.9.6
gunicorn==20.1.0

# Commandes de déploiement
git add .
git commit -m "Deploy to Heroku"
heroku create myflaskapp
heroku addons:create heroku-postgresql:hobby-dev
heroku config:set FLASK_CONFIG=production
heroku config:set SECRET_KEY=your-secret-key
git push heroku main
heroku run flask db upgrade
```

---
layout: default
---

# Extensions Flask Populaires 🔧

### Écosystème riche

**Extensions essentielles :**
- Flask-Mail (emails)
- Flask-Caching (cache)
- Flask-Admin (admin interface)
- Flask-Security (sécurité avancée)
- Flask-SocketIO (WebSockets)

---

# Flask-Mail

```python
# Configuration
from flask_mail import Mail, Message

mail = Mail()

# settings
MAIL_SERVER = 'smtp.gmail.com'
MAIL_PORT = 587
MAIL_USE_TLS = True
MAIL_USERNAME = os.environ.get('EMAIL_USER')
MAIL_PASSWORD = os.environ.get('EMAIL_PASS')

# Utilisation
def send_email(subject, sender, recipients, text_body, html_body):
    msg = Message(subject, sender=sender, recipients=recipients)
    msg.body = text_body
    msg.html = html_body
    mail.send(msg)

# Exemple d'envoi
send_email(
    'Bienvenue!',
    app.config['ADMINS'][0],
    ['user@example.com'],
    'Bienvenue sur notre site!',
    '<h1>Bienvenue sur notre site!</h1>'
)
```

---

# Flask-Caching

```python
from flask_caching import Cache

cache = Cache()

# Configuration
cache.init_app(app, config={
    'CACHE_TYPE': 'redis',
    'CACHE_REDIS_URL': 'redis://localhost:6379/0'
})

# Utilisation
@cache.cached(timeout=300)  # 5 minutes
def get_popular_posts():
    return Post.query.order_by(Post.views.desc()).limit(10).all()

# Cache avec clé dynamique
@cache.cached(timeout=300, key_prefix='user_posts')
def get_user_posts(user_id):
    return Post.query.filter_by(user_id=user_id).all()

# Invalidation manuelle
cache.delete('user_posts')
```

---

# Flask-Admin

```python
from flask_admin import Admin
from flask_admin.contrib.sqla import ModelView

admin = Admin(app, name='Admin Panel', template_mode='bootstrap3')

class UserModelView(ModelView):
    column_exclude_list = ['password_hash']
    column_searchable_list = ['username', 'email']
    column_filters = ['role', 'date_created']
    form_excluded_columns = ['password_hash', 'date_created']

class PostModelView(ModelView):
    column_list = ['title', 'author', 'date_posted']
    column_searchable_list = ['title', 'content']
    column_filters = ['date_posted']

admin.add_view(UserModelView(User, db.session))
admin.add_view(PostModelView(Post, db.session))
```

---
layout: default
---

# Performance et Optimisation 📈

### Améliorer les performances

**Techniques d'optimisation :**
- Profiling et monitoring
- Cache intelligent
- Optimisation base de données
- Compression et CDN
- Load balancing

---

# Profiling Flask

```python
# Profiler middleware
from werkzeug.middleware.profiler import ProfilerMiddleware

def create_app():
    app = Flask(__name__)
    
    if app.config.get('PROFILING'):
        app.wsgi_app = ProfilerMiddleware(
            app.wsgi_app,
            restrictions=[30],  # Top 30 functions
            profile_dir='./profiles'
        )
    
    return app

# Monitoring personnalisé
import time
from flask import g, request

@app.before_request
def before_request():
    g.start_time = time.time()

@app.after_request
def after_request(response):
    if hasattr(g, 'start_time'):
        duration = time.time() - g.start_time
        response.headers['X-Response-Time'] = f'{duration:.6f}s'
        
        # Log des requêtes lentes
        if duration > 1.0:
            app.logger.warning(f'Slow request: {request.path} took {duration:.2f}s')
    
    return response
```

---

# Optimisation Base de Données

```python
# Requêtes optimisées
def get_posts_with_authors():
    # ❌ N+1 queries
    posts = Post.query.all()
    for post in posts:
        print(post.author.username)  # Query pour chaque post
    
    # ✅ Une seule query
    posts = Post.query.options(db.joinedload(Post.author)).all()
    for post in posts:
        print(post.author.username)

# Pagination efficace
def get_posts_paginated(page=1, per_page=20):
    return Post.query\
               .options(db.joinedload(Post.author))\
               .order_by(Post.date_posted.desc())\
               .paginate(page=page, per_page=per_page)

# Index de base de données
class Post(db.Model):
    # ...
    
    # Index pour améliorer les performances
    __table_args__ = (
        db.Index('idx_post_user_date', 'user_id', 'date_posted'),
        db.Index('idx_post_title', 'title'),
    )
```

---

# Points Clés à Retenir 🎯

### Résumé Flask complet

**1. Simplicité et flexibilité :**
- Micro-framework modulaire
- Learning curve rapide
- Structure libre
- Extensions à la demande

**2. Fonctionnalités principales :**
- Routage flexible
- Templates Jinja2
- WTForms pour formulaires
- SQLAlchemy ORM

**3. Écosystème riche :**
- Extensions nombreuses
- API REST avec Flask-RESTful
- Authentification avec Flask-Login
- Tests avec pytest

**4. Production ready :**
- Configuration par environnement
- Docker et containers
- Monitoring et profiling
- Optimisations performance

**Flask : Simple, puissant et flexible !** ⚡
