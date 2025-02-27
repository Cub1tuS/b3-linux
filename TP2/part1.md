# Part I : Learn

## Sommaire

- [Part I : Learn](#part-i--learn)
  - [Sommaire](#sommaire)
  - [1. Anatomy of a program](#1-anatomy-of-a-program)
    - [A. `file`](#a-file)
    - [B. `readelf`](#b-readelf)
    - [C. `ldd`](#c-ldd)
  - [2. Syscalls basics](#2-syscalls-basics)
    - [A. Syscall list](#a-syscall-list)
    - [B. `objdump`](#b-objdump)

## 1. Anatomy of a program

### A. `file`

🌞 **Utiliser `file` pour déterminer le type de :**

```bash
[dodo@tp2 ~]$ file /usr/bin/ls
/usr/bin/ls: ELF 64-bit LSB pie executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=fd728af0cf15feae944716ff7269ec21a71c58ef, for GNU/Linux 3.7.0, stripped
```

```bash
[dodo@tp2 ~]$ file /usr/sbin/ip
/usr/sbin/ip: ELF 64-bit LSB pie executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=5d7ba5b3aa1d742d92187a711f4f98eba0129b9d, for GNU/Linux 3.7.0, stripped
```

```bash
[dodo@tp2 ~]$ file omg_meme_sound.mp3 
omg_meme_sound.mp3: Audio file with ID3 version 2.4.0, contains:MPEG ADTS, layer III, v1, 192 kbps, 44.1 kHz, Stereo
```

### B. `readelf`

🌞 **Utiliser `readelf` sur le programme `ls`**

> Header -> 

```bash
[dodo@tp2 ~]$ readelf -h /usr/bin/ls
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           AArch64
  Version:                           0x1
  Entry point address:               0x58c0
  Start of program headers:          64 (bytes into file)
  Start of section headers:          134864 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         28
  Section header string table index: 27
```

> Liste sections -> 
```bash
[dodo@tp2 ~]$ readelf -S /usr/bin/ls
There are 28 section headers, starting at offset 0x20ed0:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000238  00000238
       000000000000001b  0000000000000000   A       0     0     1
  [ 2] .note.gnu.bu[...] NOTE             0000000000000254  00000254
       0000000000000024  0000000000000000   A       0     0     4
  [ 3] .note.ABI-tag     NOTE             0000000000000278  00000278
       0000000000000020  0000000000000000   A       0     0     4
  [ 4] .gnu.hash         GNU_HASH         0000000000000298  00000298
       0000000000000040  0000000000000000   A       5     0     8
  [ 5] .dynsym           DYNSYM           00000000000002d8  000002d8
       0000000000000be8  0000000000000018   A       6     3     8
  [ 6] .dynstr           STRTAB           0000000000000ec0  00000ec0
       00000000000005ae  0000000000000000   A       0     0     1
  [ 7] .gnu.version      VERSYM           000000000000146e  0000146e
       00000000000000fe  0000000000000002   A       5     0     2
  [ 8] .gnu.version_r    VERNEED          0000000000001570  00001570
       0000000000000090  0000000000000000   A       6     3     8
  [ 9] .rela.dyn         RELA             0000000000001600  00001600
       0000000000001560  0000000000000018   A       5     0     8
  [10] .rela.plt         RELA             0000000000002b60  00002b60
       00000000000009f0  0000000000000018  AI       5    22     8
  [11] .init             PROGBITS         0000000000003550  00003550
       0000000000000018  0000000000000000  AX       0     0     4
  [12] .plt              PROGBITS         0000000000003570  00003570
       00000000000006c0  0000000000000000  AX       0     0     16
  [13] .text             PROGBITS         0000000000003c40  00003c40
       0000000000012920  0000000000000000  AX       0     0     64
  [14] .fini             PROGBITS         0000000000016560  00016560
       0000000000000014  0000000000000000  AX       0     0     4
  [15] .rodata           PROGBITS         0000000000016580  00016580
       00000000000048aa  0000000000000000   A       0     0     16
  [16] .eh_frame_hdr     PROGBITS         000000000001ae2c  0001ae2c
       0000000000000564  0000000000000000   A       0     0     4
  [17] .eh_frame         PROGBITS         000000000001b390  0001b390
       0000000000002010  0000000000000000   A       0     0     8
  [18] .init_array       INIT_ARRAY       000000000002efd8  0001efd8
       0000000000000008  0000000000000008  WA       0     0     8
  [19] .fini_array       FINI_ARRAY       000000000002efe0  0001efe0
       0000000000000008  0000000000000008  WA       0     0     8
  [20] .data.rel.ro      PROGBITS         000000000002efe8  0001efe8
       0000000000000a10  0000000000000000  WA       0     0     8
  [21] .dynamic          DYNAMIC          000000000002f9f8  0001f9f8
       0000000000000220  0000000000000010  WA       6     0     8
  [22] .got              PROGBITS         000000000002fc18  0001fc18
       00000000000003e8  0000000000000008  WA       0     0     8
  [23] .data             PROGBITS         0000000000030000  00020000
       0000000000000290  0000000000000000  WA       0     0     8
  [24] .bss              NOBITS           0000000000030290  00020290
       00000000000012a8  0000000000000000  WA       0     0     8
  [25] .gnu_debuglink    PROGBITS         0000000000000000  00020290
       0000000000000024  0000000000000000           0     0     4
  [26] .gnu_debugdata    PROGBITS         0000000000000000  000202b4
       0000000000000b10  0000000000000000           0     0     1
  [27] .shstrtab         STRTAB           0000000000000000  00020dc4
       000000000000010c  0000000000000000           0     0     1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  p (processor specific)
```

> Adresse `.text` -> 
```bash
  [13] .text             PROGBITS         0000000000003c40  00003c40
       0000000000012920  0000000000000000  AX       0     0     64
```

### C. `ldd`

`ldd` est un outil qui permet de manipuler le *dynamic linker* de Linux. Le *dynamic linker* c'est un programme qui s'occupe de trouver les librairies nécessaires quand un autre programme se lance.

**On peut utiliser `ldd` notamment pour visualiser de quelle librairie a besoin un programme donné.**

🌞 **Utiliser `ldd` sur le programme `ls`**

```bash
[dodo@tp2 ~]$ ldd /usr/sbin/ip
	linux-vdso.so.1 (0x0000ffffab8cd000)
	libbpf.so.1 => /lib64/libbpf.so.1 (0x0000ffffab6fc000)
	libelf.so.1 => /lib64/libelf.so.1 (0x0000ffffab6cb000)
	libmnl.so.0 => /lib64/libmnl.so.0 (0x0000ffffab6aa000)
	libcap.so.2 => /lib64/libcap.so.2 (0x0000ffffab689000)
	libc.so.6 => /lib64/libc.so.6 (0x0000ffffab4db000)
	/lib/ld-linux-aarch64.so.1 (0x0000ffffab890000)
	libz.so.1 => /lib64/libz.so.1 (0x0000ffffab4aa000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x0000ffffab3e9000)
```

> Libc.so -> 
```bash
	libc.so.6 => /lib64/libc.so.6 (0x0000ffffab4db000)
```

## 2. Syscalls basics

### A. Syscall list

🌞 **Donner le nom ET l'identifiant unique d'un syscall qui permet à un processus de...**

| %rax | nom | fonction |
|:-----:|:--:|:---------:|
|   0  | read | Lire un fichier stocké sur disque |
|   1  | write | Écrire dans un fichier stocké sur disque |
|  57 | fork | Lancer un nouveau processus|

### B. `objdump`

🌞 **Utiliser `objdump`** sur la commande `ls`

```bash
Disassembly of section .text:
    3d34:	97fffec3 	bl	3840 <bindtextdomain@plt>
    3d3c:	97ffff19 	bl	39a0 <textdomain@plt>
    437c:	97fffdf1 	bl	3b40 <dcgettext@plt>
    43f4:	97fffdd3 	bl	3b40 <dcgettext@plt>
    4600:	97fffd50 	bl	3b40 <dcgettext@plt>
    4a28:	97fffc46 	bl	3b40 <dcgettext@plt>
    4c2c:	97fffbc5 	bl	3b40 <dcgettext@plt>
    4f60:	97fffaf8 	bl	3b40 <dcgettext@plt>
    4f98:	97fffaea 	bl	3b40 <dcgettext@plt>
    5488:	97fff9ae 	bl	3b40 <dcgettext@plt>
[...]
```

> Sur ARM, 'call' devient 'bl'
```bash
[dodo@tp2 /]$ objdump -d /usr/bin/ls | grep -E '\bbl\b'
    355c:	940008e6 	bl	58f4 <setlocale@plt+0x1cd4>
    3c8c:	97ffff1d 	bl	3900 <strrchr@plt>
    3cb8:	97fffede 	bl	3830 <strncmp@plt>
    3cd0:	97fffed8 	bl	3830 <strncmp@plt>
    3d1c:	97ffffc1 	bl	3c20 <setlocale@plt>
    3d34:	97fffec3 	bl	3840 <bindtextdomain@plt>
    3d3c:	97ffff19 	bl	39a0 <textdomain@plt>
```

> Sur ARM, 'syscall' devient 'svc'
```bash
[dodo@tp2 /]$ objdump -d /usr/bin/ls | grep 'svc'
[dodo@tp2 /]$ 
```

🌞 **Utiliser `objdump`** sur la librairie Glibc

```bash
[dodo@tp2 /]$ objdump -d usr/lib64/libc.so.6 | grep 'svc'
0000000000027420 <__GI___syscall_error>:
   3a918:	17ffb2c2 	b	27420 <__GI___syscall_error>
   3afd8:	17ffb112 	b	27420 <__GI___syscall_error>
   496a4:	17ff775f 	b	27420 <__GI___syscall_error>
   496a8:	17ff775e 	b	27420 <__GI___syscall_error>
   496e4:	17ff774f 	b	27420 <__GI___syscall_error>
   49768:	17ff772e 	b	27420 <__GI___syscall_error>
   499f4:	17ff768b 	b	27420 <__GI___syscall_error>
   49a04:	17ff7687 	b	27420 <__GI___syscall_error>
[...]
```

> Close devient 57..
```bash
  104930:	d2800728 	mov	x8, #0x39                  	// #57
  104934:	b89d83a0 	ldursw	x0, [x29, #-40]
  104938:	b8336a96 	str	w22, [x20, x19]
  10493c:	d4000001 	svc	#0x0
```

[Part 2](part2.md)