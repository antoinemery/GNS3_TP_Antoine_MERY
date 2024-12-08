# TP1 : Compte rendu Antoine MERY B2-CS-G2

Les objectifs de cette partie :

- monter la topologie dans GNS3 *(drag'n'drop des machins et tirer des câbles dans GNS3)*
- définir des IPs sur les deux machines
- visualiser l'IP choisie, ainsi que l'adresse MAC prédeterminée
- vérifier que les deux machines peuvent communiquer en faisant un `ping`
- visualiser le `ping` avec Wireshark

Nous utiliserons les IPs suivantes :
````
| Machine           | Adresse IP    |
| ----------------- | ------------- |
| `node1.tp1.efrei` | `10.1.1.1/24` |
| `node2.tp1.efrei` | `10.1.1.2/24` |
````
**Déterminer l'adresse MAC de vos deux machines**

**MAC address node1.tp1.efrei :**

- VPCS> show
````
| NAME  | IP MASK      | Gateway         | MAC               | LPORT  | RHOST:PORT      |
| ----- | ------------ | --------------- | ----------------- | ------ | --------------- |
| VPCS1 | 10.1.1.1/24  | 255.255.255.0   | 00:50:79:66:68:00 | 20000  | 127.0.0.1:20001 |
| ----- | ------------ | --------------- | ----------------- | ------ | --------------- |
````
**L'adresse MAC de node1.tp1.efrei est `00:50:79:66:68:00`.**

**MAC address node2.tp1.efrei :**

- VPCS> show
````
| NAME  | IP MASK      | Gateway         | MAC               | LPORT  | RHOST:PORT      |
| ----- | ------------ | --------------- | ----------------- | ------ | --------------- |
| VPCS1 | 10.1.1.2/24  | 255.255.255.0   | 00:50:79:66:68:01 | 20002  | 127.0.0.1:20003 |
| ----- | ------------ | --------------- | ----------------- | ------ | --------------- |
````
**L'adresse MAC de node1.tp1.efrei est `00:50:79:66:68:01`.**

------------------------------------------------------------------------

**Définir une IP statique sur les deux machines**

**IP statique node1.tp1.efrei :**
```
# This the configuration for node1.tp1.efrei

# Uncomment the following line to enable DHCP

# dhcp

# or the line below to manually setup an IP address and subnet mask

ip 10.1.1.1 255.255.255.0

set pcname node1.tp1.efrei
````

**IP statique node2.tp1.efrei :**
```
# This the configuration for node1.tp1.efrei

# Uncomment the following line to enable DHCP

# dhcp

# or the line below to manually setup an IP address and subnet mask

ip 10.1.1.2 255.255.255.0

set pcname node1.tp1.efrei
````
  - prouver que votre changement d'IP est effectif, en une commande :
    - `show ip`

**Effectuer un `ping` d'une machine à l'autre**

````
VPCS> ping 10.1.1.1
84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=0.188 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=0.274 ms
84 bytes from 10.1.1.1 icmp_seq=3 ttl=64 time=0.340 ms
84 bytes from 10.1.1.1 icmp_seq=4 ttl=64 time=0.333 ms
84 bytes from 10.1.1.1 icmp_seq=5 ttl=64 time=0.394 ms
````
````
VPCS> ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.188 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=0.274 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=0.340 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=0.333 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=0.394 ms
````
- c'est la commande `ping`, sans surprise vous me direz

------------------------------------------------------------------------

**Wireshark !**

C'est le protocole **ICMP** qui est utilisé pour le message **ping**.

**ARP**
 ````
PC1> arp

00:50:79:66:68:01  10.1.1.2 expires in 108 seconds
 ````

````        
PC2> arp

00:50:79:66:68:00  10.1.1.1 expires in 106 seconds
 ````

# II. Ajoutons un switch

**Déterminer l'adresse MAC de vos trois machines**

````
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         : 
MAC         : 00:50:79:66:68:00
LPORT       : 20002
RHOST:PORT  : 127.0.0.1:20003
MTU         : 1500
````

````
PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         : 
MAC         : 00:50:79:66:68:01
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
````

````
PC3> show ip

NAME        : PC3[1]
IP/MASK     : 10.1.1.3/24
GATEWAY     : 0.0.0.0
DNS         : 
MAC         : 00:50:79:66:68:02
LPORT       : 20010
RHOST:PORT  : 127.0.0.1:20011
MTU         : 1500
````
**Définir une IP statique sur les trois machines**

````
PC3> ip 10.1.1.3/24

Checking for duplicate address...

PC3 : 10.1.1.3 255.255.255.0
````

````
PC3> show ip
NAME        : PC3[1]
IP/MASK     : 10.1.1.3/24
GATEWAY     : 0.0.0.0
DNS         : 
MAC         : 00:50:79:66:68:02
LPORT       : 20010
RHOST:PORT  : 127.0.0.1:20011
MTU         : 1500
````

**Effectuer des `ping` d'une machine à l'autre**

````
PC3> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=2.453 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=0.547 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=0.702 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=0.451 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=0.334 ms
````

````
PC1> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=0.471 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=1.475 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=0.353 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=0.437 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=0.376 ms
````

# III. Serveur DHCP

**Donner un accès Internet à la machine `dhcp.tp1.efrei`**

````
[antoine@efrei-xmg5afau6~]$ ping google.com
PING google.com (216.58.214.174) 56(84) bytes of data.
64 bytes from mad01s26-in-f174.1e100.net (216.58.214.174): icmp_seq q = 1 ttl I = 63 time 37.0 ms
64 bytes from par10s42-in-f14.1e100.net (216.58.214.174): icmp_seq=2 ttl I = 63 time=29.4 ms
````

**Installer et configurer un serveur DHCP**

- On commence par installer **dhcp-server**.
````
dnf-y install dhcp-server
vi /etc/dhcp/dhcpd.conf
systemctl enable --now dhcpd
````
- Ensuite on attribue une IP **fixe** au serveur.
````
authoritative;
subnet 10.1.1.0 netmask 255.255.255.0 {
	range 10.1.1.10 10.1.1.50;
	option broadcast-address 10.1.1.1;
	option routers 10.1.1.1;
}
````
**Récupérer une IP automatiquement depuis les 3 nodes**

````
PC1> dhcp
DORA IP 10.1.1.10/24 GW 10.1.1.1

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.10/24
GATEWAY     : 10.1.1.1
DNS         : 
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 563, 570/285/498
MAC         : 00:50:79:66:68:01
LPORT       : 20007
RHOST:PORT  : 127.0.0.1:20008
MTU         : 1500
````
````
PC2> dhcp
DDORA IP 10.1.1.12/24 GW 10.1.1.1

PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.1.1.12/24
GATEWAY     : 10.1.1.1
DNS         : 
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 594, 599/299/524
MAC         : 00:50:79:66:68:00
LPORT       : 20009
RHOST:PORT  : 127.0.0.1:20010
MTU         : 1500
````
````
PC3> dhcp
DDORA IP 10.1.1.11/24 GW 10.1.1.1

PC3> show ip

NAME        : PC3[1]
IP/MASK     : 10.1.1.11/24
GATEWAY     : 10.1.1.1
DNS         : 
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 584, 599/299/524
MAC         : 00:50:79:66:68:02
LPORT       : 20011
RHOST:PORT  : 127.0.0.1:20012
MTU         : 1500
````

**Wireshark !**

# METTRE 2eme capture WIRESHARK !!!!

**Configurez dnsmasq**
- insallation de dnsmasq

  ````dnf install -y dnsmasq````
- configuration
  ````
  port=0 # pour désactiver DNS
  dhcp-range=10.1.1.210,10.1.1.250,255.255.255.0,12h
  dhcp-authoritative
  interface=enp0s3
  ````

**Test !**
````
PC4> dhcp
DDORA IP 10.1.1.248/24 GW 10.1.1.50
````
````
PC4> show ip

NAME        : PC4[1]
IP/MASK     : 10.1.1.248/24`
````

**Now race !**
````
PC2> dhcp
DDORA IP 10.1.1.246/24 GW 10.1.1.50
````
````
PC3> dhcp
DDORA IP 10.1.1.247/24 GW 10.1.1.50
````
````
PC4> dhcp
DORA IP 10.1.1.21/24 GW 10.1.1.1
````
🌞 **Wireshark !**

# BLOQUE

## 3. BONUS : DHCP starvation

Une attaque très débile et simple à mettre en place pour **DOS l'accès à un LAN** s'il n'y a pas de protections particulières. **C'est naze, mais c'est là** :d

Le principe est simple : **faire de multiples échanges DORA avec le serveur DHCP pour récupérer toutes les IP disponibles dans le réseau.**

On usurpe une adresse MAC (qu'elle existe ou non), on demande une adresse IP, on la récupère (merci). On répète l'opération avec une nouvelle fake adresse MAC, une nouvelle IP (merci). Etc. Jusqu'à épuiser toutes les adresses de la range.

Il existe des tools pour faire ça, vous pouvez aussi essayer (recommandé) de le **coder vous-mêmes avec Scapy** (une dinguerie cette lib) : on peut forger à peu près tout et n'importe quoi comme trame, et très facilement, avec Scapy.
