+++
pre = '3- '
title = "Fermeture de connexion"
slug = '3-fermeture-de-connexion'
weight = "523"
draft = false
+++
-------------

Chaque sens de la connexion est fermé séparément.

![Fermeture de connexion TCP](/images/05/05-tcp-fermeture.png)

1. Le client n'a plus de données à envoyer : il envoie un segment **FIN**.
2. Le serveur accuse réception.
3. Lorsqu'il a lui aussi terminé, le serveur envoie son propre **FIN**.
4. Le client accuse réception.

{{%notice style="info" title="Remarque"%}}
En pratique, le serveur combine souvent les étapes 2 et 3 dans un seul segment `FIN, ACK`. On observe alors seulement **trois segments**.
{{%/notice%}}

## Le segment RST

Un segment **RST** interrompt une connexion immédiatement, sans l'échange FIN/ACK.

Le cas le plus courant : un client tente de se connecter à un port sur lequel **aucune application n'écoute**. Le système de l'hôte de destination répond au SYN par un RST.

![Segment RST et refus de connexion](/images/05/05-tcp-rst.png)

{{%notice style="tip" title="À retenir"%}}
Une communication TCP suit toujours trois étapes : **établissement** (SYN, SYN-ACK, ACK), **échange de données** (SEQ/ACK), **fermeture** (FIN/ACK).
{{%/notice%}}
