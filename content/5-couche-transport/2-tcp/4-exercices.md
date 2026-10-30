+++
title = "Exercices"
slug = '4-exercices'
weight = "524"
draft = false
+++
-------------

### 1 — Compléter un échange

Après l'établissement d'une connexion (numéros relatifs), on observe l'échange suivant. Complétez les valeurs manquantes.

| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 80 |
| 2 | Serveur → Client | `1` | ? | 1200 |
| 3 | Client → Serveur | ? | ? | 0 |
| 4 | Client → Serveur | ? | ? | 40 |
| 5 | Serveur → Client | ? | ? | 0 |

<!-- {{% expand title="Solution" %}}
| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 80 |
| 2 | Serveur → Client | `1` | `81` | 1200 |
| 3 | Client → Serveur | `81` | `1201` | 0 |
| 4 | Client → Serveur | `81` | `1201` | 40 |
| 5 | Serveur → Client | `1201` | `121` | 0 |

Le segment 3 ne contient pas de données : il ne fait pas avancer le SEQ du client. Le segment 4 commence donc aussi à `81`.
{{% /expand %}} -->


### 2 — Identifier un segment

Pour chaque segment, indiquez sa fonction.

1. Bits de contrôle : `SYN`
2. Bits de contrôle : `SYN, ACK`
3. Bits de contrôle : `ACK`, 0 octet de données, juste après un SYN-ACK
4. Bits de contrôle : `PSH, ACK`, 350 octets de données
5. Bits de contrôle : `FIN, ACK`
6. Bits de contrôle : `RST, ACK`, en réponse à un SYN

<!-- {{% expand title="Solution" %}}
1. Demande d'ouverture de connexion (étape 1 du handshake).
2. Acceptation de la connexion par le serveur (étape 2).
3. Dernière étape du handshake : la connexion est établie.
4. Envoi de données (avec accusé de réception des données reçues).
5. Demande de fermeture d'un sens de la connexion.
6. Connexion refusée : aucune application n'écoute sur le port demandé.
{{% /expand %}} -->

### 3 — Raisonner

1. Pourquoi TCP a-t-il besoin d'un échange en trois étapes plutôt qu'en deux?
2. Un client reçoit un segment avec `ACK=5001`. Que peut-il en conclure?
3. Un serveur annonce une fenêtre de `0`. Que doit faire le client?

<!-- {{% expand title="Solution" %}}
1. La connexion est bidirectionnelle : chaque hôte doit annoncer son numéro d'ordre initial (SYN) **et** recevoir la confirmation que l'autre l'a reçu (ACK). Le SYN du serveur et son ACK sont combinés dans un seul segment, ce qui donne trois segments au total.
2. L'autre hôte a reçu correctement tous les octets jusqu'au numéro `5000` et attend maintenant l'octet `5001`.
3. Il doit cesser temporairement d'envoyer des données, jusqu'à ce que le serveur annonce une fenêtre plus grande.
{{% /expand %}} -->



### 4 — Compléter un échange (2)

Après l'établissement d'une connexion (numéros relatifs), on observe l'échange suivant. Complétez les valeurs manquantes.

| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 250 |
| 2 | Serveur → Client | `1` | ? | 0 |
| 3 | Serveur → Client | ? | ? | 900 |
| 4 | Serveur → Client | ? | ? | 600 |
| 5 | Client → Serveur | ? | ? | 0 |
| 6 | Client → Serveur | ? | ? | 30 |
| 7 | Serveur → Client | ? | ? | 0 |

<!-- {{% expand title="Solution" %}}
| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 250 |
| 2 | Serveur → Client | `1` | `251` | 0 |
| 3 | Serveur → Client | `1` | `251` | 900 |
| 4 | Serveur → Client | `901` | `251` | 600 |
| 5 | Client → Serveur | `251` | `1501` | 0 |
| 6 | Client → Serveur | `251` | `1501` | 30 |
| 7 | Serveur → Client | `1501` | `281` | 0 |

- Le segment 2 ne contient pas de données : le segment 3 commence donc aussi à `SEQ=1`.
- Le segment 4 suit le segment 3 : `1 + 900 = 901`.
- Avec le segment 5, le client confirme avoir reçu les octets `1` à `1500` du serveur (`900 + 600`).
- Le client n'a rien reçu de nouveau entre les segments 5 et 6 : son ACK reste `1501`.
{{% /expand %}} -->

### 5 — Du handshake à la fermeture

Cette fois, les numéros sont **bruts** : le client a choisi le numéro d'ordre initial `4000` et le serveur, `9000`. Complétez les valeurs manquantes.

| # | Sens | Bits | SEQ | ACK | Données (octets) |
|---|------|------|-----|-----|------------------|
| 1 | Client → Serveur | SYN | `4000` | — | 0 |
| 2 | Serveur → Client | SYN, ACK | `9000` | ? | 0 |
| 3 | Client → Serveur | ACK | ? | ? | 0 |
| 4 | Client → Serveur | PSH, ACK | ? | ? | 120 |
| 5 | Serveur → Client | ACK | ? | ? | 1460 |
| 6 | Serveur → Client | PSH, ACK | ? | ? | 540 |
| 7 | Client → Serveur | ACK | ? | ? | 0 |
| 8 | Client → Serveur | FIN, ACK | ? | ? | 0 |
| 9 | Serveur → Client | ACK | ? | ? | 0 |

{{%notice style="tip" title="Astuce"%}}
Les segments SYN et FIN ne contiennent pas de données, mais chacun **compte pour un octet** dans la numérotation.
{{%/notice%}}

<!-- {{% expand title="Solution" %}}
| # | Sens | Bits | SEQ | ACK | Données (octets) |
|---|------|------|-----|-----|------------------|
| 1 | Client → Serveur | SYN | `4000` | — | 0 |
| 2 | Serveur → Client | SYN, ACK | `9000` | `4001` | 0 |
| 3 | Client → Serveur | ACK | `4001` | `9001` | 0 |
| 4 | Client → Serveur | PSH, ACK | `4001` | `9001` | 120 |
| 5 | Serveur → Client | ACK | `9001` | `4121` | 1460 |
| 6 | Serveur → Client | PSH, ACK | `10461` | `4121` | 540 |
| 7 | Client → Serveur | ACK | `4121` | `11001` | 0 |
| 8 | Client → Serveur | FIN, ACK | `4121` | `11001` | 0 |
| 9 | Serveur → Client | ACK | `11001` | `4122` | 0 |

- Le SYN compte pour un octet : `4000 + 1 = 4001` et `9000 + 1 = 9001`.
- Le segment 3 ne contient pas de données : le segment 4 commence donc aussi à `4001`.
- Le segment 6 suit le segment 5 : `9001 + 1460 = 10461`.
- Le client a reçu les octets `9001` à `11000` du serveur (`1460 + 540 = 2000` octets) : il attend l'octet `11001`.
- Le FIN compte pour un octet : le serveur répond `ACK = 4121 + 1 = 4122`.
- Le segment 1 n'a pas de valeur ACK, car le bit ACK n'y est pas activé.
{{% /expand %}} -->
