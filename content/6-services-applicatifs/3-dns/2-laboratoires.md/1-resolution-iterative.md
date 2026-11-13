+++
pre = '1- '
title = "Résolution itérative"
weight = "633"
draft = false
+++
-------------
 
## Contexte
 
Dans ce laboratoire, vous allez jouer le rôle d'un **serveur récursif** : en partant d'un serveur racine, vous allez interroger un à un les serveurs de la hiérarchie jusqu'à obtenir l'adresse de :
 
```text
www.wikipedia.org
```
 
Vous utiliserez l'option `+norecurse` de `dig`, qui envoie des **requêtes itératives** : chaque serveur répond avec ce qu'il sait, sans interroger personne d'autre.
 
## Préparation
 
- La machine **client** obtient sa configuration par DHCP et accède à Internet par `dhcp-router` (laboratoire 6.2.1).
- Le serveur DNS `172.16.10.2` n'est pas encore configuré : dans ce laboratoire, chaque commande précise le serveur interrogé avec `@`.
- Toutes les commandes s'exécutent dans un terminal de **client**.

## 1- Trouver les serveurs racine
 
```bash
dig @8.8.8.8 . NS
```
 
### Questions
 
1. Combien de serveurs racine la réponse contient-elle? Quel est leur nom?

Pour la suite, vous utiliserez `a.root-servers.net`, dont l'adresse est `198.41.0.4`.

Vous pouvez le vérifier en exécutant la commande `dig` suivante : 
```bash
dig @8.8.8.8 a.root-servers.net. A
```
 
## 2- Interroger la racine
 
```bash
dig +norecurse @198.41.0.4 www.wikipedia.org
```
ou
```bash
dig +norecurse @a.root-servers.net. www.wikipedia.org
```
 
### Questions
 
1. La section `ANSWER` contient-elle une réponse? Quel est le `status`?
2. Que contient la section `AUTHORITY`? Pour quelle zone?
3. Que contient la section `ADDITIONAL`? 

Relevez l'adresse IPv4 d'un des serveurs.

## 3- Interroger le TLD
 
Interrogez l'un des serveurs relevés à l'étape précédente :
 
```bash
dig +norecurse @<ADRESSE_OU_NOM_DU_SERVEUR_TLD> www.wikipedia.org
```
 
Relevez le nom et l'adresse IPv4 d'un des serveurs de `wikipedia.org`.
 
<!-- {{%notice style="tip" title="Pas d'adresse dans ADDITIONAL?"%}}
Si la section `ADDITIONAL` ne donne pas l'adresse d'un serveur, résolvez son nom avec un serveur récursif :
```bash
dig @8.8.8.8 +short <NOM_DU_SERVEUR>
``` 
{{%/notice%}}-->
 
## 4- Interroger le serveur autoritaire
 
```bash
dig +norecurse @<ADRESSE_DU_SERVEUR_WIKIPEDIA> www.wikipedia.org
```
 
### Questions
 
1. Quel drapeau (*flag*) indique que ce serveur est autoritaire?
2. La réponse est-elle une adresse IP? Quel type d'enregistrement obtenez-vous?
3. Que devez-vous faire pour obtenir l'adresse IP finale? Faites-le.
## 5- Comparer avec dig +trace
 
L'option `+trace` effectue automatiquement la même démarche :
 
```bash
dig +trace @8.8.8.8 www.wikipedia.org
```
 
Complétez le tableau à partir de vos résultats :
 
| Étape | Serveur interrogé (nom et adresse) | Zone dont il est responsable | Ce qu'il répond |
|-------|------------------------------------|------------------------------|-----------------|
| 1 | | Racine | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
 
## Questions de synthèse
 
1. Combien de serveurs avez-vous interrogés pour obtenir l'adresse? Lesquels auraient été évités si un serveur récursif avait déjà eu les serveurs de `org` en cache?
<!-- 2. Pourquoi les serveurs racine et TLD donnent-ils l'**adresse** des serveurs suivants dans la section `ADDITIONAL`, et pas seulement leur nom? -->
2. Dans ce laboratoire, qui a joué le rôle du serveur récursif? Dans une situation normale, quelle machine fait ce travail pour le client?