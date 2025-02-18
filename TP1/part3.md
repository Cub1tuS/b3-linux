# Part III : Storage is still disks in 2025

> Titre de la partie en référence au fait que le cloud est partout mais bon, c'est toujours des disques durs derrière ça n'a pas bougé. Un skill donc toujours primordial.

Dans cette partie, on joue avec le stockage de la machine. Au menu : mumuse avec des disques et des partitions, formatage, scénario de remplissage de partition.

> **Munissez vous du [mémo LVM](../../cours/memo/lvm.md) pour réaliser cette partie.**

## Index

- [Part III : Storage is still disks in 2025](#part-iii--storage-is-still-disks-in-2025)
  - [Index](#index)
  - [1. LVM](#1-lvm)
  - [2. HELP my partition is full](#2-help-my-partition-is-full)
  - [3. Prepare another partition](#3-prepare-another-partition)

![Partitions](./img/partition.png)

## 1. LVM

*LVM* (pour *Logical Volume Manager*) est l'outil de référence aujourd'hui sous Linux pour créer et gérer les partitions des disques.

> Il a beaucoup beaucoup trop de features de fou, il se contente pas de couper des disques !

🌞 **Afficher l'état actuel de LVM**

- afficher la liste des *PV* (*Volume Volumes*)
  - ce sont les disque durs et partitions physiques que LVM gère
- afficher la liste des *VG* (*Volume Groups*)
  - on regroupe les *PV* en des groupes appelés *VG*
- afficher la liste des *LV* (*Logical Volumes*)
  - les *VG* sont découpés en *LV*
  - un *LV* est une partition utilisable

🌞 **Déterminer le type de système de fichiers**

- de la partition montée sur `/`
- de la partition montée sur `/home`
- **attention** : 
  - j'attends une commande qui détecte le type de système de fichiers sur une partition donnée : `<COMMANDE> /dev/chemin/partition`
  - je ne VEUX PAS une commande qui affiche les partitions actuellement utilisées où on voit le système de fichiers utilisé (pas de `mount` par exemple)

## 2. HELP my partition is full


🌞 **Remplissez votre partition `/home`**

- on va simuler avec un truc bourrin :

```
dd if=/dev/zero of=/home/<TON_USER>/bigfile bs=4M count=2500
```

> 2500x4M ça fait 20G. Ca fait trop.

🌞 **Constater que la partition est pleine**

- avec un `df -h`

🌞 **Agrandir la partition**

- avec des commandes LVM il faut agrandir le logical volume
- récupérez tout l'espace libre restant
- ensuite il faudra indiquer au système de fichier ext4 que la partition a été agrandie
- prouvez avec un `df -h` que vous avez récupéré de l'espace en plus

🌞 **Remplissez votre partition `/home`**

- on va simuler encore avec un truc bourrin :

```
dd if=/dev/zero of=/home/<TON_USER>/bigfile bs=4M count=2500
```

> 2500x4M ça fait toujours 20G. Et ça fait toujours trop.

➜ **Eteignez la VM et ajoutez lui un disque de 40G**

🌞 **Utiliser ce nouveau disque pour étendre la partition `/home` de 20G**

- dans l'ordre il faut :
- indiquer à LVM qu'il y a un nouveau PV dispo
- ajouter ce nouveau PV au VG existant
- étendre le LV existant pour récupérer le nouvel espace dispo au sein du VG
- indiquer au système de fichier ext4 que la partition a été agrandie
- prouvez avec un `df -h` que vous avez récupéré de l'espace en plus

## 3. Prepare another partition

Pour la suite du TP, on va préparer une dernière partition. Il devrait vous rester 20G de libre avec le disque de 40 que vous venez d'ajouter.

**Cette partition contiendra des fichiers HTML pour des sites web (fictifs).**

🌞 **Créez une nouvelle partition**

- le LV doit s'appeler `web`
- elle doit faire 20G et être formatée en ext4
- il faut la monter sur `/var/www`

🌞 **Proposez au moins une option de montage**

- au moment où on monte la partition (avec fstab ou la commande `mount`), on peut choisir des options de montage
- proposez au moins une option de montage qui augmente le niveau de sécurité lors de l'utilisation de la partition
- je rappelle que la partition ne contiendra que des fichiers HTML