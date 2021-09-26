# TP 1 - LINUX 

## 0. Préparation de la machine

* un accès internet : 

Avec la commande`ìp a` on obtient le détail de nos carte réseau et on se rend bien compte qu'on a celle configuré NAT et celle en Host-Only .


Pour garantir un accès internet on décide de ping le serveur de google (sur l'ip 8.8.8.8) a l'aide de la commande `ping 8.8.8.8` on obtient une réponse des serveurs avec une latence de 20 ms en moyenne 

![](https://i.imgur.com/MkNv9gD.png)


Les deux machines se ping sans problème comme on peut le voir ici : 

![](https://i.imgur.com/QWTLUIa.png)

A l'aide de la commande ``` sudo nano /etc/hostname``` Je peux changer le nom de la machine correctement on voit donc ici que les noms de mes machines ont changer : 

![](https://i.imgur.com/ICh97Z6.png)

Après avoir setup mon serveurs dns en 1.1.1.1 avec la commande ```sudo nano /etc/resolv.conf``` on vérifie : 

```
[arthur@node1 ~]$ cat /etc/resolv.conf
nameserver 1.1.1.1
``` 
 
Et on utilise en suite la commande dig pour ynov.com : 

```
[arthur@node1 ~]$ dig ynov.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> ynov.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16130
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;ynov.com.                      IN      A

;; ANSWER SECTION:
ynov.com.               10303   IN      A       92.243.16.143

;; Query time: 19 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Wed Sep 22 12:00:44 CEST 2021
;; MSG SIZE  rcvd: 53

```

Après avoir générer une clé ssh publique je l'intègre a ma VM et je vérifie : 

```
[arthur@node1 ~]$ cat .ssh/authorized_keys
SHA256:kjwYoWjgnwZ7utbX4KSCh2WyhxbaGu2aeZQvlC39BP4 arthur@LAPTOP-HN7A2ESB
``` 

J'organise les permissions avec **chmod** ensuite de me connecter et bingo plus besoin de mot de passe !




