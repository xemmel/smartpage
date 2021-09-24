## Smartpage
## C# Sessions


## Table of Content
1. [Tasks](#tasks)
1. [Azure 101](#azure-101)




## Tasks

### Wrap a syncronic method with a Task

```csharp

public int Add(int x, int y)
{
    Thread.Sleep(3000);
    return (x+y);
}

public Task<int> AddAsync(int x, int y)
{
    return Task<int>.Run(() => Add(x, y));
}

```

### Cancellation Token

> All methods returning a Task should have an optional *CancellationToken* input parameter.

```csharp
public async Task<string> GetMessageAsync(string messageId, CancellationToken = default)
{
    var result = await _myHelper
                            .GetMessageAsync(
                                    messageId: messageId,
                                    cancellationToken: cancellationToken);
    return result;
}



```


[Back to top](#table-of-content)


## Azure 101

### Prerequisites

> Install chocolatey (powershell as administrator)

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

> Install **Azure CLI**

```powershell
choco install azure-cli -y
```

> Upgrade choco packages

```powershell
choco upgrade all
```

### Azure Cli commands

```powershell
## Login
az login

## Check current subscription
az account show

## Set subscription (if not pointing at correct)
az account set -s [subscriptionId]

## List all Resource Groups
az group list -o table

## List Resources in a Resource Group
az resource list -g [Resource Group Name] -o table

## Create Storage Account
az storage account create --name [Storage Account Name] --resource-group [Resource Group Name] --location westeurope --access-tier hot --kind StorageV2 --sku Standard_LRS

## Export Resource Group as ARM template
az group export -n [Resource Group Name]

## Deploy arm template
 az deployment group create -g [Resource Group Name] -f [arm template json file location]


```


### Arm template

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
	  "name" : { "type" : "string" },
      "location" : { "type" : "string", "defaultValue" : "[resourceGroup().location]"}
  },
  "resources" : [
    {
      "apiVersion": "2021-04-01",
      "kind": "StorageV2",
      "location": "[parameters('location')]",
      "name": "[parameters('name')]",
      "properties": {
        "accessTier": "Hot",
        "encryption": {
          "keySource": "Microsoft.Storage",
          "services": {
            "blob": {
              "enabled": true,
              "keyType": "Account"
            },
            "file": {
              "enabled": true,
              "keyType": "Account"
            }
          }
        },
        "networkAcls": {
          "bypass": "AzureServices",
          "defaultAction": "Allow",
          "ipRules": [],
          "virtualNetworkRules": []
        },
        "supportsHttpsTrafficOnly": true
      },
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "type": "Microsoft.Storage/storageAccounts"
    }
  ]
  
 }

```
[Back to top](#table-of-content)
