+++
pre = '<b>6. </b>'
title = "ARP"
weight = "360"
+++
-------------


Le protocole **ARP** (*Address Resolution Protocol*) permet d'associer une **adresse IPv4** à une **adresse MAC** sur un réseau local Ethernet.

Cette résolution est nécessaire lorsqu'un équipement connaît l'adresse IP de destination, mais doit déterminer quelle adresse MAC utiliser pour construire la trame Ethernet.

{{%notice style="info" title="Rappel"%}}
Le fonctionnement d'ARP a déjà été présenté précédemment. Ici, on retient simplement son rôle dans l'encapsulation Ethernet.
{{%/notice%}}

### Exemple

Supposons que :

```text
Ordinateur A
IP  : 192.168.1.10
MAC : AA:AA:AA:AA:AA:AA

Ordinateur B
IP  : 192.168.1.20
MAC : BB:BB:BB:BB:BB:BB
```

Si A veut communiquer avec B :

1. A détermine que `192.168.1.20` se trouve sur son réseau local.
2. A utilise ARP pour connaître la MAC associée à `192.168.1.20`.
3. A construit une trame Ethernet avec :
   - MAC source : `AA:AA:AA:AA:AA:AA`
   - MAC destination : `BB:BB:BB:BB:BB:BB`
4. La trame est transmise sur le réseau.

## Communication avec un réseau distant

Lorsque la destination se trouve sur **un autre réseau**, l'ordinateur n'utilise pas la MAC de l'hôte distant.

Il envoie la trame à la **passerelle par défaut**.

ARP permet alors de déterminer la MAC de l'interface locale du routeur.

```text
Ordinateur A
192.168.1.10
      │
      │ Trame Ethernet
      │ MAC destination = MAC du routeur
      ▼
Routeur
192.168.1.1
      │
      │ Réseau IP suivant
      ▼
Réseau distant
```


{{%notice style="tip" title="À retenir"%}}
ARP résout une adresse **IPv4 en adresse MAC sur le réseau local**. Il ne permet pas de découvrir directement la MAC d'un ordinateur situé sur Internet.
{{%/notice%}}
