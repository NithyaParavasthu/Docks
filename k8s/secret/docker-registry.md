# Docker registry

## with username and passward
```
kubectl create secret docker-registry <name> \
 --docker-server=<server> \
 --docker-username=<repo> \
 --docker-password=<pass of the repo> \
 --docker-email=<mail id>
```

## With auth token
```
kubectl create secret docker-registry <name> \
 --docker-server=<server name> \
 --docker-username=oauth2accesstoken \
 --docker-password=<token> 
```

## With json key
```
kubectl create secret docker-registry <name> \
 --docker-server=<server> \
 --docker-username=_json_key \
 --docker-password="$(cat ~/json-key-file.json)" 
```

### servers
- index.docker.io/v1/
- asia.gcr.io
- eu.gcr.io
- gcr.io
- marketplace.gcr.io
- northamerica-northeast1-docker.pkg.dev
- us-central1-docker.pkg.dev
- us-east1-docker.pkg.dev
- us.gcr.io
- so on

