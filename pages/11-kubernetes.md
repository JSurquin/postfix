---
routeAlias: 'kubernetes'
---

<a name="KUBERNETES" id="KUBERNETES"></a>

# Introduction à Kubernetes

## Qu'est-ce que Kubernetes ?

Kubernetes est un système d'orchestration de conteneurs open-source qui automatise le déploiement, la mise à l'échelle et la gestion des applications conteneurisées.

---
# Concepts fondamentaux

- **Pod** : Plus petite unité déployable
- **Service** : Point d'entrée stable pour les pods
- **Deployment** : Gestion des réplicas de pods
- **ConfigMap** : Configuration des applications
- **Secret** : Données sensibles

---
# Architecture Kubernetes

## Composants principaux
- **Master Node** : Plan de contrôle
- **Worker Node** : Exécution des conteneurs
- **kubelet** : Agent sur chaque node
- **kubectl** : Interface en ligne de commande

---
# Exemple de déploiement

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

---
# Commandes Kubernetes

```bash
# Lister les pods
kubectl get pods

# Créer un déploiement
kubectl apply -f deployment.yaml

# Voir les logs
kubectl logs pod-name

# Supprimer un déploiement
kubectl delete deployment nginx-deployment
```

---
# Avantages de Kubernetes

- Orchestration automatique
- Scalabilité horizontale
- Auto-réparation
- Gestion des secrets
- Rolling updates
- Load balancing 