+++
title = 'CSRF'
date = 2024-09-29T17:27:43-04:00
draft = false
weight = 53
+++

Le _Cross-Site Request Forgery_ consiste à profiter des permissions d'un utilisateur pour lancer des requêtes au serveur en son nom. Pour qu'une attaque CSRF fonctionne, deux conditions doivent être remplies:

- La victime doit s'être authentifiée
- L'attaquant doit trouver une manière de faire exécuter une requête

Pour la deuxième condition, une technique souvent utilisée est le _phishing_, par exemple lorsque l'attaquant incite la victime à cliquer sur un lien dans un courriel ou un message texte.

Imaginez une application où on utilise une monnaie virtuelle (par exemple les _robux_ dans Roblox) et où les utilisateurs peuvent se transférer des crédits entre eux. La requête pour transférer 100 crédits dans le compte numéro 12345 pourrait par exemple ressembler à ceci:

`GET http://www.monapp.com/trxcredits?compte=12345&montant=100`

Le compte à partir duquel les fonds sont pris est celui de l'utilisateur qui lance cette requête et est identifié par un cookie de session.

Un attaquant qui souhaite récupérer 10000 crédits dans son compte (#99999) pourrait donc créer la requête suivante:

`GET http://www.monapp.com/trxcredits?compte=99999&montant=10000`

Il doit ensuite trouver une manière de faire lancer la requête par un utilisateur authentifié du site. Pour ce faire il peut envoyer un message comme celui-ci par email à sa victime:

```html
Votre PC est plein de virus! Cliquez 
<a href="GET http://www.monapp.com/trxcredits?compte=99999&montant=10000">ici</a>
pour le nettoyer grâce à notre antivirus en ligne.
```

