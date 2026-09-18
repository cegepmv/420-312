+++
title = "Calcul de sous-réseaux"
weight = "411"
draft=true
+++

## Méthode complète de calcul d'un sous-réseau

Considérons :

```text
10.101.99.17/23
```

Nous voulons déterminer :

- le masque;
- l'adresse réseau;
- la première adresse hôte;
- la dernière adresse hôte;
- l'adresse de broadcast.

---

### Étape 1 — Trouver le masque

Le `/23` signifie :

```text
23 bits réseau
32 - 23 = 9 bits hôte
```

Le masque binaire est :

```text
11111111.11111111.11111110.00000000
```

Donc :

```text
255.255.254.0
```

---

### Étape 2 — Convertir l'adresse en binaire

```text
10  = 00001010
101 = 01100101
99  = 01100011
17  = 00010001
```

Donc :

```text
10.101.99.17

00001010.01100101.01100011.00010001
```

Le masque :

```text
11111111.11111111.11111110.00000000
```

Les 23 premiers bits sont la partie réseau et les 9 derniers bits sont la partie hôte :

```text
00001010.01100101.0110001|1.00010001
                         ↑
                    séparation
```

### Étape 3 — Adresse réseau

Mettre tous les bits hôte à `0` :

```text
00001010.01100101.0110001|0.00000000
```

On obtient :

```text
10.101.98.0
```

### Étape 4 — Première adresse hôte

```text
10.101.98.1
```

### Étape 5 — Adresse de broadcast

Mettre tous les bits hôte à `1` :

```text
00001010.01100101.0110001|1.11111111
```

On obtient :

```text
10.101.99.255
```

### Étape 6 — Dernière adresse hôte

```text
10.101.99.254
```

### Résultat

| Type d'adresse         | Adresse           |
| ---------------------- | ------------------ |
| Adresse réseau         | `10.101.98.0/23`   |
| Première adresse hôte  | `10.101.98.1`      |
| Dernière adresse hôte  | `10.101.99.254`    |
| Broadcast              | `10.101.99.255`    |

Il reste 9 bits pour les hôtes :

$$2^9 = 512$$

Donc :

$$512 - 2 = 510$$

adresses hôtes utilisables.

---

## Méthode rapide : taille du bloc

La méthode binaire permet de comprendre le fonctionnement du masque, mais il est possible d'effectuer les calculs plus rapidement.

Prenons :

```text
192.168.10.130/26
```

Le masque est :

```text
255.255.255.192
```

Le dernier octet du masque est `192`.

On calcule :

$$256 - 192 = 64$$

La taille du bloc est donc **64**.

Les réseaux commencent tous les 64 :

```text
192.168.10.0
192.168.10.64
192.168.10.128
192.168.10.192
```

`130` se trouve dans l'intervalle :

```text
128 → 191
```

Donc :

```text
Adresse réseau :     192.168.10.128
Broadcast :          192.168.10.191
Première adresse :   192.168.10.129
Dernière adresse :   192.168.10.190
```

> La méthode de la taille du bloc est particulièrement utile lorsque les calculs deviennent nombreux. La méthode binaire reste toutefois essentielle pour comprendre le fonctionnement des préfixes et des masques.

---

## Créer des sous-réseaux

Un réseau peut être divisé en plusieurs sous-réseaux.

Prenons :

```text
192.168.50.0/24
```

On souhaite créer **8 sous-réseaux de taille égale**.

### Étape 1 — Déterminer le nombre de bits à emprunter

On cherche le nombre de bits permettant de créer au moins 8 réseaux :

$$2^3 = 8$$

On emprunte donc **3 bits** à la partie hôte.

Le préfixe devient :

```text
/24 → /27
```

### Étape 2 — Trouver le masque

```text
/27 = 255.255.255.224
```


### Étape 3 — Calculer la taille du bloc

```text
256 - 224 = 32
```

Les sous-réseaux sont donc espacés de 32 adresses.

### Étape 4 — Énumérer les sous-réseaux

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

Chaque réseau possède :

$$2^5 = 32$$

adresses.

Donc :

$$32 - 2 = 30$$

adresses utilisables.


### Résultat

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

---

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