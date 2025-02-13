# Installation 

- Add Helm repo
```
helm repo add kedacore https://kedacore.github.io/charts
```
- Update Helm Repo
```
helm repo update
```
- Install the chart with the following command
```
helm install keda kedacore/keda --namespace keda --create-namespace
```
