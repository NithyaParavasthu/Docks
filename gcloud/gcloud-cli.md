# gcloud cli

## Before you begin
```
sudo apt-get install apt-transport-https ca-certificates gnupg
```

## Add the gcloud CLI distribution URI as a package source
```
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
```

## Import the Google Cloud public key
```
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
```

## Update and install the gcloud CLI
```
sudo apt-get update && sudo apt-get install google-cloud-cli
```

## (Optional) Install additional components
```
sudo apt-get install google-cloud-cli-<additional-component>
```

## Run gcloud init to get started
```
gcloud init
```

## --------------------------------------------------------------------------------

## Download gcloud CLI particular versions 
```
sudo apt-get update && sudo apt-get install google-cloud-cli=<version>
```

## Update gcloud CLI
```
gcloud components update
```
