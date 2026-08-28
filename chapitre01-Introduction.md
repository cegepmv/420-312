
# 1. Introduction"

-------------------
Un réseau informatique peut être défini comme un *"ensemble d’équipements informatiques variés (ordinateurs, imprimantes, périphériques de toutes sortes) qui communiquent entre eux par l’intermédiaire de supports ou médias de transmission, dans le but de partager des ressources (matérielles, logicielles, données)"*.

<!-- ![Réseau](./01-1.png) -->

### Utilité d'un réseau informatique

- **Partage des ressources** : les données, les programmes et divers équipements deviennent disponibles à chaque usager du réseau.

- **Réduction des coûts** grâce au partage des ressources.

- Augmentation de la **fiabilité** des données partagées.

- **Facilité de communication** entre les différents usagers du réseau. Ils peuvent travailler sur des données communes et s’échanger de l’information rapidement.

## 1.1 - Classifications

***

Il existe de nombreuses façons d'organiser les composants d'un réseau et gérer les transferts d'informations; les réseaux informatiques peuvent être classés selon plusieurs critères : en fonction de *leur taille et de leur étendue géographique*, mais aussi en fonction de *la façon dont les dispositifs sont connectés entre eux*.

### Classification en fonction de la taille et étendues

![Classification réseau](../images/010101-classification.png?width=40vw)

#### Réseau personnel (PAN)

![PAN](../images/010102-PAN.png)


**Les réseaux personnels**, ou **PAN** (*Personal Area Network*), permettent aux équipements de communiquer à l’échelle individuelle.

Un exemple courant est celui du réseau sans fil qui relie un ordinateur à ses périphériques. Pratiquement tous les ordinateurs s’accompagnent d’un moniteur, d’un clavier, d’une souris et d’une imprimante.

La connexion entre les équipements est soit câblées, soit sans fil. Sans câble, les équipements sans fil sont connectés par un protocole sans fil à courte portée nommé *Bluetooth*.

#### Réseau local (LAN)

![LAN](../images/010103-LAN.png)


**Un réseau local**, ou **LAN** (*Local Area Network*) est un réseau contenu au sein d'une zone géographique restreinte, généralement à l'intérieur d'un même bâtiment. Les réseaux WiFi domestiques et les réseaux de petites entreprises sont des exemples courants de réseaux LAN.

La plupart des LAN se connectent à Internet au niveau d'un point central : **un routeur**. Si les LAN domestiques passent souvent par un routeur unique, les réseaux LAN établis dans de grands espaces peuvent également faire usage de **commutateurs réseau** (*switch*) pour acheminer plus efficacement les paquets.

![Exemple de switch](../images/010104-switch.png?width=30vw)
*Exemple de commutateur (switch)*

Les LAN s'appuient presque toujours sur Ethernet, le WiFi, ou les deux, pour connecter des appareils au réseau.

Une vaste gamme d'équipements peuvent se connecter aux réseaux LAN, notamment les serveurs, les ordinateurs de bureau, les ordinateurs portables, les imprimantes, les appareils IdO et même les consoles de jeu. Dans les bureaux, les LAN sont souvent utilisés pour assurer un accès partagé aux imprimantes ou aux serveurs connectés pour les collaborateurs internes.

#### Réseau métropolitain (MAN)

![MAN](../images/010105-MAN.png)

**Les réseaux métropolitains** (*Metropolitan Area Network*) permettent de connecter plusieurs LAN proches l’un de l’autre. Pour les relier entre eux, on fait appel à des routeurs et des câbles de fibre optique permettant des accès à très haut débit.

Un MAN est plus grand qu'un réseau local (LAN) mais plus petit qu'un réseau étendu (WAN) . Les MAN ne doivent pas nécessairement se trouver dans des zones urbaines ; le terme "métropolitain" implique la taille du réseau, et non la démographie de la zone qu'il dessert.

Ex: réseau du Cégep avec plusieurs campus.

#### Réseau étendu (WAN)

![WAN](../images/010106-WAN.png?width=30vw)


**Les réseaux étendus** (*Wide Area Network*) permettent de connecter plusieurs LAN éloignés entre eux.

La définition de ce qui constitue un WAN est assez large. Techniquement, tout réseau de grande taille qui s'étend sur une vaste zone géographique est un WAN (d’un état, un pays ou même la planète). Internet lui-même est considéré comme le plus vaste WAN.

### Classification en fonction des interconnexions

#### Architecture client-serveur

![Architecture client serveur](../images/010107-client-serveur.png?width=20vw)


Un groupe d’ordinateurs, les clients, sont reliés à un serveur sur lequel tourne un système d’exploitation réseau. EX: Serveur de fichiers.

Les machines (clients) font des requêtes vers un serveur qui doit leur fournir le service demandé. Un serveur est généralement un ordinateur plus puissant que les machines des utilisateurs.

Les clients partagent des ressources communes gérées par le serveur sans pouvoir accéder aux ressources locales des différentes stations clientes.

La base de données des utilisateurs du réseau est centralisée sur un de ces serveurs et il est alors possible de contrôler l’accès aux ressources.

Enfin, il n’existe qu’un seul administrateur réseau.

Exemples d'architecture client/serveur : Windows Server 2012, Netware et Unix.


#### Architecture pair à pair (Peer to Peer)

![Architecture P2P](../images/010108-p2p.png?width=20vw)


Contrairement à une architecture client-serveur où les rôles de serveur et de client sont attribués définitivement, dans une architecture **pair à pair**, aussi notée **P2P**, tout ordinateur peut alternativement être serveur et client.

- Le réseau ne comporte pas de serveurs spécialisés ou dédiés.
- Le système d’exploitation est présent sur tous les postes.
- Aucun ordinateur n’a besoin d’être plus puissant qu’un autre.
- Regroupe en général peu de postes.
- Chaque utilisateur est administrateur de son propre poste.
- Demande des connaissances minimales pour travailler dans un environnement correctement structuré.

*Exemples: BitTorrent*

## 1.2 Topologies

***

Il existe plusieurs manières de relier les ordinateurs et des dispositifs entre eux dans un réseau. Chaque manière a ses avantages et inconvénients. Afin de s'y retrouver, les réseaux peuvent être catégorisés par la manière dont les dispositifs y sont reliés. Leur connectivité porte un nom : c'est la **topologie du réseau**.

Dans ce chapitre, nous allons explorer les topologies réseaux les plus courantes. 

<!-- ![Topologies](../images/010201-topologies.png) -->

<!-- - **Topologie point-à-point (point-to-point topology)**
- **Topologie maillée (mesh topology)**
- **Topologie en étoile (star topology)**
- **Topologie en bus (bus topology)**
- **Topologie en anneau (ring topology)**
- **Topologie en arbre (tree topology)**
- **Topologie hybride (hybrid topology)** -->

### Topologie point-à-point

![Topologie P2P](../images/010202.png?width=18vw)

**La topologie point à point (P2P)** connecte deux nœuds ou dispositifs (concentrateur, routeur, commutateur, etc.) directement à l'aide d'une ligne dédiée. Dans cette topologie, l'un des nœuds connectés sert d'émetteur, tandis que l'autre joue le rôle de récepteur.

Cette topologie est considérée comme le moyen le plus simple et le plus rentable de créer un réseau informatique, car elle ne nécessite qu'un seul canal de communication entre les deux nœuds connectés. En outre, cette topologie réserve la totalité de la bande passante de la connexion à la communication, ce qui minimise les risques d'encombrement et assure une connexion plus fiable.

*Exemples de topologie point-à-point :* 
+ Transfert de fichiers par Bluetooth entre deux appareils
+ Connexion directe par câble entre deux ordinateurs

### Topologie maillée

![Topologie maillée](../images/010203.png?width=20vw)

Dans une topologie maillée, chaque appareil est connecté à un autre appareil via un canal dédié.

Supposons que N appareils soient connectés les uns aux autres dans une topologie maillée, le nombre total de ports requis par chaque appareil est N-1. Dans la figure ci-dessus, il y a 5 dispositifs connectés les uns aux autres, le nombre total de ports requis par chaque dispositif est donc de 4. Le nombre total de ports requis = N * (N-1).

Supposons qu'un nombre N de dispositifs soient connectés les uns aux autres dans une topologie maillée, le nombre total de liens dédiés requis pour les connecter N(N-1)/2. Dans la figure ci-dessus, 5 appareils sont connectés les uns aux autres, le nombre total de liens nécessaires est donc de 5*4/2 = 10.

**Avantages**
+ Robuste
+ Fiable : Les données sont transférées par des canaux ou des liens dédiés
+ Sécurisé et assure la confidentialité
<!-- Questions intéressantes : Imaginons un réseau utilisant une topologie maillée contenant 3 dispositifs. De combien de ports avons-nous besoin? Combien de lignes dédiées? -->

### Topologie en étoile

![Topologie maillée](../images/010204.png?width=20vw)

Dans la **topologie en étoile**, tous les appareils sont reliés à un seul noeud central.

**Inconvénients**
+ Si le noeud central sur lequel repose toute la topologie tombe en panne, l'ensemble du système s'effondre *(single point of failure)*.
+ Les performances sont basées sur le noeud central.

*Exemples de topologie en étoile :* 
+ Réseau local (LAN) où tous les ordinateurs sont connectés à un commutateur (switch). 
+ Réseau sans fil où tous les appareils sont connectés à un point d'accès sans fil.


### Topologie en bus

![Topologie en bus](../images/010205.png?width=20vw)


Dans une **topologie en bus**, chaque périphérique du réseau est connecté à un seul câble. La communication est bidirectionnelle. Il s'agit d'une connexion multipoint et d'une topologie non robuste, car si l'épine dorsale tombe en panne, le réseau s'effondre.

**Avantages**
+ Si N appareils sont connectés les uns aux autres dans une topologie de bus, le nombre de câbles nécessaires pour les connecter est de 1.

**Inconvénients**
+ Si le câble commun tombe en panne, l'ensemble du système s'effondre.
+ Si le trafic réseau est important, il risque d'y avoir beaucoup de collisions (manque de fiabilité).
+ L'ajout de nouveaux noeuds ralentit la communication.
+ Faible sécurité.

### La topologie en anneau

![Topologie en anneau](../images/010206.png?width=20vw)

Dans **les réseaux en anneaux**, chaque noeud est relié à deux autres : un suivant et un précédent. Les données transmises font le tour de l'anneau avant d'être détruites : elles se propagent d'un noeud au suivant, jusqu’à arriver au noeud de destination. Après réception de la donnée, le noeud de destination envoie un accusé de réception à l'émetteur, qui se propage dans le même sens que la donnée envoyée.

**Inconvénients**
- La défaillance d'un seul nœud du réseau peut entraîner la défaillance de l'ensemble du réseau.
- L'ajout ou la suppression de stations intermédiaires peut perturber l'ensemble de la topologie.

### Topologie hybride
Cette topologie est la combinaison de tous les types de topologies vues précédement.

![Topologie hybride](../images/010207.png?width=40vw)
{{% center %}}
*Exemple de topologie hybride (combinaison de plusieurs topologies réseaux).*
{{% /center %}}

**Avantages**
- Très flexible.
- La taille du réseau peut être facilement étendue par l'ajout de nouveaux noeuds ou topologies.

**Inconvénients**
- La conception est difficile
- Le coût de l'infrastructure est très élevé


<!-- Un exemple courant de topologie hybride est le réseau d'un campus universitaire. Le réseau peut avoir un coeur en étoile, chaque bâtiment étant connecté au coeur par l'intermédiaire d'un commutateur ou d'un routeur. À l'intérieur de chaque bâtiment, il peut y avoir une topologie en bus ou en anneau reliant les différentes salles et les différents bureaux. Les points d'accès sans fil créent également une topologie maillée pour les appareils sans fil. Cette topologie hybride permet une communication efficace entre les différents bâtiments tout en offrant flexibilité et redondance à l'intérieur de chaque bâtiment. -->

### Topologie logique

À la base, une topologie réseau est une topologie physique : elle décrit comment les ordinateurs sont *physiquement* reliés. Mais il est possible d'émuler un réseau en bus, ou un réseau maillé avec une topologie en étoile. Cela nous pousse à faire la distinction entre la topologie physique, qui décrit comment les noeuds sont connectés, et la topologie simulée, appelée *topologie logique*.

#### Les topologies logiques en bus et maillées

Pour simuler un bus ou un réseau maillé à partir d'une topologie en étoile, il faut que l'équipement central soit un **commutateur** ou un **concentrateur**. 

![Exemple de hub](../images/010208-hub.jpg)
{{% center %}}
*Exemple de concentrateur (hub)*
{{% /center %}}

![Exemple de switch](../images/010104-switch.png?width=20vw)
{{% center %}}
*Exemple de commutateur (switch)*
{{% /center %}}


Ils sont tous deux des équipements réseaux avec plusieurs ports d'entrée/sortie, sur lesquels on vient connecter des composants réseaux : carte réseau, ordinateur, récepteur/émetteur WIFI, etc.

La différence entre concentrateur et commutateur est la topologie simulée : topologie en bus pour le concentrateur et topologie maillée pour un commutateur.

![Concentrateur vs. commutateur](../images/010209-concentrateur-commutateur.gif)
{{% center %}}
*Différence entre concentrateur (à gauche) et commutateur (à droite).*
{{% /center %}}

Un concentrateur (hub) **redistribue chaque paquet reçu sur tous les autres ports**, sans se préoccuper de sa destination : **Il simule une topologie en bus**, alors que la topologie réelle est en étoile. De même pour un point d’accès sans-fil (WAP). 

Les commutateurs (switch) ont un fonctionnement similaire aux concentrateurs, si ce n'est qu'ils **n'envoient les données qu'au composant de destination**. Un commutateur **simule donc une topologie maillée** à partir d'une topologie en étoile : on retrouve la distinction entre topologie physique et logique.

![Concentrateur vs. commutateur](../images/010210-switch.jfif)
{{% center %}}
*Fonctionnement d'un commutateur.*
{{% /center %}}

### Vidéo résumé
{{< youtube S7MNX_UD7vY>}}


## 1.3 - Modèles OSI et TCP/IP

***

Un protocole est un ensemble de règles qui définissent comment différents systèmes communiquent entre eux.

Pour pouvoir communiquer entre elles, les personnes doivent se mettre d'accord sur des règles de communication. Ces règles (ou *protocoles*) doivent être respectés pour que le message soit correctement transmis et compris. 

Exemple : Qui veut lire ?

la communication les règles entre les hommes régissent. Ilesttrèsdifficiledecomprendredesmessagesquinesontpasbienformatésetquinesuiventpaslesrèglesetlesprotocolesétablis. A estruturada gramatica, da lingua, da pontuacaoe do sentancefaza configuracaohumanacompreensivelpormuitosindividuosdiferentes.

Il est très difficile de comprendre des messages qui ne sont pas bien formatés et qui ne suivent pas les règles/protocoles établis, n'est-ce pas :sweat_smile: ? Ce sont la grammaire, la langue, la ponctuation et la structure de la phrase qui permettent de comprendre un message.

<!-- Les protocoles utilisés dans les communications réseau partagent de nombreuses caractéristiques fondamentales avec les protocoles utilisés pour régir les conversations humaines. -->

De la même manière, pour que les hôtes d'un réseau puissent communiquer et s'échanger des données, il est nécessaire d'utiliser des protocoles informatiques et réseau communs qui vont définir la manière dont un message (ou donnée) est construit puis transmis.


## Modèles en couche
Théoriquement, il est possible d’utiliser un protocole unique qui prend les données d’une application informatique et les envoie à une application sur un autre ordinateur. Le problème avec cette approche est qu’elle est très rigide, car tout changement nécessite de modifier l’ensemble du protocole.

Pour ajouter de la souplesse et de l’efficacité, des modèles en couches (« *layers* ») ont été définis. Chaque protocole va appartenir à une couche précise et chaque couche va avoir une fonction différente des autres. Le grand intérêt des modèles en couches réside dans la séparation des fonctions : on va pouvoir modifier des protocoles ou utiliser un protocole d’une couche plutôt qu’un autre sans affecter les autres.

<!-- ### Avantages d'un modèle en couches
+ Aide à la conception d’un protocole, car des protocoles qui fonctionnent à un niveau de couche spécifique disposent d’informations définies à partir desquelles ils agissent, ainsi que d’une interface définie par rapport aux couches supérieures et inférieures.
+ Il encourage la concurrence, car les produits de différents fournisseurs peuvent fonctionner ensemble.
+ Il permet d’éviter que des changements technologiques ou fonctionnels dans une couche ne se répercutent sur d’autres couches, supérieures et inférieures.
+ Il fournit un langage commun pour décrire les fonctions et les fonctionnalités réseau. -->

Aujourd’hui, dans le monde réseau, il existe deux modèles largement dominants : le modèle OSI qui définit 7 couches et le modèle TCP/IP qui en définit 4.

### Le modèle OSI
Le modèle OSI (*Open Systems Interconnection* ou Interconnexion de Systèmes Ouverts en Français) est un modèle conceptuel dont le but est de définir des normes de communication entre différents systèmes informatiques. Il est normé en 1984.

Ce modèle propose un système de communication composé de 7 couches différentes. L’idée derrière cette représentation est une nouvelle fois de décomposer la communication entre deux périphériques en différentes « étapes » bien définies afin qu’on puisse par la suite faire évoluer les composants de chacune des couches de manière indépendante plutôt que de devoir modifier l’intégralité du processus de communication dès le changement d’un composant.

Les 7 couches définies par le modèle OSI :

![Modèle OSI](../images/010301-modele-osi.png)

Chaque couche résout un certain nombre de problèmes relatifs à la transmission de données, et fournit des services bien définis aux couches supérieures. Les couches hautes sont plus proches de l'utilisateur et gèrent des données plus abstraites, en utilisant les services des couches basses qui mettent en forme ces données afin qu'elles puissent être émises sur un médium physique.



### Le modèle TCP/IP
Le modèle TCP/IP (encore appelé « modèle Internet »), qui date de 1976, a été stabilisé bien avant la publication du modèle OSI en 1984.

{{% notice style="info" %}}
TCP/IP est un modèle dérivé de l’ARPANET dont le but était de maintenir les communications coûte que coûte en cas d’attaque nucléaire. Il en découle un réseau basé sur le routage de paquets à travers une couche appelée Internet.
{{% /notice %}}

Le modèle TCP/IP tient son nom de ses deux protocoles « majeurs » : les protocoles TCP (Transmission Control Protocol) et IP (Internet Protocol).

Il présente aussi une approche modulaire (utilisation de couches) mais en contient uniquement quatre :

![Modèle TCP-IP](../images/010302-osi-vs-tcp-ip.png)

### Vidéo explicative
{{< youtube CRdL1PcherM>}}

#### Suite des protocoles
La suite des protocoles TCP/IP est l'ensemble des protocoles utilisés pour le transfert des données sur Internet. Elle est basée sur le modèle TCP/IP car chaque protocole est associé à une couche.

![Suite de protocole TCP/IP](../images/010306-suite-protocole-TCPIP.png)


### L’encapsulation
Lorsque des données sont transmises sur le réseau, elles ne sont pas envoyées telles quelles, car chaque protocole a besoin d’informations bien précises pour faire son travail. Par exemple, les protocoles de couche liaison ont besoin d’informations particulières, non présentes dans la donnée de l'application, pour détecter les erreurs ou indiquer le récepteur. Même chose pour les protocoles TCP et UDP de la couche transport, qui ont besoin d’informations sur le processus émetteur et récepteur, qui ne sont pas dans la donnée à transmettre. 

![Encapsulation](../images/010304-encapsulation.png?width=40vw)

Pour résoudre ce problème, chaque protocole ajoute les informations dont il a besoin à la donnée transmise en utilisant une méthode appelée **encapsulation**. Ces informations sont regroupées dans un en-tête, placé au début des données à transmettre. Avec cette méthode, les en-têtes de chaque couche sont séparés, placés les uns à côté des autres. Lors de la réception, ces en-tête seront enlevées dans l'ordre inverse, jusqu'à atteindre l'application de destination : c'est ce qu'on appelle la **désencapsulation**.

![Encapsulation](../images/010303-encapsulation.gif?width=40vw)

#### Unité de données de protocole (PDU)

Pour les couches liaison, réseau et transport, les paquets formés lors de l’encapsulation sont appelés des protocol data unit (PDU, unités de données d’un protocole, en français). Ils portent des noms différents selon la couche, vu qu’ils contiennent des en-têtes différents.

![Encapsulation](../images/010305-pdu.png?width=45vw)

+ Pour la couche liaison, l’unité est la **trame**.
+ Pour la couche réseau, l’unité est le **paquet**.
+ Pour la couche transport, l’unité est le **segment**.

### Vidéo explicative
{{< youtube 3kfO61Mensg>}}

+++
pre = '<b>3. </b>'
title = 'Modèles OSI et TCP/IP'
draft = false
weight = "130"
+++

***

Un protocole est un ensemble de règles qui définissent comment différents systèmes communiquent entre eux.

Pour pouvoir communiquer entre elles, les personnes doivent se mettre d'accord sur des règles de communication. Ces règles (ou *protocoles*) doivent être respectés pour que le message soit correctement transmis et compris. 

Exemple : Qui veut lire ?

la communication les règles entre les hommes régissent. Ilesttrèsdifficiledecomprendredesmessagesquinesontpasbienformatésetquinesuiventpaslesrèglesetlesprotocolesétablis. A estruturada gramatica, da lingua, da pontuacaoe do sentancefaza configuracaohumanacompreensivelpormuitosindividuosdiferentes.

Il est très difficile de comprendre des messages qui ne sont pas bien formatés et qui ne suivent pas les règles/protocoles établis, n'est-ce pas :sweat_smile: ? Ce sont la grammaire, la langue, la ponctuation et la structure de la phrase qui permettent de comprendre un message.

<!-- Les protocoles utilisés dans les communications réseau partagent de nombreuses caractéristiques fondamentales avec les protocoles utilisés pour régir les conversations humaines. -->

De la même manière, pour que les hôtes d'un réseau puissent communiquer et s'échanger des données, il est nécessaire d'utiliser des protocoles informatiques et réseau communs qui vont définir la manière dont un message (ou donnée) est construit puis transmis.


## Modèles en couche
Théoriquement, il est possible d’utiliser un protocole unique qui prend les données d’une application informatique et les envoie à une application sur un autre ordinateur. Le problème avec cette approche est qu’elle est très rigide, car tout changement nécessite de modifier l’ensemble du protocole.

Pour ajouter de la souplesse et de l’efficacité, des modèles en couches (« *layers* ») ont été définis. Chaque protocole va appartenir à une couche précise et chaque couche va avoir une fonction différente des autres. Le grand intérêt des modèles en couches réside dans la séparation des fonctions : on va pouvoir modifier des protocoles ou utiliser un protocole d’une couche plutôt qu’un autre sans affecter les autres.

<!-- ### Avantages d'un modèle en couches
+ Aide à la conception d’un protocole, car des protocoles qui fonctionnent à un niveau de couche spécifique disposent d’informations définies à partir desquelles ils agissent, ainsi que d’une interface définie par rapport aux couches supérieures et inférieures.
+ Il encourage la concurrence, car les produits de différents fournisseurs peuvent fonctionner ensemble.
+ Il permet d’éviter que des changements technologiques ou fonctionnels dans une couche ne se répercutent sur d’autres couches, supérieures et inférieures.
+ Il fournit un langage commun pour décrire les fonctions et les fonctionnalités réseau. -->

Aujourd’hui, dans le monde réseau, il existe deux modèles largement dominants : le modèle OSI qui définit 7 couches et le modèle TCP/IP qui en définit 4.

### Le modèle OSI
Le modèle OSI (*Open Systems Interconnection* ou Interconnexion de Systèmes Ouverts en Français) est un modèle conceptuel dont le but est de définir des normes de communication entre différents systèmes informatiques. Il est normé en 1984.

Ce modèle propose un système de communication composé de 7 couches différentes. L’idée derrière cette représentation est une nouvelle fois de décomposer la communication entre deux périphériques en différentes « étapes » bien définies afin qu’on puisse par la suite faire évoluer les composants de chacune des couches de manière indépendante plutôt que de devoir modifier l’intégralité du processus de communication dès le changement d’un composant.

Les 7 couches définies par le modèle OSI :

![Modèle OSI](../images/010301-modele-osi.png)

Chaque couche résout un certain nombre de problèmes relatifs à la transmission de données, et fournit des services bien définis aux couches supérieures. Les couches hautes sont plus proches de l'utilisateur et gèrent des données plus abstraites, en utilisant les services des couches basses qui mettent en forme ces données afin qu'elles puissent être émises sur un médium physique.



### Le modèle TCP/IP
Le modèle TCP/IP (encore appelé « modèle Internet »), qui date de 1976, a été stabilisé bien avant la publication du modèle OSI en 1984.

{{% notice style="info" %}}
TCP/IP est un modèle dérivé de l’ARPANET dont le but était de maintenir les communications coûte que coûte en cas d’attaque nucléaire. Il en découle un réseau basé sur le routage de paquets à travers une couche appelée Internet.
{{% /notice %}}

Le modèle TCP/IP tient son nom de ses deux protocoles « majeurs » : les protocoles TCP (Transmission Control Protocol) et IP (Internet Protocol).

Il présente aussi une approche modulaire (utilisation de couches) mais en contient uniquement quatre :

![Modèle TCP-IP](../images/010302-osi-vs-tcp-ip.png)

### Vidéo explicative
{{< youtube CRdL1PcherM>}}

#### Suite des protocoles
La suite des protocoles TCP/IP est l'ensemble des protocoles utilisés pour le transfert des données sur Internet. Elle est basée sur le modèle TCP/IP car chaque protocole est associé à une couche.

![Suite de protocole TCP/IP](../images/010306-suite-protocole-TCPIP.png)


### L’encapsulation
Lorsque des données sont transmises sur le réseau, elles ne sont pas envoyées telles quelles, car chaque protocole a besoin d’informations bien précises pour faire son travail. Par exemple, les protocoles de couche liaison ont besoin d’informations particulières, non présentes dans la donnée de l'application, pour détecter les erreurs ou indiquer le récepteur. Même chose pour les protocoles TCP et UDP de la couche transport, qui ont besoin d’informations sur le processus émetteur et récepteur, qui ne sont pas dans la donnée à transmettre. 

![Encapsulation](../images/010304-encapsulation.png?width=40vw)

Pour résoudre ce problème, chaque protocole ajoute les informations dont il a besoin à la donnée transmise en utilisant une méthode appelée **encapsulation**. Ces informations sont regroupées dans un en-tête, placé au début des données à transmettre. Avec cette méthode, les en-têtes de chaque couche sont séparés, placés les uns à côté des autres. Lors de la réception, ces en-tête seront enlevées dans l'ordre inverse, jusqu'à atteindre l'application de destination : c'est ce qu'on appelle la **désencapsulation**.

![Encapsulation](../images/010303-encapsulation.gif?width=40vw)

#### Unité de données de protocole (PDU)

Pour les couches liaison, réseau et transport, les paquets formés lors de l’encapsulation sont appelés des protocol data unit (PDU, unités de données d’un protocole, en français). Ils portent des noms différents selon la couche, vu qu’ils contiennent des en-têtes différents.

![Encapsulation](../images/010305-pdu.png?width=45vw)

+ Pour la couche liaison, l’unité est la **trame**.
+ Pour la couche réseau, l’unité est le **paquet**.
+ Pour la couche transport, l’unité est le **segment**.

### Vidéo explicative
{{< youtube 3kfO61Mensg>}}


## 1.4 - Adressages

***

Pour envoyer une lettre par la poste, les relais postiers ont besoin de connaitre le nom et l'adresse de l'émetteur et du destinataire de la lettre, sans quoi ils ne sauront pas où distribuer le courrier. Sur les réseaux, un mécanisme similaire est utilisé : chaque ordinateur ou périphérique possède une adresse qui lui permet de recevoir ou envoyer des données sur le réseau.

### Types d'adresse
Il existe deux types d'adresse réseau :

+ **Adresses physiques :** Utilisées sur les réseaux locaux, mais qui ne sont pas compatibles avec les réseaux étendus et Internet (leur portée est limitée à un réseau local). Elles sont standardisées par le standard MAC (on les appelle adresses MAC)
+ **Adresses logiques :** Utilisées sur Internet et ont une portée très large, dépassant le réseau local de l’ordinateur. elles sont standardisées par le protocole IP (adresse IP). Utilité : Permet le remplacement d’un ordinateur sans pour autant changer son adresse internet. Par exemple, si un serveur tombe en panne et que l’on le remplace, il garde son adresse IP, alors que son adresse MAC change.

#### Adresse MAC

![Exemple d'une adresse MAC](../images/010401-adresse-mac.png)
{{% center %}}
*Exemple d'adresse MAC*
{{% /center %}}

+ Elle se compose de six paires de nombres hexadécimaux pour un total de 12 nombres.
+ Également appelée adresse physique car elle est attribuée physiquement à la carte réseau d'un hôte.
+ Elle ne change pas et est unique.
+ Elle est similaire au nom d’une personne (analogie du courrier postal).

#### Adresse IP

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

+++
### 1.4.1 Configuration réseau
---------------------
#### Interface réseau
Une interface réseau est un point de connexion matériel (comme une carte réseau physique) ou logiciel qui permet à un appareil de communiquer avec un réseau, qu'il soit privé ou public, en envoyant et recevant des données. Cette interface possède obligatoirement une adresse MAC et optionnellement (si elle est configurée) une ou plusieurs adresses IP.

Il est aussi possible de donner d'autres informations à une interface réseau, notamment : 

+ **Passerelle par défaut :** La passerelle est la porte de sortie d’un réseau : lorsque vous ouvrez une connexion sur un serveur qui se trouve sur internet (par exemple www.google.com), la communication entre votre PC et ce serveur passe par cette passerelle.
+ **Adresse(s) du/des serveur(s) DNS :** Un serveur DNS permet (entre autre) à un hôte du réseau de récupérer l'adresse IP associée à un nom de domaine. Nous verrons plus tard dans le cours quelles sont les spécificités du protocole et même comment configurer un serveur DNS !

#### Fichiers de configuration

##### `/etc/resolv.conf`
+ Le fichier `/etc/resolv.conf` permet de configurer les serveurs DNS qui seront utilisés par votre machine.
+ Les instructions facultatives `domain` et `search` indiquent à quel domaine appartient votre machine et dans quel domaine doivent être cherché les noms qui ne sont pas des FQDN.
+ Les instructions `nameserver` indiquent les serveurs DNS que vous allez interroger et l’ordre dans lequel vous les interrogez.
+ Il faut un serveur par ligne.

```bash
# Generated by NetworkManager
search localdomain linux.local
nameserver 192.168.230.2
nameserver 8.8.8.8
```

##### `/etc/hosts`
+ Le fichier `/etc/hosts` permet d’associer un nom de machine à une adresse IP soit parce qu’elle n’existe pas dans le serveur DNS (réseau privé, machine de test…) ou pour préférer utiliser une adresse IP particulière plutôt que celle du DNS.
+ Ce fichier est utilisé en priorité par rapport aux serveurs DNS.
+ Sa syntaxe est la suivante:
```bash
<adresse ip> <nom>
```
+ Exemple :
```bash
192.168.230.122		www.google.com
```
Ma machine ira au `192.168.230.122` à chaque fois que je voudrai consulter www.google.com.

Grâce à ce fichier, il est donc possible de configurer la résolution de noms dans un petit réseau ou dans un laboratoire. De cette façon, toutes les machines pourront être jointes par leur nom plutôt que par leur adresse IP.

#### Commandes de base

##### `nmcli`

La commande `nmcli` permet de gérer les interfaces réseau d'une machine.

Pour lister les connections réseau :
```bash
nmcli dev
```
Pour afficher la configuration ip :
```bash
ip a
nmcli con show ens160
```

Pour configurer une adresse IP statique (au lieu de la recevoir dynamiquement) :
```bash
nmcli con mod ens160 ipv4.address 192.168.230.10/24
nmcli con mod ens160 ipv4.gateway 192.168.230.2
nmcli con mod ens160 ipv4.dns 8.8.8.8,8.8.4.4
nmcli con mod ens160 ipv4.method manual
nmcli con down ens160
nmcli con up ens160
```

Pour revenir en mode dynamique :
```bash
nmcli con mod ens160 ipv4.method auto
nmcli con mod ens160 ipv4.dns ""
nmcli con mod ens160 ipv4.gateway "" ipv4.addresses ""
nmcli con down ens160
nmcli con up ens160
```
Après avoir ajouté une interface réseau, il faut la déclarer dans nmcli
```bash
nmcli con add con-name ens224 type ethernet ifname ens224
```
Elle se configure ensuite à l’aide de DHCP ou avec une IP fixe.

##### `ifconfig`
`ifconfig` est un utilitaire de configuration du réseau.

+ Pour afficher la liste des interfaces et des informations de configuration comme l’adresse IP et le masque de sous-réseau.
```bash
ifconfig
```
+ Pour afficher la configuration d’une seule interface.
```bash
ifconfig <interface>
```
+ Pour désactiver/activer une interface réseau.
```bash
ifconfig <interface> down/up
```

##### La commande `ip`

+ La commande `ip` remplace `ifconfig` qui devient obsolète.
+ `ip` est une commande beaucoup plus puissante que `ifconfig`.
+ Cette commande permet de modifier les paramètres IP des interfaces réseau mais uniquement de façon temporaire. Si l’ordinateur redémarre, les configurations seront perdues.
+ Pour rendre un configuration permanente, il faudra modifier les fichiers texte de configuration.
+ Cette commande reste néanmoins indispensable pour lire les configurations et tester de nouveaux paramètres.

+ Pour afficher la liste des interfaces:
```bash
ip address
```
ou
```bash
ip a
```

+ Pour une seule interface réseau:
```bash
ip a show <interface>
```
+ Pour redémarrer une interface pour que les nouveaux paramètres configurés dans les fichiers de configuration soient prises en comtpe:
```bash
ifdown <interface>
ifup <interface>
```
+ Il est possible de configurer plusieurs adresses IP sur une même interface à une condition: toutes les adresses IP doivent être dans le même sous-réseau.
+ Pour ajouter une adresse IP à une interface (ce ne sera pas permanent), il est possible d’ajouter plusieurs adresses IP à une interface:
```bash
ip addr add x.x.x.x /x dev <interface>
```
+ Exemple:
```bash
ip addr add 192.168.230.132/24 dev ens33
```
+ Pour la supprimer:
```bash
ip addr del x.x.x.x/x dev <interface>
```
+ Exemple:
```bash
ip addr del 192.168.230.132/24 dev ens33
```
+ Pour ajouter la passerelle par défaut:
```bash
ip route add default via x.x.x.x
```
+ Exemple:
```bash
ip route add default via 192.168.230.2
```
+ Pour afficher la liste des routes:
```bash
ip route
```
#### Le service réseau
+ Ce service gère le réseau sous Linux.
+ Lorsque vous modifiez les fichiers de configuration du réseau, un redémarrage du réseau est nécessaire.

Pour arrêter le service:
```bash
nmcli networking off
```
+ Pour démarrer le service
```bash
nmcli networking on
```

### 1.4.2 - Commandes utilitaires
#### Ping
`ping` est une commande utilitaire d'administration réseau utilisée pour tester l'accessibilité d'un hôte sur un réseau.

`ping` mesure le temps aller-retour des messages envoyés depuis l'hôte d'origine vers un ordinateur de destination, qui sont renvoyés vers la source.

Pour faire un `ping` à une machine du réseau :
```bash
ping <adresse IP>
```

#### SSH
SSH (*Secure Shell*) est un protocole qui permet, entre autres, de prendre en main une autre machine.

+ Il est possible de spécifier l’utilisateur avec lequel on se connecte (l’utilisateur doit exister sur la machine cible) :
```bash
$ ssh user@host
```
+ `ssh` est disponible sur toutes les distributions Linux et Unix.
+ `ssh` permet aussi d’exécuter une commande à distance :
```bash
$ ssh user@host commande
```

#### ARP
ARP (*Address Resolution Protocol*) est un protocole qui permet de faire correspondre une adresse IP à une adresse MAC dans un réseau local (LAN). 

+ Pour connaitre le tableau de correspondance ARP d'une machine Linux : 
```bash
arp -a
```

+ Pour supprimer le cache ARP d'une machine :
```bash
arp -a -d
```

+++
### 1.4.3 - Atelier VMWare
----------------
#### Configuration de l'interface réseau virtuelle
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

+++
### 1.4.4 - Exercices
#### Exercice 1
*Alors que votre VM est éteinte, mettez le type de son adapteur réseau à NAT. Ensuite démarrez-la.*

1. Remplissez le tableau ci-dessous avec les paramètres de votre machine :

| Adresse IP | Masque de sous-réseau | Passerelle par défaut | DNS1 | DNS2 |
| ---------- | --------------------- | --------------------- | ---- | ---- |
|            |                       |                       |      |      |

2. Est-ce que votre machine a une adresse IP statique ou dynamique (obtenue automatiquement)?
3. En utilisant la commande `nmcli`, donnez une adresse IP statique à votre machine en utilisant les paramètres que vous avez inscrit dans le tableau. Testez que tout fonctionne et que vous avez Internet après avoir redémarré le réseau.
4. Revenez en arrière de façon que votre ordinateur obtienne son adresse IP automatiquement.
5. Testez que tout fonctionne.

#### Exercice 2
1. Créez une VM avec une interface en mode *LAN Segment* puis configurez son interface comme suit : 
    + Adresse IP : `192.168.20.10/24`
    + Passerelle par défaut : `192.168.20.1`
    + Serveurs DNS : `1.1.1.1, 1.0.0.1`

2. Créez une deuxième VM avec une interface en mode *LAN Segment*, puis configurez son interface avec une IP située sur le même réseau (`192.168.20.x/24`), différente de la première VM. 
3. Testez la connexion entre les 2 VMs à l'aide de la commande `ping`, puis avec `SSH`. 

2. Configurez le fichier `hosts` des deux VMs pour que les deux adresses répondent aux `ping` avec un nom différent.

+++
## 1.5 - Modes de transfert

Une transmission sur le réseau peut prendre différentes formes suivant le nombre de destinataires. Un hôte peut en effet vouloir communiquer avec un ordinateur bien précis, ou envoyer une donnée à plusieurs PC différents. Suivant le nombre de destinataires, on peut faire la différence entre *Unicast*, *Anycast*, *Multicast* et *Broadcast*.

![Les différents modes de transfert](../images/010501-mode-transfert.png)

+ **Unicast (monodifusion)**: un ordinateur émet des données à destination d’un autre ordinateur bien identifié.

+ **Anycast**: un ordinateur émet des données vers un ordinateur qu’il ne connaît pas : l’émetteur ne connaît pas la destination de la donnée. L’ordinateur de destination n’est cependant pas choisi au hasard : c’est le protocole de routage qui choisit vers quel ordinateur émettre la donnée.

+ **Multicast**: les données émises sont envoyées à un groupe d’ordinateurs qui veulent recevoir cette donnée. Les ordinateurs qui veulent revoir la donnée se connectent à un serveur et s’inscrivent à un groupe de diffusion. Tous les ordinateurs inscrits dans ce groupe recevront la donnée émise. C’est notamment utilisé lors du streaming d’événements en live : on émet la donnée une fois, et celle-ci sera recopiée par les routeurs à toutes les personnes inscrites au groupe que le routeur connaît. Pour faire simple, le groupe possède une adresse logique (une IP) qui permet de l’identifier. Quand une donnée est envoyée à l’adresse du groupe, le serveur reçoit le paquet et en envoie des copies à tous les ordinateurs du groupe. L’adresse IP du serveur/groupe est appelée une adresse multicast.

+ **Broadcast (difusion)**: le paquet émis est envoyé à tous les ordinateurs d’un réseau ou sous-réseau (un réseau local le plus souvent). Selon que le paquet se propage dans un réseau local ou sur internet, on distingue deux formes de broadcast.


## Annexe. Astuce
-----------------------

Dans le cas où nous connaissons l'adresse IP d'un équipement sur le réseau (par exemple `192.168.10.50`), comment faire pour connaître le constructeur et déduire la nature de cette machine ?

1. Utiliser la commande `ping` pour communiquer pour la première fois avec cette machine. Cela remplit la table ARP de notre machine avec l'adresse MAC associée à l'adresse `192.168.10.50` : 
```bash
ping 192.168.10.50
``` 

2. Lancer la commande `arp -a` pour afficher la table ARP. Dans la table, indentifier l'adresse MAC associée à l'adresse IP `192.168.10.50`.

3. Récupérer l'adresse MAC et recherchez le fabriquant de ce périphérique sur internet ([exemple de site](https://dnschecker.org/mac-lookup.php)).