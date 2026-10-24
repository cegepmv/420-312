+++
pre = '<b>2. </b>'
title = "TCP"
slug = '2-tcp'
weight = "520"
draft = false
+++
-------------

**TCP (*Transmission Control Protocol*)** est un protocole de transport **fiable** et **orienté connexion**, défini à l'origine dans la [RFC 793](https://datatracker.ietf.org/doc/html/rfc793).

Il ajoute au-dessus d'IP les garanties qu'IP n'offre pas.

| Caractéristique | Signification |
|-----------------|---------------|
| **Orienté connexion** | Une connexion est établie entre les deux hôtes avant l'échange de données, puis fermée à la fin. |
| **Acheminement fiable** | Le récepteur accuse réception des données; les données perdues sont retransmises. |
| **Reconstitution ordonnée** | Les données sont remises à l'application dans l'ordre d'envoi, même si les segments arrivent dans le désordre. |
| **Contrôle de flux** | L'émetteur n'envoie pas plus de données que le récepteur ne peut en accepter. |

{{%notice style="note" title="Rappel"%}}
IP est un protocole **best effort** : il ne garantit ni la livraison, ni l'ordre d'arrivée des paquets (chapitre 4). TCP compense ces limites au niveau des hôtes, sans rien exiger des routeurs.
{{%/notice%}}

## En-tête TCP

![En-tête TCP](/images/05/05-entete-tcp.png)

| Champ | Fonction |
|-------|----------|
| **Port source** | Port de l'application qui envoie le segment |
| **Port destination** | Port de l'application qui doit recevoir le segment |
| **Numéro d'ordre (SEQ)** | Position, dans le flux, du premier octet de données du segment |
| **Numéro d'accusé de réception (ACK)** | Numéro du **prochain octet attendu** de l'autre hôte |
| **Longueur d'en-tête** | Taille de l'en-tête, en mots de 32 bits (minimum `5`, soit 20 octets) |
| **Bits de contrôle** |  Indiquent la fonction du segment |
| **Fenêtre** |  Nombre d'octets que l'émetteur de ce segment peut encore recevoir |
| **Somme de contrôle** | Détection d'erreurs sur l'en-tête et les données |
| **Options** |  Notamment le **MSS**, annoncé à l'ouverture de la connexion |

L'en-tête TCP mesure **au minimum 20 octets**.

{{%notice style="info" title="Remarque"%}}
Les autres champs de l'en-tête (bits réservés, pointeur d'urgence, autres options) ne sont pas étudiés dans ce cours.
{{%/notice%}}

## Bits de contrôle

Les bits de contrôle (*flags*) indiquent la nature du segment. Plusieurs bits peuvent être activés dans un même segment.

| Bit | Nom | Signification |
|-----|-----|---------------|
| **SYN** | *Synchronize* | Demande d'ouverture de connexion |
| **ACK** | *Acknowledgment* | Le champ ACK contient un accusé de réception valide |
| **FIN** | *Finish* | L'émetteur n'a plus de données à envoyer : demande de fermeture |
| **RST** | *Reset* | Interruption immédiate de la connexion, ou refus de connexion |
| **PSH** | *Push* | Les données doivent être remises sans attendre à l'application |

