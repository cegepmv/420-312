+++
pre = '<b>3. </b>'
title = "UDP"
slug = '3-udp'
weight = "530"
draft = false
+++
-------------

**UDP (*User Datagram Protocol*)** est un protocole de transport **léger**, défini dans la [RFC 768](https://datatracker.ietf.org/doc/html/rfc768).

Comme TCP, il utilise des numéros de port pour identifier les applications. Mais il n'offre **aucune** des autres garanties de TCP.

| Caractéristique | TCP | UDP |
|-----------------|-----|-----|
| Établissement de connexion | Oui (handshake) | **Non** : les données sont envoyées immédiatement |
| Accusés de réception et retransmission | Oui | **Non** : un datagramme perdu est perdu |
| Reconstitution ordonnée | Oui | **Non** : les données sont remises dans l'ordre d'arrivée |
| Contrôle de flux | Oui (fenêtre) | **Non** : un récepteur surchargé abandonne les datagrammes |

{{%notice style="info" title="Remarque"%}}
On décrit généralement UDP par ce qu'il **ne fait pas** par rapport à TCP. Cette simplicité est justement son avantage : moins d'en-tête, aucun échange préalable, aucune attente d'accusé de réception.
{{%/notice%}}

## En-tête UDP

![En-tête UDP](/images/05/05-entete-udp.png)

| Champ | Fonction |
|-------|----------|
| **Port source**  | Port de l'application qui envoie |
| **Port destination** | Port de l'application qui doit recevoir |
| **Longueur** | Taille totale du datagramme (en-tête + données), en octets |
| **Somme de contrôle** | Détection d'erreurs |

L'en-tête UDP mesure **8 octets**, contre **20 octets minimum** pour TCP.

## Fiabilité gérée par l'application

Si une application utilisant UDP a besoin de fiabilité, c'est **l'application elle-même** qui doit la gérer.

Exemple : un client DNS qui ne reçoit pas de réponse à sa requête après un certain délai envoie simplement une nouvelle requête.

## Exemples de services utilisant UDP

- **DNS** (`53`) : requêtes et réponses courtes (chapitre suivant);
- **DHCP** (`67`, `68`) : attribution automatique d'adresses (chapitre suivant);
- **NTP** (`123`) : synchronisation de l'heure;
- **TFTP** (`69`) : transfert de fichiers simplifié;
- la **voix sur IP**, la **visioconférence** et les **jeux en ligne**.

<!-- {{%notice style="note" title="Rappel"%}}
Au chapitre 4, nous avons vu que `traceroute` peut utiliser UDP. Sous Linux, il envoie par défaut des datagrammes UDP vers des ports très élevés, sur lesquels aucune application n'écoute normalement.
{{%/notice%}} -->

{{%notice style="tip" title="À retenir"%}}
UDP = ports + longueur + somme de contrôle. Pas de connexion, pas d'accusé de réception, pas de retransmission, pas d'ordre.
{{%/notice%}}
