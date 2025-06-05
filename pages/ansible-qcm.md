---
layout: new-section
routeAlias: 'qcm-ansible'
---

<a name="QCM_ANSIBLE" id="QCM_ANSIBLE"></a>

# QCM : Maîtrise d'Ansible

---

## QCM sur la maîtrise d'Ansible

### 1. Quel est le principe fondamental d'Ansible ?

- [ ]  Ansible nécessite des agents sur tous les serveurs cibles
- [ ]  Ansible fonctionne en mode "push" sans agent
- [ ]  Ansible utilise uniquement le protocole HTTP
- [ ]  Ansible remplace complètement SSH

### 2. Quels sont les composants principaux d'Ansible ?

- [ ]  Control Node, Managed Nodes, Playbooks
- [ ]  Master, Workers, Registry
- [ ]  Client, Server, Database
- [ ]  Controller, Executors, Storage

---

# QCM Ansible (suite)

### 3. Que signifie "idempotent" dans le contexte Ansible ?

- [ ]  Les tâches s'exécutent toujours plus rapidement à la deuxième fois
- [ ]  Exécuter un playbook plusieurs fois produit le même résultat
- [ ]  Les erreurs sont automatiquement corrigées
- [ ]  Les tâches sont executées en parallèle

### 4. Dans quel format sont écrits les playbooks Ansible ?

- [ ]  JSON
- [ ]  XML
- [ ]  YAML
- [ ]  TOML

### 5. Qu'est-ce qu'un inventaire Ansible ?

- [ ]  La liste des playbooks disponibles
- [ ]  La liste des serveurs et groupes gérés par Ansible
- [ ]  L'historique des exécutions
- [ ]  Le catalogue des modules disponibles

---

# QCM Ansible (suite 2)

### 6. À quoi sert Ansible Vault ?

- [ ]  Stocker les playbooks de manière sécurisée
- [ ]  Chiffrer les données sensibles comme les mots de passe
- [ ]  Sauvegarder l'inventaire
- [ ]  Gérer les versions des playbooks

### 7. Quelle est la différence entre un module et un rôle ?

- [ ]  Un module est réutilisable, un rôle ne l'est pas
- [ ]  Un module exécute une tâche spécifique, un rôle est un ensemble de tâches organisées
- [ ]  Un rôle est plus rapide qu'un module
- [ ]  Il n'y a pas de différence

### 8. Quelle commande exécute une tâche ad-hoc sur tous les serveurs web ?

- [ ]  `ansible webservers -m ping`
- [ ]  `ansible-playbook -i webservers ping.yml`
- [ ]  `ansible all -m webservers -a ping`
- [ ]  `ansible run webservers ping`

---

# QCM Ansible (suite 3)

### 9. Quelle est la structure standard d'un rôle Ansible ?

- [ ]  `tasks/, handlers/, vars/, files/`
- [ ]  `src/, build/, test/, deploy/`
- [ ]  `main/, config/, scripts/, docs/`
- [ ]  `playbooks/, inventories/, modules/, plugins/`

### 10. Comment ignorer les erreurs pour une tâche spécifique ?

- [ ]  `ignore_errors: true`
- [ ]  `failed_when: false`
- [ ]  `error_handling: ignore`
- [ ]  `skip_errors: yes`

### 11. À quoi servent les tags dans Ansible ?

- [ ]  Identifier les versions des playbooks
- [ ]  Exécuter seulement certaines tâches d'un playbook
- [ ]  Catégoriser les serveurs dans l'inventaire
- [ ]  Marquer les erreurs dans les logs

---

# QCM Ansible (suite 4)

### 12. Comment implémenter un déploiement blue-green avec Ansible ?

- [ ]  Utiliser des groupes d'inventaire distincts et des variables conditionnelles
- [ ]  Créer deux playbooks séparés
- [ ]  Utiliser uniquement des rôles
- [ ]  Impossible avec Ansible seul

### 13. Quelle n'est PAS une bonne pratique de sécurité avec Ansible ?

- [ ]  Utiliser Ansible Vault pour les secrets
- [ ]  Stocker les clés SSH dans les playbooks
- [ ]  Limiter les privilèges avec `become_user`
- [ ]  Utiliser des connexions SSH avec clés

---

# Réponses (1-5)

1. **Ansible fonctionne en mode "push" sans agent**
2. **Control Node, Managed Nodes, Playbooks**
3. **Exécuter un playbook plusieurs fois produit le même résultat**
4. **YAML**
5. **La liste des serveurs et groupes gérés par Ansible**

---

# Réponses (6-10)

6. **Chiffrer les données sensibles comme les mots de passe**
7. **Un module exécute une tâche spécifique, un rôle est un ensemble de tâches organisées**
8. **`ansible webservers -m ping`**
9. **`tasks/, handlers/, vars/, files/`**
10. **`ignore_errors: true`**

---

# Réponses (11-13)

11. **Exécuter seulement certaines tâches d'un playbook**
12. **Utiliser des groupes d'inventaire distincts et des variables conditionnelles**
13. **Stocker les clés SSH dans les playbooks**

---

# 🎯 Questions Pratiques

### Question 7 : Modules vs Rôles 🧩

**Quelle est la différence entre un module et un rôle ?**

A) Un module est réutilisable, un rôle ne l'est pas

B) Un module exécute une tâche spécifique, un rôle est un ensemble de tâches organisées

C) Un rôle est plus rapide qu'un module

D) Il n'y a pas de différence

---

### Question 8 : Commandes ad-hoc ⚡

**Quelle commande exécute une tâche ad-hoc sur tous les serveurs web ?**

A) `ansible webservers -m ping`

B) `ansible-playbook -i webservers ping.yml`

C) `ansible all -m webservers -a ping`

D) `ansible run webservers ping`

---

### Question 9 : Structure de rôle 📁

**Quelle est la structure standard d'un rôle Ansible ?**

A) `tasks/, handlers/, vars/, files/`

B) `src/, build/, test/, deploy/`

C) `main/, config/, scripts/, docs/`

D) `playbooks/, inventories/, modules/, plugins/`

---

### Question 10 : Gestion des erreurs 🚨

**Comment ignorer les erreurs pour une tâche spécifique ?**

A) `ignore_errors: true`

B) `failed_when: false`

C) `error_handling: ignore`

D) `skip_errors: yes`

---

### Question 11 : Tags 🏷️

**À quoi servent les tags dans Ansible ?**

A) Identifier les versions des playbooks

B) Exécuter seulement certaines tâches d'un playbook

C) Catégoriser les serveurs dans l'inventaire

D) Marquer les erreurs dans les logs

---

# 🏆 Scénarios Avancés

### Question 12 : Déploiement Blue-Green 🔄

**Comment implémenter un déploiement blue-green avec Ansible ?**

A) Utiliser des groupes d'inventaire distincts et des variables conditionnelles

B) Créer deux playbooks séparés

C) Utiliser uniquement des rôles

D) Impossible avec Ansible seul

---

### Question 13 : Sécurité 🛡️

**Quelle n'est PAS une bonne pratique de sécurité avec Ansible ?**

A) Utiliser Ansible Vault pour les secrets

B) Stocker les clés SSH dans les playbooks

C) Limiter les privilèges avec `become_user`

D) Utiliser des connexions SSH avec clés

---

# 📊 Correction et Barème

### Réponses Correctes (Fondamentaux)

**Questions 1-6**
1. **B** - Mode push sans agent
2. **A** - Control Node, Managed Nodes, Playbooks
3. **B** - Même résultat à chaque exécution
4. **C** - Format YAML
5. **B** - Liste des serveurs gérés
6. **B** - Chiffrement des données sensibles

---

# 📊 Correction (suite)

### Réponses Correctes (Concepts)

**Questions 7-11**

7. **B** - Module = tâche spécifique, Rôle = ensemble organisé

8. **A** - `ansible webservers -m ping`

9. **A** - Structure standard des rôles

10. **A** - `ignore_errors: true`

11. **B** - Exécution sélective de tâches

**Questions 12-13 (Avancé)**

12. **A** - Groupes d'inventaire + variables

13. **B** - Ne jamais stocker les clés dans les playbooks

---

# 📊 Score d'évaluation

### Barème de notation

- **12-13 bonnes réponses** : 🏆 Expert Ansible !
- **10-11 bonnes réponses** : 🥇 Niveau avancé
- **8-9 bonnes réponses** : 🥈 Bon niveau
- **6-7 bonnes réponses** : 🥉 Niveau intermédiaire
- **< 6 bonnes réponses** : 📚 Reprenez les bases

---

# 💡 Explications Détaillées

### Points clés à retenir

**Question 3 - Idempotence** :
L'idempotence est cruciale : si l'état désiré est déjà atteint, Ansible ne fait rien. Cela permet d'exécuter le même playbook plusieurs fois sans effet de bord.

**Question 7 - Modules vs Rôles** :

---

# 💡 Exemples de code

```yaml
# Module = action unique
- name: Installer nginx
  apt:
    name: nginx
    state: present
```

---

# 💡 Exemples de code (suite)

```yaml
# Rôle = collection organisée
- hosts: webservers
  roles:
    - webserver  # Contient installation + config + service
```

---

# 💡 Sécurité avec Ansible

```yaml
# ❌ Mauvais - clé dans le playbook
ssh_key: -----BEGIN RSA PRIVATE KEY-----
```

---

# 💡 Sécurité (suite)

```yaml
# ✅ Bon - référence sécurisée
ssh_key_path: "{{ vault_ssh_key_path }}"
```

---

# 💡 Bonnes pratiques

### À retenir absolument

✅ Ansible est agentless et idempotent

✅ YAML est le format standard

✅ Utilisez Vault pour les secrets

✅ Les rôles permettent la réutilisabilité

✅ Les tags facilitent l'exécution sélective

---

# 🚀 Prochaines étapes

Maintenant que vous maîtrisez Ansible, explorez :
- **Ansible Tower/AWX** : Interface web et orchestration
- **Molecule** : Tests automatisés des rôles
- **Ansible Collections** : Modules spécialisés
- **CI/CD Integration** : GitLab, Jenkins, GitHub Actions
- **Kubernetes** : Ansible Operator

### Resources recommandées
- 📖 Documentation officielle Ansible
- 🎥 Ansible YouTube channel
- 🌟 Ansible Galaxy pour les rôles communautaires
- 💬 Communauté Ansible sur Reddit/Discord

**Pro tip** : Pratiquez en créant vos propres rôles et contribuez à la communauté !