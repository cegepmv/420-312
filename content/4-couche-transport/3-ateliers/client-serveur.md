+++
pre="<b>2. </b>"
title = "Socket TCP/UDP"
weight = "432"
+++
-------------------

Une des façons de créer une communication entre deux hôtes d'un réseau est d'utiliser l'approche client-serveur :

+ Le serveur est celui qui reçoit les messages
+ Le client est celui qui envoit les messages

Les deux protocoles de la couche transport, TCP et UDP, peuvent être utilisés pour envoyer des messages entre un client et un serveur.

+ TCP: une connexion est établie entre client et serveur. Cette connexion est ensuite utilisée pour que les deux s’échangent des messages. Lorsqu’un des deux termine la connexion, celle-ci est fermée.
+ UDP: le client envoit des messages au serveur sans établir de connexion préalable. Il n’est donc pas possible de savoir si le serveur est en ligne et prêt à recevoir les messages.
+ Pour communiquer en utilisant TCP ou UDP, il faut utiliser les *sockets*.

{{% notice title="Rappel" style="tip" %}}
Un socket représente le point terminal d’une communication entre deux hôtes sur un réseau. Il peut être la source ou la destination d’un message. Les sockets sont définis par une adresse IP et un port et sont utilisés par les programmes pour envoyer et recevoir des messages.
{{% /notice %}}

Dans cet atelier nous allons explorer comment créer une architecture client-serveur en utilisant le langage de programmation Python et la librairie standard `socket`.

## Client
### UDP
Le programme suivant envoit un message par UDP sur un réseau. L’adresse de destination est `127.0.0.1` et le port est `8888`:
```python
import socket

DEST_IP = "127.0.0.1" # localhost
DEST_PORT = 8888
MESSAGE = "Salut !\n"

try:
    # Ouverture d'un socket 
    # se ferme lorsqu'on sort du bloc with
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as socket:
        # Envoyer le message
        socket.sendto(MESSAGE.encode(), (DEST_IP, DEST_PORT))
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin du programme")
```

Vous pouvez tester ce programme comme suit: à partir d’un hôte sur le même réseau que le client, ouvrez un port UDP en mode “listen” avec la commande `nc` (`netcat`). Cet hôte joue donc le rôle de serveur. Par exemple, pour ouvrir le port UDP `8888` la commande est la suivante :

```bash
nc -ulp 8888
```

Assurez-vous que la variable `DEST_IP` du programme a bien comme valeur l’adresse de l’hôte où vous avez lancé la commande `nc`.

### TCP
Le programme suivant envoit un message par TCP sur un réseau. L’adresse de destination est `127.0.0.1` (*localhost*) et le port est `8888`:
```python
import socket

DEST_IP="127.0.0.1"
DEST_PORT = 8888
MESSAGE = "abcdefg!\n"

try:
    # Ouverture d'un socket (se ferme lorsqu'on sort du bloc with)
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as socket:
        # Demande de connexion
        socket.connect((DEST_IP, DEST_PORT))
        
        # Envoie du message
        socket.send(MESSAGE.encode())

# Erreur de connexion
except ConnectionRefusedError:
    print("Erreur: Serveur indisponible")
# Autres erreurs
except Exception as err:
    print(f"Erreur: {err}")
# Fin du programme avec CTRL+C
except KeyboardInterrupt:
    print("Fin du programme")
```

Vous pouvez aussi tester ce programme avec la commande `nc`. À partir d’un hôte sur le même réseau que la machine client (qui aura le rôle de serveur), ouvrez un port TCP en mode “listen” au port TCP `8888`:
```bash
nc -lp 8888
```

{{% notice style="tip" title=Remarque %}}
+ Les commandes `nc` ou `python` sont introuvables? Essayez de les installer :
```bash
sudo dnf install nc # Alma/Rocky/Centos
sudo apt install netcat-openbsd # Ubuntu/Debian

sudo dnf install python # Alma/Rocky/Centos
sudo apt install python # Ubuntu/Debian
```
{{% /notice %}}

Assurez-vous que la variable `DEST_IP` du programme a bien comme valeur l’adresse de l’hôte où vous avez lancé la commande `nc`; ensuite exécutez-le.

## Serveur
### UDP
Le programme suivant ouvre un socket au port UDP `8888` et affiche les messages entrants:

```python
import socket

IP = "" # Écoute sur toutes les IPs du serveur
PORT = 8888
BUFFER_SIZE = 1024

try: 
    # Ouverture d'un socket (se ferme quand on sort du bloc with)
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as socket:
        # Lie le socket à l'adresse et au port d'écoute
        socket.bind((IP, PORT))
        print(f"Attente de messages UDP sur le port {PORT}...")

        # Réception des messages
        while True:
            # Réception des données
            message, adr_dist = socket.recvfrom(BUFFER_SIZE)
            
            # Décodage binaire -> texte
            # Suppression des "impuretés"
            message = message.decode().strip()
            print(f"Message reçu: {message}")

except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print(f"Fin du programme")

```

Pour tester ce programme à partir d’un autre hôte (qui agit comme client), lancez la commande `nc` (remplacez `1.2.3.4` par l’adresse IP du serveur) et tapez le message que vous voulez envoyer:

```bash
nc -u 1.2.3.4 8888
abcdefg!
```

### TCP
Le programme suivant ouvre un socket au port TCP `8888` et attend les connexions entrantes. Lorsque la connexion est établie il affiche les messages et termine la connexion lorsque le message reçu est vide (taille 0):
```python
import socket

IP = "" # Écoute sur toutes les IPs du serveur
PORT = 8888
BUFFER_SIZE = 1024
try: 
    # Ouverture d'un socket (se ferme quand on sort du bloc with)
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as socket:
        # Lie le socket à l'adresse et au port d'écoute
        socket.bind((IP, PORT))
        
        # Attend une connexion (écoute)
        socket.listen()
        print(f"Serveur TCP en attente de connexion sur le port {PORT}...")
        
        # Accepte la connexion reçue
        conn, addr = socket.accept()
        with conn:
            print(f"Client connecté : {addr}")
            while True:
                # Attend de recevoir un message
                data = conn.recv(BUFFER_SIZE)
                if not data :
                    print("Déconnecté")
                    break
                # Décodage binaire -> texte
                # Suppression des "impuretés"
                message = data.decode().strip()
                print(f"Reçu : {message}")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print(f"Fin du programme")
    exit(1)

```

Pour tester ce programme à partir d’un autre hôte (qui agit comme client), lancez la commande `nc` (remplacez `1.2.3.4` par l’adresse IP du serveur) et tapez le message que vous voulez envoyer:

```bash
nc 1.2.3.4 8888
abcdefg!
```

{{%notice style="note" title="Note"%}}
Si vous n'arrivez pas à faire communiquer le client et le serveur, essayez de désactiver le pare-feu (ce n'est pas une très bonne pratique, mais nous n'allons pas couvrir les règles de pare-feu dans ce cours) :

```bash
sudo systemctl stop firewalld # arrêter le service du pare-feu
sudo systemctl stop firewalld # désactiver le service (ne démarre pas au prochain reboot)
sudo systemctl status firewalld # vérifier
```
{{%/notice %}}

## Exercices
### Exercice 1
1. Créez un serveur TCP qui :
    + écoute sur le port `5000`,
    + accepte une connexion,
    + reçoit un message du client,
    + affiche le message,
    + répond "Bonjour client, j'ai bien reçu ton message : <Message reçu>".

2. Créez un client TCP qui :
    + se connecte au serveur,
    + demande à l'utilisateur d'entrer un message
    + envoie le message au serveur
    + lorsque le message envoyé est "quit", le client met fin à la connexion et le programme se termine.

{{% expand title="Solution"%}}
1. **Serveur** 
```python
import socket

IP = ""
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as socket:
        socket.bind((IP, PORT))
        socket.listen()
        print(f"Serveur TCP en attente de connexion sur le port {PORT}...")
        conn, addr = socket.accept()
        with conn:
            print(f"Client connecté : {addr}")
            while True:
                data = conn.recv(1024)
                if not data:
                    break
                
                request = data.decode().strip()
                print(f"Reçu : {request}")
                
                if request == "quit":
                    break
                
                response = f"Bonjour client, j'ai bien reçu ton message : {request}"
                conn.sendall(response.encode('utf-8'))
        print("Fin de la conversation")
except ConnectionRefusedError as e:
    print("Erreur: serveur indisponible")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin de la conversation")
```
2. **Client** 
```python
import socket

IP="192.168.121.226"
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
        sock.connect((IP, PORT))

        while True:
            user_input = input(">>> ")
            sock.send(user_input.strip().encode())
            if user_input == "quit":
                print("Fin de la conversation")
                break 
            data = sock.recv(BUFFER_SIZE)
            if not data :
                print("Déconnecté")
                break
            else:
                message = data.decode("utf-8")
                print(f"Serveur: {message}")
except ConnectionRefusedError as e:
    print("Erreur: serveur indisponible")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin de la conversation")
```
{{% /expand %}}


### Exercice 2
Reproduire le comportement du client-serveur de l'exercice 1, cette fois-ci avec UDP.

{{% expand title="Solution"%}}
1. **Serveur**
```python
import socket

IP = ""
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as socket:
        socket.bind((IP, PORT))
        print(f"Serveur UDP en écoute de messages sur le port {PORT}...")
        while True:
            data, address = socket.recvfrom(BUFFER_SIZE)
            request = data.decode().strip()
            print(f"Reçu : {request}")
            
            response = f"Bonjour client, j'ai bien reçu ton message : {request}"
            socket.sendto(response.encode(), address)
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin du serveur UDP")
```

+ **Client**
```python
import socket

IP="192.168.121.226"
PORT = 5000
BUFFER_SIZE = 1024

try:
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as sock:
        while True:
            user_input = input(">>> ")
            sock.sendto(
                user_input.strip().encode(),
                (IP, PORT)
            )
            if user_input == "quit":
                break 
            data = sock.recv(BUFFER_SIZE)
            message = data.decode()
            print(f"Serveur: {message}")
        print("Fin du client UDP")
except Exception as err:
    print(f"Erreur: {err}")
except KeyboardInterrupt:
    print("Fin du client UDP")
```
{{% /expand %}}