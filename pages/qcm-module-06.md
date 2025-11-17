---

# QCM - Module 6 : Alias et tables virtuelles

## Question 1

Après avoir modifié `/etc/aliases`, quelle commande faut-il exécuter ?

A) `postfix reload`  

B) `newaliases`  

C) `systemctl restart postfix`  

D) Aucune, c'est automatique

---

## Question 2

Quelle est la différence entre alias et virtual ?

A) Il n'y a pas de différence  

B) Alias = domaines locaux, Virtual = domaines non locaux  

C) Virtual = plus récent que Alias  

D) Alias = plus sécurisé

---

## Question 3

Comment créer un catch-all pour un domaine virtuel ?

A) `*@example.com admin@example.com`  

B) `@example.com admin@example.com`  

C) `catchall@example.com admin@example.com`  

D) `example.com admin@example.com`

---

## Question 4

Quel type de table est le plus couramment utilisé pour `virtual_alias_maps` sur une installation Postfix standard ?

A) `btree:/...`  

B) `hash:/...`  

C) `texthash:/...`  

D) `ldap:/...`

---

## Question 5

Quelle commande permet de tester la résolution d'un alias virtuel spécifique ?

A) `postconf virtual_alias_maps`  

B) `postalias -q`  

C) `postmap -q user@example.com /etc/postfix/virtual`  

D) `postqueue -q user@example.com`

---

## Réponses - Module 6

<small>

**Question 1 : Réponse B** - `newaliases` (ou `postalias /etc/aliases`) compile le fichier en base de données binaire `.db` que Postfix utilise.

**Question 2 : Réponse B** - **Alias** fonctionne pour les domaines dans `mydestination` (locaux). **Virtual** pour les domaines NON locaux (dans `virtual_alias_domains`).

**Question 3 : Réponse B** - `@example.com admin@example.com` capture TOUS les emails pour example.com. Attention à le placer EN DERNIER dans le fichier !

**Question 4 : Réponse B** - Le backend **hash** (fichiers `.db` générés par `postmap`) est disponible partout et performant pour quelques centaines d'entrées.

**Question 5 : Réponse C** - `postmap -q adresse table` interroge la table exactement comme Postfix le ferait, pratique pour valider une entrée sans envoyer d'email.

</small>

---

## Exercice pratique - Module 6

### 🎯 Objectif
Configurer des alias et domaines virtuels

### 📋 Tâches (20 minutes)

1. **Créer des alias locaux** :

```bash
sudo nano /etc/aliases
# Ajouter :
# postmaster: $USER
# webmaster: $USER
# abuse: $USER

sudo newaliases
```

---

2. **Tester les alias** :

```bash
echo "Test alias" | mail -s "Test" postmaster
ls ~/Maildir/new/
```

---

3. **Configurer un domaine virtuel** :

```bash
# Dans main.cf
sudo postconf -e "virtual_alias_domains = test.local"
sudo postconf -e "virtual_alias_maps = hash:/etc/postfix/virtual"

# Créer /etc/postfix/virtual
echo "admin@test.local  $USER@$(hostname)" | sudo tee -a /etc/postfix/virtual
sudo postmap /etc/postfix/virtual
sudo systemctl reload postfix
```

---

4. **Tester** :

```bash
echo "Test virtual" | mail -s "Test" admin@test.local
```

---

**Bonus** : Créez un catch-all pour test.local
