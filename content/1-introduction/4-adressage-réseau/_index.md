+++
pre = '<b>4. </b>'
title = 'Adressages'
draft = false
weight = "140"
+++

***

Pour envoyer une lettre par la poste, les relais postiers ont besoin de connaitre le nom et l'adresse de l'émetteur et du destinataire de la lettre, sans quoi ils ne sauront pas où distribuer le courrier. Sur les réseaux, un mécanisme similaire est utilisé : chaque ordinateur ou périphérique possède une adresse qui lui permet de recevoir ou envoyer des données sur le réseau.

## Types d'adresse
Il existe deux types d'adresse réseau :

+ **Adresses physiques :** Utilisées sur les réseaux locaux, mais qui ne sont pas compatibles avec les réseaux étendus et Internet (leur portée est limitée à un réseau local). Elles sont standardisées par le standard MAC (on les appelle adresses MAC)
+ **Adresses logiques :** Utilisées sur Internet et ont une portée très large, dépassant le réseau local de l’ordinateur. elles sont standardisées par le protocole IP (adresse IP). Utilité : Permet le remplacement d’un ordinateur sans pour autant changer son adresse internet. Par exemple, si un serveur tombe en panne et que l’on le remplace, il garde son adresse IP, alors que son adresse MAC change.

## Adresse MAC

![Exemple d'une adresse MAC](../images/010401-adresse-mac.png)
{{% center %}}
*Exemple d'adresse MAC*
{{% /center %}}

+ Elle se compose de six paires de nombres hexadécimaux pour un total de 12 nombres.
+ Également appelée adresse physique car elle est attribuée physiquement à la carte réseau d'un hôte.
+ Elle ne change pas et est unique.
+ Elle est similaire au nom d’une personne (analogie du courrier postal).

## Adresse IP

![Exemple d'une adresse IP](../images/010402-adresse-IP.png)
{{% center %}}
*Exemple d'adresse IP*
{{% /center %}}

+ Elle se compose 4 nombres décimaux allant de 0 à 255. Chaque nombre décimal peut être représenté par un nombre binaire de 8 bits, pour un total de 32 bits ou 4 octets. 
+ Également appelée adresse logique, car elle est attribuée par logiciel.
+ Elle dépend de l’emplacement (logique) de l’hôte.
+ Elle est attribuée à chaque hôte par l’administrateur réseau (DHCP).
+ Elle est similaire à l’adresse d’une personne.

{{% notice style="info" %}}
L’adresse MAC physique et l’adresse IP logique sont toutes deux requises pour que des hôtes puisse communiquer dans un réseau (comme le nom et l’adresse d’une personne sont nécessaires dans la vie réelle pour envoyer une lettre).
{{% /notice %}}