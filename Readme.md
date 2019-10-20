# nginx-proxy-docker
This is a proxy setup in nginx that will forward to the internal docker dns based on the incoming url's domain or subdomain. 
Supports http and https.

## Create a Network
```
docker network create --attachable -d overlay appnet
```

## Build the Image
```
docker build -t nginx_proxy .
```

## Create Secrets
Create a certificate pair and add them to the swarm as secrets.

```
docker secret create localhost.crt secrets\localhost.crt
```

```
docker secret create localhost.key secrets\localhost.key
```

## Deploy to Swarm
```
docker stack deploy --compose-file .\docker-compose.yml proxy
```

## Make Other Containers Available
To make another container available setup its docker-compose.yml like the following:

```
version: '3'

services:
  anotherdockerapp:
    image: anotherdockerapp
    build:
      context: .
      dockerfile: AnotherDockerApp/Dockerfile
    networks:
      - appnet

networks:
  appnet:
    external: true
```
This will attach the container to the appnet network. You should be able to access it by visiting anotherdockerapp.example.com, which will forward to the container created for this service.