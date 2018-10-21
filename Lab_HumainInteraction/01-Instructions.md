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

- Starter -> **HttpAsync_Starter**
- Orchestrator -> **HttpAsyn_Orchestrator**
- Activity -> **HttpAsync_Activity**
- Status -> **HttpAsync_Status**

Utiliser les commandes suivantes :

```bash
func new --name HumanInteraction_Starter --template "Durable Functions HTTP starter" --csx
func new --name HumanInteraction_Orchestrator --template "Durable Functions orchestrator" --csx
func new --name HumanInteraction_Activity --template "Durable Functions activity" --csx
func new --name HumanInteraction_Status --template "Durable Functions HTTP starter" --csx
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
On va mettre à jour notre code (**run.csx**).
Nous allons de spécifier le nom de notre activité et supprimer le paramètre dont nous n'avons plus besoin.
Nous allons également définir une réponse contenant l'URL de statut du résultat.
Nous aurons donc un code comme ci-dessous :

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"
#r "Microsoft.Extensions.Logging"

#r "Microsoft.Azure.WebJobs.Extensions.Http"

using System.Net;
using System.Net.Http.Headers;
using Microsoft.AspNetCore.Mvc;

private const string ORCHESTRATOR_FUNCTION_NAME = "HumanInteraction_Orchestrator";

public static async Task<IActionResult> Run(
    HttpRequest req,
    DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync(ORCHESTRATOR_FUNCTION_NAME, null);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

Comme nous avons changé la définition de notre function, en enlevant un paramètre, nous allons maintenant dans le fichier **function.json** afin de modifier la route pour la remplacer par **orchestrators/humaninteraction**.


## Mise à jour de notre fonction HumanInteraction_Orchestrator

Cette fonction a pour rôle d'orchestrer nos différentes activités en attendant notamment une interaction humaine.
La méthode **WaitForExternalEvent** permet à notre orchestrateur d'attendre un évenemnt externe que nous nommerons **ApprovalEvent**.
Pour cela, modifions le code la fonction **HumanInteraction_Orchestrator**.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task<List<string>> Run(DurableOrchestrationContext context)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        bool approved = await ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        log.LogWarning("Request approved");
        // ...More process
    }
}
```

## Lancer votre application

Exécutez la commande suivante :

```bash
func host start
```

_En cas d'erreur, référez vous à la doc [01-CoreTools.md](../01-CoreTools.md) qui vous aidera à configurer votre environnement._

Vous devriez avoir un résultat similaire à celui-ci : 
![](../assets/HumanInteraction-01-Start.png)

Rendez-vous sur l'url indiqué par les tools, vous devriez avoir le résultat suivant : 
![](../assets/HumanInteraction-02-WebStart.png)

Vous pouvez voir l'état de votre job en allant sur la requête de Status: 
![](../assets/HumanInteraction-03-WebStatus.png)