# Docker 
This Docker contains a customisation of GraphDB which is able to parse Conjectures approach. Additionally, the GraphDB instance contains all datasets used to perform the efficiency assessment of existing reification methods.   

## DOCKER LOGIN
docker login

## BUILD THE DOCKER
docker build -t my-custom-graphdb:1.0 .

## RUN THE DOCKER 
docker run -p 127.0.0.1:7200:7200 --name graphdb-custom -t my-custom-graphdb:1.0

## INSPECT THE DOCKER
docker exec -it graphdb-custom /bin/bash
