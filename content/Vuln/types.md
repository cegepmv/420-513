+++
title = 'Types'
date = 2024-07-26T13:01:42-04:00
draft = false
weight = 41
+++

On définit les vulnérabilités logicielles comme suit:

> Une faiblesse dans le code des composantes logicielles ou matérielles qui, lorsqu’elle est exploitée, résulte en un impact négatif sur la confidentialité, l’intégrité ou la disponibilité. La mitigation de ce type de vulnérabilité implique habituellement des modifications du code source.

(NIST, https://nvd.nist.gov/vuln)

Dans la majorité des cas, les vulnérabilités logicielles viennent des programmes et applications développés par un tiers parti, et donc les organisations peuvent rarement les éliminer par eux-mêmes. 

Par exemple, imaginez une entreprise nommée _SaltHR_ qui développe un logiciel de gestion de paie et le vend à une centaine d’entreprises. On découvre ensuite une vulnérabilité logicielle qui permet à des attaquants d’accéder à des données confidentielles sur les employés. Toutes les entreprises qui ont acheté le logiciel de _SaltHR_ seront vulnérables tant que _SaltHR_ n’aura pas corrigé cette vulnérabilité et distribué une mise à jour à ses clients.

Pour éviter cette situation, les fournisseurs de logiciels ont souvent des procédures de test qui visent à détecter les vulnérabilités dans leurs produits. Il existe aussi de nombreux chercheurs en cybersécurité dont le travail consiste précisément à rechercher les vulnérabilités logicielles dans des produits répandus. Mais malgré cela il est possible que des vulnérabilités mettent du temps avant d'être détectées. Lorsqu'elles finissent par l'être, on adopte généralement une approche de **divulgation responsable**: par exemple, les chercheurs en cybersécurité annoncent discrètement au fabricant du logiciel l'existence de la vulnérabilité, tout en lui disant que cette vulnérabilité sera annoncée publiquement quelques jours plus tard. Ceci laisse au fabricant le temps de la corriger; les clients sont ensuite avisés de la vulnérabilité au moment où le fabricant aura terminé de développer les correctifs requis.

### « Zero-day exploit »

Une vulnérabilité logicielle peut exister longtemps avant d’être officiellement détectée et corrigée; et durant cette période, elle peut être exploitée par des attaquants. On parle d’exploitation du jour zéro lorsqu’on découvre un programme malicieux visant à exploiter une vulnérabilité avant même qu’on ait détecté l’existence de celle-ci. Ceci signifie que le fabricant doit développer d’urgence (idéalement en « 0 jours ») un correctif en sachant que la vulnérabilité a déjà été exploitée.

### Types de vulnérabilités logicielles

Il n’existe pas de liste unique et complète de toutes les catégories de vulnérabilités logicielles possibles. En effet on les classe parfois en fonction de la manière dont elles peuvent être exploitées, parfois en fonction des conséquences de leur exploitation, ou encore des erreurs de programmation à leur origine. Malgré cela, on peut répertorier les plus fréquentes; notez que souvent elles peuvent être assez complexes, les descriptions offertes ici sont plutôt simplifiées :

#### Exécution de code

L’exploitation de la vulnérabilité permet d’exécuter un programme sur un ordinateur distant.

#### Débordement (« buffer overflow »)

Une mauvaise gestion de la mémoire dans un programme exécutable permet de faire exécuter des instructions arbitraires ou de contrôler l’exécution d’un programme de manière non souhaitée.

#### XSS (« Cross-site scripting »)

Techniques permettant d’injecter du code (typiquement JS) sur un serveur qui sera exécuté sur les navigateurs des utilisateurs visitant le site hébergé par le serveur.

#### Divulgation d’information

L’exploitation de la vulnérabilité a pour effet de révéler des informations confidentielles.

#### Élévation de privilèges

L’exploitation de la vulnérabilité a pour effet d’augmenter les privilèges utilisateur de l’attaquant, lui permettant ainsi de contrôler certains programmes et services et d’accéder à des informations.

#### Déni de service

L’exploitation de la vulnérabilité empêche les utilisateurs légitimes d’accéder à un service ou une information.

#### CRSF (« Cross-site Request Forgery »)

L’exploitation de la vulnérabilité permet de faire exécuter (à leur insu) par des utilisateurs légitimes des actions malveillantes sur un serveur.

#### Injection SQL

Techniques permettant d’injecter du code SQL dans des formulaires web ou des requêtes http afin d’exécuter des commandes ou de divulguer des informations confidentielles.

#### Inclusion de fichier

Techniques permettant d’exécuter ou de lire un fichier sur le serveur (LFI) ou distant par téléchargement (RFI).


