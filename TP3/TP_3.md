# TP 3 GNS3 - ANTOINE MERY EFREI B2

🌞 **`ping` d'un client du  `LAN1` vers un client du `LAN 2`**

````
PC1> ping 10.3.2.1

84 bytes from 10.3.2.1 icmp_seq=1 ttl=62 time=41.599 ms
84 bytes from 10.3.2.1 icmp_seq=2 ttl=62 time=31.440 ms
84 bytes from 10.3.2.1 icmp_seq=3 ttl=62 time=31.336 ms
84 bytes from 10.3.2.1 icmp_seq=4 ttl=62 time=32.084 ms
84 bytes from 10.3.2.1 icmp_seq=5 ttl=62 time=35.353 ms
````
`PC1 est dans le LAN1 et ping le PC3 situé dans le LAN2.`

🌞 **Capture Wireshark `ping_partie1`**

[ping_partie1](R1_FastEthernet10_to_R2_FastEthernet00.pcap)

- **capture entre les deux routeurs pendant que les clients se `ping`**
- on doit voir :
  - les adresses MAC sont celles des routeurs dans la trame
  - mais les adresses IP du paquet sont bien celles des clients
  - on doit voir le routage en action quoi !

🌞 **Afficher les adresses MAC des routeurs**


# **PAS CERTAIN**
````
R1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.3.12.1               -   c401.0638.0010  ARPA   FastEthernet1/0
Internet  10.3.12.2              22   c402.0687.0000  ARPA   FastEthernet1/0
Internet  10.3.1.1                6   0050.7966.6800  ARPA   FastEthernet0/1
Internet  192.168.122.1           4   5254.0024.5d50  ARPA   FastEthernet0/0
Internet  192.168.122.6           -   c401.0638.0000  ARPA   FastEthernet0/0
Internet  10.3.1.254              -   c401.0638.0001  ARPA   FastEthernet0/1
R1#
````

- **sur `r1` et `r2` : trouvez une commande pour afficher les adresses MAC**
- pour confirmer les infos de la capture Wireshark
- sur leurs interfaces qui portent l'adresse en `/30`, il doit y avoir les MAC qu'on voit dans la capture

> Les clients peuvent aussi `ping` le réseau "privé" des routeurs en `/30`. On ajouterait normalement une ACL (une règle de filtrage) pour éviter ça, sur les routeurs.

### C. Accès internet

➜ **Déjà accès internet ?**

🌞 **Prouvez que vous avez déjà un accès internet sur `r1`**
```
R1#ping 8.8.8.8
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
Success rate is 100 percent (5/5), round-trip min/avg/max = 60/79/96 ms
```

🌞 **Accès internet `LAN1`**
```
PC1> ping 8.8.8.8

184 bytes from 8.8.8.8 icmp_seq=1 ttl=254 time=29.417 ms
184 bytes from 8.8.8.8 icmp_seq=2 ttl=254 time=42.280 ms
184 bytes from 8.8.8.8 icmp_seq=3 ttl=254 time=31.102 ms
```

🌞 **Accès internet `LAN2`**
```
PC3> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=254 time=44.924 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=254 time=41.382 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=254 time=52.789 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=254 time=53.021 ms
```