+++
title = 'Injection SQL'
date = 2024-09-29T17:27:33-04:00
draft = false
weight = 51
+++

Lorsqu’une application envoit des données à un serveur, elles sont transmises au serveur via une requête http POST ou GET. Lorsque la requête est de type GET, les données sont transmises par l'url; lorsque c'est une requête POST, les données sont transmises comme des paramètres.

Parfois ces données permettent au serveur de construire une requête SQL qui sera exécutée sur une base de données; le résultat de cette requête permet au serveur de construire la page présentée ensuite côté client. 

Dans le formulaire d'authentification suivant, lorsque l’utilisateur clique sur le bouton du formulaire, le navigateur web transmet les données du formulaire au serveur; ensuite celui-ci construit une requête SQL qu'il envoit à la base de données du site:

![sqlinj](/420-513/images/sqlinj.png)

Dans un cas normal, cette requête SQL retournera une rangée de données s’il existe un utilisateur nommée `admin` avec `abc-123` comme mot de passe, ou ne retournera rien sinon. 

Lorsque le programme chargé de construire la requête se contente de copier les valeurs entrées dans les champs du formulaire sans les vérifier, il est possible de générer des requêtes qui retourneront des résultats non souhaités. Par exemple, si on entre `' and 1=1; --` dans le champ `username` du formulaire, ces données seront insérées dans la requête à la BD:

![sqlinj_ex1](/420-513/images/sqlinj_ex1.png)

Or les caractères « - - » désignent des commentaires en SQL, et donc tout ce qui suit sera ignoré. La requête qui sera interprétée par SQL sera donc :
```sql
SELECT * FROM users WHERE username='' or 1=1;
```
ce qui équivaut à :
```sql
SELECT * FROM users 
```
car `username='' or 1=1` est toujours vraie.

En utilisant cette technique, il devient possible d’injecter des commandes dans les requêtes et ainsi d’obtenir de nombreuses informations sur la structure de la base de données ou une foule d’autres informations qu’elle peut contenir. Par exemple, pour obtenir la version du service _MySQL_:
```sql
' or 1=1; select @@version; --
```

Pour tester si un site est vulnérable à une injection SQL ordinaire, on peut utiliser `' or 1=1; --`.

## Injections "aveugles"
Dans certains cas, l'injection ne permet pas d'obtenir directement des résultats visibles sur une page du site vulnérable: l'effet de l'injection peut seulement être observé de manière indirecte. Dans cette situation, si le serveur est vulnérable aux injections, il existe 2 méthodes pour en extraire des données:

- Injections basées sur le contenu
- Injections basées sur le délai de réponse

#### Basées sur le contenu
Lorsque le site vulnérable n'affiche pas directement le résultat de la requête, parfois tout ce qu'on peut obtenir comme information est si la requête SQL a retourné ou non un résultat. Par exemple, une requête qui ne retourne aucun résultat affichera une page 404 et une requête qui retourne un résultat affichera une page vide. On ne peut donc pas utiliser l'injection pour voir le contenu d'une table; mais on peut l'utiliser pour poser des questions à la BD du type _est-ce que le mot de passe de l'utilisateur xyz commence par 'm'?_. Ceci permet de reconstruire graduellement des informations comme des noms de tables, de colonnes ou les valeurs de certains champs.

Par exemple, si on injecte la chaîne de caractères suivante:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 'm
```
S'il existe un utilisateur nommé "Administrator" et que son mot de passe commence par "m", cette requête retournera un résultat.

> La fonction SUBSTRING prend 3 arguments: une chaîne de caractères, un indice et une taille. Par exemple `SUBSTRING('abcde',1,2)` retourne `ab`.

#### Basée sur le délai de réponse
Dans certaines bases de données il est possible de créer des délais artificiels dans le temps de réponse et d'utiliser ces délais pour vérifier si une requête retourne ou non des résultats. Pour le tester il s'agit d'injecter des requêtes comme celles-ci:

```sql
; if (1=1) waitfor delay '0:0:10' -- La condition est vraie, délai de 10 sec.
; if (1=2) waitfor delay '0:0:10' -- La condition est fausse, pas de délai de 10 sec.
```

Ainsi, si on remplace `1=1` par une expression SELECT qui peut être évaluée comme VRAIE ou FAUSSE, il y aura un délai de 10 secondes si ce SELECT retourne des rangées, et aucun délai s'il n'en retourne pas.

## SQLMap

L’outil _sqlmap_ permet d’automatiser de nombreux types d’attaques par injection SQL. Pour l’utiliser il s’agit de fournir l’url du formulaire à tester et les paramètres qui serviront à faire l’injection, comme suit :
```bash
└─$ sqlmap -u http://www.abc.com/login_app.php --method=POST --data="usr=aaaa&pass=bbbb"
```
Ensuite, on utilisera les options qui permettent d’obtenir des informations sur la base de données :
| Option | Description |
| ------ | ----------- |
| `--current-db`    | Affiche la base de données courante |
| `-D entreprise --tables`  | Affiche les tables de la BD ‘entreprise’ |
| `-D entreprise -T utilisateurs --dump` | Affiche le contenu de la table ‘utilisateurs de la BD ‘entreprise’ |

{{% notice tip "Exercice" %}}
Essayez de révéler le contenu de la BD de la VM _Metasploitable3_ avec _sqlmap_.
{{% /notice %}}

