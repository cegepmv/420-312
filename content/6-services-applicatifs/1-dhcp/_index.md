+++
pre = '<b>1. </b>'
title = "DHCP"
weight = "510"
+++
-------------------

**DHCP** (*Dynamic Host Configuration Protocol*) est un service réseau qui automatise l’attribution des paramètres IP aux appareils. Sans lui, chaque poste devrait être configuré manuellement (de façon *statique*, comme vu jusqu’à présent) : une tâche longue, répétitive et source d’erreurs.

Avec DHCP, un ordinateur qui se connecte au réseau peut "demander" une configuration, et un serveur lui "répond" avec tout ce dont il a besoin pour communiquer :
- une adresse IP valide,
- un masque de sous-réseau,
- une passerelle par défaut,
- les serveurs DNS,
- et de nombreuses autres options.

Ce mécanisme permet non seulement de gagner du temps, mais aussi de garder un réseau cohérent, organisé et facile à administrer, que ce soit dans un petit laboratoire informatique ou dans une grande entreprise.

{{%notice style="info" title="Note"%}}
- DHCP existe pour IPv4 et IPv6, avec des fonctionnalités légèrement différentes.
- Il a d’abord été conçu comme extension du protocole **BootP**, qui permettait à une station de travail sans disque dur de démarrer par le réseau via **TFTP**.
- Il est désormais indispensable dans la majorité des entreprises, et essentiel pour les fournisseurs d’accès Internet.
- Le DHCP permet une gestion optimisée des adresses IP, en utilisant moins d’adresses que de clients (voir la section *Limitation IPv4* du cours).
{{%/notice%}}


## Le protocole

![DORA](../images/05-01-01.png?width=300px)

- **DHCP** utilise les ports `UDP 67` (serveur) et `UDP 68` (client).
- `UDP 68` n’est utilisé par le client que pendant la négociation initiale.

Pour fournir une adresse IP à un hôte, quatre messages sont échangés :

1. **DHCPDISCOVER** — client → serveur  
2. **DHCPOFFER** — serveur → client  
3. **DHCPREQUEST** — client → serveur  
4. **DHCPACK** — serveur → client  

{{%notice style="tip" title=" "%}}
Cet échange est communément appelé **DORA** ( **D**iscover-**O**ffer-**R**equest-**A**CK).
{{% /notice%}}
### DHCPDISCOVER
![DHCPDISCOVER](../images/05-01-02.png?width=550px)


- Envoyé par le client.  
- Adresse IP source : `0.0.0.0` (le client n’a pas d’adresse IP).  
- Adresse IP de destination : `255.255.255.255` (le serveur est inconnu).  
- Identifié comme un message `DHCPDISCOVER`.  
- Le paquet contient l’adresse MAC du client.  
- Transmission en **broadcast**.

### DHCPOFFER
![DHCPOFFER](../images/05-01-03.png?width=550px)

- Envoyé par le serveur DHCP.  
- Adresse IP source : IP du serveur.  
- Adresse IP de destination : `255.255.255.255`.  
- Identifié comme `DHCPOFFER`.  
- Contient l’adresse IP proposée au client, son masque, la passerelle, les DNS et autres options.  
- Contient aussi l’adresse MAC du client.  
- Transmission en **unicast** destiné spécifiquement au client.

### DHCPREQUEST
![DHCPREQUEST](../images/05-01-04.png?width=550px)

- Envoyé par le client.  
- Adresse IP source : `0.0.0.0`.  
- Adresse IP de destination : `255.255.255.255` (**broadcast**), car plusieurs serveurs ont peut-être répondu.  
- Identifié comme `DHCPREQUEST`.  
- Le client y indique l’offre (le serveur) qu’il accepte.

### DHCPACK
![DHCPACK](../images/05-01-05.png?width=550px)

- Envoyé par le serveur choisi.  
- Adresse IP source : IP du serveur.  
- Adresse IP de destination : `255.255.255.255`.  
- Identifié comme `DHCPACK`.  
- Le client peut maintenant utiliser l’adresse IP.  
- Contient les options DHCP applicables.  
- Contient l’adresse MAC du client.


## Les baux d’adresses

Une adresse IP est attribuée à un client pour une **durée limitée**, appelée *bail*.

Si le client ne renouvelle pas son bail (machine éteinte, partition du réseau…), l’adresse redevient disponible après un délai supplémentaire appelé *période de grâce*.

{{%notice style="tip" title="Notes"%}}
- Un bail trop long immobilise inutilement des adresses.
- Un bail trop court génère du trafic réseau et de la charge sur les serveurs DHCP.
- Les durées courantes vont de **24 h à 7 jours**, mais elles sont configurables.
{{%/notice%}}

### Renouvellement du bail

Un client essaie de garder son adresse IP. Le processus :

- À **50 %** du bail : premier renouvellement (*T1*).  
- À **75 %** : deuxième tentative (*T2*).  
- À **87,5 %** : troisième tentative.  
- Si aucune réponse : le client relance un processus complet **DORA**.

Le renouvellement consiste simplement à envoyer un **DHCPREQUEST** ; le serveur répond par **DHCPACK** (succès) ou **DHCPNACK** (refus).

<!-- ## Commandes côté client

Renouveler une adresse IP :

```bash
dhclient [interface]
```

Libérer l’adresse IP :

```bash
dhclient -r [interface]
```

--- -->

## Étendue DHCP (*scope*)

Une **étendue DHCP** est une plage d’adresses IP qu’un serveur peut distribuer, accompagnée de ses options.

Elle contient :
- une plage d’adresses à distribuer,
- un masque,
- la passerelle,
- les DNS,
- d’autres paramètres (WINS, NTP, nom de domaine, etc.).

Exemple de plage : `10.2.2.1` à `10.2.2.35`.

{{%notice style="note" title="Important"%}}
- L’adresse IP du serveur DHCP doit être **statique**.  
- Elle ne doit pas appartenir à la plage distribuée.  
- Le serveur doit se trouver **dans le même réseau**, car DHCP utilise `255.255.255.255` qui ne traverse pas les routeurs.
{{%/notice%}}


### Options courantes

| Option | Désignation |
|--------|-------------|
| `routers` | Routeur(s) par défaut du réseau |
| `range` | Plage d’adresses distribuées |
| `domain-name-servers` | Liste des DNS |
| `domain-name` | Nom de domaine utilisé par les clients |
| `broadcast-address` | Adresse de diffusion du réseau |
| `default-lease-time` | Durée par défaut du bail (secondes) |
| `max-lease-time` | Durée maximale du bail |

## Les exclusions

Une **plage d’exclusion** permet d’indiquer quelles adresses ne doivent pas être distribuées par DHCP.

Exemples :
- Adresses réservées pour des serveurs
- Adresses utilisées statiquement dans le réseau
- Plages réservées pour un autre usage

Avec ISC DHCP classique, on peut définir explicitement des plages d’exclusion.  
Avec **DHCP Kea**, on crée plutôt plusieurs plages distinctes qui évitent les adresses à exclure.

Exemple : exclure `192.168.10.100` à `192.168.10.150` :

```json
{
"Dhcp4": {
    "subnet4": [
        {
            "subnet": "192.168.10.0/24",
            "pools": [
                {"pool": "192.168.10.1 - 192.168.10.99"},
                {"pool": "192.168.10.151 - 192.168.10.254"}
            ],
        }
    ]
}
}
```

---

## Réservations d’adresses IP

Une **réservation** permet de garantir qu’un appareil obtiendra toujours la même adresse IP *via DHCP*.

Utile pour :
- des serveurs,
- des imprimantes,
- des appareils nécessitant des règles particulières,
- des portables qui circulent entre plusieurs réseaux.

Basée sur l’adresse **MAC** du client.

Éléments requis :
- un nom de réservation,
- l’adresse MAC,
- l’adresse IP réservée.
- (optionnel) sont nom d'hôte (*hostname*)

Exemple :

```json
{
  "subnet4": [
    {
      "reservations": [
        {
          "hw-address": "aa:bb:cc:dd:ee:ff",
          "ip-address": "192.0.10.150",
          "hostname": "server.example.org"
        }
      ],
    }
  ]
}
```