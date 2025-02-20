# Part IV : User management

## Index

- [Part IV : User management](#part-iv--user-management)
  - [Index](#index)
  - [1. Users](#1-users)
    - [A. Master what already exists](#a-master-what-already-exists)
    - [B. User creation and configuration](#b-user-creation-and-configuration)
    - [C. Hackers gonna hack](#c-hackers-gonna-hack)
  - [2. Files and permissions](#2-files-and-permissions)
    - [A. Listing POSIX permissions](#a-listing-posix-permissions)
    - [B. Protect a file using permissions](#b-protect-a-file-using-permissions)
    - [C. Extended attributes](#c-extended-attributes)

## 1. Users

### A. Master what already exists

🌞 **Déterminer l'existant :**

```bash
[root@node1 ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin
systemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:998:996:User for polkitd:/:/sbin/nologin
tss:x:59:59:Account used for TPM access:/:/usr/sbin/nologin
sssd:x:997:995:User for sssd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/usr/sbin/nologin
chrony:x:996:994:chrony system user:/var/lib/chrony:/sbin/nologin
dodo:x:1000:1000:dodo:/home/dodo:/bin/bash
tcpdump:x:72:72::/:/sbin/nologin
```

```bash
[root@node1 ~]# cat /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mem:x:8:
kmem:x:9:
wheel:x:10:dodo
cdrom:x:11:
mail:x:12:
man:x:15:
dialout:x:18:
floppy:x:19:
games:x:20:
tape:x:33:
video:x:39:
ftp:x:50:
lock:x:54:
audio:x:63:
users:x:100:
nobody:x:65534:
utmp:x:22:
utempter:x:35:
input:x:999:
kvm:x:36:
render:x:998:
systemd-journal:x:190:
systemd-coredump:x:997:
dbus:x:81:
polkitd:x:996:
ssh_keys:x:101:
tss:x:59:
sssd:x:995:
sshd:x:74:
chrony:x:994:
sgx:x:993:
dodo:x:1000:
tcpdump:x:72:
```

```bash
[root@node1 ~]# groups
root
```

🌞 **Lister tous les processus qui sont actuellement en cours d'exécution, lancés par `root`**

```bash
[root@node1 ~]# ps -u root
    PID TTY          TIME CMD
      1 ?        00:00:00 systemd
      2 ?        00:00:00 kthreadd
      3 ?        00:00:00 pool_workqueue_
      4 ?        00:00:00 kworker/R-rcu_g
      5 ?        00:00:00 kworker/R-rcu_p
      6 ?        00:00:00 kworker/R-slub_
      7 ?        00:00:00 kworker/R-netns
      9 ?        00:00:00 kworker/0:0H-events_highpri
     11 ?        00:00:00 kworker/R-mm_pe
     13 ?        00:00:00 rcu_tasks_kthre
     14 ?        00:00:00 rcu_tasks_rude_
     15 ?        00:00:00 rcu_tasks_trace
     16 ?        00:00:00 ksoftirqd/0
     17 ?        00:00:00 rcu_preempt
     18 ?        00:00:00 migration/0
     20 ?        00:00:00 cpuhp/0
     21 ?        00:00:00 cpuhp/1
     22 ?        00:00:00 migration/1
     23 ?        00:00:00 ksoftirqd/1
     25 ?        00:00:00 kworker/1:0H-events_highpri
     26 ?        00:00:00 kdevtmpfs
     27 ?        00:00:00 kworker/R-inet_
     28 ?        00:00:00 kauditd
     29 ?        00:00:00 khungtaskd
     30 ?        00:00:00 oom_reaper
     31 ?        00:00:00 kworker/R-write
     32 ?        00:00:00 kcompactd0
     33 ?        00:00:00 ksmd
     34 ?        00:00:00 khugepaged
     35 ?        00:00:00 kworker/R-crypt
     36 ?        00:00:00 kworker/R-kinte
     37 ?        00:00:00 kworker/R-kbloc
     38 ?        00:00:00 kworker/R-blkcg
     39 ?        00:00:00 kworker/R-tpm_d
     40 ?        00:00:00 kworker/R-ata_s
     41 ?        00:00:00 kworker/R-md
     42 ?        00:00:00 kworker/R-md_bi
     43 ?        00:00:00 kworker/R-edac-
     44 ?        00:00:00 kworker/R-devfr
     45 ?        00:00:00 watchdogd
     47 ?        00:00:00 kworker/1:1H-kblockd
[...]
   1504 ?        00:00:00 sshd
   1505 pts/1    00:00:00 bash
   1530 ?        00:00:00 kworker/u8:0-events_unbound
   1534 ?        00:00:00 kworker/0:0-cgroup_destroy
   1545 ?        00:00:00 jbd2/dm-4-8
   1546 ?        00:00:00 kworker/R-ext4-
   1548 ?        00:00:00 sshd
   1552 ?        00:00:00 sshd
   1553 pts/2    00:00:00 bash
   1578 ?        00:00:00 kworker/u8:1-events_unbound
   1579 ?        00:00:00 kworker/0:1-events
   1580 ?        00:00:01 kworker/1:1-events
   1607 pts/2    00:00:00 ps
```

🌞 **Lister tous les processus qui sont actuellement en cours d'exécution, lancés par votre utilisateur**

```bash
[root@node1 ~]# ps
    PID TTY          TIME CMD
   1553 pts/2    00:00:00 bash
   1632 pts/2    00:00:00 ps
```

🌞 **Déterminer le hash du mot de passe de `root`**

```bash
[root@node1 ~]# cat /etc/shadow | grep "root"
root:$6$ZHeEwXRXkbRW/DEv$kXrZw4P68BTpwmMBetkrV4rgS1rlfNQZ0IVyVMKGnGcyc4hlrZWbX3s2CNkOQg1J96b5Pc2ABxZTrdefHit8z
```

🌞 **Déterminer le hash du mot de passe de votre utilisateur**

```bash
[root@node1 ~]# cat /etc/shadow | grep "dodo"
dodo:$6$FQ8bzrNqHUyk7VhQ$BIFti1tIfro8JFm05SmsD5Q8F1TZZSamTLHOvBBynUQWEHKMokx1crgaJcFvvsSUNVdIoFbnHmSwDuea9wgij
```

🌞 **Déterminer la fonction de hachage qui a été utilisée**

>On analyse le préfixe du hash. On remarque le ```$6$``` ce qui confirme l'utilisation de l'algorithme SHA-512.

🌞 **Déterminer, pour l'utilisateur `root`** :

```bash
[root@node1 ~]# cat /etc/passwd | grep "^root"
root:x:0:0:root:/root:/bin/bash
```

>shell = /bin/bash

>homedir = /root

🌞 **Déterminer, pour votre utilisateur** :

```bash
[root@node1 ~]# cat /etc/passwd | grep "^dodo"
dodo:x:1000:1000:dodo:/home/dodo:/bin/bash
```

>shell = /bin/bash

>homedir = /home/dodo

🌞 **Afficher la ligne de configuration du fichier `sudoers` qui permet à votre utilisateur d'utiliser `sudo`**

```bash
[root@node1 ~]# sudo grep "^%wheel" /etc/sudoers
%wheel  ALL=(ALL)       ALL
```

### B. User creation and configuration

🌞 **Créer un utilisateur :**

```bash
[dodo@tp2 ~]$ sudo groupadd admins

[dodo@tp2 ~]$ sudo useradd meow -M -G admins -s /sbin/nologin
```

🌞 **Configuration `sudoers`**

**à terminer !!**

```bash
meow ALL=(dodo) NOPASSWD: /bin/ls, /bin/cat, /bin/less, /bin/more
```

```bash
```

```bash
```

- ajouter une configuration `sudoers` pour que l'utilisateur `meow` puisse exécuter seulement et uniquement les commandes `ls`, `cat`, `less` et `more` en tant que votre utilisateur
- ajouter une configuration `sudoers` pour que les membres du groupe `admins` puisse exécuter seulement et uniquement la commande `apt` en tant que `root`
- ajouter une configuration `sudoers` pour que votre utilisateur puisse exécuter n'importe quel commande en tant `root`, sans avoir besoin de saisir un mot de passe
- prouvez que ces 3 configurations ont pris effet (vous devez vous authentifier avec le bon utilisateur, et faire une commande `sudo` qui doit fonctioner correctement)

> Pour chaque point précédent, c'est une seule ligne de configuration à ajouter dans le fichier `sudoers` de la machine.

### C. Hackers gonna hack

🌞 **Déjà une configuration faible ?**

**à terminer !!**


- l'utilisateur `meow` est en réalité complètement `root` sur la machine hein là. Prouvez-le.
- proposez une configuration similaire, sans présenter cette faiblesse de configuration
  - vous pouvez ajouter de la configuration
  - ou supprimer de la configuration
  - du moment qu'on garde des fonctionnalités à peu près équivalentes !

## 2. Files and permissions

**Dans un OS, en particulier Linux, on dit souvent que "tout est fichier".**

### A. Listing POSIX permissions

🌞 **Déterminer les permissions des fichiers/dossiers...**

```bash
[dodo@tp2 ~]$ ls -l /etc/passwd
-rw-r--r--. 1 root root 1124 Feb 20 17:17 /etc/passwd

[dodo@tp2 ~]$ ls -l /etc/shadow
----------. 1 root root 917 Feb 20 15:59 /etc/shadow

[dodo@tp2 ~]$ ls -l /etc/ssh/sshd_config
-rw-------. 1 root root 3667 Nov  5 04:38 /etc/ssh/sshd_config

[dodo@tp2 ~]$ sudo ls -l /root/
total 4
-rw-------. 1 root root 1524 Feb 18 14:38 anaconda-ks.cfg

[dodo@tp2 home]$ ls -l
total 20
drwx------. 6 dodo dodo  4096 Feb 20 17:34 dodo

[dodo@tp2 home]$ ls -l /bin/ls
-rwxr-xr-x. 1 root root 136656 Nov  6 17:29 /bin/ls

[dodo@tp2 home]$ ls -l /bin/systemctl 
-rwxr-xr-x. 1 root root 334264 Nov 16 02:22 /bin/systemctl
```

### B. Protect a file using permissions

🌞 **Restreindre l'accès à un fichier personnel**

```bash
[dodo@tp2 tmp]$ pwd
/tmp

[dodo@tp2 tmp]$ ls -l
total 8
-rw-------. 1 dodo dodo 14 Feb 20 17:40 dont_readme.txt

[dodo@tp2 tmp]$ cat dont_readme.txt 
noo u read me

[dodo@tp2 tmp]$ echo "toto" >> dont_readme.txt 

[dodo@tp2 tmp]$ sudo -u meow cat dont_readme.txt 
cat: dont_readme.txt: Permission denied

[root@tp2 tmp]# cat dont_readme.txt 
noo u read me
toto
```

### C. Extended attributes

🌞 **Lister tous les programmes qui ont le bit SUID activé**

```bash
[dodo@tp2 ~]$ find / -type f -perm /4000 -exec ls -l {} \; 2>/dev/null
-rwsr-xr-x. 1 root root 70336 Dec 17 22:48 /usr/bin/newgrp
-rwsr-xr-x. 1 root root 69208 Nov  7 01:24 /usr/bin/su
-rwsr-xr-x. 1 root root 73768 Dec 17 22:48 /usr/bin/gpasswd
-rwsr-xr-x. 1 root root 69336 May 15  2022 /usr/bin/passwd
-rwsr-xr-x. 1 root root 68880 Nov  4 19:14 /usr/bin/pkexec
-rwsr-xr-x. 1 root root 68920 Nov  7 01:24 /usr/bin/mount
-rwsr-xr-x. 1 root root 68848 Nov  7 01:24 /usr/bin/umount
-rwsr-xr-x. 1 root root 73512 Dec 17 22:48 /usr/bin/chage
---s--x--x. 1 root root 205584 Feb 14  2024 /usr/bin/sudo
-rwsr-xr-x. 1 root root 69296 Dec 17 22:55 /usr/bin/crontab
-rwsr-xr-x. 1 root root 68832 Nov 26 00:29 /usr/sbin/unix_chkpwd
-rwsr-xr-x. 1 root root 68704 Feb  4 19:47 /usr/sbin/grub2-set-bootflag
-rwsr-xr-x. 1 root root 68688 Nov 26 00:29 /usr/sbin/pam_timestamp_check
-rwsr-xr-x. 1 root root 68824 Nov  4 19:14 /usr/lib/polkit-1/polkit-agent-helper-1
```

🌞 **Rendre le fichier `dont_readme.txt` immuable**

```bash
[dodo@tp2 ~]$ sudo chattr +i /tmp/dont_readme.txt

[dodo@tp2 ~]$ lsattr /tmp/dont_readme.txt
----i---------e------- /tmp/dont_readme.txt
```

```bash
[dodo@tp2 ~]$ echo "toto" > /tmp/dont_readme.txt
-bash: /tmp/dont_readme.txt: Operation not permitted
[dodo@tp2 ~]$ sudo !!
sudo echo "toto" > /tmp/dont_readme.txt
-bash: /tmp/dont_readme.txt: Operation not permitted
```

- ça se fait avec les attributs étendus
- "immuable" ça veut dire qu'il ne peut plus être modifié DU TOUT : il est donc en read-only
- prouvez que le fichier ne peut plus être modifié par **personne**

[Part 5](part5.md)