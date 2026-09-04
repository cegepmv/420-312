+++
pre = '<b>2. </b>'
title = "DNS"
weight = "520"
+++
-------------------

+ Le DNS, (*Domain Name System*), est un service essentiel du fonctionnement d’Internet. 
+ Son rôle principal est de traduire un nom de machine (par exemple www.google.com) en adresse IP (comme `142.250.65.174`), et inversement. 
+ Cette traduction est nécessaire parce que les utilisateurs retiennent beaucoup plus facilement des noms que des adresses numériques, tandis que les ordinateurs, eux, ne fonctionnent qu’avec des IP.

+ Pour fonctionner correctement, un nom doit être exprimé sous forme de **FQDN** (*Fully Qualified Domain Name*). Un FQDN contient à la fois le nom de l’hôte et le nom du domaine complet, par exemple :
`serveur1.collegemv.qc.ca`.

## Fonctionnement de DNS

Le DNS repose sur un **modèle client/serveur** :

+ **Serveur DNS :** Dans ce cours, nous allons voir comment `bind` fonctionne. Son processus s’appelle **named**.
+ **Client DNS :** appelé **resolver**, il est présent sur toute machine connectée à un réseau. C’est lui qui envoie les requêtes DNS au serveur configuré lors de l’installation du réseau.

Les échanges DNS utilisent principalement :

+ le port `UDP 53` pour les requêtes,
+ le port `TCP 53` pour les transferts de zones (nous n'allons pas aborder les transferts de zone dans ce cours).

## Structure du DNS

![Hiérarchie de DNS](../images/05-02-01.png?width=600px)

+ Le DNS est organisé de façon **hiérarchique** : un peu comme une pyramide inversée où chaque couche connaît des informations sur la couche située juste en dessous.
+ Au sommet, on trouve les **serveurs racine**.
+ Bien qu’il n’existe que 13 noms de serveurs racine (de A à M), chacun d’eux est en réalité répliqué sur des dizaines de serveurs répartis dans le monde (*anycast*), ce qui permet un accès rapide et fiable.

+ Les serveurs racine connaissent les serveurs responsables de chacun des **TLD** (*Top Level Domain*), comme `.com`, `.ca`, `.org`, etc.
+ Chaque TLD gère à son tour les domaines qui lui appartiennent, et connaît les serveurs DNS autoritaires pour chacun de ces domaines.
+ Ce modèle hiérarchique permet à Internet d’être extrêmement scalable, robuste et réparti sur toute la planète.


## Domaine vs Zone

Cette distinction est fondamentale pour comprendre la configuration d'un serveur DNS.

+ Un **domaine** : représente un ensemble logique de noms qui partagent un même suffixe.
Par exemple, le domaine `collegemv.qc.ca` inclut tous les noms terminant par cette chaîne, comme `www.collegemv.qc.ca`, `mail.collegemv.qc.ca`, etc.

+ Une **zone** (en revanche) : représente la partie du domaine qui est réellement gérée par un serveur DNS.
+ La zone peut être plus petite que le domaine si certaines parties ont été **déléguées** à d'autres serveurs.

### La délégation

La délégation est un mécanisme permettant à l’administrateur d’un domaine de confier la gestion de l’un de ses sous-domaines à un autre serveur DNS autoritaire.

Par exemple :

+ le TLD `.ca` délègue `videotron.ca` à Vidéotron,
+ le domaine `.qc.ca` délègue `collegemv.qc.ca` au Collège Marie-Victorin.

Pour effectuer une délégation, l’autorité qui gère une zone doit placer dans son DNS le nom et l’adresse IP des serveurs DNS autoritaire de la zone déléguée. Ces enregistrements se nomment des “glue record”.

Par exemple : on trouve sur le DNS de `.ca` le nom et l’adresse IP des serveurs DNS autoritaires de `videotron.ca`


## Types de requêtes
![Types de requêtes DNS](../images/05-02-02.png?width=500px)

Il existe deux types de requêtes, et comprendre la différence aide énormément à visualiser le fonctionnement du DNS.

1. 🔁 **La requête récursive :** Une requête récursive oblige le serveur interrogé à fournir une réponse complète, même s’il doit pour cela interroger d’autres serveurs.
C’est typiquement ce que fait votre DNS local ou votre routeur lorsque votre ordinateur lui demande de résoudre un nom de domaine.

2. 🔄 **La requête itérative :** 
    + Ici, le serveur interrogé renvoie simplement la meilleure réponse possible.
    + Souvent, cela consiste à dire : « Je ne connais pas la réponse, mais tu peux demander à ce serveur-là. »
    + C’est la méthode utilisée par les DNS récursifs pour parcourir l’arborescence du DNS, depuis les serveurs racine jusqu’au serveur autoritaire final.

### Types de serveurs DNS
Il existe deux types de requêtes DNS:
+ Un **DNS cache/récursif** accepte les requêtes récursives des clients, interroge d’autres serveurs, et conserve temporairement les réponses en cache.
+ Un **DNS autoritaire** détient les informations officielles d’une zone, et ne fait jamais de récursion.


## Les types d’enregistrements
Il existe de nombreux types d’enregistrement. Voici les plus courant : 

|Type|Description|
|----|-----------|
|**A**|adresse IP
|**AAAA**|adresse IPv6|
|**TXT**|texte ou commentaire|
|**PTR**|DNS inverse, pour obtenir un nom|
|**CNAME**|Canonical Name (alias)|
|**MX**|nom du serveur de courriels d’un domaine|
|**SRV**|permet de définir un serveur comme assurant un service donné|


## Références
+ Documentation de Bind (en français) https://wiki.debian.org/fr/Bind9
+ DNS for rocket scientists http://www.zytrax.com/books/dns/
+ Documentation Bind https://www.isc.org/downloads/bind/doc/