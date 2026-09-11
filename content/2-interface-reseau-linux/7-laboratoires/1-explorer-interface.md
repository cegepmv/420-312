+++
title = '1- Modes réseau VMWare'
draft = false
weight = "271"
+++
---------------------

## Objectifs

+ identifier les paramètres réseau d'une machine Linux ;
+ distinguer une adresse MAC d'une adresse IP ;
+ identifier une passerelle par défaut ;
+ observer la table de routage ;
+ tester la connectivité réseau ;
+ comprendre la différence entre les modes **NAT**, **Bridged**, **Host-Only** et **LAN Segment** ;
+ faire le lien entre un adaptateur réseau VMware et une interface réseau Linux.

### 1 — Observer le réseau de l'ordinateur hôte

Avant de modifier les VMs, observez la configuration réseau de votre ordinateur physique.

Sur Windows, ouvrez un terminal et exécutez :
```bash
ipconfig /all
```
Repérez notamment les interfaces suivantes :

+ la carte **Ethernet** ;
+ **VMware Network Adapter VMnet1** ;
+ **VMware Network Adapter VMnet8**.

Complétez le tableau :

|Interface|	Adresse IPv4|	Masque|	Passerelle|	Adresse MAC|
|---------|---------|---------|---------|---------|
|**Ethernet**|              |       |           |            |	
|**VMnet1**	|              |       |           |            |
|**VMnet8**	|              |       |           |            |
	
	
	

##### Questions
+ Quelle interface correspond au réseau physique utilisé par votre ordinateur ?
+ Quelle interface correspond généralement au réseau **Host-Only** ?
+ Quelle interface correspond généralement au réseau **NAT** ?
+ Pourquoi VMware crée-t-il des interfaces réseau supplémentaires sur l'ordinateur hôte ?

{{%notice style="info" title="À retenir"%}}

VMware utilise notamment des réseaux virtuels comme `VMnet1` et `VMnet8` pour fournir des fonctionnalités de type **Host-Only** et **NAT**. 

C'est les interfaces que la machine hôte utilise pour communiquer et se mettre en réseau avec les machines virtuelles : 
+ une machine virtuelle avec un adaptateur réseau en mode **NAT** communiquera avec la machine hôte via son interface `VMnet8`
+ une machine virtuelle avec un adaptateur réseau en mode **Host-Only** communiquera avec la machine hôte via son interface `VMnet1`
{{%/notice%}}

### 2 — Mode NAT

Éteignez la machine virtuelle. Dans *VMware*, configurez son adaptateur réseau en mode **NAT**. Démarrez ensuite la VM.


1. Identifiez l'interface de type **Ethernet** et complétez :

|Paramètre|	Valeur|
|---------|-------|
|**Nom de l'interface**|   |	
|**Adresse MAC**	|     |
|**Adresse IPv4**	|     |
|**État**	|      |

2. Quelle est l'adresse IP de la passerelle par défaut ?
3. Testez la connexion Internet en essayant d'abord avec une IP. Ensuite, testez la résolution DNS.
4. **Testez la communication avec l'hôte :** depuis l'ordinateur physique, essayez de ping la VM. Le résultat est-il celui auquel vous vous attendiez ?
5. **Testez la communication avec une autre VM :** Récupérez l'adresse IP d'une VM d'un collègue configurée elle aussi en **NAT**. Le résultat est-il concluant ?

##### Questions
+ La VM a-t-elle obtenu son adresse IP automatiquement ?
+ Sur quel réseau se trouve son adresse IP ?
+ Quelle est sa passerelle ?
+ La VM peut-elle accéder à Internet ?
+ La VM peut-elle être jointe directement depuis le réseau physique ?
+ Deux VMs connectées au même réseau NAT peuvent-elles communiquer entre elles ?

### 3 — Mode Host-Only

Éteignez la VM et modifiez son adaptateur réseau en *Host-Only*. Redémarrez ensuite la VM.

Répétez les cinq observations précédentes.

1. Observer l'interface

Relevez :
|Paramètre|	Valeur|
|---------|-------|
|**Nom de l'interface**|   |	
|**Adresse MAC**	|     |
|**Adresse IPv4**	|     |
|**État**	|      |


2. Observer la route, quelle est la passerelle par défaut ?
3. Testez Internet avec une adresse IP puis avec un nom de domaine
4. Testez la communication avec l'hôte en faisant un ping de windows à la VM linux. Le ping fonctionne-t-il ?
5. Tester la communication avec un collègue : Demandez à un collègue l'adresse IP de sa VM également configurée en **Host-Only** puis essayez de ping sa VM. Le ping fonctionne-t-il ?


<!-- 
Questions

Comparez vos observations avec le mode NAT.


	NAT	Host-Only
Adresse IP obtenue automatiquement ?	
	

Accès Internet	
	

Hôte → VM	
	

VM → autre VM	
	

Réseau physique accessible	
	 -->

### 4 — Mode Bridged

Éteignez la VM et configurez son adaptateur à **Bridged**, redémarrez la VM puis répétez les cinq tests.


##### Questions

Comparez l'adresse IP de votre VM avec celle de votre ordinateur physique (interface **Ethernet**).

+ Sont-elles sur le même réseau IP ?
+ Ont-elles la même passerelle ?
+ Pourquoi une VM en **Bridged** peut-elle être accessible par les autres machines du réseau ?
+ Quelle différence fondamentale observez-vous entre **Bridged** et **NAT** ?

### 5 — Mode LAN Segment

Le mode LAN Segment permet de créer un réseau virtuel complètement isolé.

Éteignez la VM. Dans VMware, choisissez *LAN Segment* puis créez un nouveau segment appelé : **LAB-LAN**

Connectez l'adaptateur réseau de la VM à ce segment puis redémarrez la VM.

1. Observer l'interface

Relevez :
|Paramètre|	Valeur|
|---------|-------|
|**Nom de l'interface**|   |	
|**Adresse MAC**	|     |
|**Adresse IPv4**	|     |
|**État**	|

2. Observez attentivement la présence ou l'absence d'une route par défaut.
3. Testez Internet, cela fonctionne-t-il ?
4. Tester l'hôte : depuis windows faites un ping à votre VM. Cela fonctionne-t-il?
5. Connectez une deuxième VM au même *LAN Segment*. Configurez temporairement les deux machines sur le réseau :
  + **VM1 :** `192.168.20.10/24`
  + **VM2 :** `192.168.20.20/24`

Depuis VM1 :
```bash
ping 192.168.20.20
```
Puis depuis VM2 :
```bash
ping 192.168.20.10
```

##### Questions
+ Les deux VMs peuvent-elles communiquer ?
+ Ont-elles besoin d'une passerelle pour communiquer ?
+ Pourquoi n'ont-elles pas accès à Internet ?
+ Pourquoi l'ordinateur hôte ne peut-il pas directement communiquer avec elles ?
+ Que faudrait-il ajouter pour permettre aux machines du *LAN Segment* d'accéder à Internet ?


<!-- Synthèse du laboratoire 1

Complétez le tableau suivant :

Caractéristique	NAT	Host-Only	Bridged	LAN Segment
Accès Internet	

Hôte ↔ VM	
VM ↔ VM	
Accessible depuis le réseau physique	
DHCP VMware disponible	
Réseau isolé	
Utilisation typique	
	 -->
	
	

### Question de réflexion

Expliquez avec vos propres mots la différence entre les quatre modes.

Votre réponse doit notamment expliquer à quel réseau la carte réseau virtuelle est connectée dans chacun des cas.