# Human Interaction Sample

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

## Créer les différentes fonctions nécessaires

Fonctions nécessaires :

- Starter -> **HumanInteraction_Starter**
- Request Orchestrator -> **HumanInteraction_RequestOrchestrator**
- Approval Starter -> **HumanInteraction_ApprovalStarter**

Utiliser les commandes suivantes :

```bash
func new --name HumanInteraction_Starter --template "Durable Functions HTTP starter" --csx
func new --name HumanInteraction_RequestOrchestrator --template "Durable Functions orchestrator" --csx
func new --name HumanInteraction_ApprovalStarter --template "Durable Functions HTTP starter" --csx
```

## Configuration de Azure Storage Emulator

Mettez à jour le fichier local.settings.json avec le contenu suivant :

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "dotnet",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true"
  }
}
```

## Mise à jour de notre fonction HumanInteraction_Starter

On va mettre à jour notre code (**run.csx**), afin de spécifier le nom de notre orchestrateur, et supprimer les paramètres dont nous n'avons pas besoin. Nous aurons donc un code comme ci-dessous :


```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"
#r "Newtonsoft.Json"

using System.Net;

private const string ORCHESTRATOR_FUNCTION_NAME = "HumanInteraction_RequestOrchestrator";
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync(ORCHESTRATOR_FUNCTION_NAME, null);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

Comme nous avons changé la définition de notre function, en enlevant un paramètre, nous allons maintenant dans le fichier **function.json** afin de modifier la route pour la remplacer par **orchestrators/request**

## Mise à jour de notre fonction HumanInteraction_RequestOrchestrator

Cette fonction a pour rôle d'orchestrer nos différentes activités en attendant notamment une interaction humaine.
La méthode **WaitForExternalEvent** permet à notre orchestrateur d'attendre un évenemnt externe que nous nommerons **ApprovalEvent**.
Pour cela, modifions le code la fonction **HumanInteraction_RequestOrchestrator**.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task<bool> Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    // ... Request Approval code
    bool approved = await context.WaitForExternalEvent<bool>("ApprovalEvent");
    log.LogWarning($"Request approval status is: {approved}");
    // ...More process
    return approved;
}
```

## Mise à jour de notre fonction HumanInteraction_ApprovalStarter

Cette fonction a pour rôle de simuler le processus d'approbation d'une requête.
La méthode **RaiseEventAsync** permet de déclencher l'évènement tant attendu pour une instance particulière.
Pour cela, modifions le code la fonction **HumanInteraction_ApprovalStarter**.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"
#r "Newtonsoft.Json"

using System.Net;

public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string instanceId,
    ILogger log)
{
    bool isApproved = true;
    await starter.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

Comme nous avons changé la définition de notre function, en modifiant un paramètre, nous allons maintenant dans le fichier **function.json** afin de modifier la route pour la remplacer par **orchestrators/approve/{instanceId}**

## Lancer votre application

Exécutez la commande suivante :

```bash
func host start
```

Ouvrez ensuite l'URL **http://localhost:7071/api/humaninteraction/request** dans votre navigateur.

Vous pouvez vérifiez le statut **Running** de votre requête en ouvrant la requête de Status ou en regardant les logs.

Ouvrez enfin l'URL **http://localhost:7071/api/humaninteraction/approve/9b1a7819013d4d4096cee9f855f97cbd**.

En regardant les logs ou en rafraichissant la requête de Status vous devriez voir que le traitement à poursuivi.

_En cas d'erreur, référez vous à la doc [01-CoreTools.md](../01-CoreTools.md) qui vous aidera à configurer votre environnement._

Vous devriez avoir un résultat similaire à celui-ci : 
![](../assets/HumanInteraction-01-Start.png)

Rendez-vous sur l'url indiqué par les tools, vous devriez avoir le résultat suivant : 
![](../assets/HumanInteraction-02-WebStart.png)

Vous pouvez voir l'état de votre job en allant sur la requête de Status: 
![](../assets/HumanInteraction-03-WebStatus.png)
