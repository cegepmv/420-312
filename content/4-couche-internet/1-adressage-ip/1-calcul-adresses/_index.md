+++
title = "Calcul d'adresses"
weight = "411"
draft = false
+++
-------------

## Conversion d'une adresse IPv4 en binaire

Pour comprendre le fonctionnement des masques et des sous-réseaux, il est important de savoir convertir les octets IPv4 en binaire.

Les valeurs possibles d'un octet sont :

| Bit | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| --- | --- | -- | -- | -- | - | - | - | - |

Pour convertir un nombre décimal :

1. commencer par `128`;
2. si le nombre restant est supérieur ou égal à la valeur du bit, écrire `1` et soustraire cette valeur;
3. sinon, écrire `0`;
4. continuer jusqu'au bit `1`.

##### Exemple : `192`

```text
192 - 128 = 64
64 - 64 = 0
```

Donc :

```text
192 = 11000000
```

##### Exemple : `54`

```text
54 < 128        → 0
54 < 64         → 0
54 - 32   = 22  → 1
22 - 16   = 6   → 1
6 < 8           → 0
6 - 4     = 2   → 1
2 - 2     = 0   → 1
0 < 1           → 0
```

Donc :

```text
54 = 00110110
```

##### Exemple : `168`

```text
168 - 128 = 40  → 1
40 < 64         → 0
40 - 32   = 8   → 1
8 < 16          → 0
8 - 8     = 0   → 1
0 < 4           → 0
0 < 2           → 0
0 < 1           → 0
```

Donc :

```text
168 = 10101000
```

### Exemple complet

Pour :

```text
192.168.54.12
```

on obtient :

```text
192 = 11000000
168 = 10101000
54  = 00110110
12  = 00001100
```

Donc :

```text
   192  .  168   .   54   .   12

11000000.10101000.00110110.00001100
```


## Adresses réseau, d'hôtes et broadcast

Une adresse IPv4 accompagnée d'un préfixe permet de déterminer plusieurs informations.

Prenons :

```text
192.168.1.25/24
```

Le masque est :

```text
255.255.255.0
```

La partie réseau contient 24 bits et la partie hôte 8 bits.

```text
IP :

11000000.10101000.00000001.00011001

Masque :

11111111.11111111.11111111.00000000
└────── partie réseau ────┘
                          └ partie hôte ┘
```

### Adresse réseau

Pour trouver l'adresse réseau :

> conserver les bits de la partie réseau et mettre tous les bits de la partie hôte à `0`.

```text
11000000.10101000.00000001.00000000
```

Ce qui donne :

```text
192.168.1.0
```

### Première adresse hôte

Dans un sous-réseau IPv4 classique, la première adresse utilisable est généralement l'adresse réseau + 1 :

```text
192.168.1.1
```

### Adresse de broadcast

Pour trouver l'adresse de broadcast :

> conserver les bits de la partie réseau et mettre tous les bits de la partie hôte à `1`.

```text
11000000.10101000.00000001.11111111
```

Ce qui donne :

```text
192.168.1.255
```

---

### Dernière adresse hôte

Dans un sous-réseau IPv4 classique, la dernière adresse utilisable est généralement l'adresse de broadcast - 1 :

```text
192.168.1.254
```

### Résultat

| Type                   | Adresse         |
| ---------------------- | --------------- |
| Adresse réseau         | `192.168.1.0`   |
| Première adresse hôte  | `192.168.1.1`   |
| Dernière adresse hôte  | `192.168.1.254` |
| Broadcast              | `192.168.1.255` |


## Calculer le nombre d'adresses

Le nombre de bits disponibles pour les hôtes dépend du préfixe.

Pour un réseau `/24` :

```text
32 - 24 = 8 bits hôte
```

Le nombre total d'adresses est :
```text
2^8 = 256
```

Dans un réseau IPv4 classique, l'adresse réseau et l'adresse de broadcast ne sont pas attribuées à des hôtes.

Il reste donc :
```text
256 - 2 = 254
```

adresses utilisables.

### Formules

Pour un réseau IPv4 classique `/n` :
```text

bits hôte = 32 - n

adresses totales = 2^{32-n}

hôtes utilisables = 2^{32-n}-2
```

<!-- > Pour les exercices classiques de ce chapitre, on utilise cette formule. Les préfixes `/31` et `/32` sont des cas particuliers. -->

#### Exemple : `/26`

```text
32 - 26 = 6 bits hôte
```

Donc :
```text
2^6 = 64
```

adresses au total.

Et :
```text
64 - 2 = 62
```
hôtes utilisables.
