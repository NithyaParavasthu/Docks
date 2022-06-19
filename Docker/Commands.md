# Commands

## Build
```
docker build -t <name of the registrey>/<name of the image>:<version> <path/of/the/Dockerfile>
```

## Run
```
docker run -it <name of the registrey>/<name of the image>:<version>
```
### Run with port number
```
docker run -it -p <port-no>:<port-no> <name of the registrey>/<name of the image>:<version>
```
### Run in baground
```
docker run -itd <name of the registrey>/<name of the image>:<version>
```
### Run with a specific name
```
docker run -itd --name <name of the container>  <name of the registrey>/<name of the image>:<version>
```

## Check the running containers
```
docker ps
```
### to see all the containers
```
docker ps -a
```

## Check the images in the local
```
docker images
```

## Pulling the image
```
docker pull <name of the registrey>/<name of the image>:<version>
```

## Inspect 
```
docker inspect <container id> 
```
``` or ```
```
docker inspect <container name>
```

## Logs
```
docker logs <con ID>
```
```or ```
```
docker logs <con name>
```

## Deleting Containers
```
docker rm <container id>
```

## Deleting images
```
docker rmi <image-id>
```

## Entering into the container
```
docker exec -it <container name> /bin/bash
```
