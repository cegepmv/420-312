+++
title = "Exercices"
weight = "413"
draft=false
+++
-------------

## 1 — Même réseau ou réseau différent ?

Déterminez si les deux hôtes appartiennent au même sous-réseau.

|Hôte A|	Hôte B|
|-------|-------|
|`192.168.1.10/24`|	`192.168.1.200/24`|
|`192.168.1.10/25`|	`192.168.1.200/25`|
|`10.0.5.20/16`|	`10.0.200.30/16`|
|`172.16.10.15/20`|	`172.16.20.25/20`|
|`192.168.50.65/26`|	`192.168.50.120/26`|

Justifiez chaque réponse à l'aide du masque.


## Création de sous-réseaux

### 1 — Diviser un /24

On dispose du réseau :
```text
192.168.20.0/24
```

On souhaite créer 4 sous-réseaux de taille égale.

Déterminez :

1. le nouveau préfixe;
2. le masque;
3. le nombre d'adresses par sous-réseau;
4. le nombre d'hôtes utilisables;
5. les quatre adresses réseau;
6. les quatre adresses de broadcast;
7. les plages d'adresses utilisables.

### 2 — Diviser un réseau en 8

On dispose de :
```text
192.168.50.0/24
```
Créez 8 sous-réseaux de taille égale.

Présentez les résultats sous forme de tableau :

|Réseau|	Première adresse|	Dernière adresse|	Broadcast|
|-----|-----|-----| -----|
| | | | |

### 3 — Répondre à un besoin en hôtes

Quel préfixe choisir pour créer un réseau pouvant accueillir au minimum :

1. 10 hôtes;
2. 25 hôtes;
3. 50 hôtes;
4. 100 hôtes;
5. 500 hôtes?

Pour chaque cas, indiquez :

+ le nombre de bits hôte;
+ le préfixe;
+ le masque;
+ le nombre d'hôtes utilisables.
