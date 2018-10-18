# Azure Function Core Tools 

## Installer Azure Function Core Tools

Depuis VSCode, via l'invite de commandes (CTRL+SHIFT+P), lancer la commande **Azure Functions: Install or Update Azure Function Core Tools**
![](/assets/00-InstallCoreTools.png)


## Initialisation d'un nouveau projet

Créer un nouveau dossier, puis l'ouvrir dans Visual Studio Code.

Via le terminal (_CTRL+SHIFT+ù_), lancer la commande suivante

```bash
func init --csx
```

## Ajout de l'extension DurableFunction 

```bash
func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.6.2 --csx
```

## Création d'une Durable Function

### Starter

```bash
func new --name <FunctionName_Starter> --template "Durable Functions HTTP starter" -csx
```

### Orchestrator

```bash
func new --name <FunctionName_Orchestrator> --template "Durable Functions orchestrator" --csx
```

### Activity

```bash
func new --name <FunctionName_Activity> --template "Durable Functions activity" --csx
```

## Exécuter en local nos Functions

### Compte de stockage
Mettre à jour votre fichier **local.settings.json** afin de renseigner votre compte de stockage utilisé pour éxecuter vos fonctions. 
Sous Windows, si vous avez un storage emulator qui fonctionne il faut le lancer et remplacer la valeur dans notre fichier de config comme ci-dessous : 
```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "dotnet",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true"
  }
}
```

Dans les autres cas, le plus simple est de créer un compte de stockage sur Azure, et de renseigner la chaine de connexion dans le fichier de configuration comme ci-dessous:
```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "dotnet",
    "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=mykey"
  }
}
```

### Let's go 

Lancez la commande suivante : 

```bash
func host start
```
Cette commande vous listera les différents points d'entrées de vos fonctions, par défaut, l'url commence comme ceci **http://localhost:7071/api/**
