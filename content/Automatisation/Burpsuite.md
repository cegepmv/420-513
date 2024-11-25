+++
title = 'Burpsuite'
date = 2024-11-11T13:48:45-05:00
draft = false
weight = 72
+++

_Burp_ est un programme dédié à l'analyse de sécurité des applications web, développée par la compagnie _PortSwigger_. Elle est très utilisée par les "hackers" professionnels pour identifier et corriger les vulnérabilités des applications web. 

_Burp_ est constitué d'un proxy qui intercepte les requêtes envoyées entre le navigateur et un site web et les réponses à ces requêtes. Ceci permet d'analyser tous les aspects de l'interaction entre le client et le serveur web, modifier des paramètres, générer ou automatiser des requêtes, etc.

La version distribuée dans Kali est gratuite mais limitée.


## Démarrer un projet

Lors du démarrage de _Burpsuite_, vous aurez la possibilité de créer un nouveau projet. Ceci est utile lorsque vous prévoyez consacrer plusieurs séances de travail à l'analyse d'un site; dans ce cas-ci, cela ne sera pas nécessaire: choisissez un "projet temporaire":

![proj-tmp](/420-513/images/burp-temp.png)

Après le démarrage de Burp, la première étape consiste à définir notre cible. Pour ce faire nous allons ouvrir le navigateur inclus dans Burp et accéder à la page d’accueil de notre cible. Dans l’onglet `Proxy`, cliquez sur `Open Browser` :

![open-browser](/420-513/images/open-browser.png?classes=border)

Tapez l’adresse de la VM « Juice-shop » dans le navigateur. 

Vous pouvez constater que la page ne charge pas : c’est que Burp intercepte la requête lorsque qu’elle est envoyée; vous pouvez activer ou désactiver cette fonctionnalité à l’aide d’un bouton. Lorsqu’une requête est interceptée vous pouvez soit la bloquer (_Drop_), soit la laisser passer (_Forward_), ou encore la rediriger à un module Burp (_Action_). Faites `Forward` pour accéder à la page.

![burp-fwd](/420-513/images/burp-fwd.png?classes=border)

Vous pouvez désactiver l’interception des requêtes pour l’instant.

Allez ensuite dans l’onglet `Target -> Site map` afin de voir les URL appelées durant le chargement de la page d’accueil et les sites consultés. Pour ne pas afficher de données inutiles, nous allons limiter la portée de l’analyse à notre site. Cliquez-droite sur l’url du Juice-Shop et choisissez `Add to scope`.

![burp-addscope](/420-513/images/burp-addscope.png?classes=border)

#### Construction du plan du site
À partir des liens que vous visitez dans le site, Burp construit sa structure. Donc, visitez tous les liens possibles afin de peupler le « site map ».


## _Repeater_


Le module _Repeater_ de Burp, comme son nom l'indique, permet de relancer une requête faite précédemment en modifiant certains de ses paramètres. 

Si par exemple on souhaite répéter la requête qui correspond à la connexion au site, on commencera d'abord par faire une première tentative de connexion de la manière normale; allez au formulaire de connexion du site:

![burp-login-js](/420-513/images/burp-login-js.png?classes=border)

Dans Burp, onglet `Proxy-> Intercept`, activez l’interception. Ensuite entrez un identifiant et un mot de passe et cliquez sur _Log in_.

Ensuite faites `Forward` jusqu’à arriver à la requête contenant les identifiants de connexion :

![burp-login-intercept](/420-513/images/burp-login-intercept.png?classes=border)

Il est possible de modifier le contenu des requêtes directement ici, mais il est plus utile d’utiliser le module _Repeater_ si on souhaite relancer plusieurs fois cette requête en essayant différentes modifications. Pour ce faire, cliquez sur le bouton `Action` et choisissez `Send to Repeater` :

![burp-send-repeat](/420-513/images/burp-send-repeat.png?classes=border)

Dans l’onglet _Repeater_ vous pouvez modifier des éléments de la requête et faire ensuite `Send`. Le panneau de droite montre la réponse du serveur :

![burp-view-repeat](/420-513/images/burp-view-repeat.png?classes=border)

Il est également possible d’envoyer à ce module des requêtes faites plus tôt lorsqu'elles sont enregistrées dans l'historique. Par exemple, à partir de l’onglet `Proxy -> HTTP History` :

![burp-http-hist](/420-513/images/burp-http-hist.png?classes=border)


## _Intruder_


Dans les cas où on veut lancer plusieurs fois de manière automatisée une requête similaire en faisant varier un ou plusieurs paramètres, on utilise le module _Intruder_. Ceci est utile par exemple pour lancer des attaques sur l’authentification à partir de listes de mots, ou pour faire du _fuzzing_ de paramètres de requêtes (c'est ce que nous ferons dans cet exemple).

Activez l’interception et cliquez sur un des produits de la page principale. Faites `Forward` jusqu’à arriver à la requête pour le produit que vous avez choisi :

![burp-intruder](/420-513/images/burp-intruder.png?classes=border)

Ensuite dans `Action` choisissez `Send to Intruder`, puis allez à l’onglet `Positions` :

![burp-positions](/420-513/images/burp-positions.png?classes=border)

Ces _Positions_ désignent les différents endroits dans la requête où il est possible d'injecter des valeurs. Burp suggère automatiquement des positions, qui correspondent aux paramètres susceptibles de faire l’objet de l’attaque. Ici nous allons faire varier le nombre correspondant à l’identifiant du produit. Cliquez sur `Clear` afin de supprimer les paramètres suggérés. Ensuite, sélectionnez l’identifiant de produit (« 24 » dans l’exemple) et faites `Add`. Vous verrez ceci :

![burp-add-position](/420-513/images/burp-add-position.png?classes=border)

Ensuite il s’agit de déterminer le _payload_, c’est-à-dire les valeurs par lesquelles on veut remplacer ce paramètre. Ici on choisira des nombres séquentiels de 0 à 60. Allez à l’onglet `Payloads`:

![burp-payloads](/420-513/images/burp-payloads.png?classes=border)

Les options de configuration sont nombreuses : on peut définir une courte liste manuellement (_Simple list_) ou encore utiliser des listes de mots dans des fichiers, des nombres séquentiels, des substitutions de caractères dans une chaîne, etc.

Choisissez `Numbers` comme type et définissez l’intervalle comme suit :

![burp-numbers](/420-513/images/burp-numbers.png?classes=border)

Faites ensuite `Start attack` pour lancer les requêtes.