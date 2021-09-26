# TP 2 Réseaux

## 1-ARP

Je commence par ping mes deux vms entre elles : 

```
[arthur@vm ~]$ ping 10.2.1.12
PING 10.2.1.12 (10.2.1.12) 56(84) bytes of data.
64 bytes from 10.2.1.12: icmp_seq=1 ttl=64 time=0.778 ms
64 bytes from 10.2.1.12: icmp_seq=2 ttl=64 time=0.849 ms
64 bytes from 10.2.1.12: icmp_seq=3 ttl=64 time=0.861 ms
64 bytes from 10.2.1.12: icmp_seq=4 ttl=64 time=0.733 ms

--- 10.2.1.12 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3044ms
rtt min/avg/max/mdev = 0.733/0.805/0.861/0.056 ms

///

[arthur@vm2 ~]$ ping 10.2.1.11
PING 10.2.1.11 (10.2.1.11) 56(84) bytes of data.
64 bytes from 10.2.1.11: icmp_seq=1 ttl=64 time=0.476 ms
64 bytes from 10.2.1.11: icmp_seq=2 ttl=64 time=0.767 ms
64 bytes from 10.2.1.11: icmp_seq=3 ttl=64 time=0.842 ms
64 bytes from 10.2.1.11: icmp_seq=4 ttl=64 time=0.947 ms

--- 10.2.1.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3078ms
rtt min/avg/max/mdev = 0.476/0.758/0.947/0.174 ms

```

On affiche ensuite les tables **ARP** : 

```
[arthur@vm ~]$ arp -a
? (10.2.1.12) at 08:00:27:36:bd:02 [ether] on enp0s8
? (10.2.1.1) at 0a:00:27:00:00:41 [ether] on enp0s8
_gateway (10.0.2.2) at 52:54:00:12:35:02 [ether] on enp0s3

///

[arthur@vm2 ~]$ arp -a
? (10.2.1.1) at 0a:00:27:00:00:41 [ether] on enp0s8
? (10.2.1.11) at 08:00:27:d6:aa:e7 [ether] on enp0s8
_gateway (10.0.2.2) at 52:54:00:12:35:02 [ether] on enp0s3

```

On peut observer dans ces tables les adresses MAC de chaque machine donc pour la vm2 (10.2.1.12) c'est **08:00:27:36:bd:02** et pour la vm1 (10.2.1.11) ça sera **08:00:27:d6:aa:e7**

On écoute ensuite les trams avec tcpdump et on observe les trames ARP : 

| ordre | type trame  | source                   | destination                |
|-------|-------------|--------------------------|----------------------------|
| 1     | Requête ARP | `node1` `08:00:27:d6:aa:e7` | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | `node2` `08:00:27:36:bd:02` | `node1` `08:00:27:d6:aa:e7`   |
| 3  | Réponse ARP        | `node 2` `08:00:27:36:bd:02`                      | `node1` `08:00:27:d6:aa:e7`                        |
| 4  | Réponse ARP        | `node1` `08:00:27:d6:aa:e7`                      | `node 2` `08:00:27:36:bd:02`                       |

## 2- Routage

Pour commencer je vérifie que mon routeur est bien sur les deux réseaux avec **ip a** on observe alors : 
```
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:12:6a:7b brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.254/8 brd 10.255.255.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe12:6a7b/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:ff:79:d4 brd ff:ff:ff:ff:ff:ff
    inet 10.2.2.254/24 brd 10.2.2.255 scope global noprefixroute enp0s9
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feff:79d4/64 scope link
       valid_lft forever preferred_lft forever
```

Je vais ensuite acitver le noeud de routage et je vérifie que tout soit bien activer : 

```[arthur@routeur ~]$ sudo firewall-cmd --get-active-zone
public
  interfaces: enp0s3 enp0s8 enp0s9
[arthur@routeur ~]$ sudo firewall-cmd --add-masquerade --zone=public
success
[arthur@routeur ~]$ sudo firewall-cmd --add-masquerade --zone=public --permanent
success
```

Je vais ensuite ajouter les routes statique aux deux vms et je vérifie qu'elles peuvent se ping entre elles : 

```

[arthur@vm2 ~]$ sudo ip route add 10.2.1.0/24 via 10.2.2.254 dev enp0s8
[sudo] password for arthur:
[arthur@vm2 ~]$ ip route show
10.2.1.0/24 via 10.2.2.254 dev enp0s8
10.2.2.0/24 dev enp0s8 proto kernel scope link src 10.2.2.12 metric 100
[arthur@vm2 ~]$ ping 10.2.1.11
PING 10.2.1.11 (10.2.1.11) 56(84) bytes of data.
64 bytes from 10.2.1.11: icmp_seq=1 ttl=63 time=1.56 ms
64 bytes from 10.2.1.11: icmp_seq=2 ttl=63 time=0.755 ms
64 bytes from 10.2.1.11: icmp_seq=3 ttl=63 time=0.701 ms
64 bytes from 10.2.1.11: icmp_seq=4 ttl=63 time=0.809 ms

///

[arthur@vm ~]$ sudo ip route add 10.2.2.0/24 via 10.2.1.254 dev enp0s8
[arthur@vm ~]$ ip route show
10.2.1.0/24 dev enp0s8 proto kernel scope link src 10.2.1.11 metric 100
10.2.2.0/24 via 10.2.1.254 dev enp0s8
[arthur@vm ~]$ ping 10.2.2.12
PING 10.2.2.12 (10.2.2.12) 56(84) bytes of data.
64 bytes from 10.2.2.12: icmp_seq=1 ttl=63 time=0.880 ms
64 bytes from 10.2.2.12: icmp_seq=2 ttl=63 time=0.739 ms
64 bytes from 10.2.2.12: icmp_seq=3 ttl=63 time=0.839 ms
64 bytes from 10.2.2.12: icmp_seq=4 ttl=63 time=0.758 ms
64 bytes from 10.2.2.12: icmp_seq=5 ttl=63 time=0.744 ms

```

