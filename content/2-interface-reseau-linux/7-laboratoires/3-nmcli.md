+++
title = '3- Configuration avec nmcli'
draft = false
weight = "273"
+++
---------------------

Ce laboratoire est réalisé sur VM 1.

## Objectifs

Vous allez apprendre à utiliser nmcli pour :

+ identifier les devices ;
+ identifier les connections ;
+ créer une connection ;
+ configurer une adresse IP statique ;
+ configurer une passerelle ;
+ configurer un DNS ;
+ revenir à DHCP.

## 1 — Device vs. connection

Exécutez :
```bash
nmcli device
```
Puis :
```bash
nmcli connection show
```
Comparez les résultats.

### Questions
+ Quel est le nom du device Ethernet ?
+ Quelle connection lui est associée ?
+ Un device peut-il exister sans connection ?
+ Quelle est la différence entre un device et une connection ?

## 2 — Créer une connection

Si nécessaire, créez une connection :
```bash
sudo nmcli connection add \
  con-name lab-connection \
  ifname <interface> \
  type ethernet
```
Vérifiez :
```bash
nmcli connection show
```
Puis :
```bash
nmcli device
```

## 3 — Configurer une adresse statique

Utilisez les paramètres suivants :

+ **Adresse IP :** `192.168.20.10/24`
+ **Passerelle :** `192.168.20.1`
+ **DNS        :** `1.1.1.1`

Configurez la connection :
```bash
sudo nmcli con mod lab-connection \
  ipv4.addresses 192.168.20.10/24
sudo nmcli con mod lab-connection \
  ipv4.gateway 192.168.20.1
sudo nmcli con mod lab-connection \
  ipv4.dns "1.1.1.1 1.0.0.1"
sudo nmcli con mod lab-connection \
  ipv4.method manual
```
Activez-la :
```bash
sudo nmcli con up lab-connection
Partie 4 — Vérifier
```
Utilisez :
```bash
ip a
ip route
nmcli device
nmcli connection show lab-connection
```
Testez :
```bash
ping -c 3 192.168.20.1
```
Puis testez l'accès Internet :
```bash
ping -c 3 1.1.1.1
```
et :
```bash
ping -c 3 google.com
```

## 5 — Revenir à DHCP

Configurez **NetworkManager** pour utiliser **DHCP** :
```bash
sudo nmcli con mod lab-connection ipv4.method auto
```
Supprimez les paramètres statiques :
```bash
sudo nmcli con mod lab-connection ipv4.addresses ""
sudo nmcli con mod lab-connection ipv4.gateway ""
sudo nmcli con mod lab-connection ipv4.dns ""
```
Réactivez la connection :
```bash
sudo nmcli con down lab-connection
sudo nmcli con up lab-connection
```
Vérifiez :
```bash
ip a
ip route
```

### Question

Quelles informations ont maintenant été obtenues automatiquement ?