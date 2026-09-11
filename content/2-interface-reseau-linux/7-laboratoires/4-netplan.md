+++
title = '4- Configuration avec Netplan'
draft = false
weight = "274"
+++
---------------------

Ce laboratoire est réalisé sur la VM `ubuntu-server`.

### Objectifs

Vous allez apprendre à :

+ trouver une configuration Netplan ;
+ comprendre sa structure ;
+ configurer une adresse IP statique ;
+ configurer une route par défaut ;
+ configurer des serveurs DNS ;
+ revenir à DHCP.

1. Vérifiez les fichiers de configuration **Netplan**. Identifiez le nom de l'interface de type **Ethernet** 

2. Configurez l'interface de façon statique en gardant **la même configuration reçue par DHCP** (IP, passerelle et DNS)
3. Testez la configuration (ping, internet, résolution de nom, etc...)
4. Si la résolution fonctionne, vérifiez également le fichier `/etc/resolv.conf`

##### Questions
+ Pourquoi le contenu de `/etc/resolv.conf` peut-il être différent de ce que vous avez directement écrit dans votre fichier **Netplan** ?
+ Explorez les commentaires du fichier `/etc/resolv.conf`, une commande est conseillée pour vérifier l'état de la résolution de nom (e.g. quel serveur DNS est configuré/utilisé).


5. Revenez au mode **DHCP** puis faites les tests nécessaires pour vérifier que tout fonctionne comme prévu (ping, internet, résolution de nom, etc...).
6. Démarrez la VM du laboratoire 3, ajoutez les bonnes informations au fichier `/etc/hosts` des deux machines pour qu'elles puissent utiliser des noms à la place de leur adresse IP. 
7. Testez en faisant des ping. Pour les courageux, essayez de vous connecter par SSH.