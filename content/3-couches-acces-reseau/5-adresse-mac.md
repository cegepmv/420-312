+++
pre = '<b>5. </b>'
title = "Adresse MAC"
weight = "350"
+++
-------------

Une **adresse MAC** (*Media Access Control*) est l'identifiant utilisé par Ethernet pour identifier une interface réseau sur le réseau local.

Une adresse MAC Ethernet classique est composée de **48 bits**, généralement représentés sous la forme de 12 chiffres hexadécimaux.

Exemples :

```text
00:05:9A:3C:78:00
00-05-9A-3C-78-00
0005.9A3C.7800
```

Une adresse MAC est généralement divisée en deux parties :

- les premiers bits identifient l'organisation ayant reçu le préfixe (**OUI**);
- les bits restants permettent d'identifier l'interface.

![Adresse MAC](../images/02-17.png?width=28rem)

### Unicast, broadcast

Une adresse MAC peut être utilisée pour différents types de communication.

#### Unicast

![Exemple de trame avec adresse Unicast](../images/02-21.png?width=30rem)

Une trame **unicast** est destinée à une interface précise.

```text
Ordinateur A ─────────► Ordinateur B
```

#### Broadcast

![Exemple de trame avec adresse MAC broadcast](../images/02-22.png?width=30rem)

Une trame **broadcast** est destinée à **toutes les interfaces du réseau local**.

L'adresse MAC de broadcast est :

```text
FF:FF:FF:FF:FF:FF
```



