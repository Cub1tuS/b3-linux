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
     48 ?        00:00:00 kswapd0
     53 ?        00:00:00 kworker/R-kthro
     56 ?        00:00:01 kworker/1:3-events_power_efficient
     59 ?        00:00:00 irq/12-pciehp
     60 ?        00:00:00 irq/13-pciehp
     61 ?        00:00:00 irq/14-pciehp
     62 ?        00:00:00 irq/15-pciehp
     63 ?        00:00:00 irq/16-pciehp
     64 ?        00:00:00 irq/17-pciehp
     65 ?        00:00:00 irq/18-pciehp
     66 ?        00:00:00 irq/19-pciehp
     67 ?        00:00:00 irq/20-pciehp
     68 ?        00:00:00 irq/21-pciehp
     69 ?        00:00:00 irq/22-pciehp
     70 ?        00:00:00 irq/23-pciehp
     71 ?        00:00:00 irq/24-pciehp
     72 ?        00:00:00 irq/25-pciehp
     73 ?        00:00:00 irq/26-pciehp
     74 ?        00:00:00 irq/27-pciehp
     75 ?        00:00:00 irq/28-pciehp
     76 ?        00:00:00 irq/29-pciehp
     77 ?        00:00:00 irq/30-pciehp
     78 ?        00:00:00 irq/31-pciehp
     79 ?        00:00:00 irq/32-pciehp
     80 ?        00:00:00 irq/33-pciehp
     81 ?        00:00:00 irq/34-pciehp
     82 ?        00:00:00 irq/35-pciehp
     83 ?        00:00:00 irq/36-pciehp
     84 ?        00:00:00 irq/37-pciehp
     85 ?        00:00:00 irq/38-pciehp
     86 ?        00:00:00 irq/39-pciehp
     87 ?        00:00:00 irq/40-pciehp
     88 ?        00:00:00 irq/41-pciehp
     89 ?        00:00:00 irq/42-pciehp
     90 ?        00:00:00 irq/43-pciehp
     91 ?        00:00:00 kworker/R-acpi_
     93 ?        00:00:00 kworker/R-kmpat
     94 ?        00:00:00 kworker/R-kalua
     95 ?        00:00:00 scsi_eh_0
     96 ?        00:00:00 kworker/R-scsi_
     97 ?        00:00:00 scsi_eh_1
     98 ?        00:00:00 kworker/R-scsi_
     99 ?        00:00:00 scsi_eh_2
    100 ?        00:00:00 kworker/R-scsi_
    101 ?        00:00:00 scsi_eh_3
    102 ?        00:00:00 kworker/R-scsi_
    103 ?        00:00:00 scsi_eh_4
    104 ?        00:00:00 kworker/R-scsi_
    105 ?        00:00:00 scsi_eh_5
    106 ?        00:00:00 kworker/R-scsi_
    107 ?        00:00:00 scsi_eh_6
    108 ?        00:00:00 kworker/R-scsi_
    109 ?        00:00:00 scsi_eh_7
    110 ?        00:00:00 kworker/R-scsi_
    111 ?        00:00:00 scsi_eh_8
    112 ?        00:00:00 kworker/R-scsi_
    113 ?        00:00:00 scsi_eh_9
    114 ?        00:00:00 kworker/R-scsi_
    115 ?        00:00:00 scsi_eh_10
    116 ?        00:00:00 kworker/R-scsi_
    117 ?        00:00:00 scsi_eh_11
    118 ?        00:00:00 kworker/R-scsi_
    119 ?        00:00:00 scsi_eh_12
    120 ?        00:00:00 kworker/R-scsi_
    121 ?        00:00:00 scsi_eh_13
    122 ?        00:00:00 kworker/R-scsi_
    123 ?        00:00:00 scsi_eh_14
    124 ?        00:00:00 kworker/R-scsi_
    125 ?        00:00:00 scsi_eh_15
    126 ?        00:00:00 kworker/R-scsi_
    127 ?        00:00:00 scsi_eh_16
    128 ?        00:00:00 kworker/R-scsi_
    129 ?        00:00:00 scsi_eh_17
    130 ?        00:00:00 kworker/R-scsi_
    131 ?        00:00:00 scsi_eh_18
    132 ?        00:00:00 kworker/R-scsi_
    133 ?        00:00:00 scsi_eh_19
    134 ?        00:00:00 kworker/R-scsi_
    135 ?        00:00:00 scsi_eh_20
    136 ?        00:00:00 kworker/R-scsi_
    137 ?        00:00:00 scsi_eh_21
    138 ?        00:00:00 kworker/R-scsi_
    139 ?        00:00:00 scsi_eh_22
    140 ?        00:00:00 kworker/R-scsi_
    141 ?        00:00:00 scsi_eh_23
    142 ?        00:00:00 kworker/R-scsi_
    143 ?        00:00:00 scsi_eh_24
    144 ?        00:00:00 kworker/R-scsi_
    145 ?        00:00:00 scsi_eh_25
    146 ?        00:00:00 kworker/R-scsi_
    147 ?        00:00:00 scsi_eh_26
    148 ?        00:00:00 kworker/R-scsi_
    149 ?        00:00:00 scsi_eh_27
    150 ?        00:00:00 kworker/R-scsi_
    151 ?        00:00:00 scsi_eh_28
    152 ?        00:00:00 kworker/R-scsi_
    153 ?        00:00:00 scsi_eh_29
    154 ?        00:00:00 kworker/R-scsi_
    182 ?        00:00:00 kworker/u8:30-events_unbound
    186 ?        00:00:00 kworker/R-mld
    187 ?        00:00:00 kworker/R-ipv6_
    196 ?        00:00:00 kworker/R-kstrp
    275 ?        00:00:00 kworker/u9:0
    276 ?        00:00:00 kworker/0:1H-kblockd
    464 ?        00:00:00 kworker/R-nvme-
    465 ?        00:00:00 kworker/R-nvme-
    466 ?        00:00:00 kworker/R-nvme-
    467 ?        00:00:00 kworker/R-nvme-
    582 ?        00:00:00 kworker/R-kdmfl
    591 ?        00:00:00 kworker/R-kdmfl
    610 ?        00:00:00 jbd2/dm-0-8
    611 ?        00:00:00 kworker/R-ext4-
    675 ?        00:00:00 systemd-journal
    687 ?        00:00:00 systemd-udevd
    718 ?        00:00:00 irq/62-vmw_vmci
    719 ?        00:00:00 irq/63-vmw_vmci
    720 ?        00:00:00 irq/64-vmw_vmci
    721 ?        00:00:00 irq/65-vmwgfx
    722 ?        00:00:00 irq/66-vmwgfx
    723 ?        00:00:00 irq/67-vmwgfx
    724 ?        00:00:00 irq/68-vmwgfx
    725 ?        00:00:00 irq/69-vmwgfx
    726 ?        00:00:00 irq/70-vmwgfx
    727 ?        00:00:00 kworker/R-ttm
    760 ?        00:00:00 kworker/R-kdmfl
    761 ?        00:00:00 kworker/R-kdmfl
    762 ?        00:00:00 kworker/R-kdmfl
    782 ?        00:00:00 jbd2/nvme0n1p2-
    783 ?        00:00:00 kworker/R-ext4-
    784 ?        00:00:00 jbd2/dm-3-8
    785 ?        00:00:00 kworker/R-ext4-
    787 ?        00:00:00 jbd2/dm-2-8
    788 ?        00:00:00 kworker/R-ext4-
    797 ?        00:00:00 auditd
    827 ?        00:00:00 firewalld
    828 ?        00:00:00 irqbalance
    829 ?        00:00:00 systemd-logind
    841 ?        00:00:00 NetworkManager
    912 ?        00:00:00 crond
    913 ?        00:00:00 login
    964 ?        00:00:00 rsyslogd
   1424 ?        00:00:00 systemd
   1426 ?        00:00:00 (sd-pam)
   1433 tty1     00:00:00 bash
   1468 ?        00:00:00 sshd
   1469 ?        00:00:00 sshd
   1473 ?        00:00:00 sshd
   1474 pts/0    00:00:00 bash
   1499 ?        00:00:00 kworker/1:0-events
   1500 ?        00:00:00 sshd
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

- doit s'appeler `meow`
- ne doit appartenir QUE à un groupe nommé `admins`
- ne doit pas avoir de répertoire personnel utilisable
- ne doit pas avoir un shell utilisable

> Il s'agit donc ici d'un utilisateur avec lequel on pourra pas se connecter à la machine (ni en console, ni en SSH).

🌞 **Configuration `sudoers`**

- ajouter une configuration `sudoers` pour que l'utilisateur `meow` puisse exécuter seulement et uniquement les commandes `ls`, `cat`, `less` et `more` en tant que votre utilisateur
- ajouter une configuration `sudoers` pour que les membres du groupe `admins` puisse exécuter seulement et uniquement la commande `apt` en tant que `root`
- ajouter une configuration `sudoers` pour que votre utilisateur puisse exécuter n'importe quel commande en tant `root`, sans avoir besoin de saisir un mot de passe
- prouvez que ces 3 configurations ont pris effet (vous devez vous authentifier avec le bon utilisateur, et faire une commande `sudo` qui doit fonctioner correctement)

> Pour chaque point précédent, c'est une seule ligne de configuration à ajouter dans le fichier `sudoers` de la machine.

### C. Hackers gonna hack

🌞 **Déjà une configuration faible ?**

- l'utilisateur `meow` est en réalité complètement `root` sur la machine hein là. Prouvez-le.
- proposez une configuration similaire, sans présenter cette faiblesse de configuration
  - vous pouvez ajouter de la configuration
  - ou supprimer de la configuration
  - du moment qu'on garde des fonctionnalités à peu près équivalentes !

## 2. Files and permissions

**Dans un OS, en particulier Linux, on dit souvent que "tout est fichier".**

En effet, que ce soit les programmes (que ce soit `ls`, ou Firefox, ou Steam, ou le kernel), les fichiers personnels, les fichiers de configuration, et bien d'autres, **l'ensemble des composants d'un OS, et tout ce qu'on peut y ajouter se résume à un gros tas de fichiers.**

Gérer correctement les permissions des fichiers est une étape essentielle dans le renforcement d'une machine.

**C'est la première barrière de sécurité, (beaucoup) trop souvent négligée, alors qu'elle est extrêmement efficace et robuste.**

### A. Listing POSIX permissions

🌞 **Déterminer les permissions des fichiers/dossiers...**

- le fichier qui contient la liste des utilisateurs
- le fichier qui contient la liste des hashes des mots de passe des utilisateurs
- le fichier de configuration du serveur OpenSSH
- le répertoire personnel de l'utilisateur `root`
- le répertoire personnel de votre utilisateur
- le programme `ls`
- le programme `systemctl`

> POSIX c'est le nom d'un standard qui regroupe plein de concepts avec lesquels vous êtes finalement déjà familiers. Les permissions rwx qu'on retrouve sous les OS Linux (et MacOS, et BSD, et d'autres) font partie de ce standard et sont donc appelées "permissions POSIX".

![Windows POSIX](./img/posix_compliant.png)

### B. Protect a file using permissions

🌞 **Restreindre l'accès à un fichier personnel**

- créer un fichier nommé `dont_readme.txt` (avec le contenu de votre choix)
- il doit se trouver dans un dossier lisible et écrivable par tout le monde
- faites en sorte que seul votre utilisateur (pas votre groupe) puisse lire ou modifier ce fichier
- personne ne doit pouvoir l'exécuter
- prouvez que :
  - votre utilisateur peut le lire
  - votre utilisateur peut le modifier
  - l'utilisateur `meow` ne peut pas y toucher
  - l'utilisateur `root` peut quand même y toucher

> C'est l'un des "superpouvoirs" de `root` : contourner les permissions POSIX (les permissions `rwx`). On verra bien assez tôt que `root` n'a pas de "superpouvoirs" mais que ces contournements sont liés à une mécanique qu'on appelle les *capabilites*. C'est pour plus tard ! :)

### C. Extended attributes

🌞 **Lister tous les programmes qui ont le bit SUID activé**

🌞 **Rendre le fichier `dont_readme.txt` immuable**

- ça se fait avec les attributs étendus
- "immuable" ça veut dire qu'il ne peut plus être modifié DU TOUT : il est donc en read-only
- prouvez que le fichier ne peut plus être modifié par **personne**
