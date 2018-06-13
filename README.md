## Custom RHEL image to Azure

#### CREATE SERVICE PRINCIPAL IN AZURE

```
az login
az account set --subscription <subscription_name>
az account show
az ad sp create-for-rbac -n <servicePricipalName> --password <chosen-password> --role contributor --scopes /subscriptions/<subscriptionId>
```

This is how the output looks like  (save the output in order to use its values during the image creation process)
```
{
  "appId": "aaxxx746-5xx2-4xxb-8xxa-648xxxxxx99a",
  "displayName": "<servicePricipalName>",
  "name": "http://<servicePricipalName>",
  "password": "blablabla",
  "tenant": "b0xxxx3f-8xx5-4xxd-bxxe-283xxxxxx237"
}
```

#### GENERATE SSH KEYS (no passphrase)
```
ssh-keygen -t rsa
```

#### CREATE KEY VAULT TO STORE SSH PRIVATE KEY
```
az group create -n <resourceGroupName> -l 'West Europe'
az keyvault create -n <keyVaultName> -g <resourceGroupName> -l 'West Europe' --enabled-for-template-deployment true
az keyvault secret set --vault-name <keyVaultName> -n <secretName> --file ~/temp/id_rsa
```

#### INSTALL PACKER
```
wget -O packer_1.2.1_linux_amd64.zip https://releases.hashicorp.com/packer/1.2.1/packer_1.2.1_linux_amd64.zip?_ga=2.5616755.1754411308.1519853768-856854198.1519853768
sudo unzip packer_1.2.1_linux_amd64.zip -d /usr/local/packerio
sudo ln -s /usr/local/packerio/packer packer
sudo ln -s /usr/local/packerio/packer packerio
```

#### CREATE A STORAGE ACCOUNT AND A CONTAINER TO UPLOAD THE VHD
```
az group create -n <resourceGroupName> -l 'West Europe'
az storage account create --sku Premium_LRS --resource-group <resourceGroupName> --name <storageAccountName>
```

#### Modify azure_base.json

Change parameters to reflect your values.

To create VHD and upload to the storage account:
`packerio build rhel.json`
