+++
title = "Phases d'une cyberattaque"
date = 2024-07-09T14:08:00-04:00
draft = false
weight = 16
+++

Une cyberattaque comprend plusieurs étapes. Celles-ci peuvent être plus ou moins nombreuses selon les objectifs de l'attaque. 

En anglais on utilise l'expression "cyber kill chain" (d'origine militaire) pour désigner ces étapes.

Il y a plusieurs opinions quant au nombre et à la nature de chacun de ces étapes, mais on peut les regrouper dans les phases suivantes:

#### Collecte d'informations
Aussi appelée _reconnaissance_ ou _énumération_. Consiste à recueillir le plus d’informations possibles sur la cible. Ces informations peuvent être publiques ou privées (noms de domaines, des employés, leurs courriels, les localisations des bureaux, les clients, les fournisseurs, etc.) ou encore être de nature plus technique (logiciels et composantes matérielles utilisées, leurs versions, les adresses IP des serveurs, etc.). 

Elle peut aussi être _passive_ ou _active_. La reconnaissance passive consiste à rechercher des informations disponibles sur la cible sans interagir avec celle-ci, par exemple en faisant une recherche google sur la cible, en visitant son site web, etc. La reconnaissance active est plus agressive car elle interagit avec la cible (des personnes ou des serveurs) à la recherche d'informations qui ne sont généralement pas accessibles au public. Rechercher les ports ouverts sur un serveur ou écrire à un employé sont des exemples de reconnaissance active. 

{{% notice style="note" title="Attention" %}}
La reconnaissance active est considérée comme une menace par la plupart des organisations, et peut mener à des poursuites en justice.
{{% /notice %}}

#### Recherche de vulnérabilités
Les vulnérabilités recherchées peuvent être de nature logicielle ou matérielle, ou encore touche les processus de l’entreprise. Par exemple, il arrive parfois que des testeurs d’intrusion cherchent des vulnérabilités qui leur permettraient d’entrer physiquement dans les bureaux d’une entreprise afin de collecter des informations inaccessibles de l’extérieur.

Généralement, on recherche des vulnérabilités logicielles, c'est-à-dire qu'on essaie de trouver sur la cible des programmes dont la version contient des vulnérabilités déclarées pour lesquelles il existe une technique d'exploitation connue.

#### Exploitation
Consiste à profiter de la vulnérabilité pour gagner un accès au système informatique.

Généralement on adapte une exploitation connue d'une vulnérabilité qui n'a pas été corrigée.

#### Post-exploitation
Cette phase regroupe toutes les actions à réaliser lorsque la vulnérabilité a été correctement exploitée et qu'on a gagné l'accès. Elle se compose des tâches suivantes:
- **Élévation de privilèges**: Chercher à augmenter le privilèges de l'utilisateur qu'on utilise pour se connecter. Idéalement on veut obtenir les droits d'administrateur sur la cible.
- **Évasion**: Effacer les traces de l'exploitation. Ceci consiste à effacer les fichiers qui pourraient être détectés par les antivirus, effacer les lignes des journaux système qui pourraient révéler la présence d'un intrus, etc.
- **Maintien de l'accès**: Inclut les tâches visant à faciliter les accès futurs, par exemple détecter un compte inutilisé et changer son mot de passe afin de l'utiliser pour se connecter par la suite.
- **Pivot**: Changer de cible. Lorsqu'on gagné l'accès sur un système, il est possible que celui-ci permette d'accéder à d'autres systèmes qui étaient hors de portée avant. On reprend donc le cycle à partir de la recherche de vulnérabilités, explitation, etc. pour cette nouvelle cible.
- **Exfiltration**: Dans le cas où l'objectif de l'attaque est de se procurer des données, le transfert de celles-ci peut être plus ou moins délicat en foncion de leur quantité ou du niveau de surveillance.

--------------------
#### Références
- [Mitre att&ck framework](https://attack.mitre.org/): Regroupe en différentes catégories les techniques qui peuvent être utilisées lors d'une cyberattaque.
- [Lockheed Martin Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html): Une description des différentes étapes successives d'une cyberattaque. 
- [Unified Kill Chain](https://www.unifiedkillchain.com/): Une version plus détaillée des pases d'une cyberattaque.