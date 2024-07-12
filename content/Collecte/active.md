+++
title = 'Active'
date = 2024-07-10T13:25:08-04:00
draft = false
weight = 22
+++

La reconnaissance active est caractérisée par le fait que les informations sont obtenues à la suite d’une interaction avec des entités reliées à la cible. Elle peut englober les opérations d’ingénierie sociale; ici cependant nous allons voir quelques outils sur Kali qui touchent à l’énumération des domaines et des services.

{{% notice warning "Attention" %}}
La reconnaissance active n'est pas illégale en soi. Par contre, la loi punit l'utilisation d'outils informatiques de tous types lorsqu'ils sont utilisés "dans l'intention de commettre une infraction" [(Code Criminel [1985] 342.1)](https://laws-lois.justice.gc.ca/fra/lois/c-46/section-342.1.html). 

Si les administrateurs d'un site vous soupçonnent de vouloir commenttre un acte criminel, il ont le droit de déposer une plainte qui pourra être suivie d'une enquête, et cette enquête peut aboutir à votre arrestation. À vous ensuite de convaincre la justice que vous étiez bien intentionné...

Assurez-vous donc d'avoir la permission avant d'utiliser ces outils sur des hôtes que vous ne contrôlez pas.
{{% /notice %}}


## dnsenum

Dans la section précédente, nous avons vu comment utiliser _theHarvester_ pour obtenir des informations sur un domaine. Ces informations sont obtenues de manière passive car _theHarvester_ interroge des moteurs de recherche. Dans cette section par contre, elles sont obtenues de manière active: _dnsenum_ peut en effet interroger directement des serveurs DNS d'un domaine.

Le programme _dnsenum_ utilise différentes techniques pour énumérer et classer les informations relatives à un domaine, par exemple:
+ Tente de transférer les zones DNS d'un serveur. Un transfert de zone réussi permet de voir les noms et adresses de tous les hôtes qui font partie du domaine.
+ Attaque "par dictionnaire" sur les noms de zone. Permet aussi de voir les noms et adresses des hôtes d'un domaine, mais en en interrogeant un serveur DNS à partir d'une liste de noms.
+ Requêtes DNS inverses à partir des plages d'adresses trouvées.
+ Recherche _google_ à partir des noms de domaine

Le programme _dnsenum_ a plusieurs options; en voici quelques unes:
+ `-h` : Afficher l'aide en ligne
+ `--dnsserver` : Spécifie le serveur à interroger
+ `--noreverse` : Ne pas faire de requête DNS inverse
+ `-f` : Spécifie le fichier qui contient les noms de sous-domaines à "bruteforcer" (si absent, `/usr/share/dnsenum/dns.txt` est utilisé)
+ `-r` : Recherche récursive. Pour chaque sous-domaine trouvé, relance l'attaque par force brutale.


## nmap

Un outil essentiel permettant de découvrir les hôtes d’un réseau et de collecter de nombreuses informations sur ces derniers.

Avec _nmap_ on peut par exemple détecter les services qui s’exécutent sur un serveur (par exemple ftp, http, ssh, etc.) et la version de ceux-ci. On peut contrôler l’intensité du balayage afin de ne pas saturer un réseau et éveiller les soupçons des systèmes de surveillance en place. Aussi, _nmap_ dispose d’un mécanisme de scripts permettant de cibler et présenter des informations très spécifiques, par exemple de savoir si un serveur web est vulnérable aux « CSRF ».
Les options de la commande _nmap_ sont très nombreuses, mais on peut les regrouper en deux catégories :

```
nmap  [paramètres]  [cible] 
```

Les **paramètres** regroupent le type de balayage : veut-on chercher les hôtes d’un réseau, ou alors les ports ouverts sur un hôte spécifique? Veut-on rechercher les ports TCP ou UDP? Veut-on que le balayage soit discret ou lance plusieurs sondes en parallèle? Etc.

La **cible** désigne un ou plusieurs hôtes d’un réseau ou un ou plusieurs ports sur les hôtes. 

##### Quelques exemples de base
Pour découvrir (en utilisant « ping ») tous les hôtes sur le réseau 192.168.0.0/24 :

```
└─$ sudo nmap -sn 192.168.0.0/24
```

Pour découvrir les ports (les plus communs, en tentant d’ouvrir une connexion TCP) ouverts sur l’hôte 192.168.0.106 :

```
└─$ sudo nmap -sT 192.168.0.106
```
Par défaut _nmap_ tentera d'accéder seulement aux 1000 ports les plus communément utilisés. Si on souhaite analyser les autres ports il faudra les spécifier explicitement avec l'option `-p`. On peut les énumérer en les séparant d'une virgule ou encore définir une plage d'adresses avec un tiret. Par exemple, pour les ports 11, 12, 13, 14 et 15, on pourrait utiliser les deux commandes suivantes:

```
└─$ sudo nmap -sS -p11,12,13,14,15 192.168.0.106
└─$ sudo nmap -sS -p11-15 192.168.0.106
```

Les options `-sS` et `-sT` (et quelques autres; voir la documentation) utilisent différentes techniques pour détecter les ports ouverts; l'option `-sV` quant à elle tente de déterminer la version du service qui s'exécute sur un port. Par exemple pour connaître la version du service HTTPS (au port TCP 443) sur l’hôte :

```
└─$ sudo nmap -sV -p443 192.168.0.106
```

Le programme _nmap_ vient avec plusieurs scripts qui peuvent être exécutés par _nmap_ pour trouver des informations très spécifiques. Ces scripts sont dans regroupés dans `/usr/share/nmap/scripts/`; la documentation est disponible sur https://nmap.org/nsedoc/scripts/.

Pour lancer le script permettant de vérifier si la cible à 192.168.0.106 est affectée par la vulnérabilité _ms17-010_ « EternalBlue » :

```
└─$ sudo nmap -p443 --script smb-vuln-ms17-010.nse 192.168.0.106
```

Enfin, pour obtenir toutes les informations possibles sur l’hôte (cette commande peut mettre un certain temps à se terminer) :

```
└─$ sudo nmap -A 192.168.0.106
```

