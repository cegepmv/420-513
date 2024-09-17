+++
title = 'Autres outils'
date = 2024-07-26T13:04:35-04:00
draft = false
weight = 44
+++

Les programmes utilisés pour la phase d'énumération ont aussi parfois des fonctionnalités permettant de découvrir des vulnérabilités. Dans cette section nous verrons comment `nmap` et `wapiti` peuvent être utilisés pour révéler certaines vulnérabilités spécifiques.

## Scripts _nmap_
Le programme `nmap` permet, comme on l'a vu précédemment, de découvrir les hôtes sur un réseau et découvrir les ports et les services actifs sur ces hôtes. 

Ce programme dispose aussi du "nmap scripting engine" (ou _NSE_), une fonctionnalité de _scripting_permettant de créer des programmes pour automatiser l'exécution de `nmap` et effectuer certaines actions précises selon les résultats obtenus (le langage utilisé pour ces scripts est **Lua**). Par exemple, on pourrait créer un script qui affiche les adresses IP de tous les hôtes d'un réseau dont le port 22 (SSH) est ouvert.

La distribution Kali vient avec plus de 600 scripts NSE déjà faits, tous dans le répertoire `/usr/share/nmap/scripts`. Ces scripts sont regroupés en catégories, par exemple:
- fuzzer : pour envoyer des données de "fuzzing" à un service
- exploit : pour exploiter des vulnérabilités
- dos : pour causer des dénis de service
- malware : pour détecter si un hôte est victime d'un _malware_ ou d'un virus

Plus de détails: https://nmap.org/book/nse-usage.html

> Pour voir tous les scripts d'une catégorie donnée, par exemple "malware", la commande est `nmap --script-help malware`

Une de ces catégories est **vuln**: elle contient tous les scripts qui servent à détecter des vulnérabilités. Il y en a plus de 100.

> La liste des scripts nmap dans la catégorie "vuln": https://nmap.org/nsedoc/categories/vuln.html

Par exemple, pour lancer le script NSE "irc-unrealircd-backdoor" sur l'hôte `192.168.10.101`, la commande est celle-ci:

```bash
nmap  --script irc-unrealircd-backdoor -p 6697 192.168.10.101
```

## wapiti
Le programme _wapiti_ utilise une série de modules lui permettant d'effectuer certaines tâches: par exemple, le module `brute_login_form` tente de se connecter en utilisant des identifiants populaires comme "admin/admin"; le module `backup` tente de détecter des fichiers de sauvegarde sur le serveur; etc.
Plusieurs modules servent à détecter des types de vulnérabilités typiques des serveurs web:
- **file**: Vulnérabilités reliées à des fichiers (inclusion de fichiers)
- **sql, blindsql**: Injections SQL
- **xss**: Cross-Site Scripting
- **xxe**: XML External Entity injection
- **csrf**: Cross-Site Request Forgery

Par exemple, pour vérifier si l'hôte 172.16.70.155 est vulnérable aux injections SQL, la comande est la suivante:

```
wapiti -u http://172.16.70.155 -m sql
```
<!--
Next week: examen
Vous avez un CT, vous devez trouver des infos dessus comme on a vu dans les
modules 1-4

Sur ICA ():
- Lancer nmap. Quels ports/versions?
- Lancer un script wapiti
- Lancer nessus
-->