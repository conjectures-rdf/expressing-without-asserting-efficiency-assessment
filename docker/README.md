# Docker WORK IN PROGRESS
This Docker contains a customisation of GraphDB which can parse Conjectures approach. Additionally, the GraphDB instance contains all datasets used to perform the efficiency assessment of existing reification methods.   

We produced several docker depending on the testing instance you want to run.
- Docker **ready to run**: it contains D1 and D2 datasets excluding singleton
- Docker **slow to run**: it contains D1, D2 and D3


## DOCKER ORGANISATION

```
/EWA-efficiency-docker
|-- Dockerfile
|-- entrypoint.sh
|-- repositories/
|   |-- WORK IN PROGRESS
|-- conjectures-extension-graphdb/
|   |-- rdf4j-queryparser-sparql-4.2.0.jar
|   |-- rdf4j-rio-trig-4.2.0.jar
|-- README.txt
```

## BUILD THE DOCKER
docker build -t my-custom-graphdb:1.0 .

## RUN THE DOCKER 
docker run -p 127.0.0.1:7200:7200 --name graphdb-custom -t my-custom-graphdb:1.0

## INSPECT THE DOCKER
docker exec -it graphdb-custom /bin/bash
