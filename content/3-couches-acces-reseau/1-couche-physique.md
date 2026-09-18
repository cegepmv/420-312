+++
pre = '<b>1. </b>'
title = "Couche physique"
weight = "310"
+++
-----------------

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

<!-- ![Normes Wi-Fi, Bluetooth, Wi-max et satellitaire](../images/02-9.png?width=44rem) -->
