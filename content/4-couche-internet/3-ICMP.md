+++
pre = "<b>3. </b>"
title = "ICMP"
weight = "430"
draft = true
+++

-------------

**ICMP (*Internet Control Message Protocol*)** est un protocole utilisé avec IP pour échanger des messages de contrôle, d'erreur et de diagnostic.

ICMP ne sert pas à transporter directement les données d'une application comme HTTP ou SSH.


## ping

La commande `ping` utilise notamment des messages **ICMP Echo Request** et **ICMP Echo Reply**.

```bash
ping 192.168.1.1
```

Échange simplifié :

```text
Hôte A                         Hôte B
  │                              │
  │── ICMP Echo Request ────────►│
  │                              │
  │◄── ICMP Echo Reply ──────────│
  │                              │
```

`ping` permet notamment de vérifier :

- si une destination répond;
- si le chemin réseau semble fonctionner;
- le temps de réponse;
- d'éventuelles pertes de paquets.

{{%notice style="note" title="Remarque"%}}
L'absence de réponse à `ping` ne signifie pas nécessairement que la machine est hors ligne. Un pare-feu ou une politique réseau peut bloquer les messages ICMP.
{{%/notice%}}


## traceroute

`traceroute` permet d'observer les différents routeurs traversés pour atteindre une destination.

Sous Linux :

```bash
traceroute 8.8.8.8
```

Sous Windows :

```powershell
tracert 8.8.8.8
```

Cette commande est utile pour diagnostiquer les problèmes de routage.


{{%notice style="info" title="Note"%}}
Selon l'implémentation et les options utilisées, `traceroute` peut utiliser différents types de paquets, notamment UDP ou ICMP.
{{%/notice%}}
