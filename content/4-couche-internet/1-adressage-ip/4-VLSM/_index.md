+++
title = "VLSM"
weight = "414"
draft=false
+++
---------
Le **VLSM (*Variable Length Subnet Masking*)** permet de créer des sous-réseaux de tailles différentes à partir d'un même réseau.

Sans VLSM, tous les sous-réseaux auraient la même taille.

Avec VLSM, chaque réseau peut recevoir une taille adaptée à ses besoins.

Cela permet de réduire le gaspillage d'adresses IPv4.

## Exemple

Une entreprise possède :
```text
192.168.100.0/24
```
Elle doit créer quatre réseaux :

|Service|	Hôtes nécessaires|
|--------|--------|
|Administration|	60|
|Développement|	30|
|Formation|	14|
|Gestion|	6|

##### 1 — Classer les besoins

Toujours commencer par le plus grand réseau :

```text
60
30
14
6
```

##### 2 — Déterminer le préfixe de chaque réseau

###### **60 hôtes**
```text
2^6-2=62
```

Il faut donc 6 bits hôte :
```text
/26
```
###### **30 hôtes**
```text
2^5-2=30
```

Donc :
```text
/27
```

###### **14 hôtes**

```text
2^4-2=14
```

Donc :
```text
/28
```

###### **6 hôtes**

```text
2^3-2=6
```

Donc :
```text
/29
```

###### **Résumé**
|Service|	Hôtes|	Préfixe|	Adresses totales|	Hôtes utilisables |
|------|------|------|------|------|
|**Administration**|	60|	/26|	64|	62|
|**Développement**|	30|	/27|	32|	30|
|**Formation**|	14|	/28|	16|	14|
|**Gestion**|	6|	/29|	8|	6|

##### 3 — Attribuer les sous-réseaux

###### **Administration**
```text
192.168.100.0/26
```
Plage :
```text
Réseau    : 192.168.100.0
Hôtes     : 192.168.100.1 → 192.168.100.62
Broadcast : 192.168.100.63
```
Le prochain espace disponible commence à :
```text
192.168.100.64
```
###### **Développement**
```text
192.168.100.64/27
```
Plage :
```text
Réseau    : 192.168.100.64
Hôtes     : 192.168.100.65 → 192.168.100.94
Broadcast : 192.168.100.95
```
Le prochain espace commence à :
```text
192.168.100.96
```

###### **Formation**
```text
192.168.100.96/28
```
Plage :
```text
Réseau    : 192.168.100.96
Hôtes     : 192.168.100.97 → 192.168.100.110
Broadcast : 192.168.100.111
```
Le prochain espace commence à :
```text
192.168.100.112
```
###### **Gestion**
```text
192.168.100.112/29
```
Plage :
```text
Réseau    : 192.168.100.112
Hôtes     : 192.168.100.113 → 192.168.100.118
Broadcast : 192.168.100.119
```
###### **Résultat final**
|Service|	Réseau|	Plage d'adresses hôte	|Broadcast|
|-----|-----|----|----|
|**Administration**|	`192.168.100.0/26`|	`.1` ->	`.62`|	`.63`|
|**Développement**|	`192.168.100.64/27`|	`.65` ->	`.94`|	`.95`|
|**Formation**|	`192.168.100.96/28`|	`.97` ->	`.110`|	`.111`|
|**Gestion**|	`192.168.100.112/29`|	`.113` ->	`.118`|	`.119`|

L'espace restant commence à :
```text
192.168.100.120
```
et se termine à :
```text
192.168.100.255
```