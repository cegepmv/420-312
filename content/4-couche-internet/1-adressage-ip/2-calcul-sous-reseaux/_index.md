+++
title = "Calcul de sous-réseaux"
weight = "412"
draft=false
+++


---------

## Méthode complète

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


##### 1 — Trouver le masque

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

##### 2 — Convertir l'adresse en binaire

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

##### 3 — Adresse réseau

Mettre tous les bits hôte à `0` :

```text
00001010.01100101.0110001|0.00000000
```

On obtient :

```text
10.101.98.0
```

##### 4 — Première adresse hôte

```text
10.101.98.1
```

##### 5 — Adresse de broadcast

Mettre tous les bits hôte à `1` :

```text
00001010.01100101.0110001|1.11111111
```

On obtient :

```text
10.101.99.255
```

##### 6 — Dernière adresse hôte

```text
10.101.99.254
```

##### Résultat

| Type d'adresse         | Adresse           |
| ---------------------- | ------------------ |
| Adresse réseau         | `10.101.98.0/23`   |
| Première adresse hôte  | `10.101.98.1`      |
| Dernière adresse hôte  | `10.101.99.254`    |
| Broadcast              | `10.101.99.255`    |

Il reste 9 bits pour les hôtes :
```text
2^9 = 512
```
Donc :
```text
512 - 2 = 510
```
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
```text
256 - 192 = 64
```
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
{{%notice style="tip" title=""%}}
La méthode de la taille du bloc est particulièrement utile lorsque les calculs deviennent nombreux. La méthode binaire reste toutefois essentielle pour comprendre le fonctionnement des préfixes et des masques.
{{%/notice%}}
