+++
pre = "<b>2. </b>"
title = "Commandes utiles"
weight = "320"
draft = false
+++
-------------------

#### Ajouter une route par défaut (gateway)
```bash
$ route add default gw <ip_passerelle>
```

#### Supprimer une route par défaut (gateway)
```bash
$ route del default gw < ip_passerelle>
```

#### Ajouter une route statique
```bash
$ route add -net <réseau> netmask <masque_décimal> gw <passerelle> [dev <interface_de_sortie>]
```
Ou
```bash
$ ip route add <réseau/masque> via <passerelle>
```

#### Interfaces (`nmcli`) :

##### Liste d’interfaces :
```bash
$ nmcli device
```

##### Détails actuels des interfaces
```bash
$ nmcli device show
```

##### Activer/désactiver une interface
```bash
$ nmcli device connect <interface>
$ nmcli device disconnect <interface>
```

#### Profils (*connections*)

##### Liste de profils
```bash
$ nmcli connection
```

##### Liste pour une interface
```bash
$ nmcli -f CONNECTIONS device show <interface>
```

##### Détails d'une connection
```bash
$ nmcli connection show id <profil>
```

##### Activer/désactiver
```bash
$ nmcli connection up <profil>
# ou nmcli con up <profil>
$ nmcli connection down <profil>
# ou nmcli con down <profil>
```

##### Ajout d'une IP statique :
```bash
$ nmcli con mod <profil> ipv4.address <ip/cidr>
$ nmcli con mod <profil> ipv4.method manual
$ nmcli con down <profil>
$ nmcli con up <profil>
```

##### Ajout d'une route statique  :
```bash
$ nmcli con mod <profil> +ipv4.routes <réseau de destination> <passerelle>
```

##### Ajout d'une passerelle par défaut :
```bash
$ nmcli con mod <profil> ipv4.gateway <ip>
```

##### Ajout d'adresses de serveurs dns :
```bash
$ nmcli con mod <profil> ipv4.dns <ip1,ip2>
```

