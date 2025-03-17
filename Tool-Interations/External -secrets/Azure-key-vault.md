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
- Once the key vault is created. Go to the created key vault.
- Go to the Access control (IAM), Then click on the **+ Add** button and select **Add role assignment** option.
- Then select the **Key vault Administrator** role and click on **Next**, Then click on **+ select members** and select your name and click on the **review and creat**.
#### - Generate Secrets
- Go back to the key vault, in the left column, click on the **Objects** option then click on the **secrets** option.
- Then click on **+ Generate/Import**, Then give the secret key name and value. and click on the Create button (Repeat it for all the secrets).
#### - Create a Service principle.
- Go to the Home page of the portal and search for the **App registry**. click on **+ New registration**
- Give a meaningful name and **register**.
- Crant access to the key vault as above.
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

## Step 3: Grant federated Credentials for the SP
#### Check the SP and URI with the following commands
```
az ad app list --query "[].{DisplayName:displayName, AppId:appId, ObjectId:objectId}" -o table
az aks show --resource-group Nithya-Practice --name nithya-pracs-dev --query "oidcIssuerProfile.issuerUrl" -o tsv
```
Save the Outputs for the Future Use
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

