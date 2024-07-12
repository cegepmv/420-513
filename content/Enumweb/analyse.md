+++
title = 'Analyse'
date = 2024-07-11T12:48:36-04:00
draft = false
weight = 31
+++

Dans cette section nous verrons quelques outils qui permettent d'obtenir des informations diverses sur un serveur web. 

## _whatweb_

Donne des informations sur les diverses technologies détectées sur un serveur web. Comprend plusieurs niveaux "d'agressivité": le niveau 1 (par défaut) ne fait qu'une requête sur le serveur; les autres font des requêtes supplémentaires selon les informations trouvées lors de la première requête.

#### Syntaxe
`whatweb URL OPTIONS`

L'URL peut être un ou plusieurs noms, une adresse IP ou même une plage d'adresses IP.

#### Options utiles

+ `-v`: Affichage détaillé 
+ `-a`: Niveau d'agressivité (1, 3 ou 4) 
+ `--list-plugins` : Affiche la liste de tous les "plugins" utilisés par _whatweb_. Par défaut tous sont utilisés, mais pour des raisons de performance ou de discrétion on peut vouloir choisir seulement certains plugins.

#### Exemples
```
└─$ whatweb www.monsite.org
```
```
└─$ whatweb -v -a 4 10.0.0.10/wordpress
```

-------------------
## _nikto_
Script qui recherche des informations génériques au sujet des configurations, applications ou fichiers vulnérables sur un serveur web. Assez similaire à _whatweb_ dans son fonctionnement.

#### Syntaxe
`nikto -h SITE OPTIONS`

#### Options utiles

+ `-h`: L’URL du site. Les sous-répertoires ne sont pas acceptés.
+ `-root`: Suivi du nom d’un répertoire, traitera ce répertoire comme la racine du site.
+ `-list-plugins` : Affiche la liste des plugins utilisés

#### Exemples
```
nikto -h 10.10.10.10
```
```
nikto -h www.monsite.org -root /site1
```

-------------------
## _wapiti_
Wapiti est un programme semblable aux deux précédents: il utilise une série de modules pour détecter les vulnérabilités potentielles sur un site. Il dispose cependant de fonctionnalités plus avancées pour limiter l'espace de recherche et pour la manipulation de cookies; ainsi il permet de détecter des vulnérabilités possibles reliés aux cookies comme XSS ou CSRF.

#### Syntaxe
`wapiti -u SITE OPTIONS`

#### Options utiles

+ `-h`: Affiche l'aide en ligne.
+ `--scope`: Définit la profondeur de l'analyse
+ `--list-modules`: Affiche la liste des modules disponibles
+ `-m`: Définit les modules à utiliser

-------------------
## _wpscan_
Programme spécifiquement conçu pour analyser les sites _Wordpress_. Permet d’énumérer les thèmes, « plugins » et utilisateurs du site, et de tenter des connexions par force brutale à partir de listes de noms d’utilisateurs et de mots de passe.

#### Syntaxe
`wpscan OPTIONS`

#### Options utiles
+ `--url`: L’URL du site WP, incluant les sous-répertoires
+ `-e`: Permet de spécifier ce qu’on veut énumérer. Exemples de valeurs possibles : `p` (plugins), `u` (utilisateurs), `t` (themes) 
+ `-U`: Permet de spécifier une liste de noms d’utilisateurs pour tenter de se connecter
+ `-P`: Permet de spécifier une liste de mots de passe pour tenter de se connecter

#### Exemples
```
wpscan --url www.monsite.org/wordpress 
```
```
wpscan --url 10.10.10.10 -e u
```
```
wpscan --url 10.10.10.10/wp-content -U liste-util.txt -P liste-mdp.txt
```



