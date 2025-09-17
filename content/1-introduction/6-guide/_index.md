+++
pre = '<b>A. </b>'
title = 'Astuce'
draft = false
weight = "160"
+++
-----------------------

Dans le cas où nous connaissons l'adresse IP d'un équipement sur le réseau (par exemple `192.168.10.50`), comment faire pour connaître le constructeur et déduire la nature de cette machine ?

1. Utiliser la commande `ping` pour communiquer pour la première fois avec cette machine. Cela remplit la table ARP de notre machine avec l'adresse MAC associée à l'adresse `192.168.10.50` : 
```bash
ping 192.168.10.50
``` 

2. Lancer la commande `arp -a` pour afficher la table ARP. Dans la table, indentifier l'adresse MAC associée à l'adresse IP `192.168.10.50`.

3. Récupérer l'adresse MAC et recherchez le fabriquant de ce périphérique sur internet ([exemple de site](https://dnschecker.org/mac-lookup.php)).