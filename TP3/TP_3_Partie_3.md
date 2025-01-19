# III. Services dans le LAN

Allez, c'est la partie avec plein d'acronymes.

## Sommaire

- [III. Services dans le LAN](#iii-services-dans-le-lan)
  - [Sommaire](#sommaire)
  - [1. DHCP](#1-dhcp)
  - [2. DNS](#2-dns)
- ["C:\\Users\\antoi\\GNS3\\projects\\TP 3\\TP\_3\_GNS3\\TP3\_autres\_parties\\project-files\\captures\\requetes\_DNS\_filtrees\_etape\_3-TP3.pcap"](#cusersantoigns3projectstp-3tp_3_gns3tp3_autres_partiesproject-filescapturesrequetes_dns_filtrees_etape_3-tp3pcap)
  - [3. HTTP](#3-http)
  - [Go next](#go-next)

## 1. DHCP

> Vous commencez à être rôdés non ?

➜ **Configurez `dhcp.tp3.b2` pour qu'il attribue des IPs aux clients de son réseau**

- la range de votre choix
- il doit indiquer la passerelle du réseau aux clients
- il doit indiquer `1.1.1.1` commme serveur DNS aux clients

🌞 **Prouvez avec un VPCS**

- un ptit `ip dhcp` qui fonctionne
- suivi d'un `show ip`
- et un `ping efrei.fr` qui fonctionne

````
PC2> ip dhcp
DDORA IP 10.3.2.50/24 GW 10.3.2.254

PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.3.2.50/24
GATEWAY     : 10.3.2.254
DNS         : 1.1.1.1
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 43195, 43200/21600/37800
MAC         : 00:50:79:66:68:02
LPORT       : 20023
RHOST:PORT  : 127.0.0.1:20024
MTU         : 1500

PC2> ping www.efrei.fr
www.efrei.fr resolved to 51.255.68.208

84 bytes from 51.255.68.208 icmp_seq=1 ttl=51 time=64.372 ms
84 bytes from 51.255.68.208 icmp_seq=2 ttl=51 time=60.753 ms
84 bytes from 51.255.68.208 icmp_seq=3 ttl=51 time=72.137 ms
84 bytes from 51.255.68.208 icmp_seq=4 ttl=51 time=75.154 ms
````

## 2. DNS

➜ **Configurez `dns.tp3.b2` pour qu'il soir le serveur DNS du réseau**

🌞 **Tests résolutions DNS**

- vérifier qu'un client peut correctement récupérer une IP en DHCP
- et il peut immédiatement `ping efrei.fr` ainsi que `dns.tp3.b2`

````
PC4> ip dhcp
DORA IP 10.3.2.51/24 GW 10.3.2.254

PC4> sh ip

NAME        : PC4[1]
IP/MASK     : 10.3.2.51/24
GATEWAY     : 10.3.2.254
DNS         : 10.3.3.2
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 41259, 41261/20630/36103
MAC         : 00:50:79:66:68:03
LPORT       : 20027
RHOST:PORT  : 127.0.0.1:20028
MTU         : 1500

PC4> ping dns.tp3.b2
dns.tp3.b2 resolved to 10.3.3.2

84 bytes from 10.3.3.2 icmp_seq=1 ttl=63 time=19.311 ms
84 bytes from 10.3.3.2 icmp_seq=2 ttl=63 time=15.388 ms
^C
PC4> ping efrei.fr
efrei.fr resolved to 51.255.68.208

84 bytes from 51.255.68.208 icmp_seq=1 ttl=51 time=63.298 ms
^C
````

🌞 **Capture Wireshark**

# "C:\Users\antoi\GNS3\projects\TP 3\TP_3_GNS3\TP3_autres_parties\project-files\captures\requetes_DNS_filtrees_etape_3-TP3.pcap"

- une capture Wireshark où on voit les res deux requêtes DNS d'un de vos clients
- ainsi que les réponses
- vers les nom `ping efrei.fr` et `dns.tp2.b3`

## 3. HTTP

🌞 **Preuve avec un client**

- depuis une machine d'un autre LAN, visitez le site web
- en utilisant le nom du serveur `web.tp2.b3` et `supersite.tp2.b3`
- vous pouvez ajouter une machine avec un navigateur Web si vous voulez
- pour le compte-rendu, un simple `curl` suffira (requête HTTP en ligne de commande)

````
PC4> ping web.tp3.b2
web.tp3.b2 resolved to 10.3.3.3

84 bytes from 10.3.3.3 icmp_seq=1 ttl=63 time=20.700 ms
84 bytes from 10.3.3.3 icmp_seq=2 ttl=63 time=18.845 ms
^C
PC4> ping supersite.tp3.b2
supersite.tp3.b2 resolved to 10.3.3.3

84 bytes from 10.3.3.3 icmp_seq=1 ttl=63 time=20.394 ms
84 bytes from 10.3.3.3 icmp_seq=2 ttl=63 time=16.646 ms

````

`curl :`
````
[antoine@localhost]$ curl -s web.tp3.b2 | head -n 10

<!doctype html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>HTTP Server Test Page powered by: Rocky Linux</title>
<style type="text/css">
/*<![CDATA[*/
    html {
````


## Go next

Go sur la dernière partie : [**Partie 4 : Attaques sur les services en place**](./atk.md).