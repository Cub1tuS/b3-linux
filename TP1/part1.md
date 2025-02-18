# Part I : Rocky install

## Index

- [Part I : Rocky install](#part-i--rocky-install)
  - [Index](#index)
  - [1. Install instructions](#1-install-instructions)
  - [2. Proofs](#2-proofs)

## 1. Install instructions

**FAIT !**

## 2. Proofs
🌞 **Prouvez que le schéma de partitionnement a bien été appliqué**

```bash
[root@node1 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           820M     0  820M   0% /dev/shm
tmpfs           328M  4.8M  323M   2% /run
efivarfs        256K   32K  225K  13% /sys/firmware/efi/efivars
/dev/nvme0n1p2  9.8G  1.5G  7.8G  16% /
/dev/nvme0n1p3  4.9G   24K  4.6G   1% /home
/dev/nvme0n1p4  4.9G  142M  4.5G   4% /var
/dev/nvme0n1p1 1022M  7.3M 1015M   1% /boot/efi
tmpfs           164M     0  164M   0% /run/user/0
```

🌞 **Mettre en évidence la ligne de configuration `sudo` qui concerne le groupe `wheel`**

```bash
[root@node1 ~]# cat /etc/sudoers | grep wheel | awk 'NR==2'
%wheel	ALL=(ALL)	ALL
```

🌞 **Prouvez que votre utilisateur est bien dans le groupe `wheel`**

```bash
[root@node1 ~]# groups dodo
dodo : dodo wheel
```

🌞 **Prouvez que la langue configurée pour l'OS est bien l'anglais**

```bash
[root@node1 ~]# echo $LANG
en_US.UTF-8
```

🌞 **Prouvez que le firewall est déjà actif**

```bash
[root@node1 ~]# firewall-cmd --state
running
```