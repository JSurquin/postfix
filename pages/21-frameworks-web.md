---
layout: default
routeAlias: 'frameworks-web'
---

# FRAMEWORKS WEB 🌐

### Flask et Django en 2025

---

# Flask - Micro-framework

### Framework léger et flexible

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, World!'

@app.route('/api/users', methods=['GET'])
def get_users():
    users = [
        {'id': 1, 'name': 'John'},
        {'id': 2, 'name': 'Jane'}
    ]
    return jsonify(users)

@app.route('/api/users', methods=['POST'])
def create_user():
    data = request.get_json()
    # Traitement des données
    return jsonify({'message': 'User created'}), 201

if __name__ == '__main__':
    app.run(debug=True)
```

---

# Flask - Templates et Forms

### Rendu de vues et formulaires

```python
from flask import Flask, render_template, request, flash, redirect
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key'

class UserForm(FlaskForm):
    name = StringField('Name', validators=[DataRequired()])
    email = StringField('Email', validators=[DataRequired()])
    submit = SubmitField('Submit')

@app.route('/register', methods=['GET', 'POST'])
def register():
    form = UserForm()
    if form.validate_on_submit():
        # Traitement du formulaire
        flash('User registered successfully!')
        return redirect('/')
    return render_template('register.html', form=form)
```

---

# Flask - Base de Données

### Intégration avec SQLAlchemy

```python
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    def __repr__(self):
        return f'<User {self.username}>'

@app.route('/users')
def get_users():
    users = User.query.all()
    return jsonify([{
        'id': user.id,
        'username': user.username,
        'email': user.email
    } for user in users])
```

---

# Django - Framework Full-Stack

### Framework complet et robuste

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp',
]

# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('users/', views.user_list, name='user_list'),
    path('users/<int:user_id>/', views.user_detail, name='user_detail'),
]
```

---

# Django - Modèles

### ORM Django

```python
# models.py
from django.db import models
from django.contrib.auth.models import User

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(max_length=500, blank=True)
    birth_date = models.DateField(null=True, blank=True)
    avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)
    
    def __str__(self):
        return f"{self.user.username}'s profile"

class Post(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return self.title
```

---

# Django - Vues

### Vues basées sur les classes

```python
# views.py
from django.views.generic import ListView, DetailView, CreateView, UpdateView
from django.contrib.auth.mixins import LoginRequiredMixin
from django.urls import reverse_lazy
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = 'blog/post_list.html'
    context_object_name = 'posts'
    paginate_by = 10

class PostDetailView(DetailView):
    model = Post
    template_name = 'blog/post_detail.html'

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    fields = ['title', 'content']
    template_name = 'blog/post_form.html'
    success_url = reverse_lazy('post-list')
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)
```

---

# Django - Templates

### Système de templates

```html
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
</head>
<body>
    <nav>
        <a href="{% url 'home' %}">Home</a>
        <a href="{% url 'post-list' %}">Blog</a>
    </nav>
    
    <main>
        {% block content %}
        {% endblock %}
    </main>
</body>
</html>

<!-- post_list.html -->
{% extends 'base.html' %}

{% block title %}Blog Posts{% endblock %}

{% block content %}
    {% for post in posts %}
        <article>
            <h2><a href="{% url 'post-detail' post.pk %}">{{ post.title }}</a></h2>
            <p>{{ post.content|truncatewords:50 }}</p>
            <small>By {{ post.author }} on {{ post.created_at }}</small>
        </article>
    {% endfor %}
    
    {% include 'pagination.html' %}
{% endblock %}
```

---

# Django - API REST

### Django REST Framework

```python
# serializers.py
from rest_framework import serializers
from .models import Post, User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

class PostSerializer(serializers.ModelSerializer):
    author = UserSerializer(read_only=True)
    
    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'author', 'created_at']

# views.py
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticatedOrReadOnly
from .serializers import PostSerializer

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
    
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)
```

---

# Django - Admin

### Interface d'administration

```python
# admin.py
from django.contrib import admin
from .models import Post, Profile

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'created_at', 'updated_at']
    list_filter = ['created_at', 'author']
    search_fields = ['title', 'content']
    date_hierarchy = 'created_at'
    
    def get_queryset(self, request):
        return super().get_queryset(request).select_related('author')

@admin.register(Profile)
class ProfileAdmin(admin.ModelAdmin):
    list_display = ['user', 'birth_date']
    search_fields = ['user__username', 'user__email']
```

---

# FastAPI - Framework Moderne

### Framework asynchrone moderne

```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel
from typing import List, Optional
import uvicorn

app = FastAPI()

class User(BaseModel):
    id: Optional[int] = None
    name: str
    email: str
    age: Optional[int] = None

class UserCreate(BaseModel):
    name: str
    email: str
    age: Optional[int] = None

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/users/", response_model=List[User])
async def get_users():
    return [
        {"id": 1, "name": "John", "email": "john@example.com", "age": 30},
        {"id": 2, "name": "Jane", "email": "jane@example.com", "age": 25}
    ]

@app.post("/users/", response_model=User)
async def create_user(user: UserCreate):
    return {"id": 3, **user.dict()}
```

---

# FastAPI - Dépendances et Validation

### Système de dépendances avancé

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from typing import Optional

security = HTTPBearer()

def get_current_user(credentials: HTTPAuthorizationCredentials = Depends(security)):
    # Logique d'authentification
    if credentials.credentials == "valid_token":
        return {"username": "john", "email": "john@example.com"}
    raise HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Invalid authentication credentials"
    )

@app.get("/protected/")
async def protected_route(current_user: dict = Depends(get_current_user)):
    return {"message": f"Hello {current_user['username']}"}

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: Optional[str] = None):
    return {"item_id": item_id, "q": q}
```

---

# Comparaison des Frameworks

### Choix selon les besoins

**Flask :**
- Micro-framework léger
- Flexibilité maximale
- Apprentissage rapide
- Idéal pour APIs simples

**Django :**
- Framework complet
- Admin intégré
- ORM puissant
- Idéal pour applications complexes

**FastAPI :**
- Performance asynchrone
- Validation automatique
- Documentation automatique
- Idéal pour APIs modernes

---

# Déploiement

### Déploiement en production

```python
# requirements.txt
Flask==2.3.3
Django==4.2.7
FastAPI==0.104.1
uvicorn==0.24.0
gunicorn==21.2.0

# Dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "app:app"]
```

---

# Exercices Pratiques 🎯

### Testez vos compétences

**Exercice 1 :** Créez une API REST simple avec Flask pour gérer des tâches (CRUD).

**Exercice 2 :** Implémentez un blog avec Django (modèles, vues, templates).

**Exercice 3 :** Créez une API asynchrone avec FastAPI pour un système de réservation.

---

# Solutions des Exercices 💡

### Exercice 1 - API Flask

```python
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///tasks.db'
db = SQLAlchemy(app)

class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    completed = db.Column(db.Boolean, default=False)

@app.route('/tasks', methods=['GET'])
def get_tasks():
    tasks = Task.query.all()
    return jsonify([{
        'id': task.id,
        'title': task.title,
        'completed': task.completed
    } for task in tasks])

@app.route('/tasks', methods=['POST'])
def create_task():
    data = request.get_json()
    task = Task(title=data['title'])
    db.session.add(task)
    db.session.commit()
    return jsonify({'id': task.id, 'title': task.title, 'completed': False}), 201
```

---

# Solutions des Exercices (suite)

### Exercice 2 - Blog Django

```python
# models.py
from django.db import models
from django.contrib.auth.models import User

class Category(models.Model):
    name = models.CharField(max_length=100)
    slug = models.SlugField(unique=True)
    
    class Meta:
        verbose_name_plural = "categories"
    
    def __str__(self):
        return self.name

class Post(models.Model):
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    def __str__(self):
        return self.title

# views.py
from django.views.generic import ListView, DetailView
from .models import Post, Category

class PostListView(ListView):
    model = Post
    template_name = 'blog/post_list.html'
    context_object_name = 'posts'
    paginate_by = 5

class PostDetailView(DetailView):
    model = Post
    template_name = 'blog/post_detail.html'
```

---

# Solutions des Exercices (fin)

### Exercice 3 - API FastAPI

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from datetime import datetime
from typing import List, Optional

app = FastAPI()

class Reservation(BaseModel):
    id: Optional[int] = None
    user_name: str
    room_number: int
    check_in: datetime
    check_out: datetime
    guests: int

class ReservationCreate(BaseModel):
    user_name: str
    room_number: int
    check_in: datetime
    check_out: datetime
    guests: int

# Simulation d'une base de données
reservations = []
next_id = 1

@app.post("/reservations/", response_model=Reservation)
async def create_reservation(reservation: ReservationCreate):
    global next_id
    new_reservation = Reservation(id=next_id, **reservation.dict())
    reservations.append(new_reservation)
    next_id += 1
    return new_reservation

@app.get("/reservations/", response_model=List[Reservation])
async def get_reservations():
    return reservations

@app.get("/reservations/{reservation_id}", response_model=Reservation)
async def get_reservation(reservation_id: int):
    for reservation in reservations:
        if reservation.id == reservation_id:
            return reservation
    raise HTTPException(status_code=404, detail="Reservation not found")
```

---

# Points Clés à Retenir 🎯

### Résumé des frameworks web

**1. Flask :**
- Micro-framework flexible
- Idéal pour APIs et prototypes
- Apprentissage rapide

**2. Django :**
- Framework complet
- Admin intégré
- Idéal pour applications complexes

**3. FastAPI :**
- Performance asynchrone
- Validation automatique
- Idéal pour APIs modernes

**Prêt pour la data science !** 🚀 