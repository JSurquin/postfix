---
layout: new-section
routeAlias: 'qcm-ansible'
---

<a name="QCM_ANSIBLE" id="QCM_ANSIBLE"></a>

# QCM : Maîtrise d'Ansible

---

<small>

## QCM sur la maîtrise d'Ansible

<br>

### 1. Quel est le principe fondamental d'Ansible ?

<br>

- [ ]  Ansible nécessite des agents sur tous les serveurs cibles
- [ ]  Ansible fonctionne en mode "push" sans agent
- [ ]  Ansible utilise uniquement le protocole HTTP
- [ ]  Ansible remplace complètement SSH

<br>

### 2. Quels sont les composants principaux d'Ansible ?

<br>

- [ ]  Control Node, Managed Nodes, Playbooks
- [ ]  Master, Workers, Registry
- [ ]  Client, Server, Database
- [ ]  Controller, Executors, Storage

<br>

### 3. Que signifie "idempotent" dans le contexte Ansible ?

<br>

- [ ]  Les tâches s'exécutent toujours plus rapidement à la deuxième fois
- [ ]  Exécuter un playbook plusieurs fois produit le même résultat
- [ ]  Les erreurs sont automatiquement corrigées
- [ ]  Les tâches sont executées en parallèle

<br>

### 4. Dans quel format sont écrits les playbooks Ansible ?

<br>

- [ ]  JSON
- [ ]  XML
- [ ]  YAML
- [ ]  TOML

<br>

### 5. Qu'est-ce qu'un inventaire Ansible ?

<br>

- [ ]  La liste des playbooks disponibles
- [ ]  La liste des serveurs et groupes gérés par Ansible
- [ ]  L'historique des exécutions
- [ ]  Le catalogue des modules disponibles

<br>

### 6. Quel est l'ordre de priorité des variables (du plus faible au plus fort) ?

<br>

- [ ]  group_vars → host_vars → playbook vars → command line
- [ ]  command line → playbook vars → host_vars → group_vars
- [ ]  playbook vars → group_vars → host_vars → command line
- [ ]  host_vars → group_vars → playbook vars → command line

</small>

---

<small>

### 7. À quoi sert Ansible Vault ?

<br>

- [ ]  Stocker les playbooks de manière sécurisée
- [ ]  Chiffrer les données sensibles comme les mots de passe
- [ ]  Sauvegarder l'inventaire
- [ ]  Gérer les versions des playbooks

<br>

### 8. Quelle est la différence entre un module et un rôle ?

<br>

- [ ]  Un module est réutilisable, un rôle ne l'est pas
- [ ]  Un module exécute une tâche spécifique, un rôle est un ensemble de tâches organisées
- [ ]  Un rôle est plus rapide qu'un module
- [ ]  Il n'y a pas de différence

<br>

### 9. Quand les handlers sont-ils exécutés ?

<br>

- [ ]  Immédiatement après chaque tâche
- [ ]  Uniquement à la fin du playbook, et seulement si notifiés
- [ ]  Au début de chaque play
- [ ]  En cas d'erreur uniquement

<br>

### 10. Quelle commande exécute une tâche ad-hoc sur tous les serveurs web ?

<br>

- [ ]  `ansible webservers -m ping`
- [ ]  `ansible-playbook -i webservers ping.yml`
- [ ]  `ansible all -m webservers -a ping`
- [ ]  `ansible run webservers ping`

<br>

### 11. Quelle est la structure standard d'un rôle Ansible ?

<br>

- [ ]  `tasks/, handlers/, vars/, files/`
- [ ]  `src/, build/, test/, deploy/`
- [ ]  `main/, config/, scripts/, docs/`
- [ ]  `playbooks/, inventories/, modules/, plugins/`

<br>

### 12. Comment ignorer les erreurs pour une tâche spécifique ?

<br>

- [ ]  `ignore_errors: true`
- [ ]  `failed_when: false`
- [ ]  `error_handling: ignore`
- [ ]  `skip_errors: yes`

</small>

---

<small>

### 13. Comment itérer sur une liste avec Ansible moderne ?

<br>

- [ ]   `with_items:`
- [ ]   `loop:`
- [ ]   `for_each:`
- [ ]   `iterate:`

<br>

### 14. Comment exécuter une tâche seulement sur Ubuntu ?

<br>

- [ ]   `when: ansible_os_family == "Ubuntu"`
- [ ]   `when: ansible_distribution == "Ubuntu"`
- [ ]   `if: ansible_system == "Ubuntu"`
- [ ]   `only_if: ubuntu == true`

<br>

### 15. À quoi servent les tags dans Ansible ?

<br>

- [ ]  Identifier les versions des playbooks
- [ ]  Exécuter seulement certaines tâches d'un playbook
- [ ]  Catégoriser les serveurs dans l'inventaire
- [ ]  Marquer les erreurs dans les logs

<br>

### 16. Comment implémenter un déploiement blue-green avec Ansible ?

<br>

- [ ]  Utiliser des groupes d'inventaire distincts et des variables conditionnelles
- [ ]  Créer deux playbooks séparés
- [ ]  Utiliser uniquement des rôles
- [ ]  Impossible avec Ansible seul

<br>

### 17. Quelle n'est PAS une bonne pratique de sécurité avec Ansible ?

<br>

- [ ]  Utiliser Ansible Vault pour les secrets
- [ ]  Stocker les clés SSH dans les playbooks
- [ ]  Limiter les privilèges avec `become_user`
- [ ]  Utiliser des connexions SSH avec clés

<br>

### 18. Comment optimiser les performances d'exécution d'Ansible ?

<br>

- [ ]  Augmenter `forks` et utiliser `strategy: free`
- [ ]  Réduire le nombre de tâches
- [ ]  Utiliser uniquement des modules built-in
- [ ]  Exécuter en mode séquentiel

</small>

---

# Réponses

<small>

1. Ansible fonctionne en mode "push" sans agent
2. Control Node, Managed Nodes, Playbooks
3. Exécuter un playbook plusieurs fois produit le même résultat
4. YAML
5. La liste des serveurs et groupes gérés par Ansible
6. group_vars → host_vars → playbook vars → command line
7. Chiffrer les données sensibles comme les mots de passe
8. Un module exécute une tâche spécifique, un rôle est un ensemble de tâches organisées
9. Uniquement à la fin du playbook, et seulement si notifiés
10. `ansible webservers -m ping`
11. `tasks/, handlers/, vars/, files/`
12. `ignore_errors: true`
13. `loop:`
14. `when: ansible_distribution == "Ubuntu"`
15. Exécuter seulement certaines tâches d'un playbook
16. Utiliser des groupes d'inventaire distincts et des variables conditionnelles
17. Stocker les clés SSH dans les playbooks
18. Augmenter `forks` et utiliser `strategy: free`

</small>

---

# 🎯 Questions Pratiques

### Question 11 : Structure de rôle 📁

**Quelle est la structure standard d'un rôle Ansible ?**

A) `tasks/, handlers/, vars/, files/`  
B) `src/, build/, test/, deploy/`  
C) `main/, config/, scripts/, docs/`  
D) `playbooks/, inventories/, modules/, plugins/`  

---

### Question 12 : Gestion des erreurs 🚨

**Comment ignorer les erreurs pour une tâche spécifique ?**

A) `ignore_errors: true`  
B) `failed_when: false`  
C) `error_handling: ignore`  
D) `skip_errors: yes`  

---

### Question 13 : Boucles Ansible 🔁

**Comment itérer sur une liste avec Ansible moderne ?**

A) `with_items:`  
B) `loop:`  
C) `for_each:`  
D) `iterate:`  

---

### Question 14 : Tests et conditions 🧪

**Comment exécuter une tâche seulement sur Ubuntu ?**

A) `when: ansible_os_family == "Ubuntu"`  
B) `when: ansible_distribution == "Ubuntu"`  
C) `if: ansible_system == "Ubuntu"`  
D) `only_if: ubuntu == true`  

---

### Question 15 : Tags 🏷️

**À quoi servent les tags dans Ansible ?**

A) Identifier les versions des playbooks  
B) Exécuter seulement certaines tâches d'un playbook  
C) Catégoriser les serveurs dans l'inventaire  
D) Marquer les erreurs dans les logs  

---

# 🏆 Scénarios Avancés

### Question 16 : Déploiement Blue-Green 🔄

**Comment implémenter un déploiement blue-green avec Ansible ?**

A) Utiliser des groupes d'inventaire distincts et des variables conditionnelles  
B) Créer deux playbooks séparés  
C) Utiliser uniquement des rôles  
D) Impossible avec Ansible seul  

---

### Question 17 : Sécurité 🛡️

**Quelle n'est PAS une bonne pratique de sécurité avec Ansible ?**

A) Utiliser Ansible Vault pour les secrets  
B) Stocker les clés SSH dans les playbooks  
C) Limiter les privilèges avec `become_user`  
D) Utiliser des connexions SSH avec clés  

---

### Question 18 : Performance 🚀

**Comment optimiser les performances d'exécution d'Ansible ?**

A) Augmenter `forks` et utiliser `strategy: free`  
B) Réduire le nombre de tâches  
C) Utiliser uniquement des modules built-in  
D) Exécuter en mode séquentiel  

---

# 📊 Correction et Barème

### Réponses Correctes

**Questions 1-6 (Fondamentaux)**
1. **B** - Mode push sans agent
2. **A** - Control Node, Managed Nodes, Playbooks
3. **B** - Même résultat à chaque exécution
4. **C** - Format YAML
5. **B** - Liste des serveurs gérés
6. **A** - group_vars → host_vars → playbook vars → command line

**Questions 7-12 (Concepts)**
7. **B** - Chiffrement des données sensibles
8. **B** - Module = tâche spécifique, Rôle = ensemble organisé
9. **B** - Fin du playbook si notifiés
10. **A** - `ansible webservers -m ping`
11. **A** - Structure standard des rôles
12. **A** - `ignore_errors: true`

**Questions 13-18 (Avancé)**
13. **B** - `loop:` (syntaxe moderne)
14. **B** - `ansible_distribution == "Ubuntu"`
15. **B** - Exécution sélective de tâches
16. **A** - Groupes d'inventaire + variables
17. **B** - Ne jamais stocker les clés dans les playbooks
18. **A** - Optimisation avec forks et strategy

### Score d'évaluation
- **16-18 bonnes réponses** : 🏆 Expert Ansible !
- **13-15 bonnes réponses** : 🥇 Niveau avancé
- **10-12 bonnes réponses** : 🥈 Bon niveau
- **7-9 bonnes réponses** : 🥉 Niveau intermédiaire
- **< 7 bonnes réponses** : 📚 Reprenez les bases

---

# 💡 Explications Détaillées

### Points clés à retenir

**Question 3 - Idempotence** :
L'idempotence est cruciale : si l'état désiré est déjà atteint, Ansible ne fait rien. Cela permet d'exécuter le même playbook plusieurs fois sans effet de bord.

**Question 6 - Priorité des variables** :
```
1. group_vars (plus faible)
2. host_vars  
3. playbook vars
4. command line (plus forte)
```

**Question 13 - Boucles modernes** :
```yaml
# Ancienne syntaxe (dépréciée)
with_items:
  - item1
  - item2

# Nouvelle syntaxe (recommandée)
loop:
  - item1
  - item2
```

**Question 17 - Sécurité** :
```yaml
# ❌ Mauvais - clé dans le playbook
ssh_key: "-----BEGIN RSA PRIVATE KEY-----"

# ✅ Bon - référence sécurisée
ssh_key_path: "{{ vault_ssh_key_path }}"
```

### Bonnes pratiques à retenir
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