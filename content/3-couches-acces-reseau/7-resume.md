+++
title = "Résumé"
weight = "370"
draft=true
+++
-------------


### Couche physique — L1

- Transmet des **bits**.
- Définit les caractéristiques du support et du signal.
- Utilise notamment le cuivre, la fibre et les ondes radio.
- Exemples de matériel : **répéteur, concentrateur, carte réseau**.

### Couche liaison de données — L2

- Encapsule les paquets dans des **trames**.
- Utilise les **adresses MAC**.
- Contrôle l'accès au support.
- Détecte certaines erreurs de transmission.
- Exemple principal : **Ethernet (IEEE 802.3)**.
- Exemple de matériel : **commutateur (switch)**.

### Relation entre les deux

```text
Couche 2 — Liaison de données
        │
        │  Trames + adresses MAC
        ▼
Couche 1 — Physique
        │
        │  Bits + signaux
        ▼
     Support
(cuivre / fibre / radio)
```
