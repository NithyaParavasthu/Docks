# Network 

## To create a network 
```
 docker network create <network name>
```

## To create a netwrok with driver 
```
 docker network create -d <driver> <network name>
```
### driver options are 
- bridge
- overlay
- host

## To create a network in particular subnet
```
docker network create --driver=<driver> --subnet=<sub net range> <network name>
```

## To connect network to the container 
```
docker network connect <NETWORK CONTAINER>
```

## To connect nwtwork to the run time
```
docker run -itd --network=<network name> <image name>
```

## To list the networks 
```
docker network ls
```

## To inspect the network
```
docker network inspect <network name>
```

## To remove the network 
```
docker network rm <network name>
```
