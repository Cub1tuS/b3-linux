# Part V : OpenSSH Server

## Index

- [Part V : OpenSSH Server](#part-v--openssh-server)
  - [Index](#index)
  - [1. Basics](#1-basics)
  - [2. Authentication modes](#2-authentication-modes)
    - [A. Key-based authentication](#a-key-based-authentication)
  - [3. Bonus : Cert-based authentication](#3-bonus--cert-based-authentication)
  - [4. Further hardening](#4-further-hardening)
  - [5. fail2ban](#5-fail2ban)
  - [6. Automatisation](#6-automatisation)

## 1. Basics

🌞 **Afficher l'identifiant du processus serveur OpenSSH en cours d'exécution**

```bash
[dodo@tp2 ~]$ sudo systemctl status sshd
● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset: enabled)
     Active: active (running) since Fri 2025-02-21 15:14:01 CET; 9min ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 886 (sshd)
      Tasks: 1 (limit: 3818)
     Memory: 1.6M
        CPU: 117ms
     CGroup: /system.slice/sshd.service
             └─886 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"
[...]
```

> 886

🌞 **Changer le port d'écoute du serveur OpenSSH**

```bash
[dodo@tp2 ~]$ sudo nano /etc/ssh/sshd_config
[...]
Port 6375
[...]
```

```bash
[dodo@tp2 ~]$ sudo systemctl restart sshd
```

```bash
[dodo@tp2 ~]$ sudo systemctl status sshd
● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset: enabled)
     Active: active (running) since Fri 2025-02-21 15:29:03 CET; 20s ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 1929 (sshd)
      Tasks: 1 (limit: 3818)
     Memory: 1.7M
        CPU: 11ms
     CGroup: /system.slice/sshd.service
             └─1929 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

Feb 21 15:29:03 tp2 systemd[1]: Starting OpenSSH server daemon...
Feb 21 15:29:03 tp2 sshd[1929]: Server listening on 0.0.0.0 port 6375.
Feb 21 15:29:03 tp2 sshd[1929]: Server listening on :: port 6375.
Feb 21 15:29:03 tp2 systemd[1]: Started OpenSSH server daemon.
```

```bash
[dodo@tp2 ~]$ sudo firewall-cmd --permanent --add-port=6375/tcp
[dodo@tp2 ~]$ sudo firewall-cmd --reload
```

```bash
dorian@Air-de-Dorian ~ % ssh dodo@172.16.161.129 -p 6375
```

> Il est important de changer de port par défaut, cela permet d'emêcher les attaques automatiques par bot et de "camoufler" dans une certaine mesure, le service.

## 2. Authentication modes

### A. Key-based authentication

> Un classique ! Vous **devez** être à l'aise avec ça. Jamais trop tard pour s'y mettre.

🌞 **Configurer une authentification par clé**

```bash
dorian@Air-de-Dorian ~ % ssh-copy-id tp2                
```

```bash
dorian@Air-de-Dorian ~ % ssh dodo@172.16.161.129 -p 6375
Last login: Fri Feb 21 15:43:39 2025 from 172.16.161.1
[dodo@tp2 ~]$ 
```

🌞 **Désactiver la connexion par password**

```bash
PasswordAuthentication no
```

🌞 **Désactiver la connexion en tant que `root`**

```bash
PermitRootLogin no
```

## 3. Bonus : Cert-based authentication

**à refaire**

⭐ **BONUS** : **Configurer une authentification par certificat**

```bash
[dodo@tp2 ~]$ ssh-keygen -t rsa -b 4096 -f ssh_ca

[dodo@tp2 ~]$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa

[dodo@tp2 ~]$ ssh-keygen -s ssh_ca -I "dodo" -n dodo -V +52w ~/.ssh/id_rsa.pub

[dodo@tp2 ~]$ sudo nano /etc/ssh/sshd_config
[...]
TrustedUserCAKeys /etc/ssh/ssh_ca.pub
[...]

[dodo@tp2 ~]$ sudo mv ssh_ca.pub /etc/ssh/

[dodo@tp2 ~]$ sudo systemctl restart sshd
```


- j'ai dit par certificat, pas par simple clé
- pareil, faites-le avec votre utilisateur pour les tests

> L'authentification par certificat est toujours plus forte que l'authentification par simple clé : les deux parties (typiquement, le client et le serveur) doivent prouver l'identité à l'autre. De plus, le certificat ne peut pas être falsifié, du moins si on utilise une autorité de certification digne de confiance. L'idée du certificat : on va signer la clé du client avec la clé d'une autorité de certification. Ainsi, la clé n'est plus falsifiable, l'autorité de certification peut attester que c'est la bonne clé pour le bon client.

## 4. Further hardening

🌞 **Proposer au moins 5 configurations supplémentaires qui permettent de renforcer la sécurité du serveur OpenSSH**

```
Port 6375 #Port au choix

PermitRootLogin no

AllowUsers utilisateur1 utilisateur2

PasswordAuthentication no

MaxAuthTries 3
```

## 5. fail2ban

🌞 **Installer fail2ban sur la machine**

```bash
[dodo@tp2 ~]$ sudo dnf install fail2ban
```

🌞 **Configurer fail2ban**

```bash
[dodo@tp2 jail.d]$ sudo nano jail.local

[dodo@tp2 jail.d]$ cat jail.local 
[sshd]
enabled = true
port = 6375
filter = sshd
logpath = /var/log/auth.log
maxretry = 7
findtime = 300
```

```bash
[dodo@tp2 jail.d]$ sudo systemctl restart fail2ban
```

🌞 **Prouvez que fail2ban est effectif**

```bash
[dodo@tp2 jail.d]$ 
logout
Connection to 172.16.161.129 closed.
dorian@Air-de-Dorian ~ % ssh -p 6375 tp2
dodo@172.16.161.129's password: 
Permission denied, please try again.
dodo@172.16.161.129's password: 
Permission denied, please try again.
dodo@172.16.161.129's password: 
dodo@172.16.161.129: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
dorian@Air-de-Dorian ~ % ssh -p 6375 tp2
dodo@172.16.161.129's password: 
Permission denied, please try again.
dodo@172.16.161.129's password: 
Permission denied, please try again.
dodo@172.16.161.129's password: 
dodo@172.16.161.129: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
dorian@Air-de-Dorian ~ % ssh -p 6375 tp2
dodo@172.16.161.129's password: 
Permission denied, please try again.
dodo@172.16.161.129's password: 
Permission denied, please try again.
dodo@172.16.161.129's password: 
dodo@172.16.161.129: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
dorian@Air-de-Dorian ~ % ssh -p 6375 tp2
ssh: connect to host 172.16.161.129 port 6375: Connection refused
```


```bash
[dodo@tp2 ~]$ sudo fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed:	0
|  |- Total failed:	9
|  `- Journal matches:	_SYSTEMD_UNIT=sshd.service + _COMM=sshd + _COMM=sshd-session
`- Actions
   |- Currently banned:	1
   |- Total banned:	2
   `- Banned IP list:	172.16.161.1

[dodo@tp2 ~]$ fail2ban-client set sshd unbanip 172.16.161.1
1
```

## 6. Automatisation

🌞 **Ecrire le script `harden.sh`**

```bash
#!/bin/bash

if systemctl is-active --quiet sshd; then
    echo "OpenSSH est actif."
else
    echo "OpenSSH n'est pas actif. Démarrage..."
    sudo systemctl start sshd
fi

SSH_PORT=$(sudo grep -E "^Port" /etc/ssh/sshd_config | awk '{print $2}')
if [[ -z "$SSH_PORT" || "$SSH_PORT" == "22" ]]; then
    echo "OpenSSH écoute sur le port 22. Modification en cours..."
    NEW_PORT=6375
    sudo sed -i "s/^#*Port .*/Port $NEW_PORT/" /etc/ssh/sshd_config
    echo "Port changé en $NEW_PORT. Redémarrage de SSH..."
    sudo systemctl restart sshd
else
    echo " OpenSSH écoute sur le port $SSH_PORT."
fi

sudo sed -i "s/^#*PermitRootLogin .*/PermitRootLogin no/" /etc/ssh/sshd_config
sudo sed -i "s/^#*PasswordAuthentication .*/PasswordAuthentication no/" /etc/ssh/sshd_config
sudo sed -i "s/^#*PasswordAuthentication .*/PasswordAuthentication no/" /etc/ssh/sshd_config
sudo systemctl restart sshd

if ! command -v fail2ban-client &> /dev/null; then
    echo "Fail2ban n'est pas installé. Installation en cours..."
    sudo dnf update -y && sudo dnf install fail2ban -y
else
    echo "Fail2ban est installé."
fi

if systemctl is-active --quiet fail2ban; then
    echo "Fail2ban est actif."
else
    echo "Fail2ban n'est pas actif. Démarrage..."
    sudo systemctl start fail2ban
    sudo systemctl enable fail2ban
fi

if sudo fail2ban-client status sshd &> /dev/null; then
    echo "Fail2ban surveille OpenSSH."
else
    echo "Fail2ban ne surveille pas OpenSSH. Activation en cours..."
    echo -e "[sshd]\nenabled = true\nport = ssh\nlogpath = /var/log/auth.log\nmaxretry = 7\nfindtime = 300\nbantime = 3600" | sudo tee /etc/fail2ban/jail.local
    sudo systemctl restart fail2ban
fi

echo "Sécurisation du serveur terminée."
```

[Part 6](part6.md)