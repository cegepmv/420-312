+++
title = '3- Configuration avec nmcli'
draft = false
weight = "273"
+++
---------------------

### Objectifs
Utiliser `nmcli` pour :

+ identifier les devices ;
+ identifier les connections ;
+ créer une connection ;
+ configurer une adresse IP statique ;
+ configurer une passerelle ;
+ configurer un DNS ;
+ revenir à DHCP.

1. Listez les **devices** et les **connections** de votre VM.
    + Quel est le nom du device **Ethernet** ?
    + Quelle **connection** lui est associée ?
    + Un **device** peut-il exister sans connection ?
    + Quelle est la différence entre un **device** et une **connection** ?

2. Ajoutez un nouvel adaptateur réseau à votre VM en mode *LAN SEGMENT* puis créez une nouvelle **connection** pour ce **device**. Si une connection est déja présente, supprimez-la et créez en une nouvelle (vérifiez vos résultats).
3. Configurer une adresse statique avec les paramètres suivants :
    + **Adresse IP :** `192.168.20.10/24`
    + **Passerelle :** `192.168.20.1`
    + **DNS        :** `1.1.1.1`

    Vérifiez que l'interface a bien été configurée.

4. Changez le mode de cette 2ème interface en mode **NAT**, puis configurez cette interface avec **NetworkManager** pour utiliser **DHCP**.
5. Faites les vérifications nécessaires : assurez vous d'avoir bien reçu 
    + une adresse IP, 
    + la passerelle par défaut
    + celle du DNS.
6. Vérifiez votre connexion à Internet et la résolution de nom.