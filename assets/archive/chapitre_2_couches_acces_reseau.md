# 2 - Couches d'accès réseau

Dans le modèle **TCP/IP**, la couche d'accès réseau regroupe les fonctions correspondant principalement aux **couches physique (L1)** et **liaison de données (L2)** du modèle OSI.

Ces couches sont responsables de la **transmission des données sur un réseau local** : elles définissent notamment le support utilisé, la manière dont les bits sont transmis et la façon dont les équipements s'identifient et échangent des **trames**.

Dans ce chapitre, nous étudierons :

- les principaux **supports de transmission**;
- les équipements des couches 1 et 2;
- le fonctionnement d'**Ethernet**;
- les **adresses MAC** et les trames;
- le rôle du protocole **ARP** dans la communication IPv4 sur un réseau Ethernet.

---

## 2.1 - Couche physique

### Rôle

La **couche physique (L1)** est responsable de la transmission des **bits** sur un support physique ou sans fil.

Elle définit notamment :

- le support utilisé pour transmettre les données;
- les caractéristiques électriques, optiques ou radio du signal;
- la représentation des bits sur le support;
- les connecteurs et caractéristiques physiques;
- les caractéristiques de transmission, comme le débit et la distance maximale.

La couche physique ne comprend pas la signification des données transmises. Elle s'occupe uniquement de **transmettre des bits** d'un équipement à un autre.

![Couches 1 et 2](./images/02-2.png?width=600px)

---

### Supports de transmission

Un **support de transmission** est le moyen utilisé pour transporter les données entre deux équipements.

On distingue deux grandes catégories :

- **Supports guidés** : le signal circule dans un support physique.
- **Supports non guidés** : le signal est transmis sans câble, généralement par ondes radio.

| Catégorie | Exemples |
|---|---|
| **Guidé** | Paire torsadée, câble coaxial, fibre optique |
| **Non guidé** | Wi-Fi, Bluetooth, réseaux cellulaires, satellite |

---

### Paire torsadée

La **paire torsadée** est constituée de fils de cuivre regroupés par paires et torsadés entre eux.

La torsion permet notamment de réduire les interférences électromagnétiques entre les fils.

Elle est très utilisée dans les **réseaux locaux Ethernet** en raison de son faible coût et de sa facilité d'installation.

Deux grandes catégories existent :

- **UTP** (*Unshielded Twisted Pair*) : paire torsadée non blindée;
- **STP** (*Shielded Twisted Pair*) : paire torsadée blindée.

La longueur maximale d'un lien Ethernet sur paire torsadée est généralement de **100 mètres** pour les installations courantes.

#### Catégories de câbles

Les câbles à paire torsadée sont classés en différentes catégories. La catégorie indique notamment les caractéristiques de transmission que le câble peut supporter.

| Catégorie | Usage courant |
|---|---|
| **Cat 5e** | Jusqu'à 1 Gb/s |
| **Cat 6** | Jusqu'à 1 Gb/s dans les installations courantes; peut supporter des débits supérieurs sur de plus courtes distances |
| **Cat 6a** | Jusqu'à 10 Gb/s sur 100 m |
| **Cat 8** | Jusqu'à 25 ou 40 Gb/s sur des distances plus courtes |

> **À retenir :** le choix d'un câble dépend du débit recherché, de la distance et de l'environnement d'installation.

#### Connecteur RJ-45

Les câbles Ethernet à paire torsadée utilisent généralement un connecteur **8P8C**, couramment appelé **RJ-45**.

Le câblage des conducteurs est défini notamment par les normes **T568A** et **T568B**.

![Alignement](../images/01-3.png?width=20vw)

![Alignement](../images/01-4.png?width=20vw)

---

### Câble coaxial

Le **câble coaxial** est constitué d'un conducteur central entouré d'un isolant et d'un blindage métallique.

Il est notamment utilisé dans :

- les réseaux de télévision et de câblodistribution;
- certaines installations de vidéosurveillance;
- certaines communications radio;
- les anciennes générations de réseaux Ethernet.

Il est aujourd'hui beaucoup moins utilisé que la paire torsadée et la fibre optique pour les réseaux informatiques modernes.

![Câble coaxial](../images/020101-cable-coaxial.png?width=35vw)

---

### Fibre optique

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

![Fibre optique](../images/01-8.png?width=300px)

---

### Transmission sans fil

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

![Wi-Fi](../images/02-8.png?width=30rem)

---

### Normes et organismes

Plusieurs organismes participent à la définition des normes utilisées dans les réseaux.

| Organisme | Exemples |
|---|---|
| **IEEE** | 802.3 Ethernet, 802.11 Wi-Fi, 802.15 |
| **TIA/EIA** | Normes de câblage structuré |
| **ISO** | Normes internationales, notamment pour le câblage |
| **UIT-T** | Normes de télécommunications |

> **À retenir :** les normes permettent aux équipements provenant de fabricants différents de fonctionner ensemble selon des règles communes.

---

## 2.2 - Matériel des couches basses

Les équipements réseau n'ont pas tous le même rôle. Certains travaillent principalement à la couche physique, alors que d'autres utilisent les informations de la couche liaison de données.

### Répéteur

Un **répéteur** reçoit un signal, le régénère et le retransmet afin de permettre une communication sur une distance plus importante.

Il travaille principalement à la **couche physique (L1)**.

![Répéteur](../images/02-repeteur.jpg?width=15rem)

---

### Concentrateur (Hub)

Un **concentrateur**, ou *hub*, est essentiellement un répéteur possédant plusieurs ports.

Lorsqu'il reçoit des bits sur un port, il les retransmet sur les autres ports.

Tous les appareils connectés au concentrateur reçoivent donc les transmissions, même lorsqu'elles ne leur sont pas destinées.

Il fonctionne à la **couche physique (L1)**.

![Concentrateur](../images/02-hub.jpg?width=30rem)

> Les concentrateurs ont aujourd'hui été largement remplacés par les **commutateurs (switches)**.

---

### Commutateur (Switch)

Un **commutateur** fonctionne à la **couche liaison de données (L2)**.

Contrairement au concentrateur, il peut déterminer sur quel port se trouve un équipement et transmettre une trame uniquement vers ce port.

Pour effectuer cette tâche, le commutateur utilise notamment les **adresses MAC** des équipements.

![Commutateur](../images/02-switch.png?width=30rem)

> **À retenir :** un hub répète les signaux vers plusieurs ports, tandis qu'un switch prend des décisions de transmission à partir des adresses MAC.

---

### Carte réseau

La **carte réseau**, ou **NIC** (*Network Interface Card*), permet à un ordinateur ou à un autre équipement de communiquer sur un réseau.

Elle peut notamment :

- transmettre et recevoir des signaux;
- posséder une adresse MAC;
- gérer les fonctions Ethernet de la couche liaison;
- être connectée à un câble ou utiliser une technologie sans fil.

![Carte réseau](../images/02-NIC.jpg?width=30rem)

---

## 2.3 - Couche liaison de données

### Rôle

La **couche liaison de données (L2)** permet la communication entre des équipements directement connectés au même réseau local.

Elle assure notamment :

- l'**encapsulation des paquets IP dans des trames**;
- l'**adressage physique** à l'aide des adresses MAC;
- le **contrôle de l'accès au support**;
- la **détection de certaines erreurs de transmission**.

L'unité de données de la couche liaison de données est appelée une **trame**.

![Couches 1 et 2](./images/02-5.png?width=700px)

---

### Ethernet

**Ethernet** est la technologie dominante pour les réseaux locaux filaires.

La norme **IEEE 802.3** définit notamment les caractéristiques des réseaux Ethernet aux couches physique et liaison de données.

Ethernet utilise deux sous-couches au niveau de la liaison de données :

- **LLC** (*Logical Link Control*);
- **MAC** (*Media Access Control*).

![Couches 1 et 2](../images/02-14.png?width=700px)

---

### Sous-couche LLC

La sous-couche **LLC** fait le lien entre la couche liaison de données et les couches supérieures.

Elle permet notamment d'identifier et de gérer les protocoles de couche supérieure qui utilisent la liaison.

Dans les réseaux Ethernet modernes, une grande partie de cette logique est prise en charge par les logiciels et les pilotes réseau.

---

### Sous-couche MAC

La sous-couche **MAC** est responsable des fonctions directement liées à l'accès au support Ethernet.

Elle intervient notamment dans :

- la construction et la lecture des trames;
- l'utilisation des adresses MAC;
- l'accès au support de transmission.

La sous-couche MAC est étroitement liée à la **carte réseau** et à son fonctionnement matériel.

---

## 2.4 - La trame Ethernet

Lorsqu'un paquet provenant de la couche réseau doit être transmis sur Ethernet, il est encapsulé dans une **trame Ethernet**.

De façon simplifiée, une trame contient :

| Champ | Rôle |
|---|---|
| **Adresse MAC destination** | Identifie le destinataire sur le réseau local |
| **Adresse MAC source** | Identifie l'émetteur |
| **Type** | Indique le protocole transporté, par exemple IPv4 |
| **Données** | Contient le paquet provenant de la couche réseau |
| **FCS** | Permet de détecter certaines erreurs de transmission |

![Structure d'une trame Ethernet](../images/02-12.png?width=700px)

![Structure d'une trame Ethernet](../images/02-13.png?width=700px)

La trame Ethernet possède une taille minimale et maximale définies par les normes Ethernet classiques.

Pour une trame Ethernet II standard, la taille du champ allant de l'adresse MAC destination jusqu'au FCS est généralement comprise entre **64 et 1518 octets**.

---

## 2.5 - Adresse MAC

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

![Adresse MAC](../images/02-17.png?width=700px)

---

### Unicast, broadcast et multicast

Une adresse MAC peut être utilisée pour différents types de communication.

#### Unicast

Une trame **unicast** est destinée à une interface précise.

```text
Ordinateur A ─────────► Ordinateur B
```

#### Broadcast

Une trame **broadcast** est destinée à **toutes les interfaces du réseau local**.

L'adresse MAC de broadcast est :

```text
FF:FF:FF:FF:FF:FF
```

![Exemple de trame avec adresse Unicast](../images/02-21.png?width=600px)

![Exemple de trame avec adresse MAC broadcast](../images/02-22.png?width=600px)

Le **multicast** permet quant à lui d'envoyer une trame à un groupe précis d'interfaces.

---

## 2.6 - ARP

### Rappel

Le protocole **ARP** (*Address Resolution Protocol*) permet d'associer une **adresse IPv4** à une **adresse MAC** sur un réseau local Ethernet.

Cette résolution est nécessaire lorsqu'un équipement connaît l'adresse IP de destination, mais doit déterminer quelle adresse MAC utiliser pour construire la trame Ethernet.

> Le fonctionnement détaillé d'ARP a déjà été présenté précédemment. Ici, on retient simplement son rôle dans l'encapsulation Ethernet.

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

![Fonctionnement d'ARP](../images/02-24.png?width=600px)

---

### Communication avec un réseau distant

Lorsque la destination se trouve sur **un autre réseau IP**, l'ordinateur n'utilise pas la MAC de l'hôte distant.

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

> **À retenir :** ARP résout une adresse **IPv4 en adresse MAC sur le réseau local**. Il ne permet pas de découvrir directement la MAC d'un ordinateur situé sur Internet.

---

## À retenir

### Couche physique — L1

- Transmet des **bits**.
- Définit les caractéristiques du support et du signal.
- Utilise notamment le cuivre, la fibre et les ondes radio.
- Exemples de matériel : **répéteur, concentrateur, carte réseau**.

### Couche liaison de données — L2

- Encapsule les paquets dans des **trames**.
- Utilise les **adresses MAC**.
- Contrôle l'accès au support.
- Détecte certaines erreurs de transmission.
- Exemple principal : **Ethernet (IEEE 802.3)**.
- Exemple de matériel : **commutateur (switch)**.

### Relation entre les deux

```text
Couche 2 — Liaison de données
        │
        │  Trames + adresses MAC
        ▼
Couche 1 — Physique
        │
        │  Bits + signaux
        ▼
     Support
(cuivre / fibre / radio)
```
