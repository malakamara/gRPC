<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>gRPC Service avec Reverse Proxy</title>
</head>
<body>
    <header>
        <h1>gRPC Service avec Reverse Proxy</h1>
    </header>

    <section>
        <h2>OBJECTIF(S)</h2>
        <ul>
            <li>Mettre en place un service gRPC capable de recevoir des requêtes et de renvoyer des réponses structurées via un protocole de communication performant.</li>
            <li>Créer un reverse proxy servant d'interface pour les clients et redirigeant les requêtes vers le service gRPC.</li>
        </ul>
    </section>

    <section>
        <h2>OUTILS UTILISÉS</h2>
        <ul>
            <li>Node.js</li>
            <li>Protocol Buffers (Protobuf)</li>
            <li>gRPC</li>
            <li>ProtoLoader</li>
        </ul>
    </section>

    <section>
        <h2>Introduction à Protobuf et gRPC</h2>
        <p>
            Protocol Buffers (Protobuf) est un format de données multiplateforme, open-source, utilisé pour sérialiser des données structurées. 
            Il est essentiel pour la communication entre services ou pour le stockage de données.
        </p>
        <p>
            gRPC est un framework d'appel de procédure à distance (RPC) open-source et performant, conçu pour faciliter les communications interservices à grande échelle. 
            Initialement développé par Google, gRPC repose sur Protobuf pour la sérialisation des données.
        </p>
    </section>

    <section>
        <h2>Installation</h2>
        <h3>1. Installer Protobuf et Node.js</h3>
        <p>Sur Ubuntu :</p>
        <pre>
sudo snap install protobuf --classic
sudo snap install node --classic
        </pre>
        <p>Autres systèmes :</p>
        <ul>
            <li>Télécharger et installer Protobuf depuis : <a href="https://protobuf.dev/downloads/" target="_blank">protobuf.dev/downloads/</a></li>
            <li>Télécharger et installer Node.js depuis : <a href="https://nodejs.org/en/download" target="_blank">nodejs.org/en/download</a></li>
        </ul>

        <h3>2. Initialisation du projet</h3>
        <pre>
mkdir grpc-tp
cd grpc-tp
npm init -y
        </pre>

        <h3>3. Installation des dépendances</h3>
        <pre>
npm install @grpc/grpc-js @grpc/proto-loader
        </pre>
    </section>

    <section>
        <h2>Développement</h2>
        <h3>1. Définition du fichier Protobuf (hello.proto)</h3>
        <pre>
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
        </pre>

        <h3>2. Création du serveur gRPC (server.js)</h3>
        <pre>
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
        </pre>

        <h3>3. Démarrer le serveur</h3>
        <pre>
node server.js
        </pre>
        <p>Le serveur écoutera sur localhost:50051.</p>

        <h3>4. Tester avec Postman</h3>
        <ol>
            <li>Ouvrir Postman</li>
            <li>Créer une nouvelle requête gRPC (New → gRPC Request)</li>
            <li>Renseigner <code>localhost:50051</code> comme hôte</li>
            <li>Importer ou saisir le fichier <code>hello.proto</code></li>
            <li>Sélectionner le service <code>Greeter</code> et la méthode <code>SayHello</code></li>
            <li>Envoyer un JSON comme :</li>
        </ol>
        <pre>
{
    "name": "TestUser"
}
        </pre>
    </section>
</body>
</html>
