+++
title = "(bonus) Exclusions, réservations et baux"
weight = "623"
draft = false
+++
-------------
 
## Contexte
 
Dans ce laboratoire, vous allez modifier l'étendue créée au laboratoire précédent :
 
1. **exclure** l'adresse actuelle du client;
2. **réserver** une adresse fixe pour le client;
3. **observer** le renouvellement et l'expiration d'un bail.
Après chaque modification de `/etc/kea/kea-dhcp4.conf` :
 
```bash
sudo kea-dhcp4 -t /etc/kea/kea-dhcp4.conf
sudo systemctl restart kea-dhcp4
```
 
Pour forcer le client à redemander une adresse, sur sa console :
 
```bash
sudo nmcli con down ens160
sudo nmcli con up ens160
```
 
{{%notice style="note" title="Important"%}}
Adaptez `ens160` au nom de la connexion de votre client (`nmcli con show`).
{{%/notice%}}
 
## 1- Exclusion
 
### 1. Relever l'adresse actuelle
 
Relevez l'adresse actuelle du client : ________________
 
### 2. Exclure les adresses 172.16.10.100 à 172.16.10.109
 
Ces adresses seront désormais réservées à des imprimantes configurées statiquement.
 
Modifiez les plages de l'étendue pour exclure `172.16.10.100` à `172.16.10.109`. Validez, puis redémarrez Kea.
 
{{%notice style="note" title="Remarque"%}}
Si l'adresse actuelle du client n'est pas dans cette plage, excluez plutôt une plage de 10 adresses qui la contient.
{{%/notice%}}
 
### 3. Observer la nouvelle demande
 
Sur **dhcp-router**, observez le trafic DHCP en direct :
 
```bash
sudo tcpdump -i ens224 -n udp port 67 or udp port 68
```
 
Sur **client**, redemandez une adresse (`nmcli con down` puis `up`).
 
### Questions
 
1. Combien de messages observez-vous? Le client commence-t-il par un DISCOVER? Pourquoi?
2. Le serveur accepte-t-il l'adresse demandée par le client? Quel message envoie-t-il?
3. Quelle nouvelle adresse le client obtient-il? Se trouve-t-elle dans l'une des plages?
## 2- Réservation
 
### 1. Relever l'adresse MAC du client
 
```bash
ip link show ens160
```
 
Adresse MAC du client : ________________
 
### 2. Créer la réservation
 
Dans l'étendue, ajoutez une réservation qui attribue l'adresse `172.16.10.50` au client, avec le nom `client`.
 
Validez, redémarrez Kea, puis redemandez une adresse sur le client.
 
### 3. Vérifier
 
Sur **client** :
 
```bash
ip a
nmcli -f DHCP4 device show ens160
```
 
Mettez à jour l'entrée `client` de votre `~/.ssh/config` avec la nouvelle adresse et testez `ssh client`.
 
### Questions
 
1. Le client a-t-il obtenu `172.16.10.50`? Cette adresse est-elle dans l'une des plages? Pourquoi est-ce préférable?
2. Quelle information le serveur utilise-t-il pour reconnaître le client?
3. Vous réimportez la VM `client` sur un autre poste du laboratoire, et VMware lui attribue une nouvelle adresse MAC. Quelle adresse obtiendra-t-elle?
## 3- Renouvellement et expiration du bail
 
Avec un bail d'une heure, il faudrait attendre 30 minutes pour observer un renouvellement. Pour l'observer en quelques minutes, réduisez **temporairement** les durées.
 
### 1. Raccourcir le bail
 
Dans `/etc/kea/kea-dhcp4.conf`, remplacez les durées par :
 
```json
"valid-lifetime": 120,
"renew-timer": 60,
"rebind-timer": 105,
```
 
Validez, redémarrez Kea, puis redemandez une adresse sur le client pour qu'il obtienne un bail avec ces durées.
 
### 2. Observer le renouvellement
 
Sur **dhcp-router**, observez le trafic DHCP en direct pendant environ **trois minutes** :
 
```bash
sudo tcpdump -i ens224 -n udp port 67 or udp port 68
```
 
### Questions
 
1. Combien de temps s'écoule entre deux renouvellements?
2. Quelles sont les adresses IP source et destination du REQUEST? Est-il envoyé en broadcast ou en unicast?
3. Combien de messages comporte chaque renouvellement?

### 3. Panne du serveur
 
Gardez `tcpdump` actif dans une session. Dans une autre session sur **dhcp-router**, arrêtez le serveur DHCP :
 
```bash
sudo systemctl stop kea-dhcp4
```
 
Observez le trafic pendant au moins **deux minutes**. Sur la console de **client**, vérifiez régulièrement l'adresse avec `ip a`.
 
### Questions
 
1. Comment évoluent les REQUEST du client? À quel moment l'adresse de destination devient-elle `255.255.255.255`?
2. Que se passe-t-il pour l'adresse IP du client à l'expiration du bail?
3. Quels messages le client envoie-t-il ensuite?
### 4. Rétablir le service
 
1. Redémarrez Kea :
```bash
    sudo systemctl start kea-dhcp4
```
2. Vérifiez que le client retrouve son adresse.
3. **Rétablissez les durées du laboratoire précédent** (`3600`, `1800`, `3150`), validez, puis redémarrez Kea.
{{%notice style="warning" title="Attention"%}}
Ne gardez pas un bail de 120 secondes : votre client perdrait son adresse chaque fois que `dhcp-router` est éteint plus de deux minutes, et le trafic DHCP encombrerait vos captures des prochains laboratoires.
{{%/notice%}}
 
## Questions de synthèse
 
1. Quelle différence y a-t-il entre une **exclusion** et une **réservation**? Donnez un exemple d'appareil pour chacune.
2. Expliquez pourquoi un renouvellement à T1 peut se faire en unicast, alors que la première attribution doit se faire en broadcast.
3. À partir de vos observations, expliquez pourquoi un client ne perd pas immédiatement son adresse lorsque le serveur DHCP tombe en panne. Combien de temps a-t-il pour que le serveur soit réparé?
4. Un administrateur veut pouvoir retirer rapidement les adresses des appareils qui quittent un réseau Wi-Fi public très fréquenté. Doit-il choisir un bail court ou long? Quel inconvénient doit-il accepter?