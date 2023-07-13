# Dockerisierte Full-Stack-App

In dieser Anleitung werden wir eine Fullstack App mit React Frontend, NodeJS Backend und MongoDB dockerizen. 
Wir beginnen mit der MongoDB.

- Um deine Full-Stack-App zu starten, benötigst du zunächst einen MongoDB Docker-Container. Du kannst diesen mit folgendem Befehl starten: 
```
docker run --name mongodb --rm -d -p 27017:27017 mongo
```

- Anschließend startest du das Backend im `/backend` Verzeichnis mit: 
```
node app.js
```

- Als nächstes startest du das Frontend mit: 
```
npm run start
```

- Nun sollte die App im Browser laufen.

## Dockerisierung des Backends

- Erstelle eine Dockerfile für das Backend im `/backend` Verzeichnis.

- Baue das Backend mit dem Befehl `docker build`.

- Starte den Backend-Container.

- Versuche, ein `Goal` im Frontend einzugeben. 

- Ändere die Mongoose-Verbindung in `app.js` zu: 

```
mongoose.connect(
  'mongodb://host.docker.internal:27017/course-goals',
```
### Warum nochmal host.docker.internal?

Im Docker-Kontext bezieht sich "localhost" auf den Container selbst und nicht auf den Host, auf dem der Container ausgeführt wird. Daher kann ein Docker-Container unter "localhost" keine Dienste erreichen, die auf dem Hostsystem laufen, wie z. B. deine MongoDB-Instanz.

Wenn du `mongoose.connect('mongodb://localhost:27017/course-goals')` verwendest, versucht dein Backend-Container, eine Verbindung zu einer MongoDB-Instanz herzustellen, die innerhalb desselben Containers läuft. Da sich die MongoDB-Instanz jedoch auf dem Host und nicht im Container befindet, schlägt diese Verbindung fehl.

Der spezielle Hostname `host.docker.internal` wird verwendet, um vom Container aus auf den Docker-Host zuzugreifen. Wenn du `mongoose.connect('mongodb://host.docker.internal:27017/course-goals')` verwendest, stellt der Backend-Container erfolgreich eine Verbindung zur MongoDB-Instanz her, die auf dem Docker-Host läuft.

Diese Funktion ist besonders nützlich, wenn du Dienste in separaten Containern ausführst (wie in diesem Fall MongoDB und dein Node.js-Backend) und sie miteinander kommunizieren müssen. Es ist wichtig zu beachten, dass `host.docker.internal` hauptsächlich für die Entwicklungsumgebung gedacht ist und nicht in einer Produktionsumgebung verwendet werden sollte. In der Produktionsumgebung sollten die Container über ein Docker-Netzwerk miteinander kommunizieren.

- Starte den Backend-Container erneut, vergiss dabei nicht, die Ports richtig zu mappen: `-p 80:80`.

## Dockerisierung des Frontends

- Erstelle eine Dockerfile für das Frontend mit folgendem Inhalt:

```
FROM node

WORKDIR /app

COPY package.json . 

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

- Baue und starte das Frontend. Für React benötigst du speziell die `-it` Flag:

```
docker run --name goals-frontend --rm -d -p 3000:3000 -it goals-react
```
