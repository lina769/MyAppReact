docker compose down
docker build -t mern-server ./Server
docker build -t mern-client ./Client
docker network create mern-network
docker run -d --name mongodb --network mern-network mongo
docker run -d --name server --network mern-network -p 9000:9000 mern-server
docker run -d --name client --network mern-network -p 3000:3000 mern-client
docker ps

# TP4 : Docker et Docker Compose

**lina ben khalifa 3 lig g2a**

## Objectif

Containeriser une application MERN (client React + API Express + MongoDB)
avec Docker, puis l'orchestrer avec Docker Compose.

## Structure

- `Client/` : application React (Vite), avec son `Dockerfile`
- `Server/` : API Express, avec son `Dockerfile`
- `docker-compose.yml` : orchestration des 3 services

## Étape 1 : Cloner le projet

    git clone https://github.com/wiem-kb/MyAppReact.git

## Étape 2 : Dockerfiles

- **Server** : image `node:lts-alpine`, copie de `package*.json`,
  `npm install`, copie du code, port 9000, démarrage avec `npm start`.
  La variable `MONGO_URI` est définie.
- **Client** : image `node:lts-alpine`, `npm install`, `npm run build`,
  installation de `serve`, port 3000, service du dossier `dist`.
- Un `.dockerignore` exclut `node_modules` (et `package-lock.json` côté client,
  car il a été généré sous Windows et manque le binaire natif de rolldown pour Linux).

Construction des images :

    docker build -t mern-server ./Server
    docker build -t mern-client ./Client

## Étape 3 : Réseau

    docker network create mern-network

## Étape 4 : MongoDB

    docker run -d --name mongodb --network mern-network mongo

## Étape 5 : Serveur et client

    docker run -d --name server --network mern-network -p 9000:9000 mern-server
    docker run -d --name client --network mern-network -p 3000:3000 mern-client

## Étape 6 : Docker Compose

    docker compose up --build

Trois services : `mongodb`, `server` (dépend de mongodb) et `client`
(dépend de server), sur le réseau bridge `mern-network`.
Les services communiquent par leur nom (le serveur joint `mongodb`).

## Tests

- API : http://localhost:9000/api/health
- Application : http://localhost:3000

[Insérez ici vos captures d'écran : les deux pages et `docker ps`]

## Problèmes rencontrés

- Casse des dossiers (`Client`/`Server`) sous Linux.
- Problème de lockfile Windows pour le client, résolu avec `.dockerignore`.
