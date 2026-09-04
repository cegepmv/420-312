+++
title = "VMWare"
weight = "331"
draft = false
+++
-------------------

Dans cet atelier, nous allons configurer des machines virtuelles linux, leur attribuer des adresses IP statiques et configurer des règles de routage pour qu'elles puissent communiquer entre elles sans être dans le même réseau. Nous allons mettre en place, à l’aide de *VMWare* et de quelques machines virtuelles, la topologie suivante :

![Topologie routage VMWare](../../../images/34-01.png)

Cette topologie est composé de : 
+ **2 Réseaux**
    + `192.168.10.0/24`
    + `192.168.20.0/24`
+ **3 Stations clients**
    + Une VM *Rocky/Alma Linux* d'adresse IP `192.168.10.2/24` (réseau `192.168.10.0/24`) 
    + Une VM *Rocky/Alma Linux* d'adresse IP `192.168.20.1/24` (réseau `192.168.20.0/24`) 
    + Une VM *Windows 10* d'adresse IP `192.168.20.2/24` (réseau `192.168.20.0/24`) 
+ **1 routeur Linux** avec deux interfaces, chacune dans un réseau. 
    + Son interface située dans le réseau `192.168.10.0/24` a l'adresse IP `192.168.10.1/24` (première adresse hôte disponible du réseau). 
    + Son interface située dans le réseau `192.168.20.0/24` a l'adresse IP `192.168.20.1/24` (première adresse hôte disponible du réseau).

### Étapes de réalisation

#### Étape 1 - Configuration des VMs 

1. Créer les machines virtuelles 
    + Ouvrez la machine virtuelle `alma-linux.ova` ou `rocky-linux.ova` et créer 3 machines virtuelles (en faisant des clones). Nommez-les :
        + `linux-routeur`
        + `linux-station01`
        + `linux-station02`
    + Ouvrez la machine virtuelle win10.ova. Nommez-là `win10-station03`

2. Ajoutez les adaptateurs réseau
    + Dans les paramètres de chaque machine virtuelle sur *VMWare*, ajoutez les adaptateurs réseau nécessaires. Configurez les interfaces réseau en mode “LAN Segment” et créez-en 2 (un pour chaque réseau). Appelez le premier LAN Segment `192.168.10.0/24` et le deuxième `192.168.20.0/24`.

![Network Adapters](../../../images/34-02.png)

+ Configurez  : 

    + 1 Adaptateur réseau pour *linux-station01* pour qu’il appartienne au *LAN Segment* `192.168.10.0/24` :

    ![Network Adapter linux-station01](../../../images/34-03.png)
    
    + 1 Adaptateur réseau pour *linux-station02* pour qu’il appartienne au *LAN Segment* `192.168.20.0/24` :
    
    ![Network Adapter linux-station02](../../../images/34-04.png)
    
    + 1 Adaptateur réseau pour *win10-station03* pour qu’il appartienne au *LAN Segment* `192.168.20.0/24` :
    
    + 2 Adaptateurs réseau pour le routeur *linux-routeur* : le premier dans le *LAN Segment* `192.168.10.0/24`, le deuxième dans `192.168.20.0/24` : 

    ![Network Adapter 1 linux-routeur](../../../images/34-05.png)
    ![Network Adapter 2 linux-routeur](../../../images/34-20.png)


Démarrez les VMs *Rocky/Alma Linux*  et changez leur nom d'hôte. Donnez les nom d’hôte:
+ `routeur` à *linux-routeur* ;
+ `station-01` à *linux-station01* ;
+ `station-02` à *linux-station02* ;

Pour changer le nom d'hôte, vous pouvez utiliser la commande suivante :
```bash
sudo hostnamectl set-hostname <nouveau-nom-hôte>
```
Pour vérifier que le nom d'hôte a bien été modifié, tapez la commande `hostname` ou ouvrez une nouvelle session de terminal (`exit` puis `login` ou fermer puis ouvrir une fenêtre de terminal).

#### Étape 2 - Configuration des interfaces en mode statique

##### Stations Alma/Rocky
+ `station-01`
```bash
sudo nmcli con mod ens160 ipv4.address 192.168.10.2/24
sudo nmcli con mod ens160 ipv4.method manual
sudo nmcli con down ens160
sudo nmcli con up ens160
```

+ `station-02`
```bash
sudo nmcli con mod ens160 ipv4.address 192.168.20.2/24
sudo nmcli con mod ens160 ipv4.method manual
sudo nmcli con down ens160
sudo nmcli con up ens160
```

+ `routeur`
```bash
# Interface dans le réseau 192.168.10.0/24
sudo nmcli con mod ens160 ipv4.address 192.168.10.1/24
sudo nmcli con mod ens160 ipv4.method manual
sudo nmcli con down ens160
sudo nmcli con up ens160

# D'abord déclarer l'interface ens224 dans nmcli
nmcli con add con-name ens224 type ethernet ifname ens224

# Puis la configurer
sudo nmcli con mod ens224 ipv4.address 192.168.20.1/24
sudo nmcli con mod ens224 ipv4.method manual
sudo nmcli con down ens224
sudo nmcli con up ens224
```

Vérifiez que les interfaces ont bien été configurées avec la commande `ip a`. 

Exemple avec `linux-routeur` :

![Configuration IP pour routeur](../../../images/34-21.png)

![Configuration IP pour routeur](../../../images/34-22.png)

##### Station Windows 10 (optionnel)
1. Allez dans le *Panneau de configuration*
2. Sélectionnez *Réseau et internet*, puis *Centre de Réseau et partage*
3. Sur la gauche, sélectionnez *Modifiez les paramètres de la carte*
4. Faites un clique-droit sur `Ethernet0` puis sélectionnez *Propriétés*
5. Sélectionnez *Version 4 du protocole Internet (TCP/IPv4)* puis appuyez sur *Propriétés*
6. Configurez l’adressage IP de la manière suivante : 

![Network Adapter](../../../images/34-23.png)


7. Ensuite, allez dans *Panneau de configuration -> Système et sécurité -> Pare-feu Windows Defender*
8. Sur la gauche, cliquez sur *Activez ou désactivez le pare-feu Windows Defender*
9. Configurez le pare-feu de la manière suivante (désactivez-le) : 

![Network Adapter](../../../images/34-08.png)

#### Étape 3 - Test
Pour vérifier si les stations peuvent communiquer entre elles, faites un ping :
+ de station01 à l’interface du routeur d’adresse IP 192.168.10.1
+ de station01 à l’interface du routeur d’adresse IP 192.168.20.1
+ de station02 à l’interface du routeur d’adresse IP 192.168.10.1
+ de station02 à l’interface du routeur d’adresse IP 192.168.20.1
+ de station03 à l’interface du routeur d’adresse IP 192.168.10.1
+ de station03 à l’interface du routeur d’adresse IP 192.168.20.1
+ de station01 à station02 (et/ou de station02 à station01)
+ de station01 à station03 (et/ou de station03 à station01)
+ de station02 à station03 (et/ou de station03 à station02)

*Quelles sont les stations qui ont réussi à communiquer entre elles? Lesquelles ont échoué? Pourquoi?*

#### Étape 4 - Ajout de règles de routage
Pour que des machines puissent communiquer entre elles même sans être dans le même réseau, il est nécessaire de mettre en place des règles de routage. 

Deux méthodes s'offrent à nous : 
1. Méthode 1 : Configurer des routes statiques ou 
2. Méthode 2 : Configurer des routes par défaut.

##### Méthode 1 - Routes statiques
Une route par réseau par machine à déclarer (soit 3 routes au total)  :
+ Une route sur `station01` indiquant que les paquets à destination du réseau `192.168.20.0/24` doivent passer par la passerelle `192.168.10.1` (l'interface du routeur située dans le même réseau). Pour cela, on peut utiliser la commande `ip route` :
```bash
sudo ip route add 192.168.20.0/24 via 192.168.10.1
```
+ Une route sur la `station02` indiquant que les paquets à destination du réseau `192.168.10.0/24` doivent passer par la passerelle `192.168.20.1` (l'interface du routeur située dans le même réseau). Pour cela, on peut utiliser la commande `ip route` :
```bash
sudo ip route add 192.168.10.0/24 via 192.168.20.1
```
+ Une route sur `station03` indiquant que les paquets à destination du réseau `192.168.10.0/24` doivent passer par la passerelle `192.168.20.1` (l'interface du routeur située dans le même réseau). Pour cela, on peut utiliser la commande `route` sur l’invite de commande *Windows PowerShell* (ouverte en mode administrateur):
```bash
route add 192.168.10.0 mask 255.255.255.0 192.168.20.1
```

![Network Adapter](../../../images/34-09.png)

##### Méthode 2 - Routes par défaut
Effacez les routes statiques (si vous les avez implémentées):

+ Pour les machines *Alma/Rocky Linux* :
```bash
ip route del <adresse réseau> via <passerelle>
```

+ Pour la machine *Win10* :
```powershell
route delete <réseau de destination>
```

Maintenant, configurez les routes par défaut de chaque hôte. La configuration d’une route par défaut peut se faire par l’une des méthodes suivantes sur les machines *Alma/Rocky Linux* :
```bash
sudo ip route add default via <adresse de l'interface du routeur>	
```
ou
```bash
sudo ip route add 0.0.0.0/0 via <adresse de l'interface du routeur>
```
Sur *win10-station03* :
```powershell
route add 0.0.0.0 mask 0.0.0.0 <adresse de l'interface du routeur>
```

![Ajout route statique](../../../images/34-24.png)

*Vérifiez que les routes ont bien été ajoutées à la table de routage de chaque hôte puis testez la communication entre les stations en faisant des `ping`. Êtes-vous capable de faire communiquer des stations situées dans des réseaux différents? À votre avis pourquoi?*

#### Étape 5 - *forwarding* du routeur
Pour que le routeur accepte de faire passer les paquets IP d’une interface à une autre, nous devons activer le *forwarding* avec la commande suivante :
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Pour vérifier que la commande a bien réussi, vérifiez que la commande suivante retourne bien 1 :
```bash
sudo cat /proc/sys/net/ipv4/ip_forward
```

Vérifiez que toutes les stations sont capables de communiquer entre elles en utilisant les mêmes commandes `ping` que précédemment.


#### Étape 6 - Que se passe-t-il si nous redémarrons les stations?
Redémarrez toutes les stations *Rocky/Alma Linux* et *Windows 10*. 

Au redémarrage, essayez de `ping` des stations situées dans des réseaux différents. Vérifiez les tables de routage de chaque station. Que se passe-t-il?

De même vérifiez si le *forwarding* du routeur est toujours actif. Est-ce le cas?

Les règles de routage et le *forwarding* configurés précédemment ne sont pas permanents et ont une durée de vie d’une session. Pour faire en sorte de les configurer de façon permanente, il faut procéder de la manière suivante :

+ Pour `linux-station01` et `linux-station02` :
    
<!-- + **Option 1 :** Créer un script d'initialisation dans le répertoire `/etc/init.d` contenant les commandes effectuées dans les étapes précédentes (statique ou par défaut) puis lancer la commande `chkconfig`:
```bash
sudo nano /etc/rc.d/init.d/routes.sh
sudo chkconfig --add /etc/init.d/routes.sh -->
```bash
# Route statique : 
sudo nmcli con mod <interface> ipv4.routes "<réseau de destination> <passerelle>"
# ou route par défaut :
sudo nmcli con mod <interface> ipv4.gateway <adresse IP du routeur>
```
Exemple pour *linux-station02* :
```bash
sudo nmcli con mod ens160 +ipv4.routes "192.168.10.0/24 192.168.20.1"
```

+ Pour `win10-station03` :

    + **Route statique :** rajoutez l’option `-p` à la commande tapée précédemment:
    ```powershell
    route add -p 192.168.10.0 mask 255.255.255.0 192.168.20.1
    ```

    + **Route par défaut :** 
    ```powershell
    route add -p 0.00.0 mask 0.0.0.0 192.168.20.1
    ```

+ Pour `linux-routeur` :
Ajoutez au fichier `/etc/sysctl.conf` la ligne : `net.ipv4.ip_forward=1`
Ensuite, pour activer les changements : 
```bash
sudo sysctl -p /etc/sysctl.conf
```

Vérifiez que les routes ne disparaissent pas et que le *forwarding* du routeur reste actif même après avoir redémarré toutes les stations, testez la communication entre les stations situées dans des réseaux différents.