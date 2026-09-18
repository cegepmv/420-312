+++
pre = '<b>1. </b>'
title = "Adressage IP"
weight = "410"
+++
-------------------

Une adresse **IPv4** est une valeur de **32 bits**, généralement représentée sous la forme de quatre nombres décimaux séparés par des points.

Exemple :

```text
192.168.1.25
```

Chaque nombre représente un **octet** et peut avoir une valeur comprise entre `0` et `255`.

Une adresse IPv4 complète contient donc :

```text
4 octets × 8 bits = 32 bits
```

Une adresse IPv4 seule ne permet cependant pas de déterminer quelle partie identifie le réseau et quelle partie identifie l'hôte.

Cette information est fournie par le **préfixe réseau**.


## Préfixe CIDR

La notation **CIDR (*Classless Inter-Domain Routing*)** permet d'indiquer le nombre de bits utilisés pour identifier le réseau.

Exemple :

![Exemple de séparation IP en partie réseau et partie hôte](/32-2.png?width=35rem)

Le `/24` signifie que les **24 premiers bits** appartiennent à la partie réseau.

Les **8 bits restants** appartiennent à la partie hôte.

Le préfixe est donc essentiel pour interpréter une adresse IPv4.

---

## Préfixe et masque de sous-réseau

Le préfixe CIDR peut également être représenté à l'aide d'un **masque de sous-réseau**.

Par exemple :

```text
/24
```

signifie :

```text
11111111.11111111.11111111.00000000
```

Les bits à `1` correspondent à la **partie réseau**.

Les bits à `0` correspondent à la **partie hôte**.

En notation décimale :

```text
11111111 = 255
11111111 = 255
11111111 = 255
00000000 = 0
```

Donc :

```text
/24 = 255.255.255.0
```

On peut donc représenter la même information de deux façons :

```text
192.168.1.25/24

ou

192.168.1.25
255.255.255.0
```

### Quelques équivalences importantes

| Préfixe | Masque            | Bits réseau | Bits hôte |
| ------- | ------------------ | ----------- | --------- |
| `/8`    | `255.0.0.0`         | 8           | 24        |
| `/16`   | `255.255.0.0`       | 16          | 16        |
| `/24`   | `255.255.255.0`     | 24          | 8         |
| `/25`   | `255.255.255.128`   | 25          | 7         |
| `/26`   | `255.255.255.192`   | 26          | 6         |
| `/27`   | `255.255.255.224`   | 27          | 5         |
| `/28`   | `255.255.255.240`   | 28          | 4         |
| `/29`   | `255.255.255.248`   | 29          | 3         |
| `/30`   | `255.255.255.252`   | 30          | 2         |

{{%notice style="tip" title="À retenir"%}}
Le préfixe `/n` indique directement combien de bits sont réservés à la partie réseau. Les `32 - n` bits restants constituent la partie hôte.
{{%/notice%}}