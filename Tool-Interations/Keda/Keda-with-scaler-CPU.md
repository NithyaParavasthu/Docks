# Autoscaling An Application Using Keda with CPU as Scaller

## Prerequisites
- Kubernetes cluster
- Ingress controller
- A sample application
- keda installed.

## Setup
### Step 1. 
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
   2. Add this following lines
   ```
   ingress:
     className : nginx
     hosts: botic.10.0.2.15.nip.io
   ```
- 
