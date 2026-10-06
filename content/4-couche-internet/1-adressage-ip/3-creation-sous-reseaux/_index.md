+++
title = "Création de sous-réseaux"
weight = "413"
draft=false
+++
-----------

Un réseau peut être divisé en plusieurs sous-réseaux.

Prenons :

```text
192.168.50.0/24
```

On souhaite créer **8 sous-réseaux de taille égale**.

##### 1 — Déterminer le nombre de bits à emprunter

On cherche le nombre de bits permettant de créer au moins 8 réseaux :

```text
2^3 = 8
```
On emprunte donc **3 bits** à la partie hôte.

Le préfixe devient :

```text
/24 → /27
```

##### 2 — Trouver le masque

```text
/27 = 255.255.255.224
```


##### 3 — Calculer la taille du bloc

```text
256 - 224 = 32
```

Les sous-réseaux sont donc espacés de 32 adresses.

##### 4 — Énumérer les sous-réseaux

```text
192.168.50.0/27
192.168.50.32/27
192.168.50.64/27
192.168.50.96/27
192.168.50.128/27
192.168.50.160/27
192.168.50.192/27
192.168.50.224/27
```

Chaque réseau possède `2^5 = 32` adresses.

Donc `32 - 2 = 30` adresses utilisables.


##### Résultat

| Réseau                | Plage d'hôtes   | Broadcast |
| ---------------------- | --------------- | --------- |
| `192.168.50.0/27`      | `.1 – .30`      | `.31`     |
| `192.168.50.32/27`     | `.33 – .62`     | `.63`     |
| `192.168.50.64/27`     | `.65 – .94`     | `.95`     |
| `192.168.50.96/27`     | `.97 – .126`    | `.127`    |
| `192.168.50.128/27`    | `.129 – .158`   | `.159`    |
| `192.168.50.160/27`    | `.161 – .190`   | `.191`    |
| `192.168.50.192/27`    | `.193 – .222`   | `.223`    |
| `192.168.50.224/27`    | `.225 – .254`   | `.255`    |

## Déterminer si deux hôtes sont sur le même réseau

Le préfixe permet de déterminer si deux machines appartiennent au même sous-réseau.

Exemple :

```text
Hôte A : 192.168.1.50/26
Hôte B : 192.168.1.100/26
```

Le masque `/26` correspond à :

```text
255.255.255.192
```

Les sous-réseaux possibles sont :

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

`192.168.1.50` appartient à :

```text
192.168.1.0/26
```

`192.168.1.100` appartient à :

```text
192.168.1.64/26
```

Ils appartiennent donc à **deux sous-réseaux différents**.
