+++
title = "1- Plan d’adressage avec VLSM"
weight = "471"
draft = true
+++
-------------

### 1. Analyse des besoins

À partir du nombre d’hôtes requis pour chaque département, déterminez pour chacun :

1. Le nombre minimal de bits nécessaires pour les hôtes.
2. Le nombre total d'adresses disponibles dans le sous-réseau.
3. Le préfixe CIDR approprié.
4. Le masque de sous-réseau correspondant.

Complétez le tableau :

| Département | Hôtes requis | Bits hôtes nécessaires | Préfixe | Nombre d’adresses |
| ----------- | -----------: | ---------------------: | ------: | ----------------: |
| Direction   |          100 |                        |         |                   |
| RH          |           50 |                        |         |                   |
| IT          |           25 |                        |         |                   |
| Finance     |           10 |                        |         |                   |
| WAN         |            2 |                        |         |                   |

{{%notice style="tip" title="Rappel"%}}
Pour un réseau *IPv4* classique, deux adresses sont réservées : l’adresse réseau et l’adresse de diffusion (broadcast).
{{%/notice%}}

### 2. Déterminer l’ordre d’allocation

Avec la méthode **VLSM**, les sous-réseaux doivent être attribués en commençant généralement par les besoins les plus importants.

Classez donc les réseaux du plus grand au plus petit.

### 3. Construire le plan d’adressage

À partir du bloc :
```text
172.16.0.0/16
```
Découpez l’espace d’adressage à l’aide de **VLSM**.

Pour chaque réseau, déterminez :

* l’adresse réseau ;
* le préfixe CIDR ;
* le masque de sous-réseau ;
* la première adresse hôte ;
* la dernière adresse hôte ;
* l’adresse de broadcast ;
* l’adresse à utiliser comme passerelle par défaut.

### Tableau à compléter

| Réseau    | Besoin | Adresse réseau | Préfixe | Masque | Première adresse hôte | Dernière adresse hôte | Broadcast | Passerelle |
| --------- | -----: | -------------- | ------- | ------ | --------------------- | --------------------- | --------- | ---------- |
| Direction |    100 |                |         |        |                       |                       |           |            |
| RH        |     50 |                |         |        |                       |                       |           |            |
| IT        |     25 |                |         |        |                       |                       |           |            |
| Finance   |     10 |                |         |        |                       |                       |           |            |
| WAN R1–R2 |      2 |                |         |        |                       |                       |           |            |

### Consigne importante

Pour chacun des quatre réseaux locaux, **la première adresse hôte doit être attribuée à l’interface du routeur** et servir de passerelle par défaut.

Par exemple, si un réseau est :
```text
172.16.0.0/25
```

alors la passerelle devra être :
```text
172.16.0.1
```
