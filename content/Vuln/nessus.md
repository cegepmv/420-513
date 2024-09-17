+++
title = 'Nessus'
date = 2024-07-26T13:04:27-04:00
draft = false
weight = 43
+++



_Nessus_ est un logiciel qui inspecte les hôtes d’un réseau à la recherche de vulnérabilités connues. Il est principalement utilisé par les administrateurs d’un réseau pour détecter les systèmes ayant besoin d’être mis-à-jour ou renforcés, mais il peut arriver que certains attaquants l’utilisent pour chercher les systèmes vulnérables sur un réseau.

Il existe une version gratuite de _Nessus_ qui permet de scanner un maximum de16 adresses IP sur un réseau. C’est celle que nous allons installer ici.

#### À télécharger

+ [Nessus](https://fr.tenable.com/products/nessus/nessus-essentials)

<!-- + [Metasploitable 3 (Accès VPN Maisonneuve)](http://10.70.183.96/VM/Metasploitable3-ub1404.ova)

#### Installation de Metasploitable 3

Ouvrez le fichier `Metasploitable3-ub1404.ova` dans VMWare Workstation. Les identifiants de connexion sont **_vagrant/vagrant_**.
-->

#### Installation de Nessus

Sur _Kali_, téléchargez le paquetage _.deb_ à partir du lien ci-haut. 

Vous devrez remplir un formulaire et donner votre adresse courriel (celle de Marie-Victorin) afin de recevoir un code d’activation:

![act-nessus](/420-513/images/act-nessus.png?classes=border,shadow)

Ensuite vous pourrez télécharger la version debian6, compatible avec Kali:

![dload-nessus](/420-513/images/dload-nessus.png?classes=border,shadow)

L’installation de Nessus est très simple et rapide (au début): lancez les commandes suivantes dans le répertoire où vous avez téléchargé le fichier _.deb_: 

```
┌──(kali㉿kali)-[~/Downloads]
└─$ sudo dpkg -i Nessus-10.8.3-ubuntu1604_amd64.deb 
[sudo] password for kali: 
Selecting previously unselected package nessus.
(Reading database ... 392798 files and directories currently installed.)
Preparing to unpack Nessus-10.8.3-ubuntu1604_amd64.deb ...
Unpacking nessus (10.8.3) ...
Setting up nessus (10.8.3) ...

(...)

Unpacking Nessus Scanner Core Components...

 - You can start Nessus Scanner by typing /bin/systemctl start nessusd.service
 - Then go to https://kali:8834/ to configure your scanner

                                                                                                                             
┌──(kali㉿kali)-[~/Downloads]
└─$ sudo systemctl start nessusd   
```

L’application roule sur le port local 8834 – lancez firefox à l’url `https://kali:8834`, puis faites **Continue** sur la première page.

À la page suivante, choisissez _Register for Nessus Essentials_ et continuez:

![ness-ess](/420-513/images/ness-ess.png?classes=border,shadow)

Puisque vous avez déjà obtenu votre code d’activation par courriel, choisissez `Skip` pour passer à l’étape suivante et saisissez le code à l’endroit spécifié:

![entrer-code](/420-513/images/entrer-code.png?classes=border,shadow)    

La dernière étape consiste à créer l’utilisateur ayant les droits d’administrateur et lui donner un mot de passe. Notez bien ces deux valeurs. Si vous manquez d'inspiration, choisissez "info" et "abc-123".  

Après cette étape, Nessus prendra une vingtaine de minutes pour télécharger et compiler les données requises pour l’analyse.

{{% notice warning "Attention" %}}
S'il y a des problèmes au téléchargement et que la page "Download Failed" s'affiche, vous pouvez le relancer avec la commande suivante:
```
sudo /opt/nessus/sbin/nessuscli update
sudo systemctl restart nessus
```
{{% /notice %}}


#### Analyse de vulnérabilités

À titre d'exemple nous allons lancer un _scan_ sur l'hôte local, c'est-à-dire notre VM _Kali_. Pour lancer l’analyse sur votre VM, cliquez tout d’abord sur `New Scan`:

![new-scan](/420-513/images/newscan.png?classes=border,shadow)

Il existe plusieurs types d’analyse: choisissez `Basic Network Scan`. Vous devrez ensuite spécifier les paramètres généraux et les hôtes à analyser. Donnez un nom et une description à votre _scan_ puis spécifiez l’adresse IP de votre VM dans le champ `targets`: 

![basic-net](/420-513/images/basic-net.png?classes=border,shadow)

De nombreux paramètres de configuration sont disponibles:
+ **Schedule** : Si activé, permet de programmer la fréquence des analyses sur une base quotidienne, hebdomadaire, etc.
+ **Notifications** : Configure l'envoi de notifications par courriel en fonction de certains critères. Par exemple, envoyer un courriel à l'administrateur si une vulnérabilité dont le score CVSS est supérieur à 9 est détectée.
+ **Discovery** : Permet de définir les ports TCP et UDP qui seront inspectés sur les hôtes.
+ **Assessment** : Donne accès à divers paramètres relatifs au type et à la profondeur de l'analyse.
+ **Report** : Paramètres relatifs à la présentation des résultats.
+ **Advanced** : Options de configuration reliées à la performance, à la journalisation de _Nessus_, etc.

_Nessus_ peut approfondir son analyse si on lui fournit des identifiants de connexion SSH ou autres dans l’onglet `Credentials`: il utilise ceux-ci pour se connecter et vérifier les configurations de certains services. Cette vérification peut encore aller plus loin si l’utilisateur fourni a les droits de _sudo_. 

##### _Plugins_

L’onglet `Plugins` permet d’ajouter des modules à l’analyse. 

Les _plugins_ sont à la base des modules voués à rechercher et analyser des vulnérabilités spécifiques. Chacun contient des informations sur la vulnérabilité et un programme qui permet de tester si celle-ci est présente sur différents types d'hôtes. Par exemple, si on veut savoir si un hôte Windows est affecté par la vulnérabilité MS17-010 ("EternalBlue"), on utilisera le module [97833](https://www.tenable.com/plugins/nessus/97833).

Outre la détection de vulnérabilités, les modules ont aussi d'autres fonctions comme par exemple détecter la version d'un service, la présence d'un proxy web, etc.

{{% notice info %}}
Dans l'analyse de base ("Basic Network Scan"), il est impossible de modifier la liste des _plugins_.
{{% /notice %}}

Lorsque vous aurez sauvegardé vos paramètres d’analyse, vous pourrez la lancer avec le bouton `launch`:

![launch](/420-513/images/launch.png?classes=border,shadow)

Lorsque l’analyse est terminée, vous pourrez visualiser les vulnérabilités détectées, classées selon leur indice de gravité [CVSS](https://en.wikipedia.org/wiki/Common_Vulnerability_Scoring_System)

Remarquez également que chaque vulnérabilité est associée à une description et une suggestion des mesures à prendre (section `solution`):

![vuln-details](/420-513/images/vuln-details.png?classes=border,shadow)


> La version gratuite de Nessus permet d'analyser 16 adresses IP différentes. Trouvez-vous des vulnérabilités sur votre réseau personnel à la maison?
