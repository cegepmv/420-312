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
