```
  392  az ad app create --display-name "Mytest-app"
  393  az ad app list --display-name "Mytest-app" --query "[].{AppID:appId, TenantID:publisherDomain}"
  394  az ad app credential reset --id 2714272e-c5b5-4fb3-9e27-c1c87e294018 --display-name "MyAppSecret"
```
