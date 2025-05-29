---
layout: default
routeAlias: 'mini-blog'
---

<a name="MINI_BLOG" id="MINI_BLOG"></a>

# Projet Mini-Blog

## Objectif du projet

Créer une application de blog simple utilisant Angular 18 pour le frontend, Node.js pour le backend, MongoDB pour la base de données, et Nginx comme reverse proxy. Le tout sera conteneurisé avec Docker et déployé avec Ansible.

---

# Architecture du projet

## Composants principaux
- **Frontend** : Angular 18
  - Interface utilisateur moderne
  - Routing et state management
  - Composants réutilisables

- **Backend** : Node.js
  - API RESTful
  - Authentification JWT
  - Validation des données

---

# Architecture (Suite)

## Composants principaux
- **Base de données** : MongoDB
  - Stockage des articles
  - Gestion des utilisateurs
  - Indexation optimisée

- **Infrastructure** : Docker + Nginx
  - Conteneurisation
  - Load balancing
  - SSL/TLS

---

# Structure du projet

## Organisation des fichiers
```
mini-blog/
├── frontend/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── backend/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── docker-compose.yml
└── ansible/
    ├── inventory/
    ├── playbooks/
    └── roles/
```

---

# Configuration Frontend

## Angular 18
```typescript
// frontend/src/app/app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { RouterModule } from '@angular/router';

@NgModule({
  declarations: [
    AppComponent,
    ArticleListComponent,
    ArticleDetailComponent,
    LoginComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    RouterModule.forRoot([
      { path: '', component: ArticleListComponent },
      { path: 'article/:id', component: ArticleDetailComponent },
      { path: 'login', component: LoginComponent }
    ])
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

---

# Configuration Frontend (Suite)

## Dockerfile
```dockerfile
# frontend/Dockerfile
FROM node:20-alpine as builder

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist/mini-blog /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

---

# Configuration Backend

## Node.js
```javascript
// backend/src/server.js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const jwt = require('jsonwebtoken');

const app = express();
app.use(cors());
app.use(express.json());

mongoose.connect('mongodb://db:27017/mini-blog');

const articleSchema = new mongoose.Schema({
  title: String,
  content: String,
  author: String,
  date: { type: Date, default: Date.now }
});

const Article = mongoose.model('Article', articleSchema);

app.get('/api/articles', async (req, res) => {
  const articles = await Article.find();
  res.json(articles);
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

# Configuration Backend (Suite)

## Dockerfile
```dockerfile
# backend/Dockerfile
FROM node:20-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "src/server.js"]
```

---

# Configuration Docker Compose

## docker-compose.yml
```yaml
version: '3.8'
services:
  frontend:
    build: ./frontend
    ports:
      - "80:80"
    depends_on:
      - backend

  backend:
    build: ./backend
    ports:
      - "3000:3000"
    environment:
      - MONGODB_URI=mongodb://db:27017/mini-blog
      - JWT_SECRET=your-secret-key
    depends_on:
      - db

  db:
    image: mongo:latest
    volumes:
      - mongodb_data:/data/db
    ports:
      - "27017:27017"

volumes:
  mongodb_data:
```

---

# Configuration Nginx

## nginx.conf
```nginx
# frontend/nginx.conf
server {
    listen 80;
    server_name localhost;

    location / {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    location /api {
        proxy_pass http://backend:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

# Configuration Ansible

## Playbook principal
```yaml
# ansible/playbooks/deploy.yml
---
- name: Déploiement du Mini-Blog
  hosts: all
  become: yes
  tasks:
    - name: Installation de Docker
      include_role:
        name: docker

    - name: Installation de Docker Compose
      include_role:
        name: docker-compose

    - name: Déploiement de l'application
      include_role:
        name: mini-blog
```

---

# Configuration Ansible (Suite)

## Rôle mini-blog
```yaml
# ansible/roles/mini-blog/tasks/main.yml
---
- name: Création du répertoire de l'application
  file:
    path: "{{ app_path }}"
    state: directory
    mode: '0755'

- name: Copie des fichiers de l'application
  copy:
    src: "{{ item }}"
    dest: "{{ app_path }}"
  with_items:
    - docker-compose.yml
    - frontend
    - backend

- name: Démarrage de l'application
  docker_compose:
    project_src: "{{ app_path }}"
    files:
      - docker-compose.yml
    state: present
```

---

# Fonctionnalités du blog

## Articles
- Création d'articles
- Édition d'articles
- Suppression d'articles
- Liste des articles
- Recherche d'articles

## Utilisateurs
- Inscription
- Connexion
- Profil utilisateur
- Gestion des droits

---

# Sécurité

## Mesures de sécurité
- Authentification JWT
- Validation des données
- Protection CSRF
- Rate limiting
- HTTPS/TLS

## Configuration de sécurité
```yaml
# backend/src/config/security.js
module.exports = {
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: '1h'
  },
  cors: {
    origin: process.env.FRONTEND_URL,
    credentials: true
  },
  rateLimit: {
    windowMs: 15 * 60 * 1000,
    max: 100
  }
};
```

---

# Monitoring

## Configuration Prometheus
```yaml
# monitoring/prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'backend'
    static_configs:
      - targets: ['backend:3000']

  - job_name: 'frontend'
    static_configs:
      - targets: ['frontend:80']
```

## Configuration Grafana
```yaml
# monitoring/grafana/dashboards/blog.json
{
  "dashboard": {
    "title": "Mini-Blog Metrics",
    "panels": [
      {
        "title": "API Requests",
        "type": "graph",
        "datasource": "Prometheus"
      },
      {
        "title": "Response Time",
        "type": "graph",
        "datasource": "Prometheus"
      }
    ]
  }
}
```

---

# Tests

## Tests Frontend
```typescript
// frontend/src/app/article-list.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ArticleListComponent } from './article-list.component';

describe('ArticleListComponent', () => {
  let component: ArticleListComponent;
  let fixture: ComponentFixture<ArticleListComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ArticleListComponent ]
    })
    .compileComponents();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

---

# Tests (Suite)

## Tests Backend
```javascript
// backend/src/tests/article.test.js
const request = require('supertest');
const app = require('../server');

describe('Article API', () => {
  it('should get all articles', async () => {
    const res = await request(app)
      .get('/api/articles')
      .send();
    expect(res.statusCode).toEqual(200);
    expect(Array.isArray(res.body)).toBeTruthy();
  });
});
```

---

# Déploiement

## Étapes de déploiement
1. Préparation de l'environnement
   - Installation de Docker
   - Configuration de Nginx
   - Configuration de MongoDB

2. Déploiement de l'application
   - Build des images
   - Configuration des conteneurs
   - Démarrage des services

3. Vérification
   - Tests de fonctionnalité
   - Tests de performance
   - Monitoring

---

# Maintenance

## Tâches de maintenance
- Mise à jour des dépendances
- Sauvegarde de la base de données
- Rotation des logs
- Surveillance des performances

## Scripts de maintenance
```bash
#!/bin/bash
# maintenance/backup.sh
DATE=$(date +%Y%m%d)
docker exec mini-blog_db mongodump --out /backup/$DATE
docker cp mini-blog_db:/backup/$DATE ./backup/
```

---

# Documentation

## Documentation technique
- Architecture du système
- Configuration des services
- Procédures de déploiement
- Procédures de maintenance

## Documentation utilisateur
- Guide d'utilisation
- FAQ
- Support technique
- Politique de confidentialité 