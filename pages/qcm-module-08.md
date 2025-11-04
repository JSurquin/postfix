---


# QCM - Module 8 : DKIM, SPF et DMARC

## Question 1

À quoi sert SPF ?

A) Chiffrer les emails  

B) Authentifier le serveur expéditeur  

C) Filtrer le spam  

D) Compresser les pièces jointes

---

### ✅ Réponse : B

**SPF** (Sender Policy Framework) permet de vérifier que l'IP qui envoie un email est **autorisée** à le faire pour ce domaine.

---

## Question 2

Où se trouve la signature DKIM dans un email ?

A) Dans le corps du message  

B) Dans les en-têtes (DKIM-Signature)  

C) Dans le fichier de configuration  

D) Dans les logs

---

### ✅ Réponse : B

La signature DKIM est dans les **en-têtes** de l'email (champ `DKIM-Signature:`). Elle signe cryptographiquement le message.

---

## Question 3

Que signifie `p=reject` dans un enregistrement DMARC ?

A) Rejeter tous les emails  

B) Rejeter les emails qui échouent SPF/DKIM  

C) Mettre en quarantaine  

D) Accepter mais signaler

---

### ✅ Réponse : B

`p=reject` demande aux serveurs destinataires de **REJETER** les emails qui échouent les vérifications SPF ou DKIM. C'est la politique la plus stricte.

---

## Exercice pratique - Module 8

### 🎯 Objectif
Configurer SPF, DKIM et DMARC

### 📋 Tâches (30 minutes)

1. **Créer un enregistrement SPF** :
```bash
; Dans votre zone DNS
votredomaine.local. IN TXT "v=spf1 mx a ip4:VOTRE_IP ~all"
```

2. **Installer OpenDKIM** :
```bash
sudo apt install opendkim opendkim-tools  # Ubuntu/Debian
# ou
sudo dnf install opendkim  # Rocky Linux
```

3. **Générer les clés DKIM** :
```bash
sudo mkdir -p /etc/opendkim/keys/votredomaine.local
sudo opendkim-genkey -D /etc/opendkim/keys/votredomaine.local/ -d votredomaine.local -s default
sudo chown -R opendkim:opendkim /etc/opendkim/keys/
```

4. **Afficher la clé publique** :
```bash
sudo cat /etc/opendkim/keys/votredomaine.local/default.txt
```

5. **Ajouter l'enregistrement DNS** :
```bash
default._domainkey.votredomaine.local. IN TXT "v=DKIM1; k=rsa; p=VOTRE_CLE_PUBLIQUE"
```

6. **Créer l'enregistrement DMARC** :
```bash
_dmarc.votredomaine.local. IN TXT "v=DMARC1; p=none; rua=mailto:dmarc@votredomaine.local"
```

7. **Tester** : Envoyez un email et vérifiez les en-têtes DKIM

**Bonus** : Testez avec https://www.mail-tester.com/
