<b> gRPC Service avec Reverse Proxy </b>

<b> OBJECTIF(S) </b>

<p>Mettre en place un service gRPC capable de recevoir des requêtes et de renvoyer des réponses structurées via un protocole de communication performant.</p>
<p>Créer un reverse proxy servant d'interface pour les clients et redirigeant les requêtes vers le service gRPC.</p>

<b> OUTILS UTILISÉS </b>

<ul>
  <li>Node.js</li>
  <li>Protocol Buffers (Protobuf)</li>
  <li>gRPC</li>
  <li>ProtoLoader</li>
</ul>

<b> Introduction à Protobuf et gRPC </b>

<p>Protocol Buffers (Protobuf) est un format de données multiplateforme, open-source, utilisé pour sérialiser des données structurées. Il est essentiel pour la communication entre services ou pour le stockage de données.</p>
<p>gRPC est un framework d'appel de procédure à distance (RPC) open-source et performant, conçu pour faciliter les communications interservices à grande échelle. Initialement développé par Google, gRPC repose sur Protobuf pour la sérialisation des données.</p>

<b> Installation </b>

<p><b>Installer Protobuf et Node.js</b></p>
<p>Sur Ubuntu :</p>
<pre><code>sudo snap install protobuf --classic</code></pre>
<pre><code>sudo snap install node --classic</code></pre>

<p>Autres systèmes :</p>
<p>Télécharger et installer Protobuf depuis : <a href="https://protobuf.dev/downloads/">https://protobuf.dev/downloads/</a></p>
<p>Télécharger et installer Node.js depuis : <a href="https://nodejs.org/en/download">https://nodejs.org/en/download</a></p>


<b> Initialisation du projet </b>

<pre><code>mkdir grpc-tp
cd grpc-tp
npm init -y</code></pre>

<b> Installation des dépendances </b>

<pre><code>npm install @grpc/grpc-js @grpc/proto-loader</code></pre>

<b> Développement </b>

<b> Définition du fichier Protobuf (hello.proto) </b>

<pre><code>syntax = "proto3";
package hello;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}</code></pre>

<b> Création du serveur gRPC (server.js) </b>

<pre><code>const grpc = require('@grpc/grpc-js');
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
});</code></pre>

<b> Démarrer le serveur </b>

<pre><code>node server.js</code></pre>

<p>Le serveur écoutera sur <code>localhost:50051</code>.</p>

<b> Tester avec Postman </b>

<p>Ouvrir Postman</p>
<ul>
  <li>Créer une nouvelle requête gRPC (New → gRPC Request)</li>
  <li>Renseigner localhost:50051 comme hôte</li>
  <li>Importer ou saisir le fichier hello.proto</li>
  <li>Sélectionner le service Greeter et la méthode SayHello</li>
  <li>Envoyer un JSON comme :</li>
</ul>

<pre><code>{ "name": "TestUser" }</code></pre>

