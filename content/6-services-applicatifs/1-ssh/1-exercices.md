+++
title = "Exercices"
weight = "611"
draft = false
+++
-----------------
 
### 1. Phases d'une connexion
 
Remettez les étapes suivantes dans l'ordre, puis indiquez pour chacune si elle est visible **en clair** ou **chiffrée** dans une capture.
 
1. Le client envoie la commande `ls`.
2. Le client annonce `SSH-2.0-OpenSSH_9.9`.
3. Le client envoie le SYN.
4. Le client envoie son mot de passe.
5. Le client et le serveur choisissent leurs algorithmes.

<!-- {{% expand title="Solution" %}}
| Ordre | Étape | Visibilité |
|-------|-------|------------|
| 1 | Le client envoie le SYN (3) | En clair (en-têtes TCP) |
| 2 | Le client annonce sa version (2) | En clair |
| 3 | Choix des algorithmes (5) | En clair (la liste des algorithmes est visible, mais pas la clé de session calculée) |
| 4 | Le client envoie son mot de passe (4) | Chiffrée |
| 5 | Le client envoie `ls` (1) | Chiffrée |
{{% /expand %}} -->
 
### 2. Lire un rebond
 
Pendant qu'un poste Windows est connecté à `dns-server` par rebond, on exécute `ss -tn` sur `dhcp-router` :
 
```text
State  Recv-Q  Send-Q  Local Address:Port      Peer Address:Port
ESTAB  0       0       192.168.110.130:22      192.168.110.1:50211
ESTAB  0       0       192.168.110.130:22      192.168.110.1:50236
ESTAB  0       0       172.16.10.1:39114       172.16.10.2:22
```
 
1. Quelle ligne correspond au relais vers `dns-server`? `dhcp-router` y est-il client ou serveur?
2. Le poste Windows a deux connexions vers `dhcp-router`. Proposez une explication.
3. Quelle adresse IP source `dns-server` voit-il pour cette connexion?
4. Faut-il activer le *forwarding* IPv4 sur `dhcp-router` pour que ce rebond fonctionne? Justifiez.

<!-- {{% expand title="Solution" %}}
1. La troisième ligne. `dhcp-router` est **client** : son port local `39114` est éphémère et le port distant est `22`.
2. Une connexion sert au rebond vers `dns-server`; l'autre est une session SSH ouverte directement sur `dhcp-router` (dans un autre terminal, par exemple).
3. `172.16.10.1`, l'adresse LAN de `dhcp-router`.
4. Non. `dhcp-router` ne transfère pas de paquets : il ouvre sa propre connexion TCP vers `dns-server` et relaie les données au niveau applicatif.
{{% /expand %}} -->

### 3. Configuration et copie
 
L'adresse WAN de `dhcp-router` est `192.168.110.142`. Le fichier `~/.ssh/config` du poste contient l'alias `routeur`.
 
1. Écrivez l'entrée `client` du fichier `~/.ssh/config` pour une machine dont l'adresse est `172.16.10.50`.
2. Écrivez la commande qui copie le fichier `/tmp/dns.pcap` de `dns-server` vers le répertoire courant du poste.
3. Écrivez la commande qui copie le répertoire local `site/` vers `/tmp/` sur `web-server`.
4. Sans fichier de configuration, écrivez la commande `ssh` complète pour vous connecter à `172.16.10.3` avec l'utilisateur `student`.

<!-- {{% expand title="Solution" %}}
1. Entrée du fichier :
```text
    Host client
        HostName 172.16.10.50
        User student
        ProxyJump routeur
```
2. `scp dns:/tmp/dns.pcap .`
3. `scp -r site/ web:/tmp/`
4. `ssh -J student@192.168.110.142 student@172.16.10.3`
{{% /expand %}} -->
