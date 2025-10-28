---
layout: question
---

# QCM - Module 5 : Gestion des files d'attente

## Question 1

Quelle commande affiche la file d'attente Postfix ?

A) `postfix queue`  
B) `mailq` ou `postqueue -p`  
C) `qmgr -l`  
D) `postcat`

---

### ✅ Réponse : B

`mailq` (ou `postqueue -p`) affiche tous les messages en attente avec leur Queue ID, expéditeur, destinataire et raison du délai.

---

## Question 2

Combien de temps un message reste-t-il dans la queue deferred par défaut ?

A) 1 heure  
B) 24 heures  
C) 5 jours  
D) Indéfiniment

---

### ✅ Réponse : C

Par défaut, `maximal_queue_lifetime = 5d` (5 jours). Après ça, un bounce est envoyé et le message supprimé.

---

## Question 3

Quelle commande permet de supprimer UN message spécifique de la queue ?

A) `postqueue -d QUEUE_ID`  
B) `postsuper -d QUEUE_ID`  
C) `mailq --delete QUEUE_ID`  
D) `postfix delete QUEUE_ID`

---

### ✅ Réponse : B

`postsuper -d QUEUE_ID` supprime un message spécifique. `postsuper -d ALL` supprime TOUT (dangereux !).

---

## Exercice pratique - Module 5

### 🎯 Objectif
Manipuler les files d'attente Postfix

### 📋 Tâches (15 minutes)

1. **Créer des messages en queue** : Envoyez vers un domaine inexistant :
```bash
echo "Test queue" | mail -s "Test" fake@domaine-inexistant-123.com
```

2. **Observer la queue** :
```bash
mailq
postqueue -p
```

3. **Analyser un message** :
```bash
# Récupérer le Queue ID depuis mailq
sudo postcat -q VOTRE_QUEUE_ID
```

4. **Forcer un réessai** :
```bash
postqueue -f
```

5. **Nettoyer** :
```bash
sudo postsuper -d ALL deferred
mailq  # Vérifier que la queue est vide
```

**Bonus** : Utilisez `qshape` pour analyser les statistiques de la queue

