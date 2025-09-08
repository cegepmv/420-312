+++
pre = '<b>3. </b>'
title = 'Atelier VMWare'
draft = false
weight = "143"
+++
----------------
### Configuration de l'interface réseau virtuelle
4 modes sont possibles :

+ **Bridged :** En bridge, vous êtes sur le réseau de l’école : votre adresse IP sera dans la même plage d’adresse que la machine hôte.
+ **NAT :** Votre machine virtuelle se trouve sur un réseau virtuel qui n’existe que sur votre hôte. Votre hôte joue le rôle de routeur NAT. Son adresse IP sera celle du réseau NAT.
+ **Host only :** Le moins utilisé car la machine n’a pas accès au réseau physique donc pas d’internet. La machine virtuelle ne peut communiquer qu’avec la machine hôte.
+ **LAN Segment :** Pour utiliser un réseau local virtuel. Sélectionner un LAN Segment revient à connecter votre VM à un "switch virtuel".

#### NAT vs. Bridge vs. Host-Only vs. LAN Segment
*Sur votre poste de travail (le PC physique, pas la machine virtuelle), ouvrez un terminal Windows (tapez “cmd” dans le menu Windows).*

1. Lancez la commande `ipconfig`. Dans chacune des sections quelles sont les valeurs des éléments suivants?

| **Carte Ethernet Ethernet**       |
|-----------------------------------|
| Adresse IPv4 :                    | 
| Passerelle par défaut :           |

| **Carte Ethernet Vmnet1**         |
|-----------------------------------|
| Adresse IPv4 :            |       | 
| Passerelle par défaut :   |       |

| **Carte Ethernet Vmnet8**         |
|-----------------------------------|
| Adresse IPv4 :            |       | 
| Passerelle par défaut :   |       |

*Alors que votre VM est éteinte, mettez le type de son adapteur réseau à NAT. Ensuite démarrez-la.*
1. L'interface `ens160` de votre VM a-t-elle une adresse IP ? Si oui laquelle ?
2. Lancez ensuite la commande `ip route`. Quelle est l’adresse IP de la passerelle par défaut (`default`) ?
3. Arrivez-vous à ping `www.google.com`?

*Alors que votre VM est éteinte, mettez le type de son adapteur réseau à Host-Only. Ensuite démarrez-la.*
1. L'interface `ens160` de votre VM a-t-elle une adresse IP ? Si oui laquelle ?
2. Lancez ensuite la commande `ip route`. Quelle est l’adresse IP de la passerelle par défaut (`default`) ?
3. Arrivez-vous à ping `www.google.com`?

*Alors que votre VM est éteinte, mettez le type de son adapteur réseau à LAN Segment. Ensuite démarrez-la.*
1. L'interface `ens160` de votre VM a-t-elle une adresse IP ? Si oui laquelle ?
2. Lancez ensuite la commande `ip route`. Quelle est l’adresse IP de la passerelle par défaut (`default`) ?
3. Arrivez-vous à ping `www.google.com`?

#### LAN Segment
*Démarrez deux VM et mettez le type de leur adapteur réseau à LAN Segment. Ensuite démarrez-les.*
1. Ajoutez l'adresse IP `192.168.10.10/24` à l'interface de la première VM, puis `192.168.10.20/24` à la deuxième.
2. Arrivez-vous à `ping` la première VM à partir de la deuxième ?

*Alors que votre deuxième VM est éteinte, mettez le type de son adapteur réseau dans un LAN Segment différent de la première VM (créez en un nouveau). Ensuite démarrez-la.*
1. Arrivez-vous à `ping` la première VM à partir de la deuxième ?
