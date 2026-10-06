+++
slug = '2-routes-par-defaut'
title = "2- Routes par défaut"
weight = "464"
draft = false
+++
-------------

Dans la partie précédente, chaque machine possédait une route spécifique vers l'autre réseau.

Cette approche devient rapidement difficile à gérer lorsque le réseau possède plusieurs sous-réseaux.

Nous allons donc utiliser une **route par défaut**.

### 1. Routes par défaut

En utilisant `nmcli` sur **CLIENT1** et `Netplan` sur **CLIENT2**: 
1. Supprimez la route spécifique
2. Ajoutez une route par défaut
3. Vérifiez avec `ip route`

{{%notice style="info" title="Tables de routage"%}}
La table de routage de **CLIENT1** devrait maintenant contenir une entrée similaire à :
```bash
default via 10.20.10.1
10.20.10.0/24 dev ens160
```
Tout paquet dont la destination ne correspond pas à une route plus spécifique sera envoyé vers : `10.20.10.1`

La table de routage de **CLIENT2** devrait maintenant contenir une entrée similaire à 
```bash
default via 10.20.20.1
10.20.20.0/24 dev ens160
```
{{%/notice%}}

{{%notice style="tip" title="Comprendre le changement"%}}
La route par défaut signifie :

> « Si aucune route plus précise ne correspond à la destination, envoyer le paquet à cette passerelle. »

Le routeur devient ainsi la **passerelle par défaut** des clients.
{{%/notice%}}

### 2. Tests

Depuis **CLIENT1** :
```bash
ping 10.20.20.10
```
Depuis **CLIENT2** :
```bash
ping 10.20.10.10
```
Puis :
```bash
traceroute 10.20.20.10
```

Observez le chemin emprunté par les paquets.