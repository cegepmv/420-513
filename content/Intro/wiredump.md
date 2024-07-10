+++
title = 'Wireshark et tcpdump'
date = 2024-07-09T14:07:43-04:00
draft = false
weight = 13
+++

Le programme _tcpdump_ sert à visualiser le contenu des communications qui passent sur une interface réseau. Les messages à destination de l'hôte local autant que ceux qui sont envoyés par l'hôte local peuvent être inspectés et analysés. Des fonctionnalités de filtrage avancées permettent d'isoler de manière très précise certains types de communication, ce qui fait de _tcpdump_ un outil essentiel pour diagnostiquer des problèmes ou rechercher des informations sur les services et les hôtes d'un réseau.

_Wireshark_ est une application graphique similaire à _tcpdump_. Voyez https://otardi.gitlab.io/420-312/5_wireshark/ pour plus de détails sur son fonctionnement.

## _tcpdump_


#### Choix de l'interface réseau
- **-D** : Pour voir la liste des interfaces disponibles
- **-i** : Pour spécifier une interface

```
┌──(kali㉿kali)-[~]
└─$ tcpdump -D     
1.eth0 [Up, Running, Connected]
2.any (Pseudo-device that captures on all interfaces) [Up, Running]
3.lo [Up, Running, Loopback]
4.bluetooth0 (Bluetooth adapter number 0) [Wireless]
5.bluetooth-monitor (Bluetooth Linux Monitor) [Wireless]
6.nflog (Linux netfilter log (NFLOG) interface) [none]
7.nfqueue (Linux netfilter queue (NFQUEUE) interface) [none]
8.dbus-system (D-Bus system bus) [none]
9.dbus-session (D-Bus session bus) [none]

┌──(kali㉿kali)-[~]
└─$ sudo tcpdump -i eth0
[sudo] password for kali: 
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
```

Si aucune interface n'est spécifiée _tcpdump_ écoute sur la première interface de la liste obtenue avec `-D`.


#### Fichier de capture
Il est possible d'enregistrer les captures plutôt que de les voir défiler à la console. Ceci permet de les analyser plus tard. Le format des fichiers sauvegardés est `.pcap`, un format qui peut également être lu par _Wireshark_.

- **-w** : Pour spécifier le fichier où sauvegarder la capture, ex: `tcpdump -w fichier.pcap`
- **-r** : Pour lire un fichier de capture

#### Filtres
Il est possible de définir des filtres afin de capturer les paquets selon différents critères, par exemple l'adresse IP de destination d'un message, le port local d'où vient un message, etc.

##### Types
Les filtres peuvent porter sur trois types d'éléments:
- **host** : Le critère de filtrage désigne un hôte 
- **net** : Le critère de filtrage désigne un réseau 
- **port** : Le critère de filtrage désigne un port 

Dans l'exemple suivant, on affiche les messages en provenance ou à destination de n'importe que hôte du réseau 10.200.45.0/24:
```
tcpdump net 10.200.45.0/24
```

##### Direction du trafic
- **src** : Affiche les paquets qui ont l'adresse passée comme origine
- **dst** : Affiche les paquets qui ont l'adresse passée comme destination

Afficher tous les paquets qui **viennent** de l'hôte 10.200.45.60:
```
tcpdump -i eth0 src host 10.200.45.60/24
```
Afficher tous les paquets à destination du port 443:
```
tcpdump -i eth0 dst port 80
```

##### Protocole
Pour filtrer par protocole, on utilise son nom. Attention, ce ne sont pas tous les protocoles qui peuvent être utilisés dans un filtre; la liste est la suivante: **ether, fddi, tr, wlan, ip, ip6, arp, rarp, decnet, sctp, tcp, udp**.

Par exemple, pour voir tous les messages ICMP:
```
tcpdump -i eth0 icmp
```

##### Opérateurs
Il est possible d'inclure plusieurs critères dans un même filtre en utilisant les opérateurs **and** et **or**.

Par exemple, pour afficher les paquets provenant de l'hôte 10.0.0.22 à destination du port tcp 8080: 
```
tcpdump src 10.0.0.22 and dst 8080 and tcp
```
Pour afficher les requêtes DNS sortantes ou les paquets à destination de 8.8.8.8: 
```
tcpdump '(udp and dst 53) or (dst 8.8.8.8)'
```
{{% notice style="note" title="Attention" %}}
Lorsque les filtres contiennent des parenthèses il faut utiliser les guillemets simples.
{{% /notice %}}

#### Références 
- https://www.tcpdump.org/manpages/tcpdump.1.html
- https://www.tcpdump.org/manpages/pcap-filter.7.html