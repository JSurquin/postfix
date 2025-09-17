---
layout: default
routeAlias: 'django-complet'
---

# DJANGO - COURS COMPLET 🎯

### Le framework web le plus populaire de Python

---

# Pourquoi Django ? 🤔

### Les avantages du framework

**Philosophie Django :**
- "Batteries incluses" - Tout est fourni
- DRY (Don't Repeat Yourself)
- Convention over Configuration
- Sécurité par défaut

**Avantages principaux :**
- Admin interface automatique
- ORM puissant
- Système d'authentification intégré
- Protection CSRF/XSS
- Middleware configurables
- Templates héritables

---

# Installation et Configuration 🛠️

### Setup d'un projet Django

**Installation :**
- Python 3.8+ requis
- Installation via pip
- Environnement virtuel recommandé
- Base de données (SQLite par défaut)

---

# Installation Django

```bash
# Création d'un environnement virtuel
python -m venv django_env
source django_env/bin/activate  # Linux/Mac
# django_env\Scripts\activate   # Windows

# Installation de Django
pip install django

# Vérification de l'installation
python -m django --version
```

---

# Création d'un Projet

```bash
# Créer un nouveau projet
django-admin startproject monprojet

# Structure créée
monprojet/
├── manage.py
└── monprojet/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── asgi.py
    └── wsgi.py
```

---

# Premier Démarrage

```bash
# Naviguer dans le projet
cd monprojet

# Appliquer les migrations initiales
python manage.py migrate

# Créer un superutilisateur
python manage.py createsuperuser

# Lancer le serveur de développement
python manage.py runserver
```

---
layout: default
---

# Structure d'un Projet Django 📁

### Organisation et fichiers importants

**Fichiers principaux :**
- `manage.py` - Utilitaire de gestion
- `settings.py` - Configuration du projet
- `urls.py` - Routage URL principal
- `models.py` - Modèles de données
- `views.py` - Logique de vue
- `templates/` - Templates HTML

---

# Fichier settings.py

```python
# monprojet/settings.py
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = 'your-secret-key-here'

DEBUG = True

ALLOWED_HOSTS = []

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # Vos applications ici
]
```

---

# Configuration de Base de Données

```python
# settings.py - Configuration BDD
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# Pour PostgreSQL
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'monprojet_db',
        'USER': 'username',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

---
layout: default
---

# Applications Django 📦

### Créer et structurer des applications

**Concept d'application :**
- Unité fonctionnelle du projet
- Réutilisable et modulaire
- Une app = une fonctionnalité
- Séparation des responsabilités

---

# Création d'une Application

```bash
# Créer une nouvelle application
python manage.py startapp blog

# Structure de l'application
blog/
├── __init__.py
├── admin.py
├── apps.py
├── migrations/
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```

---

# Enregistrement de l'Application

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',  # Notre nouvelle application
]
```

---
layout: default
---

# Modèles Django (ORM) 💾

### Mapping Objet-Relationnel

**Avantages de l'ORM Django :**
- Abstraction de la base de données
- Migrations automatiques
- Relations entre tables
- Requêtes en Python
- Protection contre l'injection SQL

---

# Définition d'un Modèle Simple

```python
# blog/models.py
from django.db import models
from django.contrib.auth.models import User

class Article(models.Model):
    titre = models.CharField(max_length=200)
    contenu = models.TextField()
    auteur = models.ForeignKey(User, on_delete=models.CASCADE)
    date_creation = models.DateTimeField(auto_now_add=True)
    date_modification = models.DateTimeField(auto_now=True)
    publie = models.BooleanField(default=False)

    def __str__(self):
        return self.titre

    class Meta:
        ordering = ['-date_creation']
        verbose_name = 'Article'
        verbose_name_plural = 'Articles'
```

---

# Types de Champs Principaux

```python
# Exemples de champs Django
class MonModele(models.Model):
    # Champs de texte
    nom = models.CharField(max_length=100)
    description = models.TextField()
    
    # Champs numériques
    age = models.IntegerField()
    prix = models.DecimalField(max_digits=10, decimal_places=2)
    
    # Champs de date
    date_naissance = models.DateField()
    date_creation = models.DateTimeField(auto_now_add=True)
    
    # Champs booléens
    actif = models.BooleanField(default=True)
    
    # Champs de fichier
    image = models.ImageField(upload_to='images/')
    document = models.FileField(upload_to='documents/')
```

---

# Relations entre Modèles

```python
# Relations One-to-Many (ForeignKey)
class Categorie(models.Model):
    nom = models.CharField(max_length=100)

class Article(models.Model):
    titre = models.CharField(max_length=200)
    categorie = models.ForeignKey(Categorie, on_delete=models.CASCADE)

# Relations Many-to-Many
class Tag(models.Model):
    nom = models.CharField(max_length=50)

class Article(models.Model):
    titre = models.CharField(max_length=200)
    tags = models.ManyToManyField(Tag, blank=True)

# Relations One-to-One
class Profil(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()
```

---

# Migrations Django

```bash
# Créer une migration après modification des modèles
python manage.py makemigrations

# Appliquer les migrations
python manage.py migrate

# Voir les migrations
python manage.py showmigrations

# Rollback vers une migration
python manage.py migrate blog 0001
```

---
layout: default
---

# Vues Django 👁️

### Logique de traitement des requêtes

**Types de vues :**
- Function-based views (FBV)
- Class-based views (CBV)
- Generic views
- API views (avec Django REST)

---

# Function-Based Views

```python
# blog/views.py
from django.shortcuts import render, get_object_or_404
from django.http import HttpResponse
from .models import Article

def liste_articles(request):
    """Affiche la liste des articles publiés"""
    articles = Article.objects.filter(publie=True)
    context = {'articles': articles}
    return render(request, 'blog/liste.html', context)

def detail_article(request, article_id):
    """Affiche le détail d'un article"""
    article = get_object_or_404(Article, id=article_id, publie=True)
    context = {'article': article}
    return render(request, 'blog/detail.html', context)
```

---

# Class-Based Views

```python
# blog/views.py
from django.views.generic import ListView, DetailView
from django.views.generic.edit import CreateView, UpdateView
from .models import Article

class ArticleListView(ListView):
    model = Article
    template_name = 'blog/liste.html'
    context_object_name = 'articles'
    queryset = Article.objects.filter(publie=True)
    paginate_by = 10

class ArticleDetailView(DetailView):
    model = Article
    template_name = 'blog/detail.html'
    context_object_name = 'article'

class ArticleCreateView(CreateView):
    model = Article
    fields = ['titre', 'contenu', 'categorie']
    template_name = 'blog/create.html'
```

---

# Traitement des Formulaires

```python
# blog/views.py
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import ArticleForm

def creer_article(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save(commit=False)
            article.auteur = request.user
            article.save()
            messages.success(request, 'Article créé avec succès!')
            return redirect('detail_article', article_id=article.id)
    else:
        form = ArticleForm()
    
    return render(request, 'blog/create.html', {'form': form})
```

---
layout: default
---

# URLs et Routage 🛣️

### Configuration des URLs

**Système d'URL Django :**
- URLs patterns avec regex
- Paramètres nommés
- Espaces de noms
- Inclusion d'URLs d'applications

---

# URLs Principales du Projet

```python
# monprojet/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),
    path('api/v1/', include('api.urls')),
]
```

---

# URLs de l'Application

```python
# blog/urls.py
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    path('', views.ArticleListView.as_view(), name='liste'),
    path('article/<int:pk>/', views.ArticleDetailView.as_view(), name='detail'),
    path('article/nouveau/', views.ArticleCreateView.as_view(), name='create'),
    path('article/<int:pk>/modifier/', views.ArticleUpdateView.as_view(), name='update'),
    path('categorie/<str:nom>/', views.articles_par_categorie, name='categorie'),
]
```

---

# URLs avec Paramètres

```python
# Exemples d'URLs avec paramètres
urlpatterns = [
    # Paramètre entier
    path('article/<int:article_id>/', views.detail_article),
    
    # Paramètre string
    path('categorie/<str:nom>/', views.articles_categorie),
    
    # Paramètre slug
    path('article/<slug:slug>/', views.article_par_slug),
    
    # Paramètres multiples
    path('article/<int:annee>/<int:mois>/', views.articles_par_date),
    
    # Regex complexe
    re_path(r'^article/(?P<annee>[0-9]{4})/$', views.articles_annee),
]
```

---
layout: default
---

# Templates Django 🎨

### Système de templates

**Fonctionnalités des templates :**
- Héritage de templates
- Variables et filtres
- Tags de logique
- Templates réutilisables
- Sécurité intégrée

---

# Structure des Templates

```html
<!-- blog/templates/blog/base.html -->
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Mon Blog{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <a class="navbar-brand" href="{% url 'blog:liste' %}">Mon Blog</a>
        </div>
    </nav>

    <main class="container mt-4">
        {% block content %}
        {% endblock %}
    </main>
</body>
</html>
```

---

# Template de Liste

```html
<!-- blog/templates/blog/liste.html -->
{% extends 'blog/base.html' %}

{% block title %}Articles - {{ block.super }}{% endblock %}

{% block content %}
<div class="row">
    <div class="col-md-8">
        <h1>Derniers Articles</h1>
        
        {% for article in articles %}
        <div class="card mb-3">
            <div class="card-body">
                <h5 class="card-title">
                    <a href="{% url 'blog:detail' article.pk %}">{{ article.titre }}</a>
                </h5>
                <p class="card-text">{{ article.contenu|truncatewords:30 }}</p>
                <small class="text-muted">
                    Par {{ article.auteur.username }} - {{ article.date_creation|date:"d M Y" }}
                </small>
            </div>
        </div>
        {% empty %}
        <p>Aucun article publié.</p>
        {% endfor %}
    </div>
</div>
{% endblock %}
```

---

# Template de Détail

```html
<!-- blog/templates/blog/detail.html -->
{% extends 'blog/base.html' %}

{% block title %}{{ article.titre }} - {{ block.super }}{% endblock %}

{% block content %}
<article class="row">
    <div class="col-md-8">
        <h1>{{ article.titre }}</h1>
        
        <div class="mb-3">
            <small class="text-muted">
                Par {{ article.auteur.username }} - 
                {{ article.date_creation|date:"d F Y à H:i" }}
                {% if article.date_modification != article.date_creation %}
                    (modifié le {{ article.date_modification|date:"d M Y" }})
                {% endif %}
            </small>
        </div>
        
        <div class="article-content">
            {{ article.contenu|linebreaks }}
        </div>
        
        {% if article.tags.exists %}
        <div class="mt-3">
            <strong>Tags:</strong>
            {% for tag in article.tags.all %}
                <span class="badge bg-secondary">{{ tag.nom }}</span>
            {% endfor %}
        </div>
        {% endif %}
    </div>
</article>
{% endblock %}
```

---
layout: default
---

# Formulaires Django 📝

### Gestion des formulaires

**Avantages des formulaires Django :**
- Validation automatique
- Rendu HTML automatique
- Protection CSRF
- Nettoyage des données
- Messages d'erreur

---

# Définition d'un Formulaire

```python
# blog/forms.py
from django import forms
from .models import Article, Categorie

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['titre', 'contenu', 'categorie', 'tags']
        widgets = {
            'titre': forms.TextInput(attrs={
                'class': 'form-control',
                'placeholder': 'Titre de l\'article'
            }),
            'contenu': forms.Textarea(attrs={
                'class': 'form-control',
                'rows': 10,
                'placeholder': 'Contenu de l\'article'
            }),
            'categorie': forms.Select(attrs={'class': 'form-control'}),
        }

    def clean_titre(self):
        titre = self.cleaned_data['titre']
        if len(titre) < 10:
            raise forms.ValidationError("Le titre doit contenir au moins 10 caractères.")
        return titre
```

---

# Formulaire Personnalisé

```python
# blog/forms.py
class ContactForm(forms.Form):
    nom = forms.CharField(
        max_length=100,
        widget=forms.TextInput(attrs={'class': 'form-control'})
    )
    email = forms.EmailField(
        widget=forms.EmailInput(attrs={'class': 'form-control'})
    )
    sujet = forms.CharField(
        max_length=200,
        widget=forms.TextInput(attrs={'class': 'form-control'})
    )
    message = forms.CharField(
        widget=forms.Textarea(attrs={
            'class': 'form-control',
            'rows': 5
        })
    )

    def clean_email(self):
        email = self.cleaned_data['email']
        if not email.endswith('@example.com'):
            raise forms.ValidationError("Seuls les emails @example.com sont acceptés.")
        return email
```

---

# Template de Formulaire

```html
<!-- blog/templates/blog/create.html -->
{% extends 'blog/base.html' %}

{% block title %}Nouvel Article - {{ block.super }}{% endblock %}

{% block content %}
<div class="row">
    <div class="col-md-8">
        <h1>Créer un Nouvel Article</h1>
        
        <form method="post">
            {% csrf_token %}
            
            {% if form.errors %}
            <div class="alert alert-danger">
                {{ form.errors }}
            </div>
            {% endif %}
            
            <div class="mb-3">
                <label for="{{ form.titre.id_for_label }}" class="form-label">Titre</label>
                {{ form.titre }}
            </div>
            
            <div class="mb-3">
                <label for="{{ form.categorie.id_for_label }}" class="form-label">Catégorie</label>
                {{ form.categorie }}
            </div>
            
            <div class="mb-3">
                <label for="{{ form.contenu.id_for_label }}" class="form-label">Contenu</label>
                {{ form.contenu }}
            </div>
            
            <button type="submit" class="btn btn-primary">Publier</button>
            <a href="{% url 'blog:liste' %}" class="btn btn-secondary">Annuler</a>
        </form>
    </div>
</div>
{% endblock %}
```

---
layout: default
---

# Admin Django 👑

### Interface d'administration automatique

**Fonctionnalités de l'admin :**
- Interface automatique
- CRUD complet
- Filtres et recherche
- Actions en masse
- Permissions utilisateur

---

# Configuration de l'Admin

```python
# blog/admin.py
from django.contrib import admin
from .models import Article, Categorie, Tag

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    list_display = ['titre', 'auteur', 'categorie', 'publie', 'date_creation']
    list_filter = ['publie', 'categorie', 'date_creation']
    search_fields = ['titre', 'contenu']
    date_hierarchy = 'date_creation'
    ordering = ['-date_creation']
    prepopulated_fields = {'slug': ('titre',)}
    
    fieldsets = (
        ('Contenu', {
            'fields': ('titre', 'slug', 'contenu', 'auteur')
        }),
        ('Métadonnées', {
            'fields': ('categorie', 'tags', 'publie'),
            'classes': ('collapse',)
        }),
    )

@admin.register(Categorie)
class CategorieAdmin(admin.ModelAdmin):
    list_display = ['nom', 'nombre_articles']
    
    def nombre_articles(self, obj):
        return obj.article_set.count()
    nombre_articles.short_description = 'Nombre d\'articles'

admin.site.register(Tag)
```

---

# Personnalisation de l'Admin

```python
# blog/admin.py
from django.contrib import admin
from django.utils.html import format_html

class ArticleAdmin(admin.ModelAdmin):
    list_display = ['titre', 'auteur', 'status_display', 'date_creation']
    
    def status_display(self, obj):
        if obj.publie:
            return format_html(
                '<span style="color: green;">✓ Publié</span>'
            )
        else:
            return format_html(
                '<span style="color: red;">✗ Brouillon</span>'
            )
    status_display.short_description = 'Statut'
    
    actions = ['publier_articles', 'depublier_articles']
    
    def publier_articles(self, request, queryset):
        updated = queryset.update(publie=True)
        self.message_user(request, f'{updated} articles publiés.')
    publier_articles.short_description = "Publier les articles sélectionnés"
    
    def depublier_articles(self, request, queryset):
        updated = queryset.update(publie=False)
        self.message_user(request, f'{updated} articles dépubliés.')
    depublier_articles.short_description = "Dépublier les articles sélectionnés"
```

---
layout: default
---

# Authentification Django 🔐

### Système d'authentification intégré

**Fonctionnalités d'auth :**
- Utilisateurs et groupes
- Permissions granulaires
- Sessions et cookies
- Vues d'authentification
- Décorateurs de protection

---

# Configuration de l'Authentification

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.auth',
    'django.contrib.sessions',
    # ...
]

# URLs d'authentification
# monprojet/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('accounts/', include('django.contrib.auth.urls')),
    path('', include('blog.urls')),
]
```

---

# Vues d'Authentification

```python
# blog/views.py
from django.contrib.auth import login, authenticate
from django.contrib.auth.decorators import login_required
from django.contrib.auth.forms import UserCreationForm
from django.shortcuts import render, redirect

def signup(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            username = form.cleaned_data.get('username')
            password = form.cleaned_data.get('password1')
            user = authenticate(username=username, password=password)
            login(request, user)
            return redirect('blog:liste')
    else:
        form = UserCreationForm()
    return render(request, 'registration/signup.html', {'form': form})

@login_required
def profile(request):
    return render(request, 'blog/profile.html')
```

---

# Protection des Vues

```python
# blog/views.py
from django.contrib.auth.decorators import login_required, permission_required
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin

# Décorateur pour les fonctions
@login_required
def creer_article(request):
    # Seuls les utilisateurs connectés peuvent accéder
    pass

@permission_required('blog.add_article')
def ajouter_article(request):
    # Seuls les utilisateurs avec permission peuvent accéder
    pass

# Mixin pour les classes
class ArticleCreateView(LoginRequiredMixin, CreateView):
    model = Article
    fields = ['titre', 'contenu']
    login_url = '/login/'

class ArticleUpdateView(PermissionRequiredMixin, UpdateView):
    model = Article
    fields = ['titre', 'contenu']
    permission_required = 'blog.change_article'
```

---
layout: default
---

# Middleware Django ⚙️

### Traitement des requêtes/réponses

**Types de middleware :**
- Middleware de sécurité
- Middleware de session
- Middleware d'authentification
- Middleware personnalisé

---

# Middleware Personnalisé

```python
# blog/middleware.py
import time
from django.utils.deprecation import MiddlewareMixin

class TimingMiddleware(MiddlewareMixin):
    def process_request(self, request):
        request.start_time = time.time()

    def process_response(self, request, response):
        if hasattr(request, 'start_time'):
            duration = time.time() - request.start_time
            response['X-Response-Time'] = f'{duration:.2f}s'
        return response

class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        
        # Ajouter des headers de sécurité
        response['X-Content-Type-Options'] = 'nosniff'
        response['X-Frame-Options'] = 'DENY'
        response['X-XSS-Protection'] = '1; mode=block'
        
        return response
```

---

# Enregistrement du Middleware

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'blog.middleware.TimingMiddleware',  # Notre middleware
    'blog.middleware.SecurityHeadersMiddleware',
]
```

---
layout: default
---

# Tests Django 🧪

### Tests automatisés complets

**Types de tests :**
- Tests unitaires
- Tests d'intégration
- Tests de vues
- Tests de formulaires
- Tests de modèles

---

# Tests de Modèles

```python
# blog/tests.py
from django.test import TestCase
from django.contrib.auth.models import User
from .models import Article, Categorie

class ArticleModelTest(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass'
        )
        self.categorie = Categorie.objects.create(nom='Test')
        
    def test_creation_article(self):
        article = Article.objects.create(
            titre='Test Article',
            contenu='Contenu de test',
            auteur=self.user,
            categorie=self.categorie
        )
        self.assertEqual(article.titre, 'Test Article')
        self.assertEqual(str(article), 'Test Article')
        self.assertFalse(article.publie)
        
    def test_article_str_method(self):
        article = Article.objects.create(
            titre='Mon Article',
            contenu='Contenu',
            auteur=self.user,
            categorie=self.categorie
        )
        self.assertEqual(str(article), 'Mon Article')
```

---

# Tests de Vues

```python
# blog/tests.py
from django.test import TestCase, Client
from django.urls import reverse
from django.contrib.auth.models import User
from .models import Article

class ArticleViewTest(TestCase):
    def setUp(self):
        self.client = Client()
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass'
        )
        self.article = Article.objects.create(
            titre='Test Article',
            contenu='Contenu de test',
            auteur=self.user,
            publie=True
        )
        
    def test_liste_articles_view(self):
        response = self.client.get(reverse('blog:liste'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Test Article')
        
    def test_detail_article_view(self):
        response = self.client.get(
            reverse('blog:detail', args=[self.article.pk])
        )
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, self.article.titre)
        
    def test_create_article_requires_login(self):
        response = self.client.get(reverse('blog:create'))
        self.assertEqual(response.status_code, 302)  # Redirection vers login
```

---

# Tests de Formulaires

```python
# blog/tests.py
from django.test import TestCase
from .forms import ArticleForm, ContactForm

class ArticleFormTest(TestCase):
    def test_form_valid_data(self):
        form_data = {
            'titre': 'Test Article Valide',
            'contenu': 'Contenu de test pour l\'article'
        }
        form = ArticleForm(data=form_data)
        self.assertTrue(form.is_valid())
        
    def test_form_invalid_data(self):
        form_data = {
            'titre': 'Test',  # Trop court
            'contenu': ''  # Vide
        }
        form = ArticleForm(data=form_data)
        self.assertFalse(form.is_valid())
        self.assertIn('titre', form.errors)
        
class ContactFormTest(TestCase):
    def test_contact_form_email_validation(self):
        form_data = {
            'nom': 'Test User',
            'email': 'invalid-email',
            'sujet': 'Test',
            'message': 'Message de test'
        }
        form = ContactForm(data=form_data)
        self.assertFalse(form.is_valid())
        self.assertIn('email', form.errors)
```

---
layout: default
---

# API REST avec Django 🔌

### Django REST Framework

**Fonctionnalités DRF :**
- Sérialisation automatique
- Vues basées sur les classes
- Authentification JWT
- Pagination
- Permissions granulaires

---

# Installation Django REST Framework

```bash
# Installation
pip install djangorestframework
pip install djangorestframework-simplejwt

# Configuration dans settings.py
INSTALLED_APPS = [
    # ...
    'rest_framework',
    'rest_framework_simplejwt',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20
}
```

---

# Sérializers

```python
# blog/serializers.py
from rest_framework import serializers
from .models import Article, Categorie

class CategorieSerializer(serializers.ModelSerializer):
    class Meta:
        model = Categorie
        fields = ['id', 'nom']

class ArticleSerializer(serializers.ModelSerializer):
    auteur = serializers.StringRelatedField(read_only=True)
    categorie = CategorieSerializer(read_only=True)
    
    class Meta:
        model = Article
        fields = [
            'id', 'titre', 'contenu', 'auteur', 
            'categorie', 'date_creation', 'publie'
        ]
        read_only_fields = ['auteur', 'date_creation']
        
    def validate_titre(self, value):
        if len(value) < 10:
            raise serializers.ValidationError(
                "Le titre doit contenir au moins 10 caractères."
            )
        return value
```

---

# API Views

```python
# blog/api_views.py
from rest_framework import generics, permissions
from rest_framework.decorators import api_view, permission_classes
from rest_framework.response import Response
from .models import Article
from .serializers import ArticleSerializer

class ArticleListCreateAPIView(generics.ListCreateAPIView):
    queryset = Article.objects.filter(publie=True)
    serializer_class = ArticleSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]
    
    def perform_create(self, serializer):
        serializer.save(auteur=self.request.user)

class ArticleDetailAPIView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]

@api_view(['GET'])
@permission_classes([permissions.AllowAny])
def articles_stats(request):
    stats = {
        'total_articles': Article.objects.count(),
        'articles_publies': Article.objects.filter(publie=True).count(),
        'articles_brouillon': Article.objects.filter(publie=False).count(),
    }
    return Response(stats)
```

---

# URLs de l'API

```python
# blog/api_urls.py
from django.urls import path
from . import api_views

app_name = 'blog_api'

urlpatterns = [
    path('articles/', api_views.ArticleListCreateAPIView.as_view(), name='article-list'),
    path('articles/<int:pk>/', api_views.ArticleDetailAPIView.as_view(), name='article-detail'),
    path('stats/', api_views.articles_stats, name='stats'),
]

# monprojet/urls.py
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/v1/blog/', include('blog.api_urls')),
    path('', include('blog.urls')),
]
```

---
layout: default
---

# Déploiement Django 🚀

### Mise en production

**Étapes de déploiement :**
- Configuration de production
- Serveur web (Nginx)
- Serveur d'application (Gunicorn)
- Base de données
- Fichiers statiques

---

# Configuration de Production

```python
# settings/production.py
from .base import *
import os

DEBUG = False

ALLOWED_HOSTS = ['mondomaine.com', 'www.mondomaine.com']

# Base de données PostgreSQL
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST', 'localhost'),
        'PORT': os.environ.get('DB_PORT', '5432'),
    }
}

# Sécurité
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
SECURE_CONTENT_TYPE_NOSNIFF = True
SECURE_BROWSER_XSS_FILTER = True
```

---

# Docker pour Django

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
ENV PYTHONPATH=/app
ENV DJANGO_SETTINGS_MODULE=monprojet.settings.production

# Exposition du port
EXPOSE 8000

# Commande de démarrage
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "monprojet.wsgi:application"]
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
      - "8000:8000"
    environment:
      - DEBUG=0
      - DB_NAME=monprojet_db
      - DB_USER=postgres
      - DB_PASSWORD=password
      - DB_HOST=db
    depends_on:
      - db
    volumes:
      - static_volume:/app/staticfiles
      - media_volume:/app/media

  db:
    image: postgres:14
    environment:
      - POSTGRES_DB=monprojet_db
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    depends_on:
      - web

volumes:
  postgres_data:
  static_volume:
  media_volume:
```

---

# Configuration Nginx

```nginx
# nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream django {
        server web:8000;
    }

    server {
        listen 80;
        server_name mondomaine.com;

        location /static/ {
            alias /app/staticfiles/;
        }

        location /media/ {
            alias /app/media/;
        }

        location / {
            proxy_pass http://django;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

---
layout: default
---

# Optimisation et Performance 📈

### Améliorer les performances Django

**Techniques d'optimisation :**
- Requêtes optimisées
- Cache système
- Compression
- CDN pour les statiques
- Monitoring

---

# Optimisation des Requêtes

```python
# Éviter le problème N+1
# ❌ Inefficace
articles = Article.objects.all()
for article in articles:
    print(article.auteur.username)  # Une requête par article

# ✅ Efficace
articles = Article.objects.select_related('auteur', 'categorie')
for article in articles:
    print(article.auteur.username)  # Une seule requête

# Pour les relations ManyToMany
articles = Article.objects.prefetch_related('tags')

# Requêtes complexes avec annotations
from django.db.models import Count, Avg
articles_avec_stats = Article.objects.annotate(
    nombre_commentaires=Count('commentaires'),
    note_moyenne=Avg('notes__valeur')
)
```

---

# Système de Cache

```python
# settings.py - Configuration du cache
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    }
}

# Utilisation du cache dans les vues
from django.core.cache import cache
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)  # Cache pendant 15 minutes
def liste_articles_cached(request):
    articles = Article.objects.filter(publie=True)
    return render(request, 'blog/liste.html', {'articles': articles})

# Cache manuel
def get_articles_populaires():
    cache_key = 'articles_populaires'
    articles = cache.get(cache_key)
    
    if not articles:
        articles = Article.objects.filter(
            publie=True
        ).order_by('-vues')[:10]
        cache.set(cache_key, articles, 3600)  # 1 heure
    
    return articles
```

---

# Monitoring et Logging

```python
# settings.py - Configuration du logging
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {process:d} {thread:d} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': 'django.log',
            'formatter': 'verbose',
        },
    },
    'root': {
        'handlers': ['file'],
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'INFO',
            'propagate': False,
        },
    },
}
```

---

# Points Clés à Retenir 🎯

### Résumé Django complet

**1. Architecture Django :**
- Pattern MVT (Model-View-Template)
- Applications modulaires
- ORM intégré
- Admin automatique

**2. Fonctionnalités principales :**
- Gestion des utilisateurs
- Système de templates
- Formulaires sécurisés
- API REST avec DRF

**3. Bonnes pratiques :**
- Tests automatisés
- Configuration par environnement
- Optimisation des requêtes
- Sécurité intégrée

**4. Déploiement :**
- Docker et containers
- Nginx + Gunicorn
- Base de données PostgreSQL
- Monitoring et logs

**Django est prêt pour la production !** 🚀
