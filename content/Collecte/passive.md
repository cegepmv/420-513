+++
title = 'Passive'
date = 2024-07-10T13:25:00-04:00
draft = false
weight = 21
+++

La _reconnaissance passive_ concerne les informations qu’on peut se procurer sans interagir avec la cible; généralement (mais pas toujours) ces informations sont de nature publique et ne sont pas confidentielles. Quelques exemples :
+ Les adresses IP des serveurs d’une entreprise;
+ Les hauts dirigeants d’une entreprise;
+ Les projets sur lesquels travaillent des personnes;
+ L’adresse email de travail d’un individu;
+ Les domaines, sous-domaines et noms d’hôtes


#### Google _dorks_

Les moteurs de recherche (_Google, Bing_ ou autres), ou encore d’autres plateformes ayant un outil de recherche (_GitHub, StackOverflow_ ou _ServerFault_, etc.) disposent souvent de nombreux filtres qui permettent d’accéder à des informations de manière très ciblée :

`filetype, ext, content` permettent de rechercher des types de fichiers spécifiques, ou des fichiers ayant une certaine extension, ou d’un certain type de contenu. Pour trouver des fichiers Word contenant le mot « employés » :
```
filetype:docx employés
```

`site, domain` limitent la recherche à un certain site ou domaine. Pour trouver les pages dans le domaine _cegepmv.ca_ contenant le terme « cybersécurité » :
```
domain:cegepmv.ca cybersécurité
```

`intitle, inurl` signifient que le terme recherché doit être dans le titre ou l’URL de la page. Pour trouver des pages nommées « login » contenant les termes « powered by Drupal » :
```
intitle:login "powered by drupal"
```

Il est évidemment possible de combiner ces filtres autant qu’on veut. Par exemple, pour trouver si le domaine _abc.com_ expose des pages qui contiennent des formulaires de connexion, on pourrait lancer la recherche suivante :
```
inurl:admin.php domain:abc.com
```

{{% notice info %}}
Les développeurs de Kali maintiennent une base de données des filtres de recherches utiles à des fins de recherche d’informations: https://www.exploit-db.com/google-hacking-database
{{% /notice %}}

#### whois

Utilitaire sur la ligne de commande linux qui retourne les informations relatives aux enregistrements de domaines DNS ou d’adresses IP. Celles-ci peuvent inclure les coordonnées de l’entreprise ou organisation responsable de ces adresses ou domaines, des noms et adresses de courriel d’employés, ou des noms de serveurs. 

Pour obtenir des informations sur un domaine:
```
whois cegepmv.ca
```

Pour obtenir des informations sur une adresse IP:
```
whois 15.156.222.71
```

#### theHarvester

_theHarvester_ est un programme qui recherche des informations à propos d’un domaine sur le web à partir de différents moteurs de recherche. 

Il n'y a pas de manuel pour cet outil, mais l'option `-h` permet de voir l'aide en ligne et les options possibles.

Les paramètres minimums sont `-d` pour le domaine à chercher et `-b` pour le moteur de recherche utilisé. Par exemple :
```
theHarvester -d abc.ca -b bing
```

Dans la liste des moteurs de recherche, on retrouve quelques sites spécialisés en cybersécurité (e.g. _threatminer_, _virustotal_). Ces moteurs de recherche permettent de vérifier si un site ou un domaine est compromis; lorsqu'on les utilise avec theHarvester c'est donc souvent pour vérifier si un domaine est associé à une menace, par exemple du spam ou un rançongiciel.

Quelques options utiles:
- `-r` : Résolution DNS. Donne les adresses IP associées à chaque sous-domaine.
- `-f` : Sauvegarder les résultats. Doit être suivi du nom de fichier.
- `-screenshot` : Prend une capture d'écran pour chaque sous-domaine. Doit être suivi d'un nom de répertoire.
  
{{% notice warning "Attention" %}}
La plupart des moteurs de recherche sont protégés contre les requêtes scriptées trop fréquentes : il est possible que _theHarvester_ soit rapidement bloqué.
 {{% /notice %}}


