+++
pre = '<b>2. </b>'
title = "Matériel des couches basses"
draft = false
weight = "320"
+++

----------
Les équipements réseau n'ont pas tous le même rôle. Certains travaillent principalement à la couche physique, alors que d'autres utilisent les informations de la couche liaison de données.

### Répéteur

![Répéteur](../images/02-repeteur.jpg?width=13rem)

Un **répéteur** reçoit un signal, le régénère et le retransmet afin de permettre une communication sur une distance plus importante.

Il travaille principalement à la **couche physique (L1)**.


### Concentrateur (*Hub*)

![Concentrateur](../images/02-hub.jpg?width=20rem)

Un **concentrateur**, ou *hub*, est essentiellement un répéteur possédant plusieurs ports.

Lorsqu'il reçoit des bits sur un port, il les retransmet sur les autres ports.

Tous les appareils connectés au concentrateur reçoivent donc les transmissions, même lorsqu'elles ne leur sont pas destinées.

Il fonctionne à la **couche physique (L1)**.



{{%notice style="info" title=" "%}}
Les concentrateurs ont aujourd'hui été largement remplacés par les **commutateurs (switches)**.

{{%/notice%}}
### Commutateur (*Switch*)
![Commutateur](../images/02-switch.png?width=25rem)

Un **commutateur** fonctionne à la **couche liaison de données (L2)**.

Contrairement au concentrateur, il peut déterminer sur quel port se trouve un équipement et transmettre une trame uniquement vers ce port.

Pour effectuer cette tâche, le commutateur utilise notamment les **adresses MAC** des équipements.



{{%notice style="tip" title="À retenir"%}}
Un hub répète les signaux vers plusieurs ports, tandis qu'un switch prend des décisions de transmission à partir des adresses MAC.
{{%/notice%}}

### Carte réseau

![Carte réseau](../images/02-NIC.jpg?width=22rem)

La **carte réseau**, ou **NIC** (*Network Interface Card*), permet à un ordinateur ou à un autre équipement de communiquer sur un réseau.

Elle peut notamment :

- transmettre et recevoir des signaux;
- posséder une adresse MAC;
- gérer les fonctions Ethernet de la couche liaison;
- être connectée à un câble ou utiliser une technologie sans fil.

