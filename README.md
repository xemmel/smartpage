## Smartpage
## C# Sessions


## Table of Content
1. [Tasks](#tasks)
2. [IEnum](#ienum)
3. [Var](#var)
10. [Azure 101](#azure-101)





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

## IEnum


### Use yield

> If returning a collection where the consumer may og may not need to process all elements, the return type *IEnumerable* should be used together with a **yield** pattern

```csharp

 public static IEnumerable<int> GetSlowNumbersAsIEnum(int count)
        {
             for (int i = 0; i < count; i++)
            {
                var number = GetSlowNumber(x: i);
                yield return number;
            }
        }

```

### Return what you got
> The return type of a **method** should be whatever the method has

```csharp

//Wrong
//Although totally valid, the consumer might actually need a List and call .ToList()
//even though not needed.

public static IEnumerable<int> GetNumbers()
{
    List<int> result = new List<int>();
    result.AddRange(....);
    return result;
}

//Correct
public static List<int> GetNumbers()
{
    List<int> result = new List<int>();
    result.AddRange(....);
    return result;
}

```

### Only ask for what you need when working with parameters

```csharp

//Wrong
//Why require a List<string> when Array (string[]) and all other types that implement
//IEnumerable<string> is sufficient?
public static void ProcessElements(List<string> elements)
{
    foreach(var element in elements)
    {
      Process(element);
    }
}

//Correct
public static void ProcessElements(IEnumerable<string> elements)
{
    foreach(var element in elements)
    {
      Process(element);
    }
}

//Correct if other behaviors are needed
//If we know that we also need to do operations that IEnumerable will not supply
//We use the minimal required interface
public static void ProcessElements(ICollection<string> elements)
{
    elements.Add("test");
    foreach(var element in elements)
    {
      Process(element);
    }
}


```

[Back to top](#table-of-content)

## Var

> Use var if there is an explicit human readable way to interpret what the type is

```csharp

var person = GetPerson(); //Ok
var i = 10; //Bad, don't let the compiler make decisions for you;
int i = 10; //Ok

var personHandler = new PersonHandler(); //Be carefull, if PersonHandler implements an interface, it might be what we want instead
IPersonHandler personHandler = new PersonHandler(); //Ok



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
> Install Functions CLI

```bash
choco install azure-functions-core-tools
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
