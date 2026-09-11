+++
pre = '<b>2. </b>'
title = 'NetworkManager et nmcli'
draft = false
weight = "220"
+++
---------------------

De nombreuses distributions Linux utilisent **NetworkManager** pour gérer les connexions réseau.

`nmcli` est l'outil en ligne de commande permettant d'interagir avec **NetworkManager**.

Pour afficher les interfaces :
```bash
nmcli device
```
Exemple :
```bash
DEVICE   TYPE      STATE        CONNECTION
ens160   ethernet  connected    ens160
ens224   ethernet  disconnected  --
lo       loopback  connected     lo
```

Il faut faire attention à une distinction importante :

+ **interface / device :** l'interface réseau réelle, par exemple `ens160` ;
+ **connection :** la configuration que *NetworkManager* applique à cette interface.

Dans l'exemple ci-dessus : 
+ `ens160` possède une connection appelée `ens160` ;
+ `ens224` existe bien comme **device**, mais la colonne **CONNECTION** contient `--` : aucune connection *NetworkManager* ne lui est actuellement associée.

Pour afficher les connexions :
```bash
nmcli connection show # ou nmcli con show
```
Pour afficher une connexion particulière :
```bash
nmcli connection show ens160
```

### Créer une connection pour un device

Si une interface existe mais qu'elle n'est pas encore gérée par une connection *NetworkManager*, il faut d'abord créer cette connection.

La commande générale est :
```bash
sudo nmcli connection add con-name <nom-connection> ifname <device> type ethernet
```

Par exemple :
```bash
sudo nmcli connection add con-name my-conn ifname eth0 type ethernet
```

Cette commande crée une connection appelée `my-conn` et l'associe au device `eth0`.

On peut ensuite vérifier :
```bash
nmcli con show
```
et :
```bash
nmcli device
```
On devrait maintenant voir la connection associée au device.

### Activer une connection
Après avoir créé une connection, on peut l'activer avec :

```bash
sudo nmcli connection up my-conn
```

On peut ensuite vérifier l'état :

```bash
nmcli device
```

ou :

```bash
nmcli connection show
```

### Configurer une adresse IP statique avec nmcli

Supposons que nous souhaitons configurer :

+ **Adresse IP :** `192.168.230.10/24`
+ **Passerelle :** `192.168.230.2`
+ **DNS        :** `8.8.8.8`
+ **Interface  :** `ens160`

On peut modifier la connexion avec :
```bash
nmcli con mod ens160 ipv4.addresses 192.168.230.10/24
nmcli con mod ens160 ipv4.gateway 192.168.230.2
nmcli con mod ens160 ipv4.dns 8.8.8.8
nmcli con mod ens160 ipv4.method manual
```
Puis réactiver la connexion :
```bash
nmcli con down ens160
nmcli con up ens160
```
On peut ensuite vérifier :
```bash
ip a
ip route
```
et :
```bash
nmcli con show ens160
```
### Revenir à DHCP avec nmcli

Pour demander à **NetworkManager** de récupérer automatiquement l'adresse IP :

```bash
nmcli con mod ens160 ipv4.method auto
```

On peut également supprimer les paramètres statiques :
```bash
nmcli con mod ens160 ipv4.addresses ""
nmcli con mod ens160 ipv4.gateway ""
nmcli con mod ens160 ipv4.dns ""
```
Puis :
```bash
nmcli con down ens160
nmcli con up ens160
```
L'interface devrait maintenant obtenir sa configuration automatiquement auprès d'un serveur **DHCP**.