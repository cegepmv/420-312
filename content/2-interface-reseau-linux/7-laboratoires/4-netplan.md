+++
title = '4- Configuration avec Netplan'
draft = false
weight = "274"
+++
---------------------

Ce laboratoire est réalisé sur VM 2.

## Objectifs

Vous allez apprendre à :

+ trouver une configuration Netplan ;
+ comprendre sa structure ;
+ configurer une adresse IP statique ;
+ configurer une route par défaut ;
+ configurer des serveurs DNS ;
+ revenir à DHCP.

## 1 — Trouver la configuration

Listez les fichiers :
```bash
ls /etc/netplan/
```
Affichez le fichier de configuration :
```bash
cat /etc/netplan/<fichier>.yaml
```
Identifiez le nom de l'interface Ethernet.

## 2 — Configuration statique

Configurez l'interface avec :

+ **Adresse IP :** `192.168.20.20/24`
+ **Passerelle :** `192.168.20.1`
+ **DNS        :** `1.1.1.1`

Utilisez une configuration de ce type :
```yaml
network:
  version: 2

  ethernets:
    <interface>:
      addresses:
        - 192.168.20.20/24

      routes:
        - to: default
          via: 192.168.20.1

      nameservers:
        addresses:
          - 1.1.1.1
          - 1.0.0.1
```

## 3 — Tester la configuration

Avant d'appliquer définitivement la configuration :
```bash
sudo netplan try
```
Si tout fonctionne, confirmez la configuration.

Vous pouvez également appliquer directement :
```bash
sudo netplan apply
```
Vérifiez :
```bash
ip a
ip route
```
Testez la passerelle :
```bash
ping -c 3 192.168.20.1
```

## 4 — Tester le DNS

Testez :
```bash
ping -c 3 google.com
```
Si la résolution fonctionne, vérifiez également :
```bash
cat /etc/resolv.conf
```

### Question

Pourquoi le contenu de `/etc/resolv.conf` peut-il être différent de ce que vous avez directement écrit dans votre fichier Netplan ?

## 5 — Revenir à DHCP

Modifiez la configuration :
```bash
network:
  version: 2

  ethernets:
    <interface>:
      dhcp4: true
```
Appliquez :
```bash
sudo netplan apply
```
Vérifiez :
```bash
ip a
ip route
```