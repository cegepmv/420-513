+++
title = 'Ressources'
date = 2024-07-26T13:04:10-04:00
draft = false
weight = 42
+++

L'organisme _NIST_ ("National Institute of Standards and Technology") maintient une base de donnée des vulnérabilités logicielles connues. Chacune des vulnérabilités qu'il répertorie est désignée par un code "CVE" (un identifiant unique), catégorisée par type et décrite de manière très détaillée.

Par exemple, la [cyberattaque de 2020 visant le logiciel SolarWinds utilisé par le gouvernement américain](https://fr.wikipedia.org/wiki/Cyberattaque_de_2020_contre_les_%C3%89tats-Unis) a exploité la vulnérabilité CVE-2020-10148.

Cette base de données se nomme _National Vulnerability Database_ (**NVD**) et son site officiel est à l'adresse https://nvd.nist.gov/vuln/search.

## CVE
Les vulnérabilités, lorsqu’elles sont détectées et rendues publiques, se font attribuer un identifiant nommé **CVE** ("Common Vulnerability and Exposure"); celui-ci est composé de l’année et d’un nombre séquentiel (e.g. CVE-2021-44228). Plusieurs informations sont associées à une vulnérabilité donnée, par exemple:
- CVSS, l'indice de sévérité de la vulnérabilité
- CWE, la catégorie de vulnérabilité

Quelques sites où on répertorie les vulnérabilités logicielles:
- [CVE Details](https://www.cvedetails.com/): La base de données la plus complète et détaillée à ce jour.
- [Packet Storm Security](https://packetstormsecurity.com/files/): Un site d'informations sur la cybersécurité qui contient également quelques "exploits"
- [cve.org](https://www.cve.org/): Le site maintenu par MITRE, la compagnie qui a développé le standard CVE en 1999.

## CVSS
Le **CVSS** ("Common Vulnerability Scoring System") est un indice permettant d'estimer la gravite d'une vulnérabilité. Elle est calculée à partir de nombreuses variables, par exemple:
- La facilité à exploiter la vulnérabilité. Une vulnérabilité difficile à exploiter découragera certains pirates... donc plus la vulnérabilité est facile à exploiter, plus le risque erst grand.
- L'efficacité des correctifs. S'il existe des correctifs à la vulnérabilité et s'ils sont efficaces, la vulnérabilité est moins grave.
- Les privilèges requis. Si l'exploitation de la vulnérabilité nécessite qu'on soit "root", elle est plus difficile à réaliser, donc le risque est moins grand.
- etc.

Pour plus d'informations: https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator

## CWE
Le **CWE** ("Common Weakness Enumeration") tente de faire la liste des "fablesses" logicielles ou matérielles qui peuvent causer des vaulnérabilités. Ces faiblesses sont parfois un peu abstraites, mais elles sont utiles pour catégoriser les vulnérabilités logicielles. Quelques exemples:
- CWE-798: _Use of hard-coded credentials_ - Par exemple, un programme contient le nom d'utilisateur et le mot de passe pour accéder à la base de données SQL
- CWE-306: _Missing Authentication for Critical Function_ - Le programme ne vérifie par l'identité d'un utilisateur avant de lui permettre d'exécuter des fonctionnalités critiques pour la sécurité (par exemple changer un fichier de configuration).
- CWE-434: _Unrestricted Upload of File with Dangerous Type_ - Le programme permet à un utilisateur de déposer des fichiers sur le serveur sans contrôler ses permissions ou le contenu des fichiers.

Pour plus d'informations: https://cwe.mitre.org/about/new_to_cwe.html
