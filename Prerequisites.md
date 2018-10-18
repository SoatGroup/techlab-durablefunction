# Visual Studio Code
[https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)

# .Net Core
- .Net Core 2.1 : [https://www.microsoft.com/net/download/dotnet-core/2.1](https://www.microsoft.com/net/download/dotnet-core/2.1)


Via chocolatey : 
Installation de choco (vous devez ouvrir votre terminal en mode administrator):

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Installation de la bonne version de .Net Core
```bash
choco install dotnetcore-sdk 
```

Si vous avez déja  installé une version antérieure à la v2.1.401, il vous faudra ajouter '--force' à la commande précédente, ou utiliser la commande d'upgrade.

# Visual Studio Code Extensions
- Azure Function tools : [https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- C# : [https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)

Pour installer les extensions vous pouvez utiliser les liens ou faire une recherche directement depuis VSCode.
N'oubliez pas de redémarrer VSCode après l'installation.


# Storage Emulator
[https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator)

# Compte Azure
 [https://azure.microsoft.com/en-us/free/](https://azure.microsoft.com/en-us/free/)
