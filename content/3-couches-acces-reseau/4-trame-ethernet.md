+++
pre = '<b>4. </b>'
title = "La trame Ethernet"
slug = "4-trame-ethernet"
weight = "340"
draft = false
+++

---------------

Lorsqu'un paquet provenant de la couche réseau doit être transmis sur Ethernet, il est encapsulé dans une **trame Ethernet**.

De façon simplifiée, une trame contient :

![Structure d'une trame Ethernet](/images/03/03-02-trame-ethernet.webp)

| Champ | Rôle |
|---|---|
| **Préambule** | Indique le début de la trame|
| **Adresse MAC destination** | Identifie le destinataire sur le réseau local |
| **Adresse MAC source** | Identifie l'émetteur |
| **Type** | Utilisé par LLC. Indique le protocole transporté (par exemple IPv4) |
| **Données** | Contient le paquet provenant de la couche réseau |
| **FCS** | Permet de détecter certaines erreurs de transmission |