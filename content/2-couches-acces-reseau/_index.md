+++
pre = '<b>2. </b>'
title = "Couche(s) d'accès réseau"
weight = "200"
+++
-----------------

La couche d'accès réseau du modèle *TCP/IP* est divisée en deux couches dans le *modèle OSI* : **La couche physique** (L1) et **la couche liaison de données** (L2). Dans ce chapitre, nous allons explorer leurs rôles, le matériel, les normes et protocoles associés à ces couches.

## Couche physique
### Rôle

+ Transmission physique des données entre deux équipements réseaux. 
+ Tout ce qui a trait au bas-niveau, au matériel : la transmission des bits, leur encodage, la synchronisation entre deux cartes réseau, etc.
+ Définit les standards des câbles réseaux, des fils de cuivre, du WIFI, de la fibre optique, ou de tout autre support électronique de transmission. 

![Couches 1 et 2](./images/02-2.png?width=600px)

### Normes

|Organisme de Normalisation|Normes réseau|
|---|---|
|ISO|ISO 8877: adoption officielle des connecteurs RJ (RJ-11 et RJ-45, notamment)<br>ISO11801: norme de câblage réseau similaire à la normeEIA/TIA 568|
|EIA/TIA|TIA-568-C: normes de câblage de télécommunication utilisées par presque tous les réseaux de voix, de vidéo et de données<br>TIA-569-B: normes des immeubles commerciaux pour les voies d'accès et les espaces de télécommunications<br>TIA-598-C: code couleur de la fibre optique<br>TIA-942: norme d'infrastructure de télécommunications pour les data centers|
|ANSI|568-C: brochage RJ-45 – Développée en collaboration avec les organismes EIA et TIA|
|UIT-T|G.992:ADSL|
|IEEE|802.3: Ethernet<br>802.11: LAN sans fil (WLAN) et maillé (certification Wi-Fi)<br>802.15: Bluetooth|


## Couche liaison de données

### Rôle
La couche liaison de données assure deux services de base :

+ Accepter les paquets de couche 3 et les encapsuler dans des PDU appelées **des trames**.

+ **Contrôler l'accès au support** et **détecter les erreurs**.

##### Concepts

![Couches 1 et 2](./images/02-5.png?width=700px)
- **Bande passante:** Capacité d'un support à transporter des données. La bande passante numérique mesure la quantité de données pouvant circuler d'un emplacement à un autre pendant une période donnée.

- **Débit:** Mesure du transfert de bits sur le support pendant une période donnée. De nombreux facteurs influencent le débit. Notamment: la quantité de trafic, le type de trafic, ou la latence créée par le nombre de périphériques réseau rencontrés entre la source et la destination.

<!-- ![Couches 1 et 2](../images/02-7.png?width=700px) -->