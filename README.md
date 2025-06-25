# 🔍 Projet : Surveillance des Logs Système avec Alertes par Mail

## 📘 Université de Kinshasa  
**Faculté des Sciences et Technologies**  
**Département de Mathématiques, Statistique et Informatique**  
**Année académique :** 2024–2025  
**Encadrant :** Prof. KASENGEDIA MOTUMBE Pierre  
**Groupe 23**

---

## 👥 Membres du groupe

- Mimbayi Lieko Prince  
- Matiabo Bomay Emmanuel  
- Kibeye Danisi Joel  
- Mukoko Mandina Harold  
- Inyobo Mobemba Romeo  
- Landu Kunzika David  
- Mobaku Makala Heritier  
- Jules Omesudu Babe  
- Makila Makita Merseigne  
- Omehambe Ososmbo Israel  

---

## 🎯 Objectif du projet

Ce projet vise à automatiser la **surveillance des fichiers de logs système** (notamment `/var/log/syslog`) à l’aide d’un **script Bash**.  
Lorsqu’un mot-clé critique est détecté (`error`, `unauthorized`, etc.), un **mail d’alerte** est automatiquement envoyé à l’administrateur système.

---

## 🧩 Technologies utilisées

| Outil / Service         | Rôle                                              |
|-------------------------|---------------------------------------------------|
| Bash                    | Écriture du script de surveillance                |
| Postfix                 | Serveur SMTP local                                |
| Mailutils               | Envoi de mails depuis la ligne de commande        |
| Gmail (SMTP)            | Relais d’envoi de mails                           |

---

## 🛠️ Étapes d'installation

### 1. Installer les paquets nécessaires

```bash
sudo apt update
sudo apt install postfix mailutils libsasl2-modules
```

### 2. Configurer Postfix

Lors de l'installation, choisir : **Internet Site**  
Nom du système de mail : `localhost`

Modifier `/etc/postfix/main.cf` :

```
relayhost = [smtp.gmail.com]:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```

### 3. Ajouter vos identifiants Gmail

Créer `/etc/postfix/sasl_passwd` :

```
[smtp.gmail.com]:587 groupe23se@gmail.com:mot_de_passe_application
```

Puis exécuter :

```bash
sudo postmap /etc/postfix/sasl_passwd
sudo chmod 600 /etc/postfix/sasl_passwd
sudo systemctl restart postfix
```

---

## 📄 Script Bash : `scriptlog.sh`

Voici un aperçu du script :

```bash
#!/bin/bash

LOG_FILE="/var/log/syslog"
EMAIL_ADMIN="groupe23se@gmail.com"
KEYWORDS="error|fail|unauthorized|denied|invalid|disconnected|critical"
ALERT_LOG="./alertes_detectees.log"

function traiter_alerte {
    local log_line="$1"
    local date_alerte=$(date '+%Y-%m-%d %H:%M:%S')
    local message="[$date_alerte] Alerte : $log_line"

    echo "$message" >> "$ALERT_LOG"
    echo "$message" | mail -s "Alerte Système Détectée" "$EMAIL_ADMIN"
}

if [ -f "$LOG_FILE" ]; then
    tail -Fn0 "$LOG_FILE" | while read line; do
        echo "$line" | egrep -i "$KEYWORDS" &> /dev/null
        if [ $? = 0 ]; then
            traiter_alerte "$line"
        fi
    done
else
    echo "Fichier $LOG_FILE introuvable"
    exit 1
fi
```

Rendez le script exécutable :

```bash
chmod +x scriptlog.sh
```

---

## 🚀 Lancement du script

```bash
./scriptlog.sh
```

> Le script surveille les logs système et envoie un mail dès qu'une alerte est détectée.

---

## 📝 Fichiers inclus dans le dépôt

- `rapportSE.pdf` → Rapport complet du projet  
- `scriptlog.sh` → Script de surveillance Bash  
- `README.md` → Explication du projet  
- `slides.pptx` → Présentation PowerPoint 

---

## 📚 Sources & Bibliographie

- Cooper, M. (2011). *Advanced Bash-Scripting Guide*, TLDP  
- Cours de Systèmes d’Exploitation – Prof. Kasengedia  
- https://tldp.org/LDP/abs/html/  
- https://youtu.be/UuEx_JwNI2s  

---

## 📌 Remarques

✅ Projet pratique pour comprendre la gestion des logs, la sécurité système, et l’automatisation  
💡 Possibilités d’amélioration : interface graphique, notifications Telegram, version Python, etc.

---
