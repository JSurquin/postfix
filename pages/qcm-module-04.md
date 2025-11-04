---

# QCM - Module 4 : Configuration main.cf

## Question 1

Quel paramètre définit les domaines pour lesquels Postfix accepte les emails localement ?

A) `myorigin`  

B) `mydestination`  

C) `relay_domains`  

D) `virtual_alias_domains`

---

### ✅ Réponse : B

`mydestination` liste les domaines pour lesquels Postfix **livre localement** les emails (domaines finaux).

---

## Question 2

Pour éviter un open relay, il faut ABSOLUMENT avoir :


A) `smtpd_tls_security_level = encrypt`  

B) `reject_unauth_destination` dans les restrictions  

C) `smtpd_helo_required = yes`  

D) `disable_vrfy_command = yes`

---

### ✅ Réponse : B

`reject_unauth_destination` **rejette** tout email vers un domaine non autorisé. Sans ça = **OPEN RELAY** !

---

## Question 3

Quelle commande affiche UNIQUEMENT les paramètres modifiés ?

A) `postconf`  

B) `postconf -n`  

C) `postconf -d`  

D) `postfix check`

---

### ✅ Réponse : B

`postconf -n` affiche **uniquement** les paramètres modifiés (non par défaut).

---

## Exercice pratique - Module 4

### 🎯 Objectif
Maîtriser la configuration du main.cf

### 📋 Tâches (20 minutes)

1. **Paramètres de base** : Configurez ces paramètres essentiels :
```bash
sudo postconf -e "message_size_limit = 20971520"  # 20 MB
sudo postconf -e "smtpd_banner = \$myhostname ESMTP"
sudo postconf -e "disable_vrfy_command = yes"
```

2. **Restrictions anti-spam** : Ajoutez des restrictions basiques :
```bash
sudo postconf -e "smtpd_recipient_restrictions = permit_mynetworks, reject_unauth_destination"
```

3. **Vérification** : Vérifiez votre config :
```bash
postfix check
postconf -n | grep -E "(message_size|smtpd_banner|disable_vrfy|smtpd_recipient)"
sudo systemctl reload postfix
```

4. **Test** : Testez l'envoi d'un email et vérifiez les logs

**Bonus** : Configurez le `relayhost` pour utiliser un serveur SMTP externe

