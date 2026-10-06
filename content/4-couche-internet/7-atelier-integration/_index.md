+++
pre = "<b>7. </b>"
title = "Atelier synthèse"
weight = "470"
draft = true
+++
-------------

<!-- Atelier synthèse — Conception et configuration d’un réseau IPv4 -->

## Mise en situation

Une entreprise souhaite concevoir le réseau informatique de son organisation.

L’entreprise dispose du bloc d’adresses privé suivant :

```text
172.16.0.0/16
```
Elle est composée de quatre départements :

| Département              | Nombre d’hôtes requis |
| ------------------------ | --------------------: |
| Direction                |                   100 |
| Ressources humaines (RH) |                    50 |
| Informatique (IT)        |                    25 |
| Finance                  |                    10 |

L’entreprise dispose de **deux routeurs**.

Chaque routeur dessert deux départements :

* **Routeur R1**

  * réseau de la Direction
  * réseau des RH
  * liaison WAN vers R2
* **Routeur R2**

  * réseau de l’IT
  * réseau de la Finance
  * liaison WAN vers R1

La liaison entre les deux routeurs est une liaison **point à point**.

### Topologie logique

```text
                 ┌─────────────────────┐
                 │     Direction       │
                 │     100 hôtes       │
                 └─────────┬───────────┘
                           │
                        G0/0
                           │
                      ┌────┴────┐
                      │   R1    │
                      └────┬────┘
                           │
                        G0/1
                           │
                 ┌─────────┴───────────┐
                 │         RH          │
                 │      50 hôtes       │
                 └─────────────────────┘

                      R1
                       │
                 Liaison WAN
                  point à point
                       │
                      R2

                      R2
                 ┌─────┴─────┐
                 │           │
              G0/0         G0/1
                 │           │
        ┌────────┘           └────────┐
        │                             │
   ┌────┴─────┐                 ┌─────┴─────┐
   │    IT    │                 │  Finance  │
   │ 25 hôtes │                 │ 10 hôtes  │
   └──────────┘                 └───────────┘
```

L’objectif est de concevoir le réseau, puis de l’implémenter et de le tester dans **Cisco Packet Tracer**.
