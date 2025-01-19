# # TP2 : Routage, DHCP et DNS

🌞 **Configuration de `router.tp2.efrei`**
````
[antoine@localhost network-scripts]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:83:c0 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.166/24 brd 192.168.122.255 scope global dynamic noprefixroute enp0s3
       valid_lft 3125sec preferred_lft 3125sec
    inet6 fe80::a00:27ff:fe08:83c0/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:19:20:4e brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.254/24 brd 10.2.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe19:204e/64 scope link 
       valid_lft forever preferred_lft forever
[antoine@localhost network-scripts]$
````
🌞 **Configuration de `node1.tp2.efrei`**
````
[antoine@localhost network-scripts]$ ping 10.2.1.254
PING 10.2.1.254 (10.2.1.254) 56(84) bytes of data.
64 bytes from 10.2.1.254: icmp_seq=1 ttl=64 time=7.40 ms
64 bytes from 10.2.1.254: icmp_seq=2 ttl=64 time=6.69 ms
64 bytes from 10.2.1.254: icmp_seq=3 ttl=64 time=12.3 ms
^C
--- 10.2.1.254 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2006ms
rtt min/avg/max/mdev = 6.694/8.788/12.275/2.482 ms
[antoine@localhost network-scripts]$ tracepath 10.2.1.254
 1?: [LOCALHOST]                                         pmtu 1500
 1:  _gateway                                             11.441ms !H
 1:  _gateway                                              9.034ms !H
     Resume: pmtu 1500
[antoine@localhost network-scripts]$
````
````
[antoine@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=38.0 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=39.5 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=37.8 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=114 time=35.4 ms
^C
````
**utilisez une commande traceroute pour prouver que vos paquets passent bien par router.tp2.efrei avant de sortir vers internet**

🌞 **Afficher la CAM Table du switch**
````
Switch#
Switch#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0800.2719.204e    DYNAMIC     Et0/0
   1    0800.271a.956b    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2
Switch#
````

🌞 **Install et conf du serveur DHCP** sur `dhcp.tp2.efrei`
````
"/etc/dhcp/dhcpd.conf" 14L, 313B written
[antoine@localhost ~]$ sudo systemctl enable dhcpd
[ 4215.432688] systemd-rc-local-generator[11294]: /etc/rc.d/rc.local is not marked executable, skipping
[antoine@localhost ~]$ sudo systemctl start dhcpd
[antoine@localhost ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Tue 2024-12-10 17:30:27 CET; 5min ago
       Docs: man:dhcpd(8)
   Main PID: 11268 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 23139)
     Memory: 5.2M
        CPU: 9ms
     CGroup: /system.slice/dhcpd.service
             └─11268 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Config file: /etc/dhcp/dhcpd.conf
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Database file: /var/lib/dhcpd/dhcpd.leases
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: PID file: /var/run/dhcpd.pid
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Source compiled to use binary-leases
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Wrote 0 deleted entries to leases file.
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Listening on LPF/enp0s8/08:00:27:ee:91:b5/10.2.1.0/24
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Sending on   LPF/enp0s8/08:00:27:ee:91:b5/10.2.1.0/24
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Sending on   Socket/fallback/fallback-net
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Server starting service.
Dec 10 17:30:27 localhost.localdomain systemd[1]: Started DHCPv4 Server Daemon.
[antoine@localhost ~]$
````

🌞 **Test du DHCP** sur `node1.tp2.efrei`

- enlevez toute config IP effectuée au préalable
- vous pouvez par exemple `sudo nmcli con del enp0s3` s'il s'agit de l'interface `enp0s3` pour supprimer la conf liée à `enp0s3`
- configurez l'interface pour qu'elle récupère une IP dynamique, c'est à dire avec DHCP
- vérifiez que :
- l'IP obtenue est correcte
- votre table de routage a bien été mise à jour automatiquement avec l'adresse de la passerelle en route par défaut (votre option DHCP a bien été reçue !)
- vous pouvez immédiatement joindre internet
  
````
Mon ordinateur ayant un problème encore non résolu (obligation de reparamétrer un routeur a chaque démarrage de GNS3) il est donc difficile pour moi de continuer dans de bonnes conditions le TP 2...
````

# PARTIE NON TRAITEE :(

🌟 **BONUS**

- ajouter une autre ligne dans la conf du serveur DHCP pour qu'il donne aussi l'adresse d'un serveur DNS (utilisez `1.1.1.1` comme serveur DNS : c'est l'un des serveurs DNS de CloudFlare, un gros acteur du web)

🌞 **Wireshark it !**

- je veux une capture Wireshark qui contient l'échange DHCP DORA
- vous hébergerez la capture dans le dépôt Git avec le TP


# III. ARP

## 1. Les tables ARP

🌞 **Affichez la table ARP de `router.tp2.efrei`**

````
````

🌞 **Echange ARP avec Wireshark**

## 2. ARP poisoning

🌞 **Envoyer une trame ARP arbitraire**

````
````

🌞 **Mettre en place un ARP MITM**
````
````

🌞 **Capture Wireshark `arp_mitm.pcap`**

🌞 **Réaliser la même attaque avec Scapy**
````
```` 
````
````
