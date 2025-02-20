# Part II : Networking

## Index

- [Part II : Networking](#part-ii--networking)
  - [Index](#index)
  - [1. Basic networking conf](#1-basic-networking-conf)
    - [A. Static IP](#a-static-ip)
    - [B. Hostname](#b-hostname)
  - [2. Listening ports](#2-listening-ports)
  - [3. Firewalling](#3-firewalling)

## 1. Basic networking conf

### A. Static IP

🌞 **Attribuer l'adresse IP `10.1.1.11/24`** à la VM

```bash
[root@node1 network-scripts]# ip a
[...]
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:29:c0:f9 brd ff:ff:ff:ff:ff:ff
    altname enp2s0
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute ens160
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe29:c0f9/64 scope link 
       valid_lft forever preferred_lft forever
[...]
```

### B. Hostname

🌞 **Attribuer le nom `node1.tp1.b3` à la VM**

```bash
[root@node1 network-scripts]# hostnamectl
 Static hostname: node1.tp1.b3
[...]
 ```

## 2. Listening ports

🌞 **Déterminer la liste des programmes qui écoutent sur un port TCP**

```bash
[root@node1 network-scripts]# ss -lnpt
State          Recv-Q         Send-Q                 Local Address:Port                  Peer Address:Port         Process                                
LISTEN         0              128                          0.0.0.0:22                         0.0.0.0:*             users:(("sshd",pid=753,fd=3))         
LISTEN         0              128                             [::]:22                            [::]:*             users:(("sshd",pid=753,fd=4))
```

🌞 **Déterminer la liste des programmes qui écoutent sur un port UDP**

```bash
[root@node1 network-scripts]# ss -lnpu
State          Recv-Q         Send-Q                 Local Address:Port                 Peer Address:Port        Process                                  
UNCONN         0              0                          127.0.0.1:323                       0.0.0.0:*            users:(("chronyd",pid=670,fd=5))        
UNCONN         0              0                              [::1]:323                          [::]:*            users:(("chronyd",pid=670,fd=6))
```

## 3. Firewalling

🌞 **Pour chacun des ports précédemment repérés...**

```bash
[root@node1 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens160 ens256
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```

🌞 **Fermez tous les ports inutilement ouverts dans le firewall**

```bash
[root@node1 ~]# firewall-cmd --remove-service=cockpit
success
[root@node1 ~]# firewall-cmd --remove-service=dhcpv6-client
success
```
🌞 **Pour toutes les applications qui sont en écoute sur TOUTES les adresses IP**

```bash
[root@node1 ~]# ss -tulnp | grep LISTEN
tcp   LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=753,fd=3))   
tcp   LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=753,fd=4))
```

```bash
[root@node1 ~]# cat /etc/ssh/sshd_config
[...]
ListenAddress 172.16.161.128
[...]

[root@node1 ~]# systemctl restart sshd
```

```bash
[root@node1 ~]# ss -tulnp | grep LISTEN
tcp   LISTEN 0      128    172.16.161.128:22        0.0.0.0:*    users:(("sshd",pid=1477,fd=3))  
```
> Le port écoute sur le l'ip locale, 172.16.161.128

[Part 3](part3.md)