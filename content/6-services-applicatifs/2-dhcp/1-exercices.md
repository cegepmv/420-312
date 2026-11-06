+++
title = "Exercices"
weight = "621"
draft = false
+++
----------------
 
### 1. Compléter un échange DORA
 
Un client dont l'adresse MAC est `00:0c:29:aa:bb:cc` démarre dans le réseau `172.16.10.0/24`. Le serveur DHCP a l'adresse `172.16.10.1` et propose l'adresse `172.16.10.100`. Le serveur envoie ses réponses en broadcast.
 
Complétez le tableau.
 
| Message | MAC destination | IP source | IP destination | Port source | Port destination |
|---------|-----------------|-----------|----------------|-------------|------------------|
| DISCOVER | | | | | |
| OFFER | | | | | |
| REQUEST | | | | | |
| ACK | | | | | |
 
{{% expand title="Solution" %}}
| Message | MAC destination | IP source | IP destination | Port source | Port destination |
|---------|-----------------|-----------|----------------|-------------|------------------|
| DISCOVER | `ff:ff:ff:ff:ff:ff` | `0.0.0.0` | `255.255.255.255` | `68` | `67` |
| OFFER | `ff:ff:ff:ff:ff:ff` | `172.16.10.1` | `255.255.255.255` | `67` | `68` |
| REQUEST | `ff:ff:ff:ff:ff:ff` | `0.0.0.0` | `255.255.255.255` | `68` | `67` |
| ACK | `ff:ff:ff:ff:ff:ff` | `172.16.10.1` | `255.255.255.255` | `67` | `68` |
 
Le REQUEST part encore de `0.0.0.0` : le client n'utilise l'adresse qu'après avoir reçu l'ACK.
{{% /expand %}}
 
### 2. Le bail
 
Un poste obtient un bail de **24 heures** le lundi à 8 h 00. Il reste allumé toute la semaine.
 
1. À quel moment tente-t-il de renouveler son bail pour la première fois? À qui envoie-t-il sa demande, et comment?
2. Le serveur DHCP est en panne depuis dimanche. À quel moment le poste envoie-t-il un REQUEST en broadcast?
3. Le serveur est toujours en panne. À quel moment le poste perd-il son adresse?
4. Combien de messages comporte un renouvellement réussi? Pourquoi pas quatre?
{{% expand title="Solution" %}}
1. À T1, soit 50 % du bail : lundi à **20 h 00**. Il envoie un REQUEST en **unicast** au serveur qui lui a attribué l'adresse.
2. À T2, soit 87,5 % du bail (21 heures) : mardi à **5 h 00**.
3. À l'expiration du bail : mardi à **8 h 00**. Il recommence alors des DISCOVER.
4. Deux messages : REQUEST et ACK. Le client a déjà une adresse et connaît le serveur : il n'a pas besoin de le découvrir (DISCOVER) ni de recevoir une offre (OFFER).
{{% /expand %}}
### 3. Concevoir une étendue
 
Une PME utilise le réseau `192.168.50.0/24` :
 
- le routeur a l'adresse `192.168.50.1`;
- les serveurs utilisent les adresses `192.168.50.2` à `192.168.50.10` (statiques);
- trois imprimantes doivent toujours avoir les adresses `192.168.50.21`, `.22` et `.23`, mais l'administrateur ne veut pas les configurer manuellement;
- les postes doivent obtenir une adresse entre `192.168.50.100` et `192.168.50.250`;
- les adresses `192.168.50.200` à `192.168.50.209` sont utilisées statiquement par des équipements de test.
1. Écrivez la ou les plages (`pools`) de l'étendue.
2. Combien d'adresses le serveur peut-il distribuer aux postes?
3. Comment attribuer leurs adresses aux imprimantes? De quelle information avez-vous besoin pour chacune?
4. Quelles options faut-il distribuer au minimum pour que les postes accèdent à Internet par nom?
{{% expand title="Solution" %}}
1. Plages :
```json
    "pools": [
        { "pool": "192.168.50.100 - 192.168.50.199" },
        { "pool": "192.168.50.210 - 192.168.50.250" }
    ]
```
2. `100 + 41 = 141` adresses.
3. Avec des **réservations**, en dehors des plages. Il faut l'**adresse MAC** de chaque imprimante.
4. Le **routeur** (passerelle par défaut) et les **serveurs DNS**. Le masque est déduit de l'étendue.
{{% /expand %}}
### 4. Raisonner
 
1. Pourquoi l'adresse IP du serveur DHCP doit-elle être statique?
2. Un réseau `10.0.20.0/24` est relié par un routeur au réseau `10.0.10.0/24`, où se trouve le seul serveur DHCP. Aucun relais n'est configuré. Les postes du réseau `10.0.20.0/24` obtiennent-ils une adresse? Justifiez.
3. Un utilisateur branche sur le réseau de l'entreprise un petit routeur Wi-Fi dont le serveur DHCP est activé. Que peut-il arriver aux postes qui démarrent ensuite?
4. Un poste Windows affiche l'adresse `169.254.12.7`. Quelles vérifications faites-vous?
{{% expand title="Solution" %}}
1. Le serveur ne peut pas obtenir son adresse de lui-même. De plus, les relais et les clients qui renouvellent leur bail (T1) lui écrivent directement à son adresse : elle ne doit pas changer.
2. Non. Les DISCOVER sont envoyés en broadcast, et le routeur ne transmet pas les broadcasts d'un réseau à l'autre. Il faudrait un relais DHCP sur le routeur (ou un serveur DHCP dans le réseau `10.0.20.0/24`).
3. Les postes acceptent la **première** offre reçue. Certains obtiendront donc une adresse, une passerelle et un DNS fournis par ce routeur Wi-Fi, dans un réseau différent : ils perdront l'accès aux ressources de l'entreprise. C'est aussi une attaque connue (faux serveur DHCP) : celui qui contrôle la passerelle et le DNS distribués peut intercepter le trafic.
4. Le poste n'a reçu aucune réponse DHCP. On vérifie le lien physique (câble, Wi-Fi), puis que le serveur DHCP est actif, qu'il dispose encore d'adresses libres dans sa plage, et qu'il est joignable depuis ce réseau (même réseau ou relais).
{{% /expand %}}