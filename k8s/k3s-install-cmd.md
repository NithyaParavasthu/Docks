# k3s install cmd

## Curl the installation
```
curl -sfL https://get.k3s.io | sh -
```
### for a specific version
```
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=<version> sh -
```
### To Install without traefik
```
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable=traefik" sh -
```
### To Install with a specific IP
```
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable=traefik --node-ip=<Your external IP address>" sh -
```
### To Install with a specific Name
```
curl -sfL https://get.k3s.io | INSTALL_K3S_NAME=<name> sh -
```

## Export the config file
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

## Change the mode of the file 
```
sudo chmod 644 $KUBECONFIG
```

## To directly access the cluster without any further commands 
```
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" INSTALL_K3S_EXEC="server --disable=traefik" sh -
```
