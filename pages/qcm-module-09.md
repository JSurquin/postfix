---
layout: question
---

# QCM - Module 9 : TLS et sécurité

## Question 1

Quelle est la différence entre SMTPS et STARTTLS ?

A) SMTPS utilise TLS, STARTTLS utilise SSL  
B) SMTPS chiffre dès la connexion (port 465), STARTTLS upgrade une connexion (port 25/587)  
C) Il n'y a pas de différence  
D) STARTTLS est plus sécurisé

---

### ✅ Réponse : B

**SMTPS** (port 465) : Chiffrement dès la connexion. **STARTTLS** (port 25/587) : Connexion en clair puis upgrade TLS avec commande `STARTTLS`.

---

## Question 2

Quel niveau TLS est recommandé pour le port 587 (submission) ?

A) `may` (optionnel)  
B) `encrypt` (obligatoire)  
C) `none` (désactivé)  
D) `dane` (DANE uniquement)

---

### ✅ Réponse : B

Pour le port 587 (submission), utilisez `smtpd_tls_security_level = encrypt` pour **forcer** TLS. Les clients doivent s'authentifier en chiffré !

---

## Question 3

Où obtenir des certificats SSL gratuits et valides ?

A) OpenSSL self-signed  
B) Let's Encrypt  
C) Acheter chez un CA commercial  
D) Copier depuis un autre serveur

---

### ✅ Réponse : B

**Let's Encrypt** fournit des certificats SSL **gratuits, automatiques et reconnus** par tous les navigateurs/clients mail.

---

## Exercice pratique - Module 9

### 🎯 Objectif
Configurer TLS sur Postfix

### 📋 Tâches (25 minutes)

1. **Installer Certbot** :
```bash
sudo apt install certbot  # Ubuntu/Debian
# ou
sudo dnf install certbot  # Rocky Linux
```

2. **Obtenir un certificat** (si domaine public) :
```bash
sudo certbot certonly --standalone -d mail.votredomaine.com
```

3. **Configurer TLS dans Postfix** :
```bash
sudo postconf -e "smtpd_tls_cert_file = /etc/letsencrypt/live/mail.votredomaine.com/fullchain.pem"
sudo postconf -e "smtpd_tls_key_file = /etc/letsencrypt/live/mail.votredomaine.com/privkey.pem"
sudo postconf -e "smtpd_tls_security_level = may"
sudo postconf -e "smtpd_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1"
sudo postconf -e "smtp_tls_security_level = may"
sudo postconf -e "smtp_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1"
```

4. **Configurer le port 587** :
```bash
# Dans /etc/postfix/master.cf, décommenter/ajouter :
submission inet n       -       y       -       -       smtpd
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
```

5. **Recharger et tester** :
```bash
sudo systemctl reload postfix
openssl s_client -connect localhost:587 -starttls smtp
```

**Bonus** : Configurez le renouvellement automatique avec certbot

