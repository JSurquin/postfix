---
layout: default
routeAlias: 'django-complet'
---

# DJANGO - COURS COMPLET 🎯

### Votre meilleur ami pour créer des sites web comme un chef ! 👨‍🍳

---

# Pourquoi Django nous fait craquer ? 😍

### Django, c'est comme avoir un super assistant !

**Imaginez Django comme votre couteau suisse magique ✨**

🔋 **"Batteries incluses"** = Django, c'est comme acheter une voiture avec GPS, climatisation, régulateur de vitesse... Tout est déjà dedans ! Pas besoin de bricoler.

🏗️ **DRY (Don't Repeat Yourself)** = "Ne te répète pas !" Django déteste la paresse... dans le bon sens ! Écris une fois, réutilise partout.

🎯 **Convention over Configuration** = Django a des opinions bien tranchées. Comme un chef étoilé, il sait ce qui marche et te guide !

**Ce que Django vous offre sur un plateau d'argent :**
- 👑 Une interface d'admin automatique (comme avoir un majordome !)
- 🗃️ Un ORM qui parle à votre base de données comme un traducteur
- 🔐 La sécurité intégrée (votre garde du corps personnel)
- 🛡️ Protection anti-hackers automatique
- 🧩 Des templates qui s'emboîtent comme des Lego

---

# Installation et Configuration 🛠️

### Préparer notre atelier de développement ! 🔧

**Avant de commencer l'aventure, préparons notre boîte à outils :**

🐍 **Python 3.8+** = Notre langage magique (si vous n'avez pas encore Python, c'est comme vouloir cuisiner sans cuisinière !)

🌐 **Un environnement virtuel** = Imaginez que chaque projet Python vit dans sa propre bulle. Comme ça, ils ne se disputent pas entre eux ! 

💾 **Une base de données** = Django démarre avec SQLite (une petite base de données qui tient dans un fichier). C'est parfait pour apprendre, comme apprendre à conduire sur un parking !

**Pourquoi un environnement virtuel ? 🤔**
Imaginez que vous vivez dans un appartement partagé. Sans environnement virtuel, tous vos projets Python partagent la même cuisine... Ça finit toujours en dispute ! 😅

---

# Notre première installation Django ! 🎉

**Étape par étape, comme une recette de cuisine :**

```bash
# 1. Créons notre bulle protectrice (environnement virtuel)
python -m venv django_env
source django_env/bin/activate  # Linux/Mac
# django_env\Scripts\activate   # Windows

# 2. Installons Django (notre super framework !)
pip install django

# 3. Vérifions que tout va bien
python -m django --version
```

**🎯 Petite astuce de pro :** Quand vous activez votre environnement virtuel, votre terminal affiche `(django_env)` au début de la ligne. C'est votre signal que vous êtes dans votre bulle protectrice ! 

**Si ça marche pas ?** Pas de panique ! C'est normal, on est tous passés par là. Vérifiez que Python est bien installé avec `python --version`. On apprend en faisant des erreurs ! 💪

---

# Créons notre premier bébé Django ! 👶

**Django va nous créer un projet tout beau, tout neuf :**

```bash
# La commande magique qui crée tout !
django-admin startproject monprojet

# Regardons ce que Django nous a préparé :
monprojet/
├── manage.py        # Notre baguette magique
└── monprojet/       # Le cerveau de notre projet
    ├── __init__.py  # Dit à Python "je suis un package"
    ├── settings.py  # Le tableau de bord de notre projet
    ├── urls.py      # Le GPS de notre site
    ├── asgi.py      # Pour les trucs en temps réel
    └── wsgi.py      # Pour déployer sur un serveur
```

**🎭 Métaphore du théâtre :**
- `manage.py` = Le régisseur qui organise tout
- `settings.py` = Le script avec tous les réglages
- `urls.py` = Le plan de la scène (où va chaque acteur)
- Le dossier interne = Les coulisses du spectacle

**💡 Le saviez-vous ?** Django crée toujours un dossier dans un dossier avec le même nom. C'est normal, ne vous inquiétez pas ! Le premier dossier est votre "maison", le second contient la "machinerie" !

---

# Premier démarrage : Le moment magique ! ✨

**Maintenant, donnons vie à notre création :**

```bash
# Entrons dans notre nouveau monde
cd monprojet

# Django prépare sa base de données (comme faire son lit le matin)
python manage.py migrate

# Créons notre compte "super admin" (le propriétaire du château !)
python manage.py createsuperuser

# TADAAA ! Lançons notre serveur !
python manage.py runserver
```

**🎉 Et voilà ! Si tout se passe bien, vous devriez voir :**
```
Starting development server at http://127.0.0.1:8000/
```

**🚀 Ouvrez votre navigateur et allez sur `http://127.0.0.1:8000/`**

Vous devriez voir une belle page "The install worked successfully!" avec une fusée qui décolle ! 

**💫 C'est VOTRE première application Django qui tourne !** Prenez une photo, c'est un moment historique ! 😄

**Pro tip :** Pour arrêter le serveur, faites `Ctrl+C` dans votre terminal. Votre serveur s'endort gentiment ! 😴

---
layout: default
---

# Structure d'un Projet Django 📁

### Votre nouvelle maison ! Faisons le tour du propriétaire 🏠

**Django, c'est comme une maison bien organisée :**

🔧 **`manage.py`** = Votre boîte à outils magique ! Ce petit fichier peut TOUT faire : créer des apps, gérer la base de données, lancer le serveur...

⚙️ **`settings.py`** = Le tableau de bord de votre maison. Température (DEBUG), systèmes de sécurité (SECRET_KEY), connexions (BASE DE DONNÉES)...

🗺️ **`urls.py`** = Votre GPS personnel ! Quand quelqu'un tape une adresse, Django regarde ici pour savoir où l'emmener.

🗃️ **`models.py`** = Vos tiroirs bien rangés ! Vous définissez comment ranger vos données (comme des fiches dans un classeur).

🎭 **`views.py`** = Le chef d'orchestre ! Il reçoit les demandes, fait le travail, et renvoie les réponses.

🎨 **`templates/`** = Votre atelier d'artiste ! Là où vous créez de beaux templates HTML.

**🏗️ Analogie de la pizzeria :** Django, c'est comme une pizzeria bien organisée. Chaque fichier a son rôle : la carte (urls), les recettes (models), le pizzaïolo (views), et la salle (templates) !

---

# Le settings.py : Votre tableau de bord ! 🎛️

**Ce fichier, c'est le cerveau de votre projet Django :**

```python
# monprojet/settings.py - Le centre de contrôle !
import os
from pathlib import Path

# Où est-ce qu'on habite ? Django se repère grâce à ça
BASE_DIR = Path(__file__).resolve().parent.parent

# ATTENTION ! Votre mot de passe secret ! 🔐
SECRET_KEY = 'your-secret-key-here'

# Mode développement = Django nous aide quand on fait des bêtises
DEBUG = True

# Qui a le droit de visiter votre site ?
ALLOWED_HOSTS = []

# Vos super-pouvoirs Django ! (les apps installées)
INSTALLED_APPS = [
    'django.contrib.admin',      # Interface admin magique 👑
    'django.contrib.auth',       # Système d'utilisateurs 👤
    'django.contrib.contenttypes',
    'django.contrib.sessions',   # Django se souvient de vous 🧠
    'django.contrib.messages',   # Pour les petits messages 💬
    'django.contrib.staticfiles', # Images, CSS, JS... 🎨
    # Vos futures créations iront ici ! ⭐
]
```

**🚨 DEBUG = True** signifie "mode développement" ! Django devient super bavard et vous explique tout quand ça plante. En production, JAMAIS True ! ⚠️

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

# Applications Django : Vos petites fabriques ! 🏭

### Chaque app Django = Une mission spéciale !

**Imaginez votre projet comme une ville 🏙️**

Une **application Django**, c'est comme un quartier spécialisé dans votre ville :
- 🏥 Un hôpital (app de gestion des patients)
- 🏫 Une école (app de cours en ligne)  
- 🛒 Un centre commercial (app e-commerce)
- 📰 Un journal local (app blog)

**La règle d'or :** Une app = Un seul métier !

**💡 Pourquoi séparer en apps ?**
- 🧩 **Modulaire** : Vous pouvez déplacer une app d'un projet à l'autre !
- 🛠️ **Maintenable** : Plus facile de réparer une petite pièce qu'une grosse machine
- 👥 **Collaboration** : Chaque développeur peut travailler sur son app
- 🔄 **Réutilisable** : Votre app "blog" peut servir dans 10 projets différents !

**🎯 Conseil de grand-mère :** Commencez simple ! Une app par grande fonctionnalité, vous complexifierez plus tard si besoin.

---

# Créons notre première app ! 🎊

**Une commande, et POUF ! Votre app est née :**

```bash
# La baguette magique pour créer une app
python manage.py startapp blog

# Regardez ce que Django nous a préparé !
blog/
├── __init__.py      # "Je suis un package Python !"
├── admin.py         # Votre interface d'administration
├── apps.py          # Les infos sur votre app
├── migrations/      # L'historique de votre base de données
│   └── __init__.py
├── models.py        # Vos "moules" à données
├── tests.py         # Pour vérifier que tout marche
└── views.py         # Vos pages web !
```

**🎪 Métaphore du cirque :**
- `models.py` = Les plans des numéros (structure des données)
- `views.py` = Les artistes qui font le spectacle
- `admin.py` = Le directeur qui gère tout depuis les coulisses
- `tests.py` = Les répétitions avant le grand show !

**🍰 C'est comme préparer un gâteau :** Django vous donne tous les ingrédients, à vous de créer la recette ! Les fichiers sont vides et n'attendent que votre créativité.

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

# Modèles Django : Vos moules à données ! 🍯

### L'ORM Django, ou comment parler à votre base de données en Python

**L'ORM, c'est votre traducteur personnel ! 🗣️**

Imaginez que vous ne parlez que français, mais votre base de données ne comprend que le SQL (une langue bizarre pleine de SELECT et WHERE 😵‍💫).

**L'ORM Django = Votre interprète magique ! ✨**

Au lieu d'écrire :
```sql
SELECT * FROM articles WHERE titre LIKE '%Python%';
```

Vous écrivez simplement :
```python
Article.objects.filter(titre__contains='Python')
```

**🎯 Les super-pouvoirs de l'ORM Django :**
- 🛡️ **Sécurité** : Fini les injections SQL ! Django protège vos fesses
- 🔄 **Migrations automatiques** : Django se souvient de tous vos changements
- 🔗 **Relations** : Connectez vos données comme des Lego
- 🐍 **100% Python** : Pas besoin d'apprendre le SQL (même si c'est bien de connaître)
- 💾 **Multi-bases** : MySQL, PostgreSQL, SQLite... Django s'adapte !

**🧙‍♂️ En gros :** Vous pensez "objets Python", Django traduit en "langage base de données" !

---

# Notre premier modèle : Un Article de blog ! 📝

**Créons le plan de construction de nos articles :**

```python
# blog/models.py - Notre première "recette" de données !
from django.db import models
from django.contrib.auth.models import User

class Article(models.Model):
    # Le titre de l'article (pas plus de 200 caractères)
    titre = models.CharField(max_length=200)
    
    # Le contenu (aussi long qu'on veut !)
    contenu = models.TextField()
    
    # Qui a écrit cet article ? (lien vers un utilisateur)
    auteur = models.ForeignKey(User, on_delete=models.CASCADE)
    
    # Quand l'article a été créé (Django met la date automatiquement)
    date_creation = models.DateTimeField(auto_now_add=True)
    
    # Quand il a été modifié pour la dernière fois
    date_modification = models.DateTimeField(auto_now=True)
    
    # Est-ce que l'article est publié ? (par défaut : non)
    publie = models.BooleanField(default=False)

    def __str__(self):
        # Comment Django affiche cet article (par son titre)
        return self.titre

    class Meta:
        # Les articles les plus récents en premier
        ordering = ['-date_creation']
        verbose_name = 'Article'
        verbose_name_plural = 'Articles'
```

**🏗️ Pensez à un formulaire papier :** Chaque `models.` quelque chose, c'est comme une case à remplir sur un formulaire ! Django crée automatiquement la base de données à partir de ça.

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
