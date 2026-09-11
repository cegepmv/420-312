+++
title = '2- La commande ip'
draft = false
weight = "272"
+++
---------------------

Dans ce laboratoire, nous allons nous concentrer sur la **gestion d'une interface réseau Linux** avec la commande `ip`.

## Objectifs

+ consulter une interface ;
+ modifier son état ;
+ ajouter et supprimer une adresse IP ;
+ observer les routes ;
+ observer ARP ;
+ effectuer des modifications temporaires.

{{%notice style="warning" title="Rappel"%}}

Les modifications effectuées directement avec la commande `ip` ne constituent généralement pas une configuration permanente.

Elles sont utilisées ici pour expérimenter et comprendre le fonctionnement du réseau.
{{%/notice%}}

1. Identifiez les interfaces de votre machine : l'interface **loopback**, l'interface **Ethernet**, son adresse **MAC**, son adresse **IP** puis **l'état** de l'interface.
2. Désactivez votre interface **Ethernet**, revérifiez son état, puis réactivez-la et vérifiez à nouveau.

**Question :** Quelle différence faites-vous entre l'état **UP** et **DOWN** ?

3. Ajoutez temporairement l'adresse IP `192.168.20.10/24` à votre interface Ethernet. Vérifiez puis ajoutez en une troisième : `192.168.20.11/24`.


**Questions**
+ Combien d'adresses IPv4 l'interface possède-t-elle maintenant ?
+ Est-il possible d'avoir plusieurs adresses IP sur une même interface ?

4. Redémarrez ensuite votre VM. L'interface a t-elle gardé les changements au redémarrage?
5. Identifiez la passerelle par défaut, puis ajoutez en une nouvelle.
6. Allumez une deuxième VM, dans le même mode que celle-ci.
7. Observez le cache ARP de votre VM, puis effectuez un ping vers la deuxième VM. Expliquez l'apparition éventuelle d'une nouvelle entrée. 

**Question :** L'adresse MAC correspond-elle à celle de l'interface de la **VM2**?
