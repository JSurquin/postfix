---
layout: default
routeAlias: 'exercices-django'
---

# EXERCICES DJANGO 🏗️

### Pratique avec le framework Django

---

# Structure des Exercices 📋

### Progression pédagogique

**4 niveaux de difficulté :**
- 🌱 **Débutant** - Premiers pas avec Django
- 🌿 **Intermédiaire** - Fonctionnalités avancées
- 🌳 **Avancé** - Applications complètes
- 🚀 **Expert** - Optimisation et production

**Types d'exercices :**
- Configuration et setup
- Modèles et base de données
- Vues et templates
- API et authentification

---
layout: default
---

# Exercice 1 - Blog Simple 🌱

### Créer un blog de base

**Objectifs :**
- Setup d'un projet Django
- Modèles Article et Catégorie
- Admin interface
- Vues de liste et détail

**Temps estimé :** 2 heures

---

# Exercice 1 - Setup Initial

```bash
# 1. Créer le projet
django-admin startproject blog_project
cd blog_project

# 2. Créer l'application
python manage.py startapp blog

# 3. Configuration initiale
python manage.py migrate
python manage.py createsuperuser

# 4. Structure attendue
blog_project/
├── manage.py
├── blog_project/
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── blog/
    ├── models.py
    ├── views.py
    ├── urls.py
    └── admin.py
```

---

# Exercice 1 - Modèles

```python
# blog/models.py - À implémenter
from django.db import models
from django.contrib.auth.models import User

class Categorie(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    description = models.TextField(blank=True)
    
    def __str__(self):
        return self.nom
    
    class Meta:
        ordering = ['nom']

class Article(models.Model):
    # À compléter :
    # - titre (CharField, max 200)
    # - contenu (TextField)
    # - auteur (ForeignKey vers User)
    # - categorie (ForeignKey vers Categorie)
    # - date_creation (DateTimeField, auto_now_add)
    # - date_modification (DateTimeField, auto_now)
    # - publie (BooleanField, default False)
    
    def __str__(self):
        return self.titre
    
    class Meta:
        ordering = ['-date_creation']
```

---

# Exercice 1 - Admin Interface

```python
# blog/admin.py - À implémenter
from django.contrib import admin
from .models import Article, Categorie

@admin.register(Categorie)
class CategorieAdmin(admin.ModelAdmin):
    # Configuration à ajouter :
    # - list_display
    # - search_fields
    pass

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    # Configuration à ajouter :
    # - list_display avec titre, auteur, categorie, publie, date_creation
    # - list_filter avec publie, categorie, date_creation
    # - search_fields avec titre, contenu
    # - date_hierarchy sur date_creation
    # - actions personnalisées pour publier/dépublier
    pass
```

---

# Exercice 1 - Vues

```python
# blog/views.py - À implémenter
from django.shortcuts import render, get_object_or_404
from .models import Article, Categorie

def liste_articles(request):
    # Récupérer tous les articles publiés
    # Rendre le template 'blog/liste.html'
    pass

def detail_article(request, article_id):
    # Récupérer l'article par ID (404 si pas trouvé)
    # Rendre le template 'blog/detail.html'
    pass

def articles_par_categorie(request, categorie_id):
    # Récupérer les articles d'une catégorie
    # Rendre le template 'blog/categorie.html'
    pass
```

---

# Exercice 1 - URLs

```python
# blog/urls.py - À créer
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    # À compléter avec les patterns pour :
    # - liste_articles (racine)
    # - detail_article avec ID
    # - articles_par_categorie avec ID
]

# blog_project/urls.py - À modifier
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    # Ajouter l'include pour blog.urls
]
```

---

# Exercice 1 - Templates

```html
<!-- blog/templates/blog/base.html - À créer -->
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Mon Blog{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
        <!-- Navigation à implémenter -->
    </nav>
    
    <main class="container mt-4">
        {% block content %}
        {% endblock %}
    </main>
</body>
</html>

<!-- blog/templates/blog/liste.html - À créer -->
<!-- Template pour afficher la liste des articles -->

<!-- blog/templates/blog/detail.html - À créer -->
<!-- Template pour afficher le détail d'un article -->
```

---
layout: default
---

# Exercice 2 - Système d'Authentification 🌿

### Ajouter la gestion des utilisateurs

**Objectifs :**
- Login/Logout/Register
- Profils utilisateur
- Protection des vues
- Gestion des permissions

**Temps estimé :** 3 heures

---

# Exercice 2 - Formulaires

```python
# blog/forms.py - À créer
from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User
from .models import Article

class InscriptionForm(UserCreationForm):
    email = forms.EmailField(required=True)
    
    class Meta:
        model = User
        fields = ('username', 'email', 'password1', 'password2')
    
    def save(self, commit=True):
        # Implémenter la sauvegarde avec email
        pass

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        # Champs à inclure : titre, contenu, categorie
        # Widgets avec classes Bootstrap
        pass
    
    def clean_titre(self):
        # Validation : titre minimum 10 caractères
        pass
```

---

# Exercice 2 - Vues d'Authentification

```python
# blog/views.py - Ajouter ces vues
from django.contrib.auth import login, authenticate
from django.contrib.auth.decorators import login_required
from django.shortcuts import redirect
from .forms import InscriptionForm, ArticleForm

def inscription(request):
    # Vue pour l'inscription des utilisateurs
    # GET : afficher le formulaire
    # POST : traiter l'inscription et connecter l'utilisateur
    pass

@login_required
def creer_article(request):
    # Vue pour créer un article (utilisateur connecté uniquement)
    # GET : afficher le formulaire
    # POST : sauvegarder avec l'auteur = utilisateur connecté
    pass

@login_required
def mes_articles(request):
    # Vue pour afficher les articles de l'utilisateur connecté
    pass

@login_required
def modifier_article(request, article_id):
    # Vue pour modifier un article (propriétaire uniquement)
    pass
```

---

# Exercice 2 - Templates d'Auth

```html
<!-- templates/registration/login.html - À créer -->
{% extends 'blog/base.html' %}

{% block title %}Connexion - {{ block.super }}{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <h2>Connexion</h2>
        <!-- Formulaire de connexion avec Bootstrap -->
    </div>
</div>
{% endblock %}

<!-- blog/templates/blog/inscription.html - À créer -->
<!-- Template pour l'inscription -->

<!-- blog/templates/blog/creer_article.html - À créer -->
<!-- Template pour créer/modifier un article -->
```

---

# Exercice 2 - URLs d'Auth

```python
# blog/urls.py - Ajouter ces patterns
urlpatterns = [
    # URLs existantes...
    
    # Nouvelles URLs à ajouter :
    # - inscription
    # - creer_article
    # - mes_articles
    # - modifier_article avec ID
]

# blog_project/urls.py - Ajouter
urlpatterns = [
    path('admin/', admin.site.urls),
    path('accounts/', include('django.contrib.auth.urls')),
    path('', include('blog.urls')),
]
```

---
layout: default
---

# Exercice 3 - API REST 🌳

### Créer une API pour le blog

**Objectifs :**
- Django REST Framework
- Sérializers pour les modèles
- ViewSets et permissions
- Authentification JWT

**Temps estimé :** 4 heures

---

# Exercice 3 - Installation DRF

```bash
# Installation des dépendances
pip install djangorestframework
pip install djangorestframework-simplejwt
pip install django-cors-headers

# Configuration dans settings.py
INSTALLED_APPS = [
    # ... apps existantes
    'rest_framework',
    'rest_framework_simplejwt',
    'corsheaders',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20
}
```

---

# Exercice 3 - Sérializers

```python
# blog/serializers.py - À créer
from rest_framework import serializers
from django.contrib.auth.models import User
from .models import Article, Categorie

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

class CategorieSerializer(serializers.ModelSerializer):
    # À implémenter pour le modèle Categorie
    pass

class ArticleSerializer(serializers.ModelSerializer):
    auteur = UserSerializer(read_only=True)
    categorie = CategorieSerializer(read_only=True)
    
    class Meta:
        model = Article
        # Champs à inclure
        # read_only_fields pour auteur et dates
        pass
    
    def validate_titre(self, value):
        # Validation personnalisée du titre
        pass
```

---

# Exercice 3 - ViewSets

```python
# blog/api_views.py - À créer
from rest_framework import viewsets, permissions, status
from rest_framework.decorators import action
from rest_framework.response import Response
from .models import Article, Categorie
from .serializers import ArticleSerializer, CategorieSerializer

class CategorieViewSet(viewsets.ReadOnlyModelViewSet):
    # ViewSet en lecture seule pour les catégories
    pass

class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.filter(publie=True)
    serializer_class = ArticleSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]
    
    def perform_create(self, serializer):
        # Assigner l'auteur à l'utilisateur connecté
        pass
    
    def get_permissions(self):
        # Permissions spécifiques selon l'action
        if self.action in ['update', 'partial_update', 'destroy']:
            # Seul l'auteur peut modifier/supprimer
            pass
        return super().get_permissions()
    
    @action(detail=False, methods=['get'])
    def mes_articles(self, request):
        # Action personnalisée pour les articles de l'utilisateur
        pass
```

---

# Exercice 3 - URLs API

```python
# blog/api_urls.py - À créer
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
)
from .api_views import ArticleViewSet, CategorieViewSet

router = DefaultRouter()
# Enregistrer les ViewSets

app_name = 'blog_api'

urlpatterns = [
    path('', include(router.urls)),
    path('auth/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('auth/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]

# blog_project/urls.py - Ajouter
urlpatterns = [
    # ... URLs existantes
    path('api/v1/', include('blog.api_urls')),
]
```

---
layout: default
---

# Exercice 4 - Projet E-commerce 🚀

### Application e-commerce complète

**Objectifs :**
- Modèles complexes avec relations
- Panier et commandes
- Système de paiement
- Interface d'administration avancée

**Temps estimé :** 8 heures

---

# Exercice 4 - Modèles E-commerce

```python
# shop/models.py - À implémenter
from django.db import models
from django.contrib.auth.models import User

class Categorie(models.Model):
    nom = models.CharField(max_length=100)
    slug = models.SlugField(unique=True)
    parent = models.ForeignKey('self', null=True, blank=True, on_delete=models.CASCADE)
    
class Produit(models.Model):
    nom = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    description = models.TextField()
    prix = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.PositiveIntegerField()
    disponible = models.BooleanField(default=True)
    categorie = models.ForeignKey(Categorie, on_delete=models.CASCADE)
    image = models.ImageField(upload_to='produits/', blank=True)
    date_creation = models.DateTimeField(auto_now_add=True)
    
    # Méthodes à implémenter :
    # - get_absolute_url()
    # - est_en_stock()
    # - reduire_stock(quantite)

class Commande(models.Model):
    STATUT_CHOICES = [
        ('en_attente', 'En attente'),
        ('confirmee', 'Confirmée'),
        ('expediee', 'Expédiée'),
        ('livree', 'Livrée'),
        ('annulee', 'Annulée'),
    ]
    
    # Champs à ajouter :
    # - utilisateur (ForeignKey)
    # - date_creation
    # - statut
    # - total
    # - adresse_livraison
    
class LigneCommande(models.Model):
    # Relation Many-to-One avec Commande et Produit
    # quantite, prix_unitaire
    pass
```

---

# Exercice 4 - Vues E-commerce

```python
# shop/views.py - À implémenter
from django.shortcuts import render, get_object_or_404, redirect
from django.contrib.auth.decorators import login_required
from django.contrib import messages
from .models import Produit, Categorie, Commande

def catalogue(request, categorie_slug=None):
    # Vue pour afficher le catalogue avec filtrage par catégorie
    # Implémenter la pagination (12 produits par page)
    pass

def detail_produit(request, slug):
    # Vue pour afficher le détail d'un produit
    # Ajouter formulaire pour ajouter au panier
    pass

@login_required
def ajouter_au_panier(request, produit_id):
    # Vue pour ajouter un produit au panier (session)
    # Vérifier le stock disponible
    pass

@login_required
def panier(request):
    # Vue pour afficher le contenu du panier
    pass

@login_required
def checkout(request):
    # Vue pour finaliser la commande
    # Créer la commande et les lignes de commande
    # Réduire le stock des produits
    pass

class PanierMixin:
    """Mixin pour gérer le panier en session"""
    
    def get_panier(self, request):
        # Récupérer le panier depuis la session
        pass
    
    def ajouter_produit(self, request, produit, quantite):
        # Ajouter un produit au panier
        pass
    
    def supprimer_produit(self, request, produit_id):
        # Supprimer un produit du panier
        pass
    
    def vider_panier(self, request):
        # Vider complètement le panier
        pass
```

---

# Exercice 4 - Templates E-commerce

```html
<!-- shop/templates/shop/catalogue.html -->
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <!-- Sidebar catégories -->
    <div class="col-md-3">
        <h5>Catégories</h5>
        <!-- Liste des catégories avec liens -->
    </div>
    
    <!-- Grille de produits -->
    <div class="col-md-9">
        <div class="row">
            {% for produit in produits %}
            <div class="col-md-4 mb-4">
                <div class="card">
                    <!-- Image, nom, prix, bouton "Voir détail" -->
                </div>
            </div>
            {% endfor %}
        </div>
        
        <!-- Pagination -->
    </div>
</div>
{% endblock %}

<!-- shop/templates/shop/detail_produit.html -->
<!-- Template pour le détail produit avec formulaire d'ajout au panier -->

<!-- shop/templates/shop/panier.html -->
<!-- Template pour afficher le panier avec possibilité de modifier les quantités -->
```

---

# Exercice 4 - Admin Avancé

```python
# shop/admin.py - À implémenter
from django.contrib import admin
from django.utils.html import format_html
from .models import Categorie, Produit, Commande, LigneCommande

@admin.register(Categorie)
class CategorieAdmin(admin.ModelAdmin):
    list_display = ['nom', 'parent', 'nombre_produits']
    prepopulated_fields = {'slug': ('nom',)}
    
    def nombre_produits(self, obj):
        return obj.produit_set.count()

class LigneCommandeInline(admin.TabularInline):
    model = LigneCommande
    extra = 0

@admin.register(Commande)
class CommandeAdmin(admin.ModelAdmin):
    list_display = ['id', 'utilisateur', 'statut', 'total', 'date_creation']
    list_filter = ['statut', 'date_creation']
    search_fields = ['utilisateur__username', 'id']
    inlines = [LigneCommandeInline]
    
    actions = ['marquer_confirmee', 'marquer_expediee']
    
    def marquer_confirmee(self, request, queryset):
        # Action pour confirmer les commandes
        pass

@admin.register(Produit)
class ProduitAdmin(admin.ModelAdmin):
    list_display = ['nom', 'categorie', 'prix', 'stock', 'disponible', 'image_preview']
    list_filter = ['disponible', 'categorie', 'date_creation']
    search_fields = ['nom', 'description']
    prepopulated_fields = {'slug': ('nom',)}
    
    def image_preview(self, obj):
        if obj.image:
            return format_html('<img src="{}" width="50" height="50" />', obj.image.url)
        return "Pas d'image"
```

---
layout: default
---

# Solutions et Corrections 💡

### Exercice 1 - Blog Simple

**Points clés de la solution :**
- Configuration correcte de INSTALLED_APPS
- Modèles avec relations ForeignKey
- Admin avec list_display et actions
- Vues utilisant get_object_or_404
- Templates avec héritage

---

# Solution Exercice 1 - Modèles

```python
# blog/models.py - Solution
from django.db import models
from django.contrib.auth.models import User
from django.urls import reverse

class Categorie(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    description = models.TextField(blank=True)
    
    def __str__(self):
        return self.nom
    
    def get_absolute_url(self):
        return reverse('blog:articles_categorie', args=[self.pk])
    
    class Meta:
        ordering = ['nom']

class Article(models.Model):
    titre = models.CharField(max_length=200)
    contenu = models.TextField()
    auteur = models.ForeignKey(User, on_delete=models.CASCADE, related_name='articles')
    categorie = models.ForeignKey(Categorie, on_delete=models.CASCADE, related_name='articles')
    date_creation = models.DateTimeField(auto_now_add=True)
    date_modification = models.DateTimeField(auto_now=True)
    publie = models.BooleanField(default=False)
    
    def __str__(self):
        return self.titre
    
    def get_absolute_url(self):
        return reverse('blog:detail_article', args=[self.pk])
    
    class Meta:
        ordering = ['-date_creation']
```

---

# Solution Exercice 1 - Admin

```python
# blog/admin.py - Solution
from django.contrib import admin
from .models import Article, Categorie

@admin.register(Categorie)
class CategorieAdmin(admin.ModelAdmin):
    list_display = ['nom', 'nombre_articles']
    search_fields = ['nom', 'description']
    
    def nombre_articles(self, obj):
        return obj.articles.count()
    nombre_articles.short_description = 'Nombre d\'articles'

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    list_display = ['titre', 'auteur', 'categorie', 'publie', 'date_creation']
    list_filter = ['publie', 'categorie', 'date_creation', 'auteur']
    search_fields = ['titre', 'contenu']
    date_hierarchy = 'date_creation'
    list_editable = ['publie']
    
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

# Solution Exercice 1 - Vues

```python
# blog/views.py - Solution
from django.shortcuts import render, get_object_or_404
from django.core.paginator import Paginator
from .models import Article, Categorie

def liste_articles(request):
    articles = Article.objects.filter(publie=True).select_related('auteur', 'categorie')
    
    # Pagination
    paginator = Paginator(articles, 5)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    
    context = {
        'articles': page_obj,
        'categories': Categorie.objects.all()
    }
    return render(request, 'blog/liste.html', context)

def detail_article(request, article_id):
    article = get_object_or_404(
        Article.objects.select_related('auteur', 'categorie'),
        id=article_id,
        publie=True
    )
    
    # Articles similaires de la même catégorie
    articles_similaires = Article.objects.filter(
        categorie=article.categorie,
        publie=True
    ).exclude(id=article.id)[:3]
    
    context = {
        'article': article,
        'articles_similaires': articles_similaires
    }
    return render(request, 'blog/detail.html', context)

def articles_par_categorie(request, categorie_id):
    categorie = get_object_or_404(Categorie, id=categorie_id)
    articles = Article.objects.filter(
        categorie=categorie,
        publie=True
    ).select_related('auteur')
    
    context = {
        'categorie': categorie,
        'articles': articles
    }
    return render(request, 'blog/categorie.html', context)
```

---

# Points d'Évaluation 📊

### Critères de réussite

**Pour chaque exercice :**
- ✅ **Fonctionnalité** - Le code fonctionne correctement
- ✅ **Bonnes pratiques** - Respect des conventions Django
- ✅ **Sécurité** - Protection CSRF, validation des données
- ✅ **Performance** - Optimisation des requêtes
- ✅ **Code qualité** - Lisibilité et documentation

**Barème suggéré :**
- 🌱 Débutant : 15 points
- 🌿 Intermédiaire : 20 points  
- 🌳 Avancé : 25 points
- 🚀 Expert : 40 points

**Total : 100 points**

---

# Ressources Complémentaires 📚

### Pour aller plus loin

**Documentation officielle :**
- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Django Packages](https://djangopackages.org/)

**Tutoriels avancés :**
- Tests automatisés avec pytest-django
- Déploiement avec Docker et Kubernetes
- Optimisation des performances
- Sécurité et bonnes pratiques

**Projets pratiques suggérés :**
- Système de gestion de contenu (CMS)
- Application de réservation en ligne
- Plateforme de formation en ligne
- API GraphQL avec Graphene

**Maîtrisez Django avec la pratique !** 🎯
