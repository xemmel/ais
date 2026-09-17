### First agenda

```

AIS (Azure Integration Services)

Morten la Cour
integration-it.com
flowgrait.io


- BizTalk
- Azure (Dev, Adm, Security, AI, Integration)
- Containers/Kubernetes


- Hvem er du?
- Hvorfor er du her?
- Erfaring med: 
  - Azure
  - EntraId
  - C#




```

### Notes

```


GitHub.com/xemmel/integrationit

Morten la Cour
lacour@gmail.com



## Naming

portal.azure.com

entra: entra.microsoft.com


Resource Group

ResourceGroupId (global unique)

/SubId/RgName


Resource:

ResourceId (global unique)

/SubId/RgName/Type(vm,webapp)/ResourceName




Storage Account!!!

  - 0 $


## Azure Functions

   Azure Web App -> Predefined code


  Azure Function App = Azure Resource (Web App)
     Function (Trigger: Http, Timer, Resource)
     Function
     Function (code) 
  Web App -> App Service Plan (*)
     Storage Account (1-1)




winget install Microsoft.Azure.FunctionsCoreTools

func init myfunctionapp --worker-runtime dotnet-isolated

func new -t httptrigger -n myhttptrigger





az storage blob upload `
  --account-name eventgridmlc `
  --container-name messages `
  --file message.json `
  --overwrite
;


az storage blob upload `
  --account-name flowgraitdevst `
  --container-name maps `
  --file customer_to_internal.jsonata `
  --name teknodemo/customer_to_internal.jsonata `
  --overwrite `
  --subscription 5a05f537-a707-4bc8-99bc-0ad16de9d511
;


Managed_Identity:

"StorageQueueConnection__queueServiceUri": "https://mlclocaltest.queue.core.windows.net/"



Api Management Service (Azure Resource)
   - Data endpoint (Gateway)

       - API  -> API
          - Operation (GET|POST, /id/{id})

       - API  -> API

API MANAGEMENT


API    API    API 


GET https://mlcapim.azure-api.net/webhook
  API
    Operation

Consumption

- Logic App  (Trigger, Actions) (workflow) (resour)
- Logic App

Standard

- Logic App (Azure Resource)
  - Workflow1 (Trigger, Actions) -> Consum Logic
  - Workflow2


@outputs('HTTP')['body']

@body('HTTP')


$ENV:AZURE_TENANT_ID = "ffff";
$ENV:AZURE_CLIENT_ID = "85751572-46f7-437f-bb02-9d1f83296d11";
$ENV:AZURE_CLIENT_SECRET = "....";


### Message Queue

Storage Account Queue $
 - message size 75K
 - Queue TB

Service Bus Queue $$
 - message size 256K
 - Queue GB
 - Sessions!
 - Duplicate Detection
 - Topics (!!)
 - Enqueue Time
 - Queue Forwarding


Service Bus Namespace (Resource)
  - Queue
  - Queue
  - Topics
  - 


## CLI

$location="swedencentral"
$rgName = "rg-ais-mlc-clitest"


## az | select-string group

### Create a Resource Group
az group create `
  --location $location `
  --name $rgName 

`

## az | select-string storage

### Create a storage account in RG
az storage account create `
  --resource-group $rgName `
  --name teknomlcdemo `
  --location $location



az group list | ConvertFrom-Json | out-gridview -PassThru | ForEach-Object {az group delete --name $_.name --no-wait --yes }






az group delete --name $rgName --yes --no-wait


az login --use-device-code

az group list -o table


Regions

Region Pairs

- Same Geo-polic Zone


LRS (Local) -> 1 data center (MS Choose) -> 3 disk
GRS (Geo)   -> 1  do.                              -> async rep "pair"




-> ENTRA Login

-> GET_TOKEN  -> client

client -> TOKEN -> API (azure)

HTTP 
  Headers
    Authorization: Bearer [token]
 

asp  id 1

webapp
  - aspid: 1



## Blobs

- Container = Folder
- Blob = file



HTTP Basic File System



```

### Deploy webapp

```powershell


$rgName = "rg-ais-mlc-webcodedemo";
$webAppName = "mlcwebcode";

Write-Host("Removing existing published files");

if (Test-Path .\publish\) {
    Get-Item .\publish\ | Remove-Item -Recurse -Force
}

if (Test-Path .\publish.zip) {
    Get-Item .\publish.zip | Remove-Item -Recurse -Force
}


Write-Host("Build/Publish .NET code");

dotnet publish -c Release -o ./publish

Write-Host("Zip..");

cd .\publish
Compress-Archive -Force -Path * -DestinationPath ..\publish.zip
cd ..

 az webapp deploy `
        --resource-group $rgName `
        --name $webAppName `
        --src-path ./publish.zip `
        --slot pre
;


```

### Kusto Queries

```

AppRequests
| where TimeGenerated > ago(1h)
| where Name == 'GET api/calculator'
| project TimeGenerated, Success, DurationMs,ClientCity, OperationId
| order by TimeGenerated desc


AppRequests
| where TimeGenerated > ago(1h)
| where Name == 'GET api/calculator'
| summarize count() by tostring(Success)
| render piechart 


AppRequests
| where TimeGenerated > ago(1h)
| where Name == 'GET api/calculator'
| summarize count() by bin(TimeGenerated,5m), tostring(Success)
| render barchart 

```