+++
title = 'Adressage IP'
date = 2024-07-09T14:06:54-04:00
draft = false
weight = 11
+++

Le protocole IP définit comment les hôtes peuvent communiquer entre eux dans un réseau informatique.

Il permet d'identifier ces hôtes au moyen d'une _adresse_. Les adresses IP peuvent avoir deux formats selon la version du protocole IP. 

En IPv4:
- Les adresses sont stockées sur 32 bits
- Il y a environ 4,3 milliards d'adresses possibles (2^32)
- Elles sont représentées par 4 nombres décimaux de 0 à 255 séparées par un point
- Exemples: 127.0.0.1, 10.200.0.121, etc.

En IPv6:
- Les adresses sont stockées sur 128 bits
- Il y a environ 3400 milliards de milliards de milliards de milliards d'adresses possibles (2^128) 
- Elles sont représentées par 8 nombres hexadécimaux de 0000 à FFFF séparées par `:`
- Exemple: 10a6:5228:5cde:0484:a000:b1ba:f00d:c788

#### Abréviation des adresses IPv6
Pour simplifier la notation en IPv6, il est possible de raccourcir les adresses. Il y a deux règles:
1. Les `0` à gauche peuvent être effacés: `0123` devient `123`; `000b` devient `b`, etc.
2. Lorsqu'une adresse contient une séquence de `0000`, celle-ci peut être remplacée par `::`. 


Exemples:
+ `c000:0800:fe33:000a:0000:0000:0000:1121` -> `c000:800:fe33:a::1121`
+ `fe88:0000:0000:0000:0000:0000:0000:00a1` -> `fe88::a1`

#### Masques
La partie de gauche d'une aadresse IP identifie le réseau dont un hôte fait partie, et la partie de droite identifie l'hôte lui-même.

Par exemple, si j'ai deux hôtes sur un même réseau, leurs adresses pourraient être 10.50.177.27 et 10.50.177.201. 
Ces hôtes sont donc identifiés par les nombres `27` et `201`, et le réseau est `10.50.177.0`. La partie de l'adresse qui désigne le réseau contient donc ici 3 octets et celle qui désigne les hôtes en contient 1; comme il n'y a qu'un octet pour désigner les hôtes, ce réseau ne peut pas contenir plus de 255 hôtes.

Il est possible de changer la taille relative des parties de l'adresse qui désignent le réseau et les hôtes, si on a besoin de plus ou moins d'adresses pour les hôtes. Par exemple on pourrait préférer que le réseau soit désigné par `10.50` et les hôtes seraient par exemple `0.1` et `166.23`. Il y aurait donc ici 2 octets pour désigner les hôtes, soit 65 535 adresses.

C'est le _masque_ d'une adresse IP qui détermine la taille de la partie qui désigne le réseau. On peut représenter ce masque par un nombre de bits (notation "CIDR") ou par la valeur décimale qui correspond à ces bits. Par exemple, si dans l'adresse 10.50.177.201 les 3 premiers octets désignent le réseau, 3 octets = 24 bits et donc le masque vaut `/24` (CIDR) ou `255.255.255.0`. Si pour la même adresse ce sont les 2 premiers octets qui désignent le réseau, 2 octets = 16 bits et donc le masque vaut `/16` (CIDR) ou `255.255.0.0`.

En IPv6, la notion de masque existe mais elle est fixée à 64 bits. Donc dans l'adresse `abcd:ef12:3456:7890:0000:0000:0000:0022`, l'adresse du réseau est `abcd:ef12:3456:7890::/64`


{{% notice style="info" %}}
Pour calculer les masques plus complexes et visualiser leurs effets, de nombreux sites existent, notamment https://jodies.de pour IPv4.
{{% /notice %}}

#### Références 
https://otardi.gitlab.io/420-312/3_adressage_ip/3.2_adresses_mac_vs_ip/ 









