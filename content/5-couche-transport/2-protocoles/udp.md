+++
pre="<b>2. </b>"
title = "UDP"
weight = "422"
+++
-------------------

Le protocole **UDP** (*User Datagram Protocol*) est un protocole de transport d’*acheminement au mieux*, décrit dans le document [RFC 768](https://datatracker.ietf.org/doc/html/rfc768). C'est un protocole de transport **léger** qui offre les mêmes fonctions de segmentation et de réorganisation des données que le protocole TCP, mais sans la fiabilité et le contrôle de flux du protocole TCP.

## Caractéristiques
![Caractéristiques du protocole UDP](../../images/04-04-01.png)

+ **Sans connexion –** le protocole UDP n’établit pas de connexion entre les hôtes avant que les données puissent être envoyées et reçues.

+ **Acheminement non fiable –** le protocole UDP ne fournit pas de services garantissant que les données sont acheminées de façon fiable. Il n’existe pas de processus dans le protocole UDP permettant de faire retransmettre à l’expéditeur les données perdues ou endommagées.

+ **Aucune reconstitution ordonnée des données –**  n’offre aucun mécanisme permettant de réorganiser les données dans leur ordre initial. Les données sont simplement remises à l’application dans l’ordre où elles arrivent.

+ **Aucun contrôle de flux –** le protocole UDP ne propose aucun service permettant de contrôler la quantité de données envoyées par la source pour éviter de submerger le périphérique de destination. La source envoie les données. Si les ressources sur l’hôte de destination sont surexploitées, l’hôte de destination abandonne généralement les données envoyées jusqu’à ce que des ressources soient disponibles.

{{% notice title=Remarque style="info"%}}
En général, on définit UDP par ce qu'il n'a pas par rapport à TCP.
{{% /notice %}}

## En-tête UDP

![En-tête UDP](../../images/04-04-02.png)

## Exemples de services

+ Système de noms de domaine (*DNS*)
+ SNMP (*Simple Network Management Protocol*)
+ Protocole DHCP (*Dynamic Host Configuration Protocol*)
+ Protocole RIP (*Routing Information Protocol*)
+ TFTP (*Trivial File Transfer Protocol*)
+ Téléphonie IP ou voix sur IP (*VoIP*)
+ Jeux en ligne