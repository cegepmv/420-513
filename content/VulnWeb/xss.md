+++
title = 'XSS'
date = 2024-09-29T17:27:38-04:00
draft = false
weight = 52
+++

Le _Cross-Site Scripting_ (XSS) est une vulnérabilité qui consiste à injecter du code _Javascript_ malveillant sur un site afin que des utilisateurs légitimes téléchargent ce code malveillant à leur insu. Ce code peut avoir pour effet par exemple de rediriger l'utilisateur sur un site dangereux, ou encore envoyer des informations confidentielles (données personnelles, cookies d'authentification, etc.) à un tiers.

Un type de sites potentiellement vulnérables sont ceux où il est possible de créer du contenu, par exemple les forums de discussion ou les pages acceptant des commentaires. 

Il y a 3 types de XSS:
- Réfléchie
- Stockée
- Basée sur le DOM

Ici nous verrons seulement les deux premières.

## XSS réfléchie
Ce type de XSS survient lorsque le code exécuté est directement passé par un paramètre de la page, sans être stocké sur le serveur. Lorsqu'un site affiche tel quel dans la page un paramètre passé dans son url, il y a une possibilité de XSS réfléchie.

Par exemple, imaginez une page qui affiche le nom passée dans son url. Par exemple pour l'url suivante:
```
http://www.monsite.ca/mapage?nom=georges
```
la page affiche "Bonjour georges".

Si la page n'est pas protégée contre les XSS, l'url `http://www.monsite.ca/mapage?nom=<script> # Code malveillant ici # </script>` aura pour effet d'éxécuter un programme JS dans la page affichée.

## XSS Stockée
Les XSS stockées, aussi appelées XSS persistantes, consistent à écrire sur le serveur le code malicieux qui sera ensuite exécuté par tous les utilisateurs qui visitent la page qui contient le code. 

Par exemple, un groupe de discussion comme StackOverflow ou une plateforme comme Discord; dans ces applications, les messages envoyés par les utilisateurs sont stockés dans une base de données. Imaginez maintenant un message qui aurait le contenu suivant:
```
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin luctus nulla nulla, ut posuere ligula interdum eu. Cras tempus arcu nec massa posuere, non aliquam quam pretium. Fusce eget lacus eu quam elementum gravida. Morbi mollis ipsum at quam facilisis rhoncus. Maecenas sed luctus nunc. 

<script>window.location.href="https://site-malveillant.com"</script>

Cras aliquet consequat nibh, eu mattis nulla suscipit vitae. Etiam quis tortor id enim hendrerit mollis. Quisque quis magna augue. Maecenas sed metus iaculis, porttitor elit pulvinar, egestas leo. 
```
Lorsqu'un utilisateur affiche cette page, la balise `<script>` sera interprétée par le navigateur comme un extrait de code JS à exécuter, et le code qu'elle contient redirigera l'utilisateur vers le site "site-malveillant.com".

Le code peut se trouver dans un fichier sur le serveur, ou dans la base de données.

