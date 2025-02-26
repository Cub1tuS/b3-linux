
# Part II : Observe

## Sommaire

- [Part II : Observe](#part-ii--observe)
  - [Sommaire](#sommaire)
  - [1. strace](#1-strace)
  - [2. sysdig](#2-sysdig)
    - [A. Intro](#a-intro)
    - [B. Use it](#b-use-it)
  - [3. Bonus : Stratoshark](#3-bonus--stratoshark)

## 1. strace

Si on veut tracer un processus avec `strace`, c'est comme ça :

```bash
# pour tracer l'exécution d'un echo par exemple
$ strace echo yo
```

🌞 **Utiliser `strace` pour tracer l'exécution de la commande `ls`**

```bash
[dodo@tp2 ~]$ strace ls 2>&1 | grep write
write(1, "omg_meme_sound.mp3\n", 19omg_meme_sound.mp3
```

🌞 **Utiliser `strace` pour tracer l'exécution de la commande `cat`**

```bash
[dodo@tp2 ~]$ strace cat toto 2>&1 | grep ^open
[...]
openat(AT_FDCWD, "toto", O_RDONLY)      = 3

[dodo@tp2 ~]$ strace cat toto 2>&1 | grep ^write
write(1, "tata\n", 5tata
```

🌞 **Utiliser `strace` pour tracer l'exécution de `curl example.org`**

```bash
[dodo@tp2 ~]$ strace -C curl example.org
[...]
------ ----------- ----------- --------- --------- ----------------
 19.55    0.000458          13        33           ppoll
 16.09    0.000377           3        98           rt_sigaction
 11.69    0.000274           5        54           close
  8.92    0.000209         209         1           recvfrom
  8.32    0.000195           3        60        14 openat
  4.99    0.000117          16         7           write
  3.20    0.000075           1        55           mprotect
  3.16    0.000074           0        89           mmap
  2.94    0.000069          69         1         1 connect
  2.77    0.000065           2        22           futex
  2.35    0.000055          27         2           socket
  1.96    0.000046           1        46           fstat
  1.71    0.000040           1        36           read
  1.49    0.000035          17         2           socketpair
  1.49    0.000035          35         1           clone
  1.28    0.000030          10         3           rt_sigprocmask
  1.02    0.000024          12         2           statfs
  0.98    0.000023          23         1           sendto
  0.81    0.000019           9         2           getdents64
  0.73    0.000017           8         2           newfstatat
  0.64    0.000015          15         1           pipe2
  0.60    0.000014          14         1           munmap
  0.51    0.000012           3         4           brk
  0.47    0.000011           1         6           fcntl
  0.43    0.000010          10         1           sysinfo
  0.43    0.000010           2         4           setsockopt
  0.38    0.000009           4         2           ioctl
  0.38    0.000009           4         2         1 faccessat
  0.17    0.000004           4         1           getsockopt
  0.17    0.000004           4         1           prlimit64
  0.13    0.000003           3         1           getsockname
  0.13    0.000003           3         1           getrandom
  0.09    0.000002           2         1           getpeername
  0.00    0.000000           0         1           set_tid_address
  0.00    0.000000           0         1           set_robust_list
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         1           rseq
------ ----------- ----------- --------- --------- ----------------
100.00    0.002343           4       547        16 total
```

## 2. sysdig

### A. Intro

`sysdig` est un outil qui permet de faire pleiiin de trucs, et notamment tracer les *syscalls*  que le kernel reçoit.

Si on le lance sans préciser, il affichera TOUS les *syscalls*  que reçoit votre kernel.

On peut ajouter des filtres, pour ne voir que les *syscalls*  qui nous intéressent.

Par exemple :

```bash
# si on veut tracer les *syscalls*  effectués par le programme echo
sysdig proc.name=echo
```

```bash
sudo dnf update -y 
sudo dnf install -y epel-release
sudo dnf install -y dkms gcc kernel-devel make perl kernel-headers
sudo reboot
curl -SLO https://github.com/draios/sysdig/releases/download/0.39.0/sysdig-0.39.0-aarch64.rpm
sudo rpm -ivh sysdig-0.39.0-aarch64.rpm
```

### B. Use it

🌞 **Utiliser `sysdig` pour tracer les *syscalls*  effectués par `ls`**

```bash
[dodo@tp2 ~]$ sudo sysdig proc.name=ls
[...]
108838 16:03:40.190512211 1 ls (3590.3590) < write res=80 data=.[0m.[01;36momg_meme_sound.mp3.[0m  .[01;31msysdig-0.39.0-aarch64.rpm.[0m  toto.
```

🌞 **Utiliser `sysdig` pour tracer les *syscalls*  effectués par `cat`**

```bash
5546 16:06:45.663733520 1 cat (3600.3600) < openat fd=3(<f>/home/dodo/toto) dirfd=-100(AT_FDCWD) name=toto(/home/dodo/toto) flags=1(O_RDONLY) mode=0 dev=FD03 ino=131081
[...]
5782 16:06:45.665022311 1 cat (3600.3600) > write fd=1(<f>/dev/pts/1) size=5
5784 16:06:45.665031686 1 cat (3600.3600) < write res=5 data=tata.
```

🌞 **Utiliser `sysdig` pour tracer les *syscalls*  effectués par votre utilisateur**

```bash
[dodo@tp2 ~]$ sudo sysdig user.name=dodo
```

🌞 **Livrez le fichier `curl.scap` dans le dépôt git de rendu**

[curl.scap](curl.scap)
[Part 3](part3.md)
