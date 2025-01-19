# Partie II. Router-on-a-stick

Alors quoi, on ajoute un routeur + un switch dès qu'on rajoute un réseau ? Woah hé le bordel hé.

Et dans la vraie vie, c'est physique tout ça, ils sont où ces équipements ? Un routeur + un switch par salle ? Rich boi.

Et s'il y a des gens de réseaux différents dans la même salle, on fait comment ? (genre un serveur et Jean-Mi qui bosse à la compta dans la même pièce).

**Une topologie un peu plus classique pour ce genre de réseau c'est le *router-on-a-stick*** (tellement classique qu'on lui a donné un nom oè).

## 1. Schéma

## 2. Tableau d'adressage

### A. Réseaux

| Réseau | Adresse Réseau | VLAN |
| ------ | -------------- | ---- |
| `LAN1` | `10.3.1.0/24`  | 10   |
| `LAN2` | `10.3.2.0/24`  | 20   |
| `LAN3` | `10.3.3.0/24`  | 30   |

### B. Tableau adressage

| Machine       | `LAN1`       | `LAN2`       | `LAN3`       |
| ------------- | ------------ | ------------ | ------------ |
| `pc1.tp3.b2`  | `10.3.1.1`   | x            | x            |
| `pc2.tp3.b2`  | x            | `10.3.2.1`   | x            |
| `pc3.tp3.b2`  | `10.3.1.2`   | x            | x            |
| `pc4.tp3.b2`  | x            | DHCP         | x            |
| `dhcp.tp3.b2` | x            | `10.3.2.253` | x            |
| `r1.tp3.b2`   | `10.3.1.254` | `10.3.2.254` | `10.3.3.254` |
| `dns.tp3.b2`  | x            | x            | `10.3.3.1`   |
| `web.tp3.b2`  | x            | x            | `10.3.3.2`   |

## 3. Marche à suivre

### A. VLANs

➜ **On configure les switches en premier**

- cette fois, les switches vont avoir être responsable d'une partie de la logique
- on va y configurer des VLANs
- munissez-vous du [mémo Cisco](../../memo/cisco.md#a-vlan), y'a tout dedans !

➜ **Déclarer les VLANs**

- sur tous les switches, vous déclarez tous les VLANs

➜ **Définir les ports `access`**

- ce sont les ports avec des clients du réseau branchés
- donc les VPCS, les serveurs (VMs)
- cela permet de définir dans quel VLAN se trouve tel ou tel client du réseau

➜ **Définir les ports `trunk`**

- ce sont les ports avec d'autres équipements réseau connectés
- un port sur lequel y'a un autre switch connecté
- aussi un port sur lequel un routeur est connecté

🌞 **Tests de `ping`**

- une fois tous les VLANs en place, les membres d'un même VLAN sont autorisés à se `ping`
- prouvez que `client1` et `client3` peuvent se `ping`

--------------------------------------------------------------------------

`PC 1 vers PC 3`
````
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.682 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=1.176 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=0.815 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=1.162 ms
84 bytes from 10.3.1.2 icmp_seq=5 ttl=64 time=0.973 ms

````
`PC 2 vers PC 4`

````
PC2> ping 10.3.2.2

84 bytes from 10.3.2.2 icmp_seq=1 ttl=64 time=0.559 ms
84 bytes from 10.3.2.2 icmp_seq=2 ttl=64 time=0.798 ms
84 bytes from 10.3.2.2 icmp_seq=3 ttl=64 time=1.080 ms
84 bytes from 10.3.2.2 icmp_seq=4 ttl=64 time=0.884 ms
84 bytes from 10.3.2.2 icmp_seq=5 ttl=64 time=1.024 ms

````

### B. Routeur

➜ **On passe sur le routeur**

- on a un soucis : le routeur a une seule interface qui doit servir de passerelle pour tous les LANs
- il doit donc avoir plusieurs adresses IP sur une seule interface !
- on fait ça avec des sous-interfaces :
  - on "découpe" l'interface en plusieurs sous-interfaces
  - chacune des sous-interfaces a une adresse IP
  - chaque sous-interface est définie comme étant dans un VLAN particulier

➜ **Configuration IP du routeur**

- adresse IP DHCP côté nuage
- adresses IP sur les sous-interfaces qui pointent vers le LAN
- n'oubliez pas d'allumer les interfaces

🌞 **Tests de `ping`**

- le routeur peut ping tout le monde
- le `LAN1` et le `LAN2` se `ping`
  - prouvez le avec un `show ip` suivi d'un `ping` sur un VPCS

````
PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.3.2.1/24
GATEWAY     : 10.3.2.254
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20020
RHOST:PORT  : 127.0.0.1:20021
MTU         : 1500

PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=63 time=20.011 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=63 time=13.141 ms
84 bytes from 10.3.1.1 icmp_seq=3 ttl=63 time=27.810 ms
84 bytes from 10.3.1.1 icmp_seq=4 ttl=63 time=12.692 ms
84 bytes from 10.3.1.1 icmp_seq=5 ttl=63 time=15.577 ms


````

➜ **Configuration du NAT**

- configurez du NAT sur le routeur pour que les clients aient un accès Internet

🌞 **Tests de `ping`**

- le routeur peut ping internet
- n'importe quel client a un accès internet

````
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 68/87/96 ms
````
````
PC1> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=58 time=35.015 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=58 time=36.085 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=58 time=41.733 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=58 time=53.997 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=58 time=31.964 ms
````


## La suiiite

Direction la [**Partie 3 : Services**](./services.md), toujours avec cette topologie.