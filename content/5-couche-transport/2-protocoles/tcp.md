+++
pre="<b>1. </b>"
title = "TCP"
weight = "421"
+++
-------------------


Le protocole TCP (*Transmission Control Protocol*) a été initialement décrit dans le document [RFC 793](https://datatracker.ietf.org/doc/html/rfc793). 

## Caractéristiques

+ **Encapsulation**
+ **Reconstitution ordonée :** Les segments envoyés peuvent emprunter des routes différentes et risquent donc d'arriver dans le désordre. Certaines applications ont besoin que les segments arrivent dans un ordre donné pour être traités correctement (une page web par exemple)
+ **Acheminement fiable :** le récepteur doit envoyer à l'émetteur un accusé de réception;
+ **Contrôle de flux :** le récepteur peut demander à l'émetteur de réduire le nombre de segments transférés et à demander l'accusé de réception.
+ **Orienté connexion :** Avant le début de l'envoie de segments, l'émetteur doit établir une connexion bidirectionnelle avec le récepteur.

### Reconstitution ordonée

Étant donné que les réseaux peuvent fournir plusieurs routes dont les débits de transmission varient, il se peut que les données arrivent dans le désordre. En numérotant et en ordonnant les segments, le protocole TCP s'assure que ces segments sont remis dans le bon ordre.


+ C'est le **numéro d'ordre** de chaque segment *(voir en-tête d'un segment TCP)* qui assure la fiabilité en indiquant comment réassembler et réorganiser les segments reçus lorsqu'ils arrivent à destination.
+ Le processus TCP récepteur place les données d'un segment dans une mémoire tampon de réception (*buffer*). 
+ Les segments sont remis dans l'ordre correct et sont transmis à la couche application une fois qu'ils ont été réassemblés. 
+ Tous les segments reçus dont les numéros d'ordre ne sont pas contigus (*qui se suivent*) sont conservés en vue d'un traitement ultérieur. 

### Acheminement fiable

La fiabilité consiste à **veiller à ce que chaque bloc de données envoyé par la source parvienne à destination**. 
TCP peut garantir que tous les blocs atteignent leur destination en demandant au périphérique source de retransmettre les données perdues ou endommagées.
Les services TCP sur l'hôte de destination accusent réception des données reçues à l'application source.

+ **Le numéro d'ordre (*SEQ, Sequence Number*)** et **le numéro d'accusé de réception (*ACK, Acknowledgement Number*)** sont utilisés ensemble pour confirmer la réception des octets de données contenus dans les segments envoyés. 

+ Le numéro *SEQ*  indique le nombre relatif d'octets qui ont été transmis dans cette session, y compris les octets dans le segment actuel. 

+ Le protocole TCP utilise le numéro *ACK* renvoyé à la source pour indiquer l'octet suivant que le destinataire s'attend à recevoir. C'est ce que l'on appelle un **accusé de réception prévisionnel**.

+ L'hôte expéditeur est **censé envoyer un segment qui utilise un numéro d'ordre égal au numéro ACK**.

### Contrôle de flux

Les hôtes du réseau disposent de ressources limitées, par exemple en ce qui concerne (mémoire, bande passante, etc.). Quand le protocole TCP détermine que ces ressources sont surexploitées, il peut demander à l'application qui envoie les données d'en réduire le flux. Cette opération consiste à réguler la quantité de données transmises par la source, ce qui permet d'éviter la perte de segments sur le réseau et à rendre inutiles les retransmissions.

Le protocole TCP inclut également des **mécanismes de contrôle de flux**. Le contrôle de flux aide à maintenir la fiabilité des transmissions TCP en réglant le flux de données entre la source et la destination pour une session donnée. Cela consiste à limiter la quantité de données de segments transférées en une seule fois et à demander des accusés de réception avant de transmettre davantage de données.

Pour effectuer le contrôle de flux, la première chose que le protocole TCP doit déterminer est la quantité de données de segments que le périphérique de destination peut accepter. L'**en-tête TCP comprend un champ de 16 bits appelé la taille de fenêtre**. Il s'agit du nombre d'octets que le périphérique de destination d'une session TCP peut accepter et traiter en une seule fois. La taille de fenêtre initiale est convenue lors du démarrage de la session (*voir la section Connexion*). Une fois cette taille approuvée, le périphérique source doit limiter la quantité de données de segments envoyées au périphérique de destination en fonction de la taille de fenêtre.

### Orienté connexion

+ TCP **négocie et établit une connexion permanente** (ou *session*) entre les périphériques source et de destination avant de transmettre du trafic. L'établissement de session prépare les périphériques à communiquer entre eux. 
+ Grâce à ce mécanisme, les périphériques négocient la quantité de trafic pouvant être transmise à un moment donné (*MSS*). 
+ La session est interrompue une fois que toutes les communications sont terminées.



## Entête TCP

!["Segment-TCP"](../../images/04-03-01.png)

L'entête TCP comprend :

+ **Numéro d'ordre (32 bits) (*SEQ*)** – utilisé pour identifier la position de chaque segment dans le message d'origine. (*Propriété 2 : Reconstitution ordonnée*)
+ **Numéro d'accusé de réception (32 bits) (*ACK*)** – indique les données qui ont été reçues (*Propriété 3 - Acheminement fiable*).
+ **Longueur d'en-tête (4 bits) (*OFFSET*)** – Indique la longueur de l'en-tête du segment TCP.
+ **Réservé (6 bits)** - pour un futur usage.
+ **Bits de contrôle (6 bits) (code ou *flags*)** – des indicateurs indiquant la fonction du segment (la nature du segment) :
+ **Taille de fenêtre (16 bits)** – taille du buffer de réception (*Propritété 4 : Contrôle de flux*).
+ **Somme de contrôle (16 bits) (*Checksum*)** – utilisée pour le contrôle des erreurs sur l'en-tête et les données de segment.
+ **Urgent (16 bits)** – indique la position des données sont urgentes (lorsque le bit URG est activé).
+ **Options (taille variable):** sa présence est détectée lorsque l'offset est supérieur à 5. On trouve dans ce champ :
    + **MSS :** au moment de l'établiseement d'une connexion, chaque partie annonce sa taille de *MSS* (*Maximum Segment Size*) ou longueur maximum de segment.
    + ***Timestamp* (estampille temporelle)** : permet de calculer la durée qu'un segment prend aller et retour (RTT, Round Trip Time)
    + **Wscale (*Window Scale*)** : pour augmenter la taille de la fenêtre. 

### Bits de contrôle
Les bits de contrôle représentent la fonction du segment TCP : 
+ **URG (*Urgent pointer*):** Le segment transporte des données urgentes dont la place est indiquée par le champ Pointeur d'urgence.
+ **ACK (*Akcnowledgment*):** Le segment transporte un accusé de reception.
+ **PSH (*Push*):** Le segment devra être délivré immédiatement. Le récepteur ne doit pas attendre que son tampon de réception soit plein pour délivrer les données à l'application.
+ **RST (*Reset*):** Réinitialisation de la connexion
+ **SYN (*Synchronize*):** Indique qu'il s'agit de l'ouverture de connexion. Le champ Numéro d'ordre contient la valeur de début de la connexion (ISN ou *initial sequence number*).
+ **FIN (*Final*):** Pour initier une fermeture de connexion.

## Communication TCP

La communication TCP suit 3 étapes :
1. Connexion
2. Envoie de segments et d'accusés de réception (ACK)
3. Fermeture de connexion

### Connexion
!["Segment-TCP"](../../images/04-03-13.png)

Communément appelé *3-Way-TCP-Handshake* car elle se fait en 3 étapes. Les trois étapes de l'établissement d'une connexion TCP :
1. Le client demande l'établissement d'une session de communication *client->serveur* avec le serveur.
2. Le serveur accuse réception de la session de communication *client->serveur* et demande l'établissement d'une session de communication *serveur->client*.
3. Le client accuse réception de la session de communication *serveur->client*.

La communication entre les deux hôtes est **bi-directionnelle**, c'est pourquoi chacun d'eux demande un établissement de session. D'ailleurs, le client possède un *ISN* (x dans la figure) différent du serveur (y).

### Envoie de segments et d'accusés de réception

!["Segment-TCP"](../../images/04-03-12.png)


#### Taille de fenêtre et accusé de réception

L'utilisation de **tailles de fenêtres** dynamiques permet de contrôler le flux de données. Quand les ressources réseau sont soumises à de fortes contraintes, le protocole TCP peut réduire la taille de fenêtre afin d'imposer l'envoi plus fréquent d'accusés de réception pour les segments reçus. Ceci a pour effet de ralentir le taux de transmission car la source attend des accusés de réception des données plus fréquents.

L'hôte destinataire renvoie la valeur de taille de fenêtre à l'hôte expéditeur pour indiquer le nombre d'octets qu'il est prêt à recevoir. Si la destination doit ralentir le débit de communication parce que la mémoire tampon est limitée, elle peut envoyer une valeur de taille de fenêtre plus petite à la source en l'intégrant à un accusé de réception.

!["Segment-TCP"](../../images/04-03-11.png)

### Fermeture de connexion

!["Segment-TCP"](../../images/04-03-14.png)

1. Quand le client n'a plus de données à envoyer dans le flux, il envoie un segment dont l'indicateur *FIN* est défini.

2. Le serveur envoie un segment *ACK* pour informer de la bonne réception du segment *FIN*, afin de fermer la session *client -> serveur*.

3. le serveur envoie un segment *FIN* au client pour mettre fin à la session *serveur -> client*.

4. le client répond à l'aide d'un segment *ACK* pour accuser réception du segment *FIN* envoyé par le serveur.

## Services utilisant TCP
+ HTTP (80)
+ HTTPS (443)
+ FTP (*File transfer protocol*) (20 + 21)
+ Telnet (23)
+ SSH (22)
+ etc...
<!-- !["Applications-TCP"](../../images/04-03-08.png) -->
