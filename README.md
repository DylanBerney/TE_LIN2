# Evaluation du module LIN2 [Docker]

Classe : SI-T1b

Date : 26 janvier 2023 de 13h30 à 15h00 (2 périodes)

# 1. Objectifs

- Connaître les caractéristiques d’un conteneur Linux
- Savoir installer et utiliser Docker
- Maîtriser la création des images Docker et les Dockerfile
- Savoir interagir avec le Docker Hub et des registry privés
- Maîtriser les notions réseaux de Docker (networks, links)
- Maîtriser la gestion des données avec Docker (volumes)
- Savoir utiliser Docker Compose

# 2. Introduction

Lors de cette évaluation vous allez automatiser la phase de construction et déployer deux conteneurs Docker. Deux `Dockerfile`  et un `docker-compose.yaml` seront remis à la fin de l’épreuve. Un dépôt personnel docker sera créé pour héberger les 2 images précédemment créées. Un second `docker-compose.yaml` sera créé pour déployer l’infrastructure à partir de votre Docker Hub personnel.  Une démonstration du fonctionnement de votre infrastructure déployée à partir de votre dépôt personnel devra être réalisée avant la fin de votre épreuve. 

<aside>
⚠️ Pour réaliser cette épreuve, il vous faudra respecter scrupuleusement les conventions de nommage et de configuration demandées.

</aside>

## Déroulement

Il vous sera demandé de construire deux conteneurs simples permettant de communiquer entre eux. Les deux applicatifs python (`socket_server.py` et `socket_client.py`) sont fournis et doivent uniquement être déployés, il ne s'agit donc pas de développement mais de mise en production, orienté système et services.

Dans cet examen vous allez donc déployer à l’aide de docker une application serveur et une application cliente en python.

1. L’API Serveur s’exécute d’abord et attend toute demande du client
2. L’API Client lance la conversation au début.
3. Le serveur répondra en conséquence aux demandes du client.
4. Le client se termine si l’utilisateur entre le message **`bye`**. 
5. Le serveur se termine également lorsque le programme client se termine.

# Les API (Application Programming Interface)

Les applications sont les suivantes :

- **API Serveur**: `socket_server.py`
- **API Client** : `socket_client.py`

Chaque API est codé en Python. 

## **API Serveur**

`socket_server.py`

```python
import socket

def server_program():
    # get the hostname
    host = socket.gethostname()
    port = 5000  # initiate port no above 1024

    server_socket = socket.socket()  # get instance
    # look closely. The bind() function takes tuple as argument
    server_socket.bind((host, port))  # bind host address and port together

    # configure how many client the server can listen simultaneously
    server_socket.listen(2)
    conn, address = server_socket.accept()  # accept new connection
    print("Connection from: " + str(address))
    while True:
        # receive data stream. it won't accept data packet greater than 1024 bytes
        data = conn.recv(1024).decode()
        if not data:
            # if data is not received break
            break
        print("from connected user: " + str(data))
        data = input(' -> ')
        conn.send(data.encode())  # send data to the client

    conn.close()  # close the connection

if __name__ == '__main__':
    server_program()
```

Notre serveur fonctionne sur le port 5000 et il attendra la demande du client. `Python while loop` est utilisé pour exécuter le programme serveur indéfiniment et continuer à attendre la demande du client. Il est recommandé d’utiliser l’adresse de port supérieure à 1024 car le numéro de port inférieur à 1024 est réservé au protocole Internet standard.

## API Client

`socket_client.py`

```python
import socket

def client_program():
    host = socket.gethostname()  # as both code is running on same pc
    port = 5000  # socket server port number

    client_socket = socket.socket()  # instantiate
    client_socket.connect((host, port))  # connect to the server

    message = input(" -> ")  # take input

    while message.lower().strip() != 'bye':
        client_socket.send(message.encode())  # send message
        data = client_socket.recv(1024).decode()  # receive response

        print('Received from server: ' + data)  # show in terminal

        message = input(" -> ")  # again take input

    client_socket.close()  # close the connection

if __name__ == '__main__':
    client_program()
```

## Exemple de fonctionnement

1. Exécutez d’abord le programme serveur. 
2. Exécutez ensuite le programme client . 
3. Après cela, écrivez quelque chose à partir du programme client. 
4. Ensuite, écrivez à nouveau la réponse du programme serveur. 
5. Enfin, écrivez `bye` ****sur le client pour mettre fin aux deux programmes. 

![python-socket-example.gif](Evaluation%20du%20module%20LIN2%20%5BDocker%5D%20da6e7e686a6e44a2a425e36dae3181e0/python-socket-example.gif)

# Rendu

Vous devrez faire un fork du dépôt `Github` afin de récupérer les deux applications et de pouvoir envoyer vos modifications sur votre dépôt. Vous devrez mettre à disposition deux `Dockerfile` (un par application) ainsi qu'un fichier `docker-compose.yaml` pour le déploiement automatique des deux applications. Vous devriez avoir une arborescence telle que :

```
.
├── api-server
│   ├── Dockerfile
│   └── socket_server.py
├── api-client
│   ├── Dockerfile
│   └── socket_client.py
└── docker-compose.yaml
└── readme.md
```

Vous devrez également publier vos images sur un compte `Dockerhub` que vous aurez préalablement crée. La version fonctionnelle doit être taggué en `latest`. L'adresse des images sur le `Dockerhub` devra figurer dans un champs `LABEL` de chaque Dockerfile, ayant pour valeur `hub_url`.

```python
**Information à envoyer par mail à crn@cpnv.ch**
Adresse du dépot GitHub :
Adresse de l'image Dockerhub :
```

# **POC**

1. Exécutez votre `docker-compose.yaml` afin de créer les deux containers et initialiser les 2 API, en premier le Serveur suivie ensuite du Client.

```python
**Information à mettre dans le fichier readme.md**
Commande 1 :
```

1. Ouvrez une connexion terminal interactive sur l’API Serveur

```python
**Information à mettre dans le fichier readme.md**
Commande 2 :
```

1. Ouvrez une connexion terminal interactive sur l’API Client

```python
**Information à mettre dans le fichier readme.md**
Commande 3 :
```

1. Tapez `Hello` dans le Client. Le message `Hello` doit apparaitre sur le Serveur.
2. Tapez `Hello` dans le Serveur. Le message `Hello` doit apparaitre sur le Client.
3. Taper `bye` dans le Client. Le Serveur et le Client doivent se fermer.

# Evaluation

```python
Dépot Github [6 pts]
- 1 pt par fichier (5x)
- 1 pt pour le dépot public

Dépot Dockerhub [6 pts]
- 3 pts par image avec tag et version (x2)

POC réussi : [12 pts]
1. [5 pts]
2. [2 pts]
3. [2 pts]
4. [1 pts]
5. [1 pts]
6. [1 pts]
```
