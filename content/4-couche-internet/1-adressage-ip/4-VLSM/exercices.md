+++
title = "Exercices"
weight = "414"
draft=false
+++
------------
### 1 — VLSM simple

Une entreprise possède :
```text
192.168.10.0/24
```
Elle doit créer les réseaux suivants :

|Service|	Hôtes nécessaires|
|----|-------|
|Administration|	50|
|Développement||	25|
|Support|	12|
|Direction|	5|

Utilisez le VLSM.

Pour chaque service :

1. déterminez le préfixe nécessaire;
2. déterminez le masque;
3. attribuez une adresse réseau;
4. déterminez la plage d'adresses hôte disponible (première-dernière adresse hôte);
5. déterminez le broadcast.

Présentez le résultat dans un tableau.

### 2 — VLSM et optimisation

Une entreprise dispose du réseau :
```text
10.10.0.0/23
```
Elle doit créer les réseaux suivants :

|Service|	Hôtes nécessaires|
|-------|-----------------|
|**Serveurs** |	100 |
|**Employés** |	60 |
|**Wi-Fi** |	40|
|**Administration** |	20|
|**Imprimantes** |	10|
|**Gestion réseau** |	5|

Utilisez le VLSM afin de minimiser l'espace gaspillé.

Vous devez :

1. classer les besoins du plus grand au plus petit;
2. déterminer le préfixe de chaque réseau;
3. attribuer les réseaux sans chevauchement;
4. respecter l'alignement des adresses réseau;
5. calculer les plages d'hôtes;
6. calculer les broadcasts;
7. indiquer l'espace d'adressage restant.