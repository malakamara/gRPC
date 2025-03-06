gRPC Service avec Reverse Proxy

OBJECTIF(S)

Mettre en place un service gRPC capable de recevoir des requêtes et de renvoyer des réponses structurées via un protocole de communication performant.

Créer un reverse proxy servant d'interface pour les clients et redirigeant les requêtes vers le service gRPC.

OUTILS UTILISÉS

Node.js

Protocol Buffers (Protobuf)

gRPC

ProtoLoader

Introduction à Protobuf et gRPC

Protocol Buffers (Protobuf) est un format de données multiplateforme, open-source, utilisé pour sérialiser des données structurées. Il est essentiel pour la communication entre services ou pour le stockage de données.

gRPC est un framework d'appel de procédure à distance (RPC) open-source et performant, conçu pour faciliter les communications interservices à grande échelle. Initialement développé par Google, gRPC repose sur Protobuf pour la sérialisation des données.

Installation

1. Installer Protobuf et Node.js

Sur Ubuntu :

sudo snap install protobuf --classic
sudo snap install node --classic

Autres systèmes :

Télécharger et installer Protobuf depuis : https://protobuf.dev/downloads/

Télécharger et installer Node.js depuis : https://nodejs.org/en/download

2. Initialisation du projet

mkdir grpc-tp
cd grpc-tp
npm init -y

3. Installation des dépendances

npm install @grpc/grpc-js @grpc/proto-loader

Développement

4. Définition du fichier Protobuf (hello.proto)

syntax = "proto3";
package hello;

service Greeter {
    rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
    string name = 1;
}

message HelloReply {
    string message = 1;
}

5. Création du serveur gRPC (server.js)

const grpc = require('@grpc/grpc-js');
const protoLoader = require('@grpc/proto-loader');
const PROTO_PATH = './hello.proto';

const packageDefinition = protoLoader.loadSync(PROTO_PATH);
const helloProto = grpc.loadPackageDefinition(packageDefinition).hello;

const server = new grpc.Server();
server.addService(helloProto.Greeter.service, {
    SayHello: (call, callback) => {
        callback(null, { message: `Bonjour, ${call.request.name}!` });
    },
});

server.bindAsync('0.0.0.0:50051', grpc.ServerCredentials.createInsecure(), () => {
    console.log('Serveur gRPC en écoute sur le port 50051');
    server.start();
});

6. Démarrer le serveur

node server.js

Le serveur écoutera sur localhost:50051.

7. Tester avec Postman

Ouvrir Postman

Créer une nouvelle requête gRPC (New → gRPC Request)

Renseigner localhost:50051 comme hôte

Importer ou saisir le fichier hello.proto

Sélectionner le service Greeter et la méthode SayHello

Envoyer un JSON comme :

{ "name": "TestUser" }
