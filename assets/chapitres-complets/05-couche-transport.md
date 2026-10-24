# 5. Couche transport
-------------------

Dans le chapitre précédent, nous avons vu que la couche Internet permettait (entre autres) de transmettre des paquets d'un périphérique source à un périphérique de destination grâce à l'adressage IP.

Mais lorsqu'une donnée arrive à un ordinateur, comment savoir à quelle application est-elle destinée ?   

La couche transport est chargée de l’établissement d’une session de communication temporaire **entre deux applications** et de l’acheminement des données entre ces deux applications.

## Rôle

![role-transport](images/04-01-01.png)

La couche tranport est responsable de trois tâches :

+ Suivi des conversations individuelles
+ Segmentation des données et reconstruction des segments
+ Identification de l’application appropriée pour chaque flux de communication


### Suivi des conversations individuelles

![role-transport-01](images/04-01-02.png)

Chaque ensemble de données transitant entre une application source et une application de destination est appelé *une conversation*. Un hôte peut héberger plusieurs applications qui communiquent sur le réseau simultanément. Chacune de ces applications communique avec une ou plusieurs applications sur un ou plusieurs hôtes distants. La couche transport est chargée de **garantir ces multiples conversations** et d’**en effectuer le suivi**.

### Segmentation et reconstitution de données
![role-transport-02](images/04-01-03.png)

+ **Lors de l'encapsulation :** la couche transport segmentent les données d’application en blocs de données de taille appropriée. Un en-tête, (utilisé pour la réorganisation), est ajouté à chaque bloc de données.

+ **Au niveau du destinataire (désencapsulation):** La couche transport reconstitue le flux de données à partir de ses segments, grâce aux informations contenues dans les en-têtes.

#### Multiplexage
![multiplexage](images/04-01-05.png)

+ L’envoi de certains types de données (par exemple, un flux vidéo en continu) sur le réseau en tant que flux de communication complet peut utiliser toute la bande passante disponible et empêcher d’autres communications d’avoir lieu en même temps.

+ La segmentation des données en parties plus petites permet d'avoirs plusieurs communications différentes provenant de nombreux utilisateurs, d’être imbriquées (*multiplexées*) sur le même réseau. 
+ Sans segmentation, une seule application pourrait recevoir des données.
    + Exemple: Pour un flux vidéo en continu (*stream*), le support serait monopolisé par le flux de communication au lieu d’être partagé. Il serait impossible de recevoir des e-mails, de parler sur une messagerie instantanée et d’afficher des pages Web tout en regardant la vidéo.

### Identification des applications
![role-couche-transport-04](images/04-01-04.png)

De nombreux services ou applications peuvent s’exécuter en même temps sur chaque hôte du réseau. Pour que les flux de données atteignent la bonne application, la couche transport identifie l'application en lui affectant un identificateur qu'on appelle **numéro de port**. Chaque processus logiciel ayant besoin d’accéder au réseau se voit affecter un numéro de port unique sur son hôte.

### Le bon protocole pour une application

![Bon-Protocole-Pour-Bonne-App](images/04-01-07.png)

+ **TCP** et **UDP** sont les deux protocoles de la couche transport, chacun ayant des caractéristiques spécifiques.

+ Le choix entre TCP et UDP dépend des besoins et des exigences des applications ; les développeurs doivent décider quel protocole est le plus approprié dépendamment des besoins de leur application


#### TCP (Transmission Control Protocol)

+ Assure que les segments de données arrivent dans le bon ordre et que toutes les informations sont reçues correctement.
+ Garantit l’intégrité et la fiabilité des données transmises.
+ Convient aux applications qui nécessitent que toutes les données soient présentes et intactes pour fonctionner correctement.
+ Exemples d’applications : bases de données, navigateurs web, clients de messagerie électronique.
+ Avantages : assure la fiabilité de la communication et prévient toute perte de données.
+ Inconvénients : génère une surcharge réseau supplémentaire en raison des mécanismes d’accusés de réception et de retransmission des paquets manquants.

#### UDP (User Datagram Protocol) :

+ Offre une transmission plus rapide en réduisant la surcharge réseau.
+ Ne garantit pas la livraison de tous les paquets, mais privilégie la rapidité plutôt que la fiabilité.
+ Adapté aux applications qui peuvent tolérer la perte de quelques données sans impact significatif sur l’expérience utilisateur.

+ Exemples d’applications : streaming vidéo et audio, VoIP (voix sur IP).

+ Cas pratiques :
    + Flux vidéo : la perte de quelques segments peut entraîner une légère distorsion ou interruption momentanée, mais le flux global continue sans retard.
    + Radio sur Internet : si des paquets sont perdus, le son peut subir de petites interruptions, mais l’expérience reste acceptable ; l’utilisation de TCP ralentirait le flux et amplifierait les interruptions à cause des retransmissions.

#### Conclusion : TCP vs. UDP
+ TCP est privilégié pour les applications nécessitant une fiabilité totale.

+ UDP est privilégié pour les applications où la rapidité et la fluidité de la transmission sont plus importantes que la perte occasionnelle de données.


# 4.1. Adressage de port
-------------------

+ Les protocoles de transport (TCP et UDP) utilisent des **numéros de port** pour permettre à plusieurs applications/processus de communiquer simultanément sur un même hôte. 

+ Chaque segment ou datagramme contient : un port source et un port de destination. Le port de destination informe le service (application) demandé. 

+ **Exemple -** si un client demande un service web, il envoie un message vers le port de destination 80 (HTTP) sur le serveur. Le serveur sait alors qu’il doit traiter un service Web. 

+ Le port source est généré aléatoirement (dans une certaine plage) par le client afin d’identifier sa propre application et permettre le retour de la réponse vers la bonne application. 

## Plages de ports

+ L’IANA (*Internet Assigned Numbers Authority*) est responsable de l’attribution des numéros de port. 

Il existe trois plages principales :
|Plage de numéros de ports|Groupe de ports  |
|--------------------|-----------------|
|0 à 1023                 | Ports réservés  |
|1024 à 49151             | Ports inscrits  |
|49152 à 65535            | Ports dynamiques|

+ **Ports réservés (0 à 1023):** utilisés pour des services serveurs bien connus (HTTP : 80, FTP : 21, SMTP : 25, etc.). 

+ **Ports inscrits (1024 à 49151) :** affectés à des applications utilisateurs ou moins standardisées. 

+ **Ports dynamiques/privés (49152 à 65535):** souvent utilisés comme ports sources par les clients, dits "*éphémères*". 

+ Exemples de ports:
    + TCP : 21, 23, 25, 80, 443 sont dans la plage "réservée". 
    + UDP : 69 (TFTP), 520 (RIP) sont dans "réservés". 
    + TCP & UDP : 53 (DNS), 161 (SNMP). 

## Sockets

Une socket est la combinaison de : l’adresse IP (source ou destination) + numéro de port (source ou destination) + protocole de transport (TCP ou UDP) — ce qui permet d’identifier de façon unique un point de communication. 

+ Une paire de sockets (source & destination) **identifie une conversation spécifique entre deux hôtes** : par exemple, client `192.168.1.5:1099` et serveur `192.168.1.7:80` constituent une paire. 

+ Les sockets permettent simultanément :
    + à plusieurs processus sur le client de mener diverses requêtes (chacun avec un port source différent)

    + au serveur de distinguer plusieurs connexions entrantes vers le même service (du même client ou de clients différents) grâce à la combinaison adresses/ports. 

### Exemple de communication

![Exemple adressage de port](../images/04-01-08.png)

## Vérification/surveillance de ports

L’utilitaire `netstat` permet de voir les connexions actives, les adresses locales/distantes + numéros de ports + état de la connexion. 

Cela permet de détecter des connexions "inexpliquées" ou suspectes, ou d’analyser des performances réseau. 

# 4.2. Protocoles

# 4.2.1. TCP
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


# 4.2.2. UDP

Le protocole **UDP** (*User Datagram Protocol*) est un protocole de transport d’*acheminement au mieux*, décrit dans le document [RFC 768](https://datatracker.ietf.org/doc/html/rfc768). C'est un protocole de transport **léger** qui offre les mêmes fonctions de segmentation et de réorganisation des données que le protocole TCP, mais sans la fiabilité et le contrôle de flux du protocole TCP.

## Caractéristiques
![Caractéristiques du protocole UDP](../../images/04-04-01.png)

+ **Sans connexion –** le protocole UDP n’établit pas de connexion entre les hôtes avant que les données puissent être envoyées et reçues.

+ **Acheminement non fiable –** le protocole UDP ne fournit pas de services garantissant que les données sont acheminées de façon fiable. Il n’existe pas de processus dans le protocole UDP permettant de faire retransmettre à l’expéditeur les données perdues ou endommagées.

+ **Aucune reconstitution ordonnée des données –**  n’offre aucun mécanisme permettant de réorganiser les données dans leur ordre initial. Les données sont simplement remises à l’application dans l’ordre où elles arrivent.

+ **Aucun contrôle de flux –** le protocole UDP ne propose aucun service permettant de contrôler la quantité de données envoyées par la source pour éviter de submerger le périphérique de destination. La source envoie les données. Si les ressources sur l’hôte de destination sont surexploitées, l’hôte de destination abandonne généralement les données envoyées jusqu’à ce que des ressources soient disponibles.

{{% notice title=Remarque style="info"%}}
En général, on définit UDP par ce qu'il n'a pas par rapport à TCP.
{{% /notice %}}

## En-tête UDP

![En-tête UDP](../../images/04-04-02.png)

## Exemples de services

+ Système de noms de domaine (*DNS*)
+ SNMP (*Simple Network Management Protocol*)
+ Protocole DHCP (*Dynamic Host Configuration Protocol*)
+ Protocole RIP (*Routing Information Protocol*)
+ TFTP (*Trivial File Transfer Protocol*)
+ Téléphonie IP ou voix sur IP (*VoIP*)
+ Jeux en ligne


# 4.3. Ateliers
-------------------

# 4.3.1. Introduction à Wireshark
-------------------

{{% notice style="tip" title=".docx"%}}
+ [Version Word du laboratoire](../../lab-wireshark.docx) (utile pour garder une trace de votre travail) !
{{% /notice%}}
Dans ce travail pratique, vous utiliserez Wireshark pour examiner les paquets échangés entre votre ordinateur et internet lorsque vous allez sur la page www.google.com. La première partie couvrira l’ouverture de connexion TCP en trois étapes, tandis que la deuxième couvrira la communication UDP avec un serveur DNS pour récupérer l’adresse IP associée au domaine www.google.com.

## Partie I - Préparation de l’environnement
Pour commencer, utilisez la commande `ipconfig /all` sur l’ordinateur de la salle de laboratoire pour trouver et enregistrer les adresses MAC et IP de la carte réseau et l’adresse IP de la passerelle par défaut et du serveur DNS. Enregistrez ces informations dans le tableau ci-dessous. Elles seront utilisées dans les prochaines sections. Si vous ne terminez pas le laboratoire durant la première séance, assurez-vous de prendre le même poste à la prochaine.

|||
|-|-------------------|
|**Adresse IP**|                            |
|**Adresse MAC**|                           |
|**Adresse IP de la passerelle par défaut**|        |
|**Adresse IP du serveur DNS**|              |

+ **Faites une capture d’écran des résultats de votre commande** 


Ensuite, allez sur `Wireshark` et sélectionnez l’interface associée aux adresses IP et MAC du PC enregistrées ci-haut pour capturer les paquets.

Après avoir sélectionné l’interface, cliquez sur `Démarrer` pour commencer à capturer les paquets. Ensuite, ouvrez un navigateur Web et rendez-vous à l’adresse `www.google.com`. Réduisez la fenêtre *Google* et retournez dans *Wireshark*. Appuyez sur `Stop` (Arrêter) pour arrêter la capture *Wireshark*.

## Partie II - Examiner une connexion TCP en trois étapes
Dans *Wireshark*, localisez les colonnes `Source`, `Destination`, et `Protocol`.

Recherchez le premier paquet de la connexion en trois étapes. Dans l’exemple ci-dessous il s’agit de la **trame 61**: 

![Wireshark-01](../../images/04-05-01.png)

*2.1. Quelle est l’adresse IP du serveur Web de Google dans le cas ci-dessus?* 

Si vous avez de nombreux paquets qui ne sont pas liés à la connexion TCP, il peut être nécessaire d’utiliser la fonction de **filtre** de *Wireshark*. Saisissez `tcp` dans la zone de saisie du filtre dans *Wireshark* et appuyez sur Entrée.

Dans notre exemple, la **trame 61** correspond au début de la connexion en trois étapes entre l’ordinateur et le serveur Web de Google. Dans le volet de la liste des paquets (section supérieure de la fenêtre principale), sélectionnez la trame correspondante. Cette action met en surbrillance la ligne et affiche les informations décodées de ce paquet dans les deux volets inférieurs. 

Cliquez sur l’icône `+` à gauche du protocole TCP (Transmission Control Protocol) dans le volet de détails des paquets pour développer l’affichage des informations TCP.

Cliquez sur l’icône `+` à gauche des bits de contrôle. 

Examinez les ports source et de destination ainsi que les bit de contrôles définis.

![Wireshark-02](../../images/04-05-02.png)

+ **Faites une capture d’écran de vos résultats**

*2.2. Quel est le numéro du port source TCP ?*

*2.3. Comment classifieriez-vous le port source ?*

*2.4. Quel est le numéro du port de destination TCP ?*

*2.5. Comment classifieriez-vous le port de destination ?*

*2.6. Quel bit de contrôle est défini ? (plusieurs réponses possibles)*

*2.7. Sur quoi le numéro d’ordre relatif est-il défini ?*

Pour sélectionner la trame suivante dans la connexion en trois étapes, sélectionnez `Go` dans le menu *Wireshark* et sélectionnez `Next Packet In Conversation`. Dans cet exemple, il s’agit de la **trame 87**. C’est la réponse du serveur Web Google à la requête initiale (*SYN*) de démarrage d’une session :

![Wireshark-03](../../images/04-05-03.png)


*2.8. Quelles sont les valeurs des ports source et de destination ?*

*2.9. Sur quoi les numéros d’ordre relatif et d’accusé de réception sont-ils définis ?*

Enfin, examinez le troisième paquet de la connexion en trois étape. Dans l’exemple ci-dessous, c’est la **trame 88** :

![Wireshark-04](../../images/04-05-04.png)

*2.10. Quel bit de contrôle est défini ? (plusieurs réponses possibles)*


+ **Faites une capture d’écran de votre résultat.** 

Les numéros d’ordre relatif et d’accusé de réception sont définis sur 1 comme point de départ. La connexion TCP est désormais établie, et la communication entre l’ordinateur source et le serveur Web peut commencer.

## Partie III - Examiner une capture DNS UDP
Dans cette partie, vous examinerez les paquets *UDP* générés lors de la communication avec le serveur DNS afin de récupérer les adresses IP associées au domaine `www.google.com`.

Dans la fenêtre principale de Wireshark, tapez `dns` dans la zone de saisie de la barre de filtre. Cliquez sur `Apply` ou appuyez sur Entrée.

{{% notice style="info" title="Remarque" %}}
Si vous ne voyez aucun résultat après l’application du filtre DNS, fermez le navigateur web et dans la fenêtre d’invite de commandes, tapez `ipconfig /flushdns` pour supprimer tous les résultats DNS précédents. Redémarrez la capture *Wireshark* et répétez les instructions de la partie I. Si cela ne résout pas le problème, dans la fenêtre d’invite de commandes, vous pouvez taper `nslookup www.google.com` au lieu d’utiliser le navigateur Web.
{{% /notice %}}

![Wireshark-05](../../images/04-05-05.png)

Dans le volet de la liste des paquets (section supérieure) de la fenêtre principale, localisez le paquet qui inclut « **standard query** » et « **A www.google.com** » (voir la **trame 25** ci-dessus comme exemple).

Examinez le segment UDP de la requête DNS capturée par *Wireshark*. Dans cet exemple, la **trame 25** est sélectionnée pour l’analyse. Les protocoles dans cette requête apparaissent Les entrées de protocole sont mises en surbrillance en gris : 

![Wireshark-06](../../images/04-05-06.png)

Dans le volet de détails des paquets, on peut voir que la **trame 25** possède 74 octets de données sur le câble comme indiqué à la première ligne. C’est le nombre d’octets nécessaires pour envoyer une requête DNS à un serveur et demander les adresses IP de `www.google.com`.

La ligne `Ethernet II` affiche les adresses MAC source et de destination. L’adresse MAC provient de votre PC, car c’est lui qui a émis la requête DNS. L’adresse MAC de destination provient de la passerelle par défaut, car c’est le dernier arrêt avant la sortie de cette requête du réseau local.

+ **Effectuez une capture d’écran de votre résultat**

*3.1 L’adresse MAC source est-elle identique à celle enregistrée dans la première partie pour le PC ?*

Sur la ligne `Internet Protocol Version 4`, la capture Wireshark de paquets IP indique que l’adresse IP source de cette requête DNS est `192.168.0.43` et l’adresse IP de destination est `192.168.0.1`. Dans cet exemple, l’adresse de destination est la passerelle par défaut. Le routeur est la passerelle par défaut sur ce réseau. Associez les adresses MAC et IP pour les périphériques source et destination : 

||||
|----|----|----|
|Périphérique|Adresse IP| Adresse MAC|
|Votre PC|||
|Passerelle par défaut|||

+ **Faites une capture d’écran montrant où vous avez trouvé les informations entrées dans le tableau ci-dessus.**


Développez le protocole *UDP (User Datagram Protocol)* dans le volet de détails des paquets en cliquant sur le signe plus (`+`). Notez qu’il n’existe que quatre champs. Le numéro du port source dans cet exemple est `49430`. Le port source a été généré aléatoirement par votre PC au moyen des numéros de port qui ne sont pas réservés. Le port de destination est le `53`. Le port `53` est un port réservé destiné à une utilisation avec DNS. Les serveurs DNS écoutent sur le port `53` les requêtes DNS provenant des clients.

![Wireshark-07](../../images/04-05-07.png)

Dans cet exemple, la longueur de ce segment UDP est de 40 octets. Sur 40 octets, 8 sont utilisés pour l’en-tête. Les 32 autres octets sont utilisés par les données de requête DNS. Les 32 octets de données de requête DNS sont mis en surbrillance dans l’illustration suivante du volet d’octets des paquets (section inférieure) de la fenêtre principale de *Wireshark*.

![Wireshark-08](../../images/04-05-08.png)

+ **Faites une capture d’écran de votre résultat.**

La somme de contrôle est utilisée pour déterminer l’intégrité du paquet une fois qu’il a transité par Internet. L’en-tête UDP surcharge peu le réseau parce que le protocole UDP n’a pas de champs associés à l’échange en trois étapes du protocole TCP. Tous les problèmes de fiabilité liés au transfert des données doivent être gérés par la couche application.

Notez les résultats de Wireshark dans la table ci-dessous.

|||
|---|---|
|Taille de la trame||
|Adresse MAC source||
|Adresse MAC de destination||
|Adresse IP source||
|Adresse IP de destination||
|Port source||
|Port de destination||

Ensuite, examinez le paquet de réponse DNS et vérifiez que ce paquet utilise aussi le protocole UDP.

Dans cet exemple, la **trame 29** est le paquet de réponse DNS. Notez que le nombre d’octets sur le câble correspond à 90 octets. Il s’agit d’un paquet plus gros par rapport au paquet de
requête.

![Wireshark-09](../../images/04-05-09.png)


+ **Faites une capture d’écran de votre résultat**

*Dans la trame Ethernet II de la réponse DNS, de quel périphérique provient l’adresse MAC source et à quel périphérique correspond l’adresse MAC de destination ?*

*Notez les adresses IP source et de destination du paquet IP. Quelle est l’adresse IP de destination ? Quelle est l’adresse IP source ?*

Dans le segment UDP, le rôle des numéros de port a également été inversé. Le numéro du port de destination est `49430`. Le numéro de port `49430` est le même port que celui qui a été généré par votre PC lorsque la requête DNS a été envoyée au serveur DNS. Votre PC a attendu une réponse DNS sur ce port.

Le numéro du port source est `53`. Le serveur DNS attend une requête DNS sur le port `53`, puis envoie une réponse DNS avec le numéro de port source 53 à l’émetteur de la requête DNS. Lorsque la réponse DNS est développée, examinez les adresses IP résolues pour `www.google.com` dans la section `Answers` (Réponses).

![Wireshark-10](../../images/04-05-10.png)

+ **Faites une capture d’écran de votre résultat.**

# 4.3.2. Socket TCP/UDP
-------------------

Une des façons de créer une communication entre deux hôtes d'un réseau est d'utiliser l'approche client-serveur :

+ Le serveur est celui qui reçoit les messages
+ Le client est celui qui envoit les messages

Les deux protocoles de la couche transport, TCP et UDP, peuvent être utilisés pour envoyer des messages entre un client et un serveur.

+ TCP: une connexion est établie entre client et serveur. Cette connexion est ensuite utilisée pour que les deux s’échangent des messages. Lorsqu’un des deux termine la connexion, celle-ci est fermée.
+ UDP: le client envoit des messages au serveur sans établir de connexion préalable. Il n’est donc pas possible de savoir si le serveur est en ligne et prêt à recevoir les messages.
+ Pour communiquer en utilisant TCP ou UDP, il faut utiliser les *sockets*.

{{% notice title="Rappel" style="tip" %}}
Un socket représente le point terminal d’une communication entre deux hôtes sur un réseau. Il peut être la source ou la destination d’un message. Les sockets sont définis par une adresse IP et un port et sont utilisés par les programmes pour envoyer et recevoir des messages.
{{% /notice %}}

Dans cet atelier nous allons explorer comment créer une architecture client-serveur en utilisant le langage de programmation Python et la librairie standard `socket`.

## Client
### UDP
Le programme suivant envoit un message par UDP sur un réseau. L’adresse de destination est `127.0.0.1` et le port est `8888`:
```python
import socket

DEST_IP = "127.0.0.1" # localhost
DEST_PORT = 8888
MESSAGE = "Salut !\n"

try:
    # Ouverture d'un socket 
    # se ferme lorsqu'on sort du bloc with
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as socket:
        # Envoyer le message
        socket.sendto(MESSAGE.encode(), (DEST_IP, DEST_PORT))
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin du programme")
```

Vous pouvez tester ce programme comme suit: à partir d’un hôte sur le même réseau que le client, ouvrez un port UDP en mode “listen” avec la commande `nc` (`netcat`). Cet hôte joue donc le rôle de serveur. Par exemple, pour ouvrir le port UDP `8888` la commande est la suivante :

```bash
nc -ulp 8888
```

Assurez-vous que la variable `DEST_IP` du programme a bien comme valeur l’adresse de l’hôte où vous avez lancé la commande `nc`.

### TCP
Le programme suivant envoit un message par TCP sur un réseau. L’adresse de destination est `127.0.0.1` (*localhost*) et le port est `8888`:
```python
import socket

DEST_IP="127.0.0.1"
DEST_PORT = 8888
MESSAGE = "abcdefg!\n"

try:
    # Ouverture d'un socket (se ferme lorsqu'on sort du bloc with)
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as socket:
        # Demande de connexion
        socket.connect((DEST_IP, DEST_PORT))
        
        # Envoie du message
        socket.send(MESSAGE.encode())

# Erreur de connexion
except ConnectionRefusedError:
    print("Erreur: Serveur indisponible")
# Autres erreurs
except Exception as err:
    print(f"Erreur: {err}")
# Fin du programme avec CTRL+C
except KeyboardInterrupt:
    print("Fin du programme")
```

Vous pouvez aussi tester ce programme avec la commande `nc`. À partir d’un hôte sur le même réseau que la machine client (qui aura le rôle de serveur), ouvrez un port TCP en mode “listen” au port TCP `8888`:
```bash
nc -lp 8888
```

{{% notice style="tip" title=Remarque %}}
+ Les commandes `nc` ou `python` sont introuvables? Essayez de les installer :
```bash
sudo dnf install nc # Alma/Rocky/Centos
sudo apt install netcat-openbsd # Ubuntu/Debian

sudo dnf install python # Alma/Rocky/Centos
sudo apt install python # Ubuntu/Debian
```
{{% /notice %}}

Assurez-vous que la variable `DEST_IP` du programme a bien comme valeur l’adresse de l’hôte où vous avez lancé la commande `nc`; ensuite exécutez-le.

## Serveur
### UDP
Le programme suivant ouvre un socket au port UDP `8888` et affiche les messages entrants:

```python
import socket

IP = "" # Écoute sur toutes les IPs du serveur
PORT = 8888
BUFFER_SIZE = 1024

try: 
    # Ouverture d'un socket (se ferme quand on sort du bloc with)
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as socket:
        # Lie le socket à l'adresse et au port d'écoute
        socket.bind((IP, PORT))
        print(f"Attente de messages UDP sur le port {PORT}...")

        # Réception des messages
        while True:
            # Réception des données
            message, adr_dist = socket.recvfrom(BUFFER_SIZE)
            
            # Décodage binaire -> texte
            # Suppression des "impuretés"
            message = message.decode().strip()
            print(f"Message reçu: {message}")

except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print(f"Fin du programme")

```

Pour tester ce programme à partir d’un autre hôte (qui agit comme client), lancez la commande `nc` (remplacez `1.2.3.4` par l’adresse IP du serveur) et tapez le message que vous voulez envoyer:

```bash
nc -u 1.2.3.4 8888
abcdefg!
```

### TCP
Le programme suivant ouvre un socket au port TCP `8888` et attend les connexions entrantes. Lorsque la connexion est établie il affiche les messages et termine la connexion lorsque le message reçu est vide (taille 0):
```python
import socket

IP = "" # Écoute sur toutes les IPs du serveur
PORT = 8888
BUFFER_SIZE = 1024
try: 
    # Ouverture d'un socket (se ferme quand on sort du bloc with)
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as socket:
        # Lie le socket à l'adresse et au port d'écoute
        socket.bind((IP, PORT))
        
        # Attend une connexion (écoute)
        socket.listen()
        print(f"Serveur TCP en attente de connexion sur le port {PORT}...")
        
        # Accepte la connexion reçue
        conn, addr = socket.accept()
        with conn:
            print(f"Client connecté : {addr}")
            while True:
                # Attend de recevoir un message
                data = conn.recv(BUFFER_SIZE)
                if not data :
                    print("Déconnecté")
                    break
                # Décodage binaire -> texte
                # Suppression des "impuretés"
                message = data.decode().strip()
                print(f"Reçu : {message}")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print(f"Fin du programme")
    exit(1)

```

Pour tester ce programme à partir d’un autre hôte (qui agit comme client), lancez la commande `nc` (remplacez `1.2.3.4` par l’adresse IP du serveur) et tapez le message que vous voulez envoyer:

```bash
nc 1.2.3.4 8888
abcdefg!
```

{{%notice style="note" title="Note"%}}
Si vous n'arrivez pas à faire communiquer le client et le serveur, essayez de désactiver le pare-feu (ce n'est pas une très bonne pratique, mais nous n'allons pas couvrir les règles de pare-feu dans ce cours) :

```bash
sudo systemctl stop firewalld # arrêter le service du pare-feu
sudo systemctl disable firewalld # désactiver le service (ne démarre pas au prochain reboot)
sudo systemctl status firewalld # vérifier
```
{{%/notice %}}

## Exercices
### Exercice 1
1. Créez un serveur TCP qui :
    + écoute sur le port `5000`,
    + accepte une connexion,
    + reçoit un message du client,
    + affiche le message,
    + répond "Bonjour client, j'ai bien reçu ton message : <Message reçu>".

2. Créez un client TCP qui :
    + se connecte au serveur,
    + demande à l'utilisateur d'entrer un message
    + envoie le message au serveur
    + lorsque le message envoyé est "quit", le client met fin à la connexion et le programme se termine.

{{% expand title="Solution"%}}
1. **Serveur** 
```python
import socket

IP = ""
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as socket:
        socket.bind((IP, PORT))
        socket.listen()
        print(f"Serveur TCP en attente de connexion sur le port {PORT}...")
        conn, addr = socket.accept()
        with conn:
            print(f"Client connecté : {addr}")
            while True:
                data = conn.recv(1024)
                if not data:
                    break
                
                request = data.decode().strip()
                print(f"Reçu : {request}")
                
                if request == "quit":
                    break
                
                response = f"Bonjour client, j'ai bien reçu ton message : {request}"
                conn.sendall(response.encode('utf-8'))
        print("Fin de la conversation")
except ConnectionRefusedError as e:
    print("Erreur: serveur indisponible")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin de la conversation")
```
2. **Client** 
```python
import socket

IP="192.168.121.226"
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
        sock.connect((IP, PORT))

        while True:
            user_input = input(">>> ")
            sock.send(user_input.strip().encode())
            if user_input == "quit":
                print("Fin de la conversation")
                break 
            data = sock.recv(BUFFER_SIZE)
            if not data :
                print("Déconnecté")
                break
            else:
                message = data.decode("utf-8")
                print(f"Serveur: {message}")
except ConnectionRefusedError as e:
    print("Erreur: serveur indisponible")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin de la conversation")
```
{{% /expand %}}


### Exercice 2
Reproduire le comportement du client-serveur de l'exercice 1, cette fois-ci avec UDP.

{{% expand title="Solution"%}}
1. **Serveur**
```python
import socket

IP = ""
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as socket:
        socket.bind((IP, PORT))
        print(f"Serveur UDP en écoute de messages sur le port {PORT}...")
        while True:
            data, address = socket.recvfrom(BUFFER_SIZE)
            request = data.decode().strip()
            print(f"Reçu : {request}")
            
            response = f"Bonjour client, j'ai bien reçu ton message : {request}"
            socket.sendto(response.encode(), address)
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin du serveur UDP")
```

+ **Client**
```python
import socket

IP="192.168.121.226"
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as sock:
        while True:
            user_input = input(">>> ")
            sock.sendto(
                user_input.strip().encode(),
                (IP, PORT)
            )
            if user_input == "quit":
                break 
            data = sock.recv(BUFFER_SIZE)
            message = data.decode()
            print(f"Serveur: {message}")
        print("Fin du client UDP")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin du client UDP")
```
{{% /expand %}}