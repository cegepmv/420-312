+++
pre = '2- '
title = "Fiabilité et contrôle de flux"
slug = '2-controle-de-flux'
weight = "522"
draft = false
+++
-------------

## Numéros d'ordre et accusés de réception

TCP numérote chaque **octet** envoyé.

- Le **SEQ** d'un segment indique le numéro de son premier octet de données.
- L'**ACK** indique le numéro du **prochain octet attendu**. Il confirme donc la réception de tous les octets précédents.

On a donc toujours :

```text
ACK renvoyé = SEQ reçu + nombre d'octets de données reçus
```

### Exemple

Après le handshake (en numéros relatifs), le client envoie une requête de 100 octets. Le serveur répond avec 500 octets.

![Numéros d'ordre et accusés de réception](/images/05/05-tcp-seq-ack.png)
{{%center%}}
*L'ACK indique le prochain octet attendu : SEQ reçu + nombre d'octets reçus.*
{{%/center%}}

- Le serveur a reçu les octets `1` à `100` : il attend l'octet `101` → `ACK=101`.
- Le client a reçu les octets `1` à `500` : il attend l'octet `501` → `ACK=501`.
- Le prochain segment du client commencera à l'octet `101` → `SEQ=101`.

{{%notice style="tip" title="SEQ différent pour le client et le serveur"%}}
Chaque côté a sa **propre numérotation** : le SEQ du client et le SEQ du serveur évoluent indépendamment.
{{%/notice%}}
## Retransmission

Lorsqu'il envoie un segment, l'émetteur démarre un délai d'attente.

Si l'accusé de réception n'arrive pas avant la fin de ce délai, il considère que le segment est perdu et le **retransmet**.

![Retransmission TCP](/images/05/05-tcp-retransmission.png)
{{%center%}}
*Sans accusé de réception avant la fin du délai, l'émetteur retransmet le segment.*
{{%/center%}}


## Reconstitution ordonnée

Les segments peuvent arriver dans le désordre, par exemple s'ils empruntent des chemins différents.

Le récepteur place les segments reçus dans une **mémoire tampon** et utilise les numéros d'ordre pour les remettre dans le bon ordre avant de transmettre les données à l'application.

## Contrôle de flux : la fenêtre

Le récepteur dispose d'une mémoire tampon de taille limitée.

Dans chaque segment, il indique dans le champ **Fenêtre** combien d'octets il peut encore recevoir. L'émetteur peut envoyer cette quantité de données **sans attendre d'accusé de réception**.


### Exemple

Avec une fenêtre de `3000` octets et un MSS de `1000` octets, l'émetteur peut envoyer 3 segments avant de devoir attendre un ACK :

![Exemple de taille de fenêtre](/images/05/05-taille-de-fenetre.png?width=45rem)


Si le récepteur est surchargé, il annonce une fenêtre plus petite, ce qui ralentit l'émetteur. Une fenêtre de `0` lui demande d'arrêter temporairement d'envoyer :

![Exemple de changement de taille de fenêtre après encombrement du serveur](/images/05/05-changement-taille-de-fenetre.png)

{{%notice style="tip" title="MSS ou fenêtre?"%}}
Le **MSS** limite la taille d'**un** segment; il est annoncé une fois, à l'ouverture de la connexion. La **fenêtre** limite la quantité de données envoyées **sans accusé de réception**; elle est annoncée dans chaque segment et peut varier.
{{%/notice%}}