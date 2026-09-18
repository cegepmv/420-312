+++
pre = '<b>4. </b>'
title = "La trame Ethernet"
weight = "340"
+++

---------------

Lorsqu'un paquet provenant de la couche réseau doit être transmis sur Ethernet, il est encapsulé dans une **trame Ethernet**.

De façon simplifiée, une trame contient :

![Structure d'une trame Ethernet](/03-02-trame-ethernet.webp)

| Champ | Rôle |
|---|---|
| **Préambule** | Indique le début de la trame|
| **Adresse MAC destination** | Identifie le destinataire sur le réseau local |
| **Adresse MAC source** | Identifie l'émetteur |
| **Type** | Utilisé par LLC. Indique le protocole transporté (par exemple IPv4) |
| **Données** | Contient le paquet provenant de la couche réseau |
| **FCS** | Permet de détecter certaines erreurs de transmission |



<!-- La trame Ethernet possède une taille minimale et maximale définies par les normes Ethernet classiques.

Pour une trame Ethernet II standard, la taille du champ allant de l'adresse MAC destination jusqu'au FCS est généralement comprise entre **64 et 1518 octets**.

--- -->