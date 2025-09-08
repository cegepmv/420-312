+++
pre = '<b>4. </b>'
title = 'Exercices'
draft = false
weight = "144"
+++
----------------
### Exercice 1
*Alors que votre VM est éteinte, mettez le type de son adapteur réseau à NAT. Ensuite démarrez-la.*

1. Remplissez le tableau ci-dessous avec les paramètres de votre machine :

| Adresse IP | Masque de sous-réseau | Passerelle par défaut | DNS1 | DNS2 |
| ---------- | --------------------- | --------------------- | ---- | ---- |
|            |                       |                       |      |      |

2. Est-ce que votre machine a une adresse IP statique ou dynamique (obtenue automatiquement)?
3. En utilisant la commande `nmcli`, donnez une adresse IP statique à votre machine en utilisant les paramètres que vous avez inscrit dans le tableau. Testez que tout fonctionne et que vous avez Internet après avoir redémarré le réseau.
4. Revenez en arrière de façon que votre ordinateur obtienne son adresse IP automatiquement.
5. Testez que tout fonctionne.

### Exercice 2
1. Créez une VM avec une interface en mode *LAN Segment* puis configurez son interface comme suit : 
    + Adresse IP : `192.168.20.10/24`
    + Passerelle par défaut : `192.168.20.1`
    + Serveurs DNS : `1.1.1.1, 1.0.0.1`

2. Créez une deuxième VM avec une interface en mode *LAN Segment*, puis configurez son interface avec une IP située sur le même réseau (`192.168.20.x/24`), différente de la première VM. 
3. Testez la connexion entre les 2 VMs à l'aide de la commande `ping`, puis avec `SSH`. 

2. Configurez le fichier `hosts` des deux VMs pour que les deux adresses répondent aux `ping` avec un nom différent.
