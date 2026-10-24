# 3. Couches accès réseau

Dans le modèle **TCP/IP**, la couche d'accès réseau regroupe les fonctions correspondant principalement aux **couches physique (L1)** et **liaison de données (L2)** du modèle OSI.

![Couches basses des modèles OSI et TCP/IP](./images/02-1.png?width=500px)


Ces couches sont responsables de la **transmission des données sur un réseau local** : elles définissent notamment le support utilisé, la manière dont les bits sont transmis et la façon dont les équipements s'identifient et échangent des **trames**.

Dans ce chapitre, nous étudierons :

- les principaux **supports de transmission**;
- les équipements des couches 1 et 2;
- le fonctionnement d'**Ethernet**;
- les **adresses MAC** et les trames;
- le rôle du protocole **ARP** dans la communication IPv4 sur un réseau Ethernet.

# 3.1. Couche physique

## Rôle

La **couche physique (L1)** est responsable de la transmission des **bits** sur un support physique ou sans fil.

Elle définit notamment :

- le support utilisé pour transmettre les données;
- les caractéristiques électriques, optiques ou radio du signal;
- la représentation des bits sur le support;
- les connecteurs et caractéristiques physiques;
- les caractéristiques de transmission, comme le débit et la distance maximale.

La couche physique ne comprend pas la signification des données transmises. Elle s'occupe uniquement de **transmettre des bits** d'un équipement à un autre.

![Couches 1 et 2](../images/02-2.png?width=600px)

---

## Supports de transmission

Un **support de transmission** est le moyen utilisé pour transporter les données entre deux équipements.

On distingue deux grandes catégories :

- **Supports guidés** : le signal circule dans un support physique.
- **Supports non guidés** : le signal est transmis sans câble, généralement par ondes radio.

| Catégorie | Exemples |
|---|---|
| **Guidé** | Paire torsadée, câble coaxial, fibre optique |
| **Non guidé** | Wi-Fi, Bluetooth, réseaux cellulaires, satellite |


### Supports guidés
#### Paire torsadée

La **paire torsadée** est constituée de fils de cuivre regroupés par paires et torsadés entre eux.

La torsion permet notamment de réduire les interférences électromagnétiques entre les fils.

Elle est très utilisée dans les **réseaux locaux Ethernet** en raison de son faible coût et de sa facilité d'installation.

Deux grandes catégories existent :

- **UTP** (*Unshielded Twisted Pair*) : paire torsadée non blindée;
- **STP** (*Shielded Twisted Pair*) : paire torsadée blindée.

La longueur maximale d'un lien Ethernet sur paire torsadée est généralement de **100 mètres** pour les installations courantes.

##### Catégories de câbles

Les câbles à paire torsadée sont classés en différentes catégories. La catégorie indique notamment les caractéristiques de transmission que le câble peut supporter.

| Catégorie | Usage courant |
|---|---|
| **Cat 5e** | Jusqu'à 1 Gb/s |
| **Cat 6** | Jusqu'à 1 Gb/s dans les installations courantes; peut supporter des débits supérieurs sur de plus courtes distances |
| **Cat 6a** | Jusqu'à 10 Gb/s sur 100 m |
| **Cat 8** | Jusqu'à 25 ou 40 Gb/s sur des distances plus courtes |

> **À retenir :** le choix d'un câble dépend du débit recherché, de la distance et de l'environnement d'installation.

##### Connecteur RJ-45

Les câbles Ethernet à paire torsadée utilisent généralement un connecteur **8P8C**, couramment appelé **RJ-45**.

Le câblage des conducteurs est défini notamment par les normes **T568A** et **T568B**.
<div style="display: flex; align-items: center; gap: 10px;">
<img src="../images/01-3.png" width="300"/>
<img src="../images/01-4.png" width="250"/>
</div>
{{% center %}}
*Alignement des câble - norme **T568A***
{{% /center %}}

<div style="display: flex; align-items: center; gap: 10px;">
<img src="../images/01-5.png" width="300"/>
<img src="../images/01-6.png" width="250"/>
</div>
{{% center %}}
*Alignement des câble - norme **T568B***
{{% /center %}}


#### Câble coaxial

![Câble coaxial](../images/020101-cable-coaxial.png?width=28rem)

Le **câble coaxial** est constitué d'un conducteur central entouré d'un isolant et d'un blindage métallique.

Il est notamment utilisé dans :

- les réseaux de télévision et de câblodistribution;
- certaines installations de vidéosurveillance;
- certaines communications radio;
- les anciennes générations de réseaux Ethernet.

Il est aujourd'hui beaucoup moins utilisé que la paire torsadée et la fibre optique pour les réseaux informatiques modernes.

#### Fibre optique

![Fibre optique](/03-01-fibre-optique.webp?width=28rem)

La **fibre optique** utilise des impulsions lumineuses pour transmettre les données.

Elle offre notamment :

- des débits élevés;
- de longues distances de transmission;
- une faible sensibilité aux interférences électromagnétiques;
- une faible atténuation par rapport aux supports en cuivre.

Elle est largement utilisée dans :

- les réseaux d'entreprise;
- les réseaux des fournisseurs Internet;
- les réseaux longue distance;
- les câbles sous-marins;
- les connexions **FTTH** (*Fiber To The Home*).



### Supports non guidés (sans fil)

Dans une transmission **sans fil**, les données sont transmises par des ondes électromagnétiques plutôt que par un câble.

Les principales technologies étudiées dans le contexte des réseaux locaux sont notamment :

- **Wi-Fi** — IEEE 802.11;
- **Bluetooth** — IEEE 802.15.

**Avantages :**

- mobilité;
- installation simplifiée;
- absence de câblage entre les appareils.

**Contraintes :**

- portée limitée;
- interférences;
- partage du support;
- sécurité des communications.


# 3.2. Matériel des couches basses

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


# 3.3. Couche liaison de données

----------------------
## Rôle

La **couche liaison de données (L2)** permet la communication entre des équipements directement connectés au même réseau local.

Elle assure notamment :

- l'**encapsulation des paquets IP dans des trames**;
- l'**adressage physique** à l'aide des adresses MAC;
- le **contrôle de l'accès au support**;
- la **détection de certaines erreurs de transmission**.

L'unité de données de la couche liaison de données est appelée une **trame**.


## Ethernet

![Ethernet: protocole des couches 1 et 2](../images/02-14.png?width=30rem)

**Ethernet** est la technologie dominante pour les réseaux locaux filaires.

La norme **IEEE 802.3** définit notamment les caractéristiques des réseaux Ethernet aux couches physique et liaison de données.

Ethernet utilise deux sous-couches au niveau de la liaison de données :

- **LLC** (*Logical Link Control*);
- **MAC** (*Media Access Control*).


### Sous-couche LLC

La sous-couche **LLC** fait le lien entre la couche liaison de données et les couches supérieures.

Elle permet notamment d'identifier et de gérer les protocoles de couche supérieure qui utilisent la liaison.

Dans les réseaux Ethernet modernes, une grande partie de cette logique est prise en charge par les logiciels et les pilotes réseau.

### Sous-couche MAC

La sous-couche **MAC** est responsable des fonctions directement liées à l'accès au support Ethernet.

Elle intervient notamment dans :

- la construction et la lecture des trames;
- l'utilisation des adresses MAC;
- l'accès au support de transmission.

La sous-couche MAC est étroitement liée à la **carte réseau** et à son fonctionnement matériel.

# 3.4. La trame Ethernet

---------------

Lorsqu'un paquet provenant de la couche réseau doit être transmis sur Ethernet, il est encapsulé dans une **trame Ethernet**.

De façon simplifiée, une trame contient :

![Structure d'une trame Ethernet](/03-02-trame-ethernet.webp)

| Champ | Rôle |
|---|---|
| **Préambule** | Indique le début de la trame|
| **Adresse MAC destination** | Identifie le destinataire sur le réseau local |
| **Adresse MAC source** | Identifie l'émetteur |
| **Type** | Utilisé par LLC. Indique le protocole transporté (par exemple IPv4) |
| **Données** | Contient le paquet provenant de la couche réseau |
| **FCS** | Permet de détecter certaines erreurs de transmission |



# 3.5. Adresse MAC
-------------

Une **adresse MAC** (*Media Access Control*) est l'identifiant utilisé par Ethernet pour identifier une interface réseau sur le réseau local.

Une adresse MAC Ethernet classique est composée de **48 bits**, généralement représentés sous la forme de 12 chiffres hexadécimaux.

Exemples :

```text
00:05:9A:3C:78:00
00-05-9A-3C-78-00
0005.9A3C.7800
```

Une adresse MAC est généralement divisée en deux parties :

- les premiers bits identifient l'organisation ayant reçu le préfixe (**OUI**);
- les bits restants permettent d'identifier l'interface.

![Adresse MAC](../images/02-17.png?width=28rem)

### Unicast, broadcast

Une adresse MAC peut être utilisée pour différents types de communication.

#### Unicast

![Exemple de trame avec adresse Unicast](../images/02-21.png?width=30rem)

Une trame **unicast** est destinée à une interface précise.

```text
Ordinateur A ─────────► Ordinateur B
```

#### Broadcast

![Exemple de trame avec adresse MAC broadcast](../images/02-22.png?width=30rem)

Une trame **broadcast** est destinée à **toutes les interfaces du réseau local**.

L'adresse MAC de broadcast est :

```text
FF:FF:FF:FF:FF:FF
```

# 3.6. ARP

-------------


Le protocole **ARP** (*Address Resolution Protocol*) permet d'associer une **adresse IPv4** à une **adresse MAC** sur un réseau local Ethernet.

Cette résolution est nécessaire lorsqu'un équipement connaît l'adresse IP de destination, mais doit déterminer quelle adresse MAC utiliser pour construire la trame Ethernet.

{{%notice style="info" title="Rappel"%}}
Le fonctionnement d'ARP a déjà été présenté précédemment. Ici, on retient simplement son rôle dans l'encapsulation Ethernet.
{{%/notice%}}

### Exemple

Supposons que :

```text
Ordinateur A
IP  : 192.168.1.10
MAC : AA:AA:AA:AA:AA:AA

Ordinateur B
IP  : 192.168.1.20
MAC : BB:BB:BB:BB:BB:BB
```

Si A veut communiquer avec B :

1. A détermine que `192.168.1.20` se trouve sur son réseau local.
2. A utilise ARP pour connaître la MAC associée à `192.168.1.20`.
3. A construit une trame Ethernet avec :
   - MAC source : `AA:AA:AA:AA:AA:AA`
   - MAC destination : `BB:BB:BB:BB:BB:BB`
4. La trame est transmise sur le réseau.

## Communication avec un réseau distant

Lorsque la destination se trouve sur **un autre réseau**, l'ordinateur n'utilise pas la MAC de l'hôte distant.

Il envoie la trame à la **passerelle par défaut**.

ARP permet alors de déterminer la MAC de l'interface locale du routeur.

```text
Ordinateur A
192.168.1.10
      │
      │ Trame Ethernet
      │ MAC destination = MAC du routeur
      ▼
Routeur
192.168.1.1
      │
      │ Réseau IP suivant
      ▼
Réseau distant
```


{{%notice style="tip" title="À retenir"%}}
ARP résout une adresse **IPv4 en adresse MAC sur le réseau local**. Il ne permet pas de découvrir directement la MAC d'un ordinateur situé sur Internet.
{{%/notice%}}
