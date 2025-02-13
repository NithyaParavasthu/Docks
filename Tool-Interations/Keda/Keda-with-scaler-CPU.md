# Autoscaling An Application Using Keda with CPU as Scaller

## Prerequisites
- Kubernetes cluster
- Ingress controller
- A sample application
- keda installed.

## Setup
### Step 1. Setup the application in your cluster
- Once your cluster is up and running, you can install a sample application to test the auto-scaling.
- Here I am using a sample micro-service application by Google called `Online Boutique`.
- Clone the repo with the following commands
```
git clone git@github.com:GoogleCloudPlatform/microservices-demo.git
```
- Now edit the values,yaml, and add the following lines.
  1.  Edit the below lines
   ```
     externalService: false   # near frontend configurations

      create: false  # near loadGenerator configuration
   ```
   2. Add the following lines
   ```
   ingress:
     className : nginx
     hosts: botic.10.0.2.15.nip.io
   ```
- dcc
### Step 2: Setup Keda
- Now you have your application running. let's configure a keda.yaml
- Create a keda.yaml as below.
```
apiVersion: keda.sh/v1alpha1   # Api version of Keda
kind: ScaledObject             # Kind
metadata:
  name: botic-scaleobject      # any name or application name
  namespace: default           # Namespace where the application is deployed
spec:
  scaleTargetRef:
    name: frontend             # Name of the service to be scaled 
  pollingInterval: 10          # Interval to scale up the pods
  cooldownPeriod: 100          # Time spane to cool down and scale down the pods.
  maxReplicaCount: 5           # Max pods to create
  minReplicaCount: 1           # Min pods to maintain
  triggers:                    # When to trigger the Auto scalling
  - type: cpu                  # Type of scaler, using CPU for this example
    metricType: Utilization # Allowed types are 'Utilization' or 'AverageValue' 
    metadata:
      value: "70"              # cpu percentage to trigger the autoscaling 
```
- Now apply this file with the `kubectl` apply command.
```
kubectl apply -f keda.yaml
```
- You will see an object called scaled object is created. You can also see an HPA resource is running in the cluster with the following command.
```
kubectl get all,scaledobjects.keda.sh 
```

## Testing.
- Generate load with K6. and test whether the load is increasing the pods by CPU.
- You can see example of k6 with here
  
## Cleanup

- Clean up the environment by uninstalling the application with the following command.
  ```
  helm uninstall keda
  ```
  ```
  helm uninstall boutique
  ```

