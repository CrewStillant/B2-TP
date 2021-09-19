#    1 TP 1 B2 Réseau - 2021

# I- Exploration locale en solo

## 1- Affichage d'informations sur la pile TCP/IP locale
A l'aide de la commande **ipconfig /all** on obtient les informations suivantes

Pour l'interface **wifi** : 
    - nom : Carte réseau sans fil Wifi
    - IP : 10.33.2.62
    - MAC : B0-7D-64-B1-98-D3
    
Pour l'interface **ethernet** : 
    - nom : Carte Ethernet Ethernet
    - IP : a définir
    - MAC : 08-97-98-E2-5F-1E
    
Passerelle de la carte : 10.33.3.253

**EN GRAPHIQUE (GUI : Graphical User Interface)**

On retrouve dans les paramètres Wifi windows les informations suivantes : 
        - IP : 10.33.2.62
        - MAC : B0-7D-64-B1-98-D3
        
Dans le centre réseau et partage dans les détails de ma carte wifi je trouve la gateway : 10.33.3.253

A la question a quoi sert le gateway on peut répondre que cela nous sert a relier deux réseau différent donc a ynov notre réseau LAN a internet (WAN).

## 2-  Modifications des informations

Toujours dans le centre de réseau et partage j'accède aux propriétés de ma carte et je n'ai plus qu'a modifier mon adresse IPv4 comme ci-dessous.

**insérer screen**

Il est possible de perdre un accès a internet car l'adresse ip est celle qui a été défini par le serveur DHCP est bloqué et ne nous permette pas d'être accepter sur le réseau si on modifiait notre adresse IP.

Maintenant je passe a la table ARP pour l'afficher dans mon invit de commande j'utilise la commande **arp -a** qui me donne donc une liste d'adresses MAC.

L'adresse ip de ma passerelle étant 10.33.3.253 je regarde l'adresse MAC a laquelle elle correspond et c'est donc l'adresse MAC : 00-12-00-40-4c-bf.

A la suite de cela on effectue divers ping pour obtenir les adresses MAC de ses ip on obtient donc : 
    IP :  10.33.1.76  MAC : e0-cc-f8-7a-4f-4a
    IP :  10.33.1.103 MAC : 18-65-90-ce-f5-63
    IP :  10.33.2.8   MAC : a4-5e-60-ed-0b-27

J'ai effectué ces pings avec la commande **ping** + IP et afficher les correspondances toujours avec **arp -a**.

On passe maintenant a l'utilisation du logiciel **nmap**

![](https://i.imgur.com/UGLTBcS.png)

A travers ce screen on remarque que j'ai donc lancer un **Ping Scan** comme indiqué en haut a droite du logiciel et ce scan a été réalisé sur le réseau d'ynov donc le 10.33.0.0/22.

Ma table arp me renvoi donc toutes les ips que j'ai ping !

Maitenant je vais modifier mon adresse ip libre je choisis donc une adresse ip libre et j'obtiens : 
```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::60b2:faf0:f0af:c554%14
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.2.103
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Passerelle par défaut. . . . . . . . . : 10.33.3.253

Carte Ethernet Connexion réseau Bluetooth :

   Statut du média. . . . . . . . . . . . : Média déconnecté
   Suffixe DNS propre à la connexion. . . :

C:\Windows\system32>ping 8.8.8.8

Envoi d’une requête 'Ping'  8.8.8.8 avec 32 octets de données :
Réponse de 8.8.8.8 : octets=32 temps=37 ms TTL=115
Réponse de 8.8.8.8 : octets=32 temps=49 ms TTL=115
Réponse de 8.8.8.8 : octets=32 temps=41 ms TTL=115
Réponse de 8.8.8.8 : octets=32 temps=49 ms TTL=115

Statistiques Ping pour 8.8.8.8:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 37ms, Maximum = 49ms, Moyenne = 44ms
```

# II- Exploration Locale en Duo

## 1- Modification d'adresses IP

Après avoir modifier mon ip pour être sur le bon réseau comme indiquer ci dessous : 

```Carte Ethernet Ethernet :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::1d1c:336f:93d4:d32a%4
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.10.2
   Masque de sous-réseau. . . . . . . . . : 255.255.255.252
   ```
Je ping L'ordinateur avec lequel je suis branché en ethernet : 

```C:\Windows\system32>ping 192.168.10.1

Envoi d’une requête 'Ping'  192.168.10.1 avec 32 octets de données :
Réponse de 192.168.10.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.10.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.10.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.10.1 : octets=32 temps<1ms TTL=128
```

Ensuite on essaye de passer pas un seul ordinateur pour se connecter a internet . On défini donc l'autre ip comme passerelle et on setup le serveur de cloudflare comme dns et on a donc les 3 éléments pour se connecter a **internet** on ping donc 8.8.8.8 pour prouver notre accès a internet on obtient : 

``` C:\Windows\system32>ping 8.8.8.8

Envoi d’une requête 'Ping'  8.8.8.8 avec 32 octets de données :
Réponse de 8.8.8.8 : octets=32 temps=148 ms TTL=114
Réponse de 8.8.8.8 : octets=32 temps=497 ms TTL=114
Réponse de 8.8.8.8 : octets=32 temps=275 ms TTL=114
Réponse de 8.8.8.8 : octets=32 temps=103 ms TTL=114

``` 

On utilise **traceroute** pour suivre l'envoi de paquets entre les 2 ordinateurs dans le réseau et  on obtient :  
````
C:\Windows\system32>tracert 192.168.10.1

Détermination de l’itinéraire vers DESKTOP-ON7BJGL [192.168.10.1]
avec un maximum de 30 sauts :

  1    <1 ms    <1 ms    <1 ms  DESKTOP-ON7BJGL [192.168.10.1]

Itinéraire déterminé.  
````

A l'aide de netcat on va maintenant créer un tchat entre nos 2 ordinateurs : 
`` 
Cmd line: 192.168.10.1 8888

eh oui
BONJOUR
``

On décide ensuite de se connecter seulement sur l'ethernet on obtient donc : 

```
Cmd line: 192.168.10.1 9999
I am blue
dabeudi dabeuda
dabeudi dabeuda
dabeudi dabeuda

``` 
Afin d'autoriser les ping a travers le firewall,on met en place des exceptions dans le protocole de mon firewall : 

```
netsh advfirewall firewall add rule name="ICMP Allow incoming V4 echo request" protocol=icmpv4:8,any dir=in action=allow
```

```
netsh advfirewall firewall add rule name="ICMP Allow incoming V6 echo request" protocol=icmpv6:8,any dir=in action=allow
```
 
# III. Manipulations d'autres outils/protocoles côté client

## 1- DHCP 

Dans le centre réseau et internet de windows j'accède aux informations de ma carte wifi et obtient les informations suivantes sur mon DHCP : 

![](https://i.imgur.com/w9V1NyR.png)


## 2-DNS

Voici les serveurs dns que mon ordianteur connait : 

![](https://i.imgur.com/nC4MjML.png)

Après un lookup pour google et ynov on obtient dans notre invite de commande : 

```
C:\Windows\system32>nslookup google.com
Serveur :   UnKnown
Address:  10.33.10.2

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:811::200e
          172.217.18.206


C:\Windows\system32>nslookup ynov.com
Serveur :   UnKnown
Address:  10.33.10.2

Réponse ne faisant pas autorité :
Nom :    ynov.com
Address:  92.243.16.143
``` 

On a donc fait ses requêtes au serveur dns 10.33.10.2 .

Après cela on execute un reverse lookup et on obtient : 

```
C:\Windows\system32>nslookup 78.74.21.21
Serveur :   UnKnown
Address:  10.33.10.2

Nom :    host-78-74-21-21.homerun.telia.com
Address:  78.74.21.21


C:\Windows\system32>nslookup 92.146.54.88
Serveur :   UnKnown
Address:  10.33.10.2

Nom :    apoitiers-654-1-167-88.w92-146.abo.wanadoo.fr
Address:  92.146.54.88
``` 
