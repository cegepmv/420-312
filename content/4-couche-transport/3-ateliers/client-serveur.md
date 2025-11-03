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

PORT = 8888
DEST_IP = "127.0.0.1"
MESSAGE = "abcdefg!\n"

# Créer le socket
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# Créer un objet pour l'adresse
dest_addr = (DEST_IP, PORT)

# Envoyer le message
sock.sendto(MESSAGE.encode(), dest_addr)

# Fermer le socket
sock.close()
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

PORT = 8888
DEST_IP = "127.0.0.1"
MESSAGE = "abcdefg!\n"

# Créer le socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Créer un tuple pour le socket de destination
dest_addr = (DEST_IP, PORT)

try:
    # Établir la connexion
    sock.connect(dest_addr)
    # Envoyer le message
    sock.send(MESSAGE.encode())
except Exception as error:
    # Si erreur de connexion ou d'envoie
    # Afficher l'erreur sans faire planter le programme
    print(f"Erreur: {error}")

# Fermer la connexion
sock.close()
```

Vous pouvez aussi tester ce programme avec la commande `nc`. À partir d’un hôte sur le même réseau que la machine client (qui aura le rôle de serveur), ouvrez un port TCP en mode “listen” au port TCP `8888`:
```bash
nc -lp 8888
```

Assurez-vous que la variable `DEST_IP` du programme a bien comme valeur l’adresse de l’hôte où vous avez lancé la commande `nc`; ensuite exécutez-le.

## Serveur
### UDP
Le programme suivant ouvre un socket au port UDP `8888` et affiche les messages entrants:

```python
import socket

PORT = 8888
BUFFER_SIZE = 1024

# Créer le socket
socket_local = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
adr_local = ('', PORT)

# Lier le socket à l'adresse
socket_local.bind(adr_local)

print(f"On attend les messages UDP entrants au port {PORT}...")

# Réception des messages
while True:
    # Réception des données
    buffer, adr_dist = socket_local.recvfrom(BUFFER_SIZE)
    
    # Décodage binaire -> texte
    message = buffer.decode()

    print(f"Message reçu: {message}", end='')
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

PORT = 8888
BUFFER_SIZE = 1024

# Créer le socket
socket_local = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
address = ('', PORT)  

# Lier le socket à l'adresse
socket_local.bind(address)

# Attendre une connexion
socket_local.listen(3)
print(f"Serveur TCP en écoute au port {PORT}...")

socket_dist, client_address = socket_local.accept()
print(f"Socket distant: {client_address}")

# Réception des messages
while True:
    # Données
    data = socket_dist.recv(BUFFER_SIZE)
    if not data:
        print("Déconnecté")
        break
    else:
        # Décoder + afficher les message
        message = data.decode()
        print(f"Reçu: {message}", end='')

socket_dist.close()
socket_local.close()
```

Pour tester ce programme à partir d’un autre hôte (qui agit comme client), lancez la commande `nc` (remplacez `1.2.3.4` par l’adresse IP du serveur) et tapez le message que vous voulez envoyer:

```bash
nc 1.2.3.4 8888
abcdefg!
```

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
    + envoie le message "Bonjour serveur !",
    + affiche la réponse reçue.
    + lorsque le message envoyé est "quit", le client met fin à la connexion et le programme se termine.

{{% expand title="Solution"%}}
1. **Serveur** 
```python
import socket

PORT = 5000         # Port d'écoute

# Création du socket TCP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind((HOST, PORT))
server_socket.listen(1)

print(f"Serveur TCP en attente de connexion sur le port {PORT}...")

# Attente de connexion d'un client
conn, addr = server_socket.accept()
print(f"Connexion établie avec {addr}")

# Boucle de communication
while True:
    data = conn.recv(1024)  # Réception (max 1024 octets)
    if not data:
        break

    message = data.decode('utf-8')
    print(f"Message reçu du client : {message}")

    if message.lower() == "quit":
        print("Le client a quitté la conversation.")
        break

    reponse = f"Bonjour client, j'ai bien reçu ton message : {message}"
    conn.sendall(reponse.encode('utf-8'))

# Fermeture de la connexion
conn.close()
server_socket.close()
print("Serveur terminé.")
```

2. **Client**
```python
import socket

HOST = '127.0.0.1'
PORT = 5000

# Création du socket TCP
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect((HOST, PORT))

print("Connecté au serveur TCP.")
print("Tape 'quit' pour quitter.")

while True:
    message = input(">>> ")
    client_socket.send(message.encode('utf-8'))

    if message.lower() == "quit":
        print("Fermeture de la connexion.")
        break

    data = client_socket.recv(1024)
    print("Réponse du serveur :", data.decode('utf-8'))

client_socket.close()
```
{{% /expand %}}


### Exercice 2
Reproduit le comportement des client-serveur de l'exercice 1, cette fois-ci avec UDP.

{{% expand title="Solution"%}}
1. **Serveur**
```python
import socket

HOST = '127.0.0.1'
PORT = 5000

# Création du socket UDP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_socket.bind((HOST, PORT))

print(f"Serveur UDP en écoute sur le port {PORT}...")

while True:
    data, addr = server_socket.recvfrom(1024)
    message = data.decode('utf-8')
    print(f"Message reçu de {addr} : {message}")

    if message.lower() == "quit":
        print("Le client a demandé la fin de la communication.")
        break

    reponse = f"Bonjour client, j'ai bien reçu ton message : {message}"
    server_socket.sendto(reponse.encode('utf-8'), addr)

server_socket.close()
print("Serveur UDP terminé.")
```

+ **Client**
```python
import socket

HOST = '127.0.0.1'
PORT = 5000

# Création du socket UDP
client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

print("Client UDP prêt.")
print("Tape 'quit' pour quitter.")

while True:
    message = input(">>> ")
    client_socket.sendto(message.encode('utf-8'), (HOST, PORT))

    if message.lower() == "quit":
        print("Fermeture du client UDP.")
        break

    data, _ = client_socket.recvfrom(1024)
    print("Réponse du serveur :", data.decode('utf-8'))

client_socket.close()
```
{{% /expand %}}