+++
title = 'Fuzzing'
date = 2024-07-11T12:48:46-04:00
draft = false
weight = 32
+++

Le _**fuzzing**_ est une technique qui consiste à automatiser l’envoi de données à un système (une fonction, un exécutable, une application, des serveurs web, etc.) afin de voir comment répond ce système et ainsi obtenir des indices sur son fonctionnement.

Le _fuzzing_ est souvent utilisé pour déboguer des programmes en testant comment ceux-ci réagissent face à des données erronées ou aléatoires; mais dans le contexte de la cybersécurité il s'apparente à une attaque par **dictionnaire**. 

Dans une attaque par dictionnaire, on utilise des listes de mots pour tester l'existence d'utilisateurs, de mots de passe, de répertoires, etc. Par exemple si on recherche tous les URL possibles sur le site _http&#58;//www.abc.ca_, on peut utiliser une liste de mots pour générer les URLs suivants et tenter d'ouvrir la page correspondante:
+ http&#58;//www.abc.ca/**access**
+ http&#58;//www.abc.ca/**accounting**
+ http&#58;//www.abc.ca/**action**
+ http&#58;//www.abc.ca/**activities**
+ ...

Dans une attaque par _fuzzing_ on utilise des listes de mots ou des séquences numériques pour remplacer des paramètres dans une URL. Par exemple si j'ai une page dont le lien est _http&#58;//www.abc.ca/index.php?id=211_, on peut automatiser la génération des liens suivants pour tester les pages du site:
+ http&#58;//www.abc.ca/index.php?id=**1**
+ http&#58;//www.abc.ca/index.php?id=**2**
+ http&#58;//www.abc.ca/index.php?id=**3**
+ http&#58;//www.abc.ca/index.php?id=**4**
+ ...

Les outils présentés dans cette section utilisent ces techniques.

{{% notice info %}}
Dans Kali, le répertoire `/usr/share/wordlists` contient plusieurs listes de mots distribués avec les programmes présentés dans cette section. 
{{% /notice %}}

## _dirb_
Outil visant à énumérer, à partir d’une liste de mots, les répertoires publics sur un serveur http. Par défaut **dirb** utilise `/usr/share/dirb/wordlists/common.txt`, mais on peut en spécifier plusieurs à la suite de l’URL. Par défaut la recherche est récursive.

#### Syntaxe
`dirb http://URL LISTE OPTIONS`

#### Options utiles
+ `-r`: Ne pas faire de recherche récursive
+ `-X`: Suivi d’une extension de fichier, recherchera les fichiers ayant cette extension dans l’URL donnée à partir de la liste de mots

#### Exemples
Cherche les sous-répertoires du site à partir de la liste par défaut, `common.txt`:
```
dirb http://www.monsite.org
```
Cherche les sous-répertoires immédiatement sous `http://www.monsite.org/boutique` à partir des mots de la liste `repertoires.txt`:
```
dirb http://www.monsite.org/boutique /home/kali/repertoires.txt -r
```
Cherche les fichiers `.txt` dont le nom fait partie de la liste `fichiers.lst`:
```
dirb http://www.monsite.org /home/kali/fichiers.lst -X .txt
```
-------------------
## _gobuster_
Outil permettant d’énumérer les répertoires, sous-domaines, hôtes virtuels et autres chaînes de caractères d’un URL.

#### Syntaxe
`gobuster COMMANDE OPTIONS`

Les commandes possibles sont:
+ **dir** : Rechercher les sous-répertoires
+ **dns** : Rechercher les sous-domaines
+ **fuzz** : Remplacer des parties de l'URL par des mots d'une liste
+ **vhost** : Recherche d'hôtes virtuels

#### Options utiles
+ `-u`: L’URL à tester. Peut inclure des sous-répertoires.
+ `-w`: La liste de mots à utiliser.

#### Exemples
Chercher les sous-répertoires de _&nbsp;www.monsite.org_, par exemple `www.monsite.org/abc`, `www.monsite.org/def`, etc.
```
gobuster dir -u www.monsite.org -w /usr/share/dirb/wordlists/common.txt
```
Chercher les sous-domaines de _monsite.org_, par exemple `abc.monsite.org`, `def.monsite.org`, etc.
```
gobuster dns -d monsite.org -w /usr/share/spiderfoot/dicts/subdomains.txt
```
Remplacer la valeur du paramètre `psw` par les mots de la liste `mdp.txt`:
```
gobuster fuzz -u www.monsite.org/login.php?user=admin&psw=FUZZ -w mdp.txt
```

-------------------
## _wfuzz_
Génère des url afin d’énumérer les pages d’un site. Les façons de générer des url sont nombreuses: plages d’adresses IP, mots dans une liste ou dans un fichier, permutations de caractères, etc. Le terme « FUZZ » dans l’url sera remplacé par la chaîne de caractère généré; "FUZ2Z" s'il faut faire un 2e remplacement.

#### Syntaxe
`wfuzz OPTIONS URL`


#### Options utiles

+ `-c`: Affichage coloré
+ `-z`: Doit être suivi du _payload_, i.e. la manière choisie pour générer l’URL. 
+ `-hc`: N'affiche pas les réponses qui contiennent un certain code HTTP (par exemple 404)
+ `-sc`: Affiche uniquement les réponses qui contiennent un certain code HTTP (par exemple 200)
+ `--dry-run`: Génère les url sans faire de requêtes. Utile pour tester les payloads.

Le _payload_ a le format NOM,PARAM, ou NOM est son nom (par exemple `list`, `range`, `name`, etc.) et PARAM ses paramètres: par exemple le payload `list,a-b-c-xyz` pour l'URL `http://www.monsite.ca/FUZZ` génèrera les liens suivants:
+ _http&#58;//www.monsite.ca/a_
+ _http&#58;//www.monsite.ca/b_
+ _http&#58;//www.monsite.ca/c_
+ _http&#58;//www.monsite.ca/xyz_

Pour obtenir la liste des _payloads_ possibles et leurs paramètres, faites `wfuzz -z help`.

#### Exemples
Pour remplacer la valeur de `pass` par les éléments dans le fichier _mdp.txt_:
```
wfuzz -c -z file,mdp.txt "http://www.site.com/log.php?user=admin&pass=FUZZ"
```
Pour générer des noms de sous-répertoires à partir d'une intervalle de nombres (_http&#58;//www.abc.org/item-1/_, _http&#58;//www.abc.org/item-2/_, etc.):
```
wfuzz -c -z range,1-10 "http://www.abc.org/item-FUZZ/"
```
Pour générer des noms d'utilisateur à partir de "Peter Parker" et les mots de passe à partir d'un fichier:
```
wfuzz -c -z name,Peter-Parker -z file,mdp.txt "http://www.site.com/log.php?user=FUZZ&pass=FUZ2Z"
```

-------------------
## _ffuf_

Le programme _ffuf_ combine certaines fonctionnalités des programmes précédents, tout en étant plus rapide et un peu plus simple d'utilisation. Il ne permet pas de générer des URL aussi complexes que _wfuzz_ par contre.

#### Syntaxe
`ffuf OPTIONS`

#### Options utiles
+ `-c`: Affichage en couleurs
+ `-u`: Spécifie l'URL cible. Doit conteni le mot-clé "FUZZ"
+ `-w`: Spécifie le fichier qui contient la liste de mots
+ `-fs`: N'affiche pas les résultats ayant une certaine taille (en octets). Utile pour éliminer les pages qui ne contiennent pas d'informations utiles.
+ `-X`: Suivi de "POST", permet de générer des requêtes POST
+ `-d`: Spécifier la chaîne de paramètres POST
+ `-e`: Spécifier une extension de fichier à rechercher

#### Exemples
Usage de base: rechercher des sous-répertoires à partir d'une liste de mots:
```
ffuf -c -u http://www.magoo.com/FUZZ -w liste.txt
```
Lancer des requêtes POST pour le paramètre `user`:
```
ffuf -c -u http://www.magoo.com/ -X POST -d "user=FUZZ" -w liste.txt
```
Rechercher des fichiers `.conf` dans le sous-répertoire `/local`:
```
ffuf -c -u http://www.magoo.com/local/FUZZ -e .conf -w liste.txt
```
