# Commands of eks

## Download eksctl
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```
### move the folder 
```
sudo mv /tmp/eksctl /usr/local/bin
```
### check version
```
eksctl version
```

## Create cluster with Managed nodes 
```
eksctl create cluster --name eks-clusterr --region us-east-1
```
## Create cluster with fargate
```
eksctl create cluster --name my-cluster --region region-code --fargate
```

## Kubeconfig file
```
aws eks update-kubeconfig --region us-east-1 --name eks-clusterr
```
>  if this command does't work, try re installing aws cli and restart your terminal 

## Delete cluster
```
eksctl delete cluster --name eks-clusterr --region us-east-1
```


