+++
title = "1- Routeur DHCP"
weight = "622"
draft = false
+++
----------------
 
## Contexte
 
Dans ce laboratoire, `dhcp-router` devient la **porte d'entrée** du réseau du chapitre :
 
1. il **route** le trafic du LAN vers Internet, avec du NAT (chapitre 4);
2. il **attribue** leur configuration IP aux clients avec le serveur DHCP **Kea**.
Vous capturerez ensuite l'échange DORA lors du démarrage du client.
 
| Machine | Interface | Configuration |
|---------|-----------|---------------|
| **dhcp-router** | `ens160` (WAN, NAT) | Automatique |
| **dhcp-router** | `ens224` (LAN-SERVICES) | `172.16.10.1/24` (statique, section 6.1) |
| **dns-server** | `ens160` (LAN-SERVICES) | `172.16.10.2/24` (statique, section 6.1) |
| **client** | `ens160` (LAN-SERVICES) | **Automatique (DHCP)** |
 
L'étendue à configurer :
 
| Élément | Valeur |
|---------|--------|
| Réseau | `172.16.10.0/24` |
| Plage distribuée | `172.16.10.100` à `172.16.10.199` |
| Passerelle | `172.16.10.1` |
| Serveur DNS | `172.16.10.2` |
| Bail | 1 heure |
 
## Préparation
 
- `dhcp-router` et `dns-server` sont configurés selon le laboratoire 6.1.1.
- La VM `client` est créée (section 6.0), avec sa carte réseau dans **LAN-SERVICES**. **Ne la démarrez pas encore.**
- Ouvrez une session `ssh routeur` depuis le poste Windows.
<!-- ### Prédire avant d'observer
 
Avant toute configuration, complétez le tableau à partir de la théorie :
 
| Message | IP source | IP destination | Port source | Port destination |
|---------|-----------|----------------|-------------|------------------|
| DISCOVER | | | | |
| OFFER | | | | |
| REQUEST | | | | |
| ACK | | | | | -->
 
## 1- Activer le routage
 
### 1. Activer le transfert IPv4
 
Comme au chapitre 4, activez le *forwarding* IPv4 de façon permanente sur **dhcp-router** :
 
```bash
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p /etc/sysctl.conf
```
 
### 2. Activer le NAT
 
Les adresses `172.16.10.0/24` sont privées : elles doivent être traduites en l'adresse WAN de dhcp-router pour sortir du réseau (chapitre 4). Avec `firewalld`, cette traduction s'appelle le **masquerading** :
 
```bash
sudo firewall-cmd --add-masquerade --permanent
sudo firewall-cmd --reload
```
 
### 3. Tester
 
Depuis **dns-server** (`ssh dns`), qui a déjà `172.16.10.1` comme passerelle :
 
```bash
ping -c 3 8.8.8.8
```
 
### Questions
 
1. Le test fonctionne-t-il? Quelles sont les deux conditions qui l'ont rendu possible?
2. Quelle adresse IP source les serveurs d'Internet voient-ils pour ce `ping`?
## 2- Configurer le serveur DHCP
 
### 1. Le fichier de configuration
 
Sur **dhcp-router**, conservez le fichier fourni avec Kea, qui contient de nombreux exemples commentés :
 
```bash
sudo mv /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.exemple
```
 
Créez un nouveau fichier :
 
```bash
sudo nano /etc/kea/kea-dhcp4.conf
```
 
Contenu :
 
```json
{
"Dhcp4": {
    # Interface(s) sur laquelle le serveur répond aux clients
    "interfaces-config": {
        "interfaces": [ "ens224" ]
    },
 
    # Enregistrement des baux dans un fichier
    "lease-database": {
        "type": "memfile",
        "persist": true,
        "name": "/var/lib/kea/kea-leases4.csv"
    },
 
    # Durées en secondes : bail, T1 (50 %), T2 (87,5 %)
    "valid-lifetime": 3600,
    "renew-timer": 1800,
    "rebind-timer": 3150,
 
    # Étendues
    "subnet4": [
        {
            "id": 1,
            "subnet": "172.16.10.0/24",
            "pools": [
                { "pool": "172.16.10.100 - 172.16.10.199" }
            ],
            "option-data": [
                { "name": "routers", "data": "172.16.10.1" },
                { "name": "domain-name-servers", "data": "172.16.10.2" }
            ]
        }
    ]
}
}
```
 
{{%notice style="info" title="Format du fichier"%}}
Le fichier est au format **JSON**, auquel Kea ajoute les commentaires (`#`). Une virgule manquante ou en trop entre deux éléments suffit à empêcher le démarrage du service.
{{%/notice%}}
 
### 2. Valider et démarrer
 
```bash
sudo kea-dhcp4 -t /etc/kea/kea-dhcp4.conf
sudo systemctl enable --now kea-dhcp4
systemctl status kea-dhcp4
```
 
En cas d'erreur, suivez la démarche de diagnostic de la section 6.0.
 
### 3. Pare-feu et écoute
 
```bash
sudo firewall-cmd --add-service=dhcp --permanent
sudo firewall-cmd --reload
sudo ss -ulnp | grep :67
```
 
### Questions
 
1. Sur quel port et quel protocole Kea écoute-t-il?
2. Pourquoi le serveur DHCP ne doit-il **pas** répondre sur l'interface `ens160`?
3. Pourquoi la plage distribuée commence-t-elle à `172.16.10.100`?

<!--
## 3- Capturer l'échange DORA
 
### 1. Démarrer la capture
 
 Sur **dhcp-router** :
 
```bash
sudo tcpdump -i ens224 -n -w /tmp/dora.pcap udp port 67 or udp port 68 
```-->
<!-- ### 2. Démarrer le client -->
 
## 3- Tester le fonctionnement 

### 1. Vérifier la configuration du client
 
 
Démarrez la VM **client**,ouvrez une session, puis un terminal (`Ctrl+Alt+T`) :
 
```bash
ip a
ip route
nmcli -f DHCP4 device show ens160
```
 
Complétez le tableau :
 
| Élément | Valeur obtenue |
|---------|----------------|
| Adresse IP et préfixe | |
| Passerelle par défaut | |
| Serveur DNS | |
| Durée du bail (`dhcp_lease_time`) | |
| Serveur DHCP (`dhcp_server_identifier`) | |
 
 
### 2. Le bail côté serveur
 
Sur **dhcp-router** :
 
```bash
sudo cat /var/lib/kea/kea-leases4.csv
```
 
Kea ajoute une ligne à chaque attribution ou renouvellement. Repérez la **dernière** ligne correspondant au client : son adresse IP, son adresse MAC et la date d'expiration (`expire`, en secondes depuis le 1<sup>er</sup> janvier 1970).
 
### 3. Tester la connectivité
 
Sur **client** :
 
```bash
ping -c 3 172.16.10.1
ping -c 3 8.8.8.8
ping -c 3 www.google.com
```
 
### Questions
 
1. Le client a-t-il reçu la première adresse de la plage? Comparez l'adresse MAC du fichier des baux avec celle de `ip a`.
2. Pourquoi `ping 8.8.8.8` fonctionne-t-il, mais pas `ping www.google.com`?
3. Ajoutez une entrée `client` à votre `~/.ssh/config` (section 6.1) avec cette adresse. Pourquoi une adresse attribuée dynamiquement est-elle peu pratique pour administrer une machine?
<!-- ## 4- Analyser l'échange
 
Récupérez la capture sur le poste Windows :
 
```powershell
scp routeur:/tmp/dora.pcap .
```
 
Ouvrez-la dans Wireshark et appliquez le filtre `dhcp`.
 
### 1. Vue d'ensemble
 
Complétez le tableau avec les valeurs observées :
 
| Message (Option 53) | MAC destination | IP source | IP destination | Port source | Port destination |
|---------------------|-----------------|-----------|----------------|-------------|------------------|
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |
 
Comparez avec vos prédictions.
 
### 2. Le DISCOVER
 
Développez la section **Dynamic Host Configuration Protocol** du DISCOVER.
 
1. Relevez le **Transaction ID**. Retrouvez-le dans les trois autres messages.
2. Relevez la **Client MAC address**. Où apparaît-elle ailleurs dans la trame?
3. Développez l'option **(55) Parameter Request List** : quelles informations le client demande-t-il?
4. Relevez la valeur de **Bootp flags** : le client demande-t-il des réponses en broadcast ou en unicast?
### 3. L'OFFER
 
1. Relevez l'adresse proposée (**Your (client) IP address**).
2. Relevez la valeur des options `1`, `3`, `6`, `51` et `54`. Retrouvez-vous la configuration de Kea?
### 4. Le REQUEST
 
Repérez les options **(50) Requested IP Address** et **(54) DHCP Server Identifier**. À quoi servent-elles?
 
### Questions
 
1. Pourquoi le REQUEST est-il envoyé de `0.0.0.0`, alors que le client connaît déjà l'adresse qui lui est proposée?
2. Les réponses du serveur sont-elles envoyées en broadcast ou en unicast? Est-ce cohérent avec le champ *Bootp flags* du DISCOVER?
3. Quel champ permet au client de savoir qu'une réponse lui est destinée, si plusieurs clients démarrent en même temps?
## Questions de synthèse
 
1. Décrivez le rôle de chacun des quatre messages DORA en une phrase, et expliquez pourquoi le client utilise le broadcast.
2. Dans ce laboratoire, `dhcp-router` assure trois fonctions pour les clients. Lesquelles? Pour chacune, indiquez la commande ou le fichier de configuration correspondant.
3. Le serveur DNS distribué (`172.16.10.2`) n'est pas encore configuré. Quelles conséquences cela a-t-il pour le client? Que faudra-t-il faire à la section 6.3? -->
