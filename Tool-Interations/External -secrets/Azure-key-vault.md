# External secret with Azure Key Vault.

## Prerequisites
- Kubernetes Cluster
- Helm
- kubectl

## Step 1: Creating and configuring AKV 
**Chose between GUI and CLI.** 
### Gui
#### - Create key vault
- Login to your Azure portal.
- In the navigation bar, search for '**key**'. Select the '**Key vault**' in the options.
- Click on '**create the key vault**'
- In the '**Basic**' column, give the '**Resource group**' and '**Key vault name**'. Then click on next
- Leave everything else on default and click on the **create** button
#### - Grant Access
- Once the key vault is created. You can go to the created key vault.
- Go to the Access control (IAM), then click on the **+ Add** button and select the **Add role assignment** option.
- Then select the **Key vault Administrator** role and click on **Next**, Then click on **+ select members** and select your name and click on the **review and creat**.
#### - Generate Secrets
- Go back to the key vault, in the left column, click on the **Objects** option, then click on the **secrets** option.
- Then click on **+ Generate/Import**, Then give the secret key name and value. Click on the Create button (Repeat it for all the secrets).
#### - Create a Service principal.
- Go to the Home page of the portal and search for the **App registry**. click on **+ New registration**
- Give a meaningful name and **register**.
- Grant access to the key vault as above.
### CLI
#### - Create a Key vault
```
az keyvault create --name <YOUR_KEYVAULT_NAME> --resource-group <YOUR_RESOURCE_GROUP> 
```
#### - Generate Secrets
```
az keyvault secret set --vault-name <YOUR_KEYVAULT_NAME> --name "app-db-password" --value "<passwprd>"
az keyvault secret set --vault-name <YOUR_KEYVAULT_NAME> --name "app-db-user" --value "<user>"
```
#### - Create a Service Principle and grant access. 
```
az ad sp create-for-rbac --name <YOUR_SP_NAME> --role "Key Vault Administrator"
```
save the output for future use.

## Step 2: Create a SA
#### create a service account with the following configuration and apply it. 
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: workload-identity-sa
  annotations:
    azure.workload.identity/client-id: 6d21a1ec-ea27-44e9-8bdd-acba42944fe2
    azure.workload.identity/tenant-id: 042d269c-32ba-4c55-bc0c-2e2edf8a8b1d
```
```
kubectl apply -f service-account.yml
```
## Step 3: Grant federated Credentials for the SP
#### Check the SP and URI with the following commands
```
az ad app list --query "[].{DisplayName:displayName, AppId:appId, ObjectId:objectId}" -o table
az aks show --resource-group Nithya-Practice --name nithya-pracs-dev --query "oidcIssuerProfile.issuerUrl" -o tsv
```
Save the Outputs for Future Use
#### Generate Federated Credentials with the following command.
```
az ad app federated-credential create   --id <App ID from the above command>   --parameters '{
           "name": "<name-federated-credential>",
           "issuer": "https://<URL from the above command>",
           "subject": "system:serviceaccount:default:<Service account name>",
           "audiences": ["api://AzureADTokenExchange"]  
             }'
```

## Step 4: Install an external secret operator into the cluster.
#### Install the Operator with these three commands into the cluster.
```
helm repo add external-secrets https://charts.external-secrets.io
helm repo update
helm install external-secrets    external-secrets/external-secrets     -n external-secrets     --create-namespace
```
#### Verify the installation
```
kubectl get po -n external-secrets
```
## Step 5: Create the secret YAMLs.
#### Create a file that contains secret store details as below and called `secret-store.yaml`
```
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: azure-store
spec:
  provider:
    azurekv:
      authType: WorkloadIdentity
      vaultUrl: "https://nithya-prac-vault.vault.azure.net"
      serviceAccountRef:
        name: workload-identity-sa
```
#### Create a file that contains secrets in it as shown below, with the name "external-secrets.yaml"
```
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: wordpress-secrets
spec:
  refreshInterval: "15s"
  secretStoreRef:
    name: azure-store
    kind: SecretStore
  target:
    name: wordpress-secret
  data:
    - secretKey: mariadb-user
      remoteRef:
        key: secret/mariadb-user
    - secretKey: mariadb-password
      remoteRef:
        key: secret/mariadb-password        
    - secretKey: mariadb-root-password
      remoteRef:
        key: secret/mariadb-root-password
```
#### Apply both these yamls with the following command
```
kubectl apply -f secret-store.yaml
kubectl apply -f external-secret.yaml
```
#### Verify the creation of the secrets with the following commands
```
kubectl get secretstores.external-secrets.io
kubectl get externalsecrets.external-secrets.io
kubectl get secrets
```
## Step 6: Install the WordPress application
#### Edit the values file of WordPress with the secrets mentioned in the external secrets. (for reference)
```
mariadb:
  auth:
    existingSecret: wordpress-secret
```
#### Install the application with the following commands
```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install wordpress bitnami/wordpress -f ~/Downloads/values-wordpress.yaml
```

## Step 7: Verify the installation 
#### Verify the application and secret configuration by following the commands
```
kubectl get po
kubectl get ing 
```
#### check the output with the ingress URL and test the application.

## Step 8: Cleanup
#### Uninstall the WordPress application 
```
helm uninstall wordpress
```
#### Delete the external secret and secret store YAMLs
```
kubectl delete -f secret-store.yaml
kubectl delete -f external-secret.yaml
```
#### Uninstall the external secrets
```
helm uninstall external-secrets -n external-secrets
```
#### Delete the Federated Credentials with the following command
```
az ad app federated-credential delete   --id <App ID from the above command>   --federated-credential-id "<name-federated-credential>"
```
#### delete the key vault 
#### Delete App registry
#### Delete the cluster. 
