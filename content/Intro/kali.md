+++
title = 'Kali linux'
date = 2024-07-09T14:07:23-04:00
draft = false
weight = 12
+++

Kali est une distribution linux vouée à la cybersécurité: elle inclut une quantité impressionnante de programmes utiles pour toutes les tâches, de la capture du trafic sur un réseau à l’attaque d’applications web en passant par la rétro-ingénierie de programmes exécutables.

D’autres distributions de linux ont la même vocation (_Backbox_, _ParrotOS_, _BlackArch_ pour linux et _Commando VM_ du côté de Windows); mais Kali est certainement la plus répandue et une des mieux supportée.

## Installation
Dans ce cours nous utiliserons Kali comme une machine virtuelle dans VMWare Workstation.

Il y a deux options d'installation: soit on télécharge le fichier ISO et on lance l'installation d'une nouvelle VM, soit on télécharge la VM déjà prête sous forme d'un fichier OVA. Nous choisirons cette 2e option.

1. Téléchargez la version la plus récente de Kali à l'adresse https://www.kali.org/get-kali/#kali-virtual-machines, dans la section _VMWare_.
2. Copiez le fichier `7z` sur votre disque amovible et décompressez-le.
3. Dans VMWare Workstation, faites **File -> Open** et recherchez le fichier `.vmx` dans les fichiers décompressés.

L'identifiant de connexion est `kali` et le mot de passe est `kali`.

## Utilisation
Le menu principal de navigation dans Kali est organisé par catégories: il y a donc par exemple un groupe de raccourcis pour l’analyse de vulnérabilités, un groupe pour les outils d’analyse d’applications web, etc.

![kalimenu](/420-513/images/kalimenu.png?width=600px)

Une barre de recherche permet de trouver les programmes du menu par leur nom.

Les répertoires suivent la structure habituelle des systèmes linux (_/bin, /home, /etc, /usr_…).


