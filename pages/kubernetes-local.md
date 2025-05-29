---
layout: default
routeAlias: 'kubernetes-local'
---

# Kubernetes Local Multi-Node 🌐

## Installation et Configuration

- **Prérequis**
  - Docker Desktop
  - kubectl
  - kind ou minikube

- **Configuration**
  ```bash
  # Création d'un cluster multi-node avec kind
  kind create cluster --config multi-node-config.yaml
  ```

- **Vérification**
  ```bash
  # Vérifier les nodes
  kubectl get nodes
  ```

---
layout: default
routeAlias: 'kubernetes-local-config'
---

# Configuration du Cluster

## Exemple de configuration kind

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
- role: worker
- role: worker
```

## Déploiement d'applications

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
        image: nginx:latest
```

---
layout: default
routeAlias: 'kubernetes-local-networking'
---

# Networking et Services

## Configuration réseau

- **Services**
  - ClusterIP
  - NodePort
  - LoadBalancer

- **Ingress**
  - Nginx Ingress Controller
  - Traefik
  - Cert-Manager

## Exemple de service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30000
  selector:
    app: nginx
```

---
layout: default
routeAlias: 'kubernetes-local-storage'
---

# Stockage et Volumes

## Types de stockage

- **Volumes persistants**
  - HostPath
  - NFS
  - Cloud Storage

- **Storage Classes**
  - Standard
  - SSD
  - Premium

## Exemple de PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: standard
``` 