+++
title = 'SSH'
date = 2024-07-09T14:08:18-04:00
draft = false
weight = 14
+++

SSH (_Secure Shell_) est un service permettant d'établir une connexion chiffrée par ligne de commande ("shell") sur un serveur distant.

#### Connexion simple
Pour se connecter à un serveur SSH, il suffit de passer à la commande le nom d'utilisateur suivi du nom d'hôte (ou de son adresse) comme suit:
```
ssh marco@10.67.60.154
```
Dans sa configuration par défaut, le service SSH est généralement configuré pour que les utilisateurs ordinaires du système puissent se connecter par mot de passe. 

#### Authentification par clé publique
L'authnetification par clé est considérée plus sécuritaire que par mot de passe, et en plus elle simplifie la procédure de connexion pour les utilisateurs.

Le principe consiste à générer une paire de clés, une publique et une privée. La clé _publique_ sera copiée sur le serveur et permettra à celui-ci de reconnaître le possesseur de la clé _privée_ lorsqu'il essaie de se connecter.

> On peut imaginer la clé publique comme un cadenas que seule la clé privée peut ouvrir.

Les étapes pour configurer l'authentification par clé:
###### 1. Générer la paire de clés 
```
ssh-keygen 
``` 
Pas besoin ici de protéger la clé par un "passphrase". Le fichier de clé publique a l'extension `.pub`, la clé privée n'a pas d'extension.

###### 1. Copier la clé publique dans le fichier _authorized_keys_ sur le serveur. 
```
cat id_rsa.pub | ssh utilisateur@serveur 'cat >> ~/.ssh/authorized_keys'
```
La commande `ssh-copy-id utilisateur@serveur` est également disponible sur kali pour arriver au même résultat.

###### 3. (Optionnel) Désactiver l'authnetification par mot de passe. 
Dans le fichier de configuration du serveur (habituellement `/etc/ssh/sshd_config`), activer la directive suivante:
```
PasswordAuthentication no
```

#### Raccourcis
On peut ajouter à la configuration du client ssh des raccourcis pour les serveurs sur lesquels on se connecte souvent. Sur kali le fichier est dans `~/.ssh/config`. 

Par exemple pour créer un raccourci pour se connecter en tant que "bob" sur un serveur à l'adresse 10.10.34.101, on ajoute les lignes suivantes dans `~/.ssh/config`:
```
Host sv12
    HostName 10.10.34.101
    User bob
```

Ensuite il sera possible de se connecter avec la commande `ssh sv12`.

#### Commandes
Il est possible d'utiliser ssh simplement pour lancer une commande sur le serveur distant. Les résultats de cette commande seront affichés localement. 

Pour ce faire il suffit de passer la commande et ses arguments à la suite de la commande de connexion. Par exemple pour voir le contenu du fichier `/etc/hosts` sur le serveur distant:
```
kali@kali:~$ ssh admin@172.16.70.148 "cat /etc/hosts"
127.0.0.1	localhost
127.0.1.1	sv12
::1		localhost ip6-localhost ip6-loopback
ff02::1		ip6-allnodes
ff02::2		ip6-allrouters
```

Si la commande est une application graphique, utiliser l'option `-X` nous permettra d'ouvrir une fenêtre locale pour l'application:
```
kali@kali:~$ ssh -X admin@172.16.70.148 firefox
```
Notez cependant que pour cela soit possible, le _serveur_ doit avoir la directive `X11Forwarding yes` dans sa configuration.

{{% notice tip "Attention" %}}
Si le programme à exécuter nécessite une élévation de privilèges avec _sudo_, il faut utiliser l'option **_-S_** de _sudo_ car le mode interactif est impossible avec _ssh_:
```
ssh olivier@10.30.0.78 "sudo -S systemctl apache2 stop"
```
{{% /notice %}}
 
En élaborant un peu, ceci permet d'écrire dans des fichiers sur l'hôte distant:
```
ssh olivier@10.30.0.78 "echo bonjour > /home/olivier/salutations.txt"
```

En utilisant la redirection de commandes, on peut aussi copier des fichiers:
```
cat programme.py | ssh olivier@10.30.0.78 "cat > /home/olivier/programme.py"
```
(Cela dit il est certainement plus simple d'utiliser **_scp_** pour transférer des fichiers par _ssh_...)





