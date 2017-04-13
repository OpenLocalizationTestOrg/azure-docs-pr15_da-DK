<properties
    pageTitle="Installere en VM ved hjælp af C# og en skabelon for ressourcestyring | Microsoft Azure"
    description="Lær, hvordan du bruger C# og en ressourcestyring skabelon til at installere en Azure VM."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>

# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Installere en Azure virtuelt ved hjælp af C# og en ressourcestyring skabelon

Ved hjælp af grupper og skabeloner, er du i stand til at administrere alle de ressourcer sammen, der understøtter dit program. I denne artikel beskrives, hvordan du bruger Visual Studio og C# konfigurere godkendelse, skal du oprette en skabelon og derefter installere Azure ressourcer ved hjælp af den skabelon, du har oprettet.

Du skal først at sikre, at du har udført disse konfigurationstrin af:

- Installer [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Kontrollere installation af [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) eller [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Få et [godkendelse token](../resource-group-authenticate-service-principal.md)
- Oprette en ressourcegruppe bruger [Azure PowerShell](../resource-group-template-deploy.md), [Azure CLI](../resource-group-template-deploy-cli.md)eller [Azure-portalen](../resource-group-template-deploy-portal.md).

Det tager om 30 minutter for at udføre disse trin.
    
## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Trin 1: Oprette Visual Studio-projekt, skabelonfilen og Parameterfilen

### <a name="create-the-template-file"></a>Oprette skabelonfilen

En skabelon til Azure ressourcestyring gør det muligt for dig at installere og administrere Azure ressourcer sammen. Skabelonen er en JSON beskrivelse af de ressourcer og tilknyttede installation parametre.

I Visual Studio, skal du gøre følgende:

1. Klik på **filer** > **nye** > **Project**.

2. I **skabeloner** > **Visual C#**, Vælg **Console program**, angive navn og placering for projektet, og klik derefter på **OK**.

3. Højreklik på projektnavnet i Solution Explorer skal du klikke på **Tilføj** > **Nyt element**.

4. Klik på Web, Vælg JSON-fil, Angiv *VirtualMachineTemplate.json* for navnet, og klik derefter på **Tilføj**.

5. Tilføje elementet påkrævede skema og elementet påkrævet contentVersion i begyndende og afsluttende parenteser i filen VirtualMachineTemplate.json skal:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Parametre](../resource-group-authoring-templates.md#parameters) er ikke altid påkrævet, men de er en måde at skrive værdier, når skabelonen, der er installeret. Du kan tilføje elementet parametre og de underordnede elementer efter elementet contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

7. [Variabler](../resource-group-authoring-templates.md#variables) kan bruges i en skabelon til at angive værdier, der kan blive ændret ofte eller værdier, der skal oprettes en kombination af parameterværdier. Du kan tilføje elementet variabler efter sektionen parametre:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }

8. [Ressourcer](../resource-group-authoring-templates.md#resources) som den virtuelle maskine, det virtuelle netværk og kontoen lagerplads defineres næste i skabelonen. Du kan tilføje afsnittet ressourcer efter sektionen variabler:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
      
9. Gem skabelonfilen, du har oprettet.

### <a name="create-the-parameters-file"></a>Oprette Parameterfilen

For at angive værdier for ressourceparametrene, der er defineret i skabelonen, skal oprette du en parametre-fil, der indeholder de værdier, der bruges, når skabelonen, der er installeret. I Visual Studio, skal du gøre følgende:

1. Højreklik på projektnavnet i Solution Explorer skal du klikke på **Tilføj** > **Nyt element**.

2. Klik på Web, Vælg JSON-fil, Angiv *Parameters.json* for navnet, og klik derefter på **Tilføj**.

3. Åbn filen parameters.json, og Føj derefter denne JSON indhold:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] I denne artikel opretter en virtuel maskine, der kører en version af Windows Server-operativsystemet. Hvis du vil vide mere om at vælge andre billeder, skal du se [Naviger og vælge Azure virtuelt billeder med Windows PowerShell og Azure CLI](virtual-machines-linux-cli-ps-findimage.md).

4. Gem parameterfilen, du har oprettet.

## <a name="step-2-install-the-libraries"></a>Trin 2: Installer bibliotekerne

NuGet pakker er den nemmeste måde at installere de biblioteker, du vil afslutte dette selvstudium. Du har brug for biblioteket Azure ressource administration og Azure Active Directory Authentication Library for at oprette ressourcerne. For at få disse biblioteker i Visual Studio skal du gøre følgende:

1. Højreklik på projektnavnet i Solution Explorer, skal du klikke på **Administrer NuGet-pakker**, og klik derefter på Gennemse.

2. Type *Active Directory* i søgefeltet, klik på **Installer** til Active Directory Authentication Library-pakken, og følg derefter vejledningen for at installere pakken.

4. Øverst på siden skal du vælge **Medtage foreløbig version**. Type *Microsoft.Azure.Management.ResourceManager* i søgefeltet, klik på **Installer** til Microsoft Azure ressource Management biblioteker, og følg derefter vejledningen for at installere pakken.

Nu er du klar til at begynde at bruge bibliotekerne til at oprette dit program.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Trin 3: Opret de legitimationsoplysninger, der bruges til at godkende anmodninger

Azure Active Directory-programmet er oprettet og biblioteket godkendelse er installeret. Nu kan du formatere programoplysninger til legitimationsoplysninger, der bruges til at godkende anmodninger til Azure ressourcestyring.

1. Åbn filen Program.cs for det projekt, du har oprettet, og derefter tilføje dem ved hjælp af sætningerne til toppen af filen:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;

2.  Tilføje denne metode til klassen Program for at få det token, der er nødvendige for at oprette legitimationsoplysningerne:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token.");
          }
          return token;
        }

    Erstatte {klient-id} med id for Azure Active Directory program, {klient-hemmeligt} hurtigtast AD programmet og {lejer-id} med lejer id for dit abonnement. Du kan finde lejer-id'et ved at køre Get-AzureRmSubscription. Du kan finde hurtigtast ved hjælp af portalen Azure.

3. Oprette legitimationsoplysningerne, skal tilføje denne kode til metoden Main i filen Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Gem filen Program.cs.

## <a name="step-4-deploy-the-template"></a>Trin 4: Installere skabelonen

I dette trin skal du bruge ressourcegruppen, som du tidligere har oprettet, men du kan også oprette en ressourcegruppe ved hjælp af [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) og [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) klasser.

1. Tilføj variabler i metoden Main i klassen Program til at angive navnene på de ressourcer, som du tidligere har oprettet, installation navnet og dit abonnement-id:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Erstat værdien af gruppenavn med navnet på din ressourcegruppe. Erstat værdien af deploymentName med det navn, du vil bruge til installationen. Du kan finde abonnement-id ved at køre Get-AzureRmSubscription.

2. Tilføje denne metode til klassen Program for at installere ressourcerne til ressourcegruppen ved hjælp af den skabelon, du har defineret:

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Hvis du vil installere skabelonen ud fra en lagerplads-konto, kan du erstatte egenskaben skabelon med egenskaben TemplateLink.

3. Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Trin 5: Slet ressourcerne

Fordi du betaler for ressourcer, der bruges i Azure, er det altid er en god ide at slette ressourcer, som ikke længere skal bruges. Du behøver ikke at slette hver ressource separat fra en ressourcegruppe. Slet ressourcegruppen, og alle dens ressourcer slettes automatisk.

1.  Hvis du vil slette ressourcegruppen, skal du føje denne metode til klassen Program:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Hvis du vil ringe den metode, du lige har tilføjet, du Føj denne kode til metoden Main:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

##<a name="step-6-run-the-console-application"></a>Trin 6: Køre programmet console

1.  Hvis du vil køre programmet console, klik på **Start** i Visual Studio, og derefter logge på Azure AD med de samme legitimationsoplysninger, som du bruger til dit abonnement.

2.  Tryk på **Enter** , når accepterede status vises.

    Det skal tage om fem minutter for denne console program tilladelse til at køre helt fra start til slut. Før du trykker på Enter for at begynde at slette ressourcer, kan du tage et par minutter til at kontrollere oprettelse af ressourcer på portalen Azure, før du slette dem.

3. For at se status for ressourcerne, der skal du gå til overvågningslogge i portalen Azure:

    ![Gennemse overvågningslogge Azure-portalen](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Næste trin

- Hvis der var problemer med installationen, er næste trin at kigge på [fejlfinding ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md).
- Få mere at vide, hvordan du administrerer den virtuelle maskine, du har oprettet ved at gennemgå [Administrer virtuelle maskiner ved hjælp af Azure ressourcestyring og PowerShell](virtual-machines-windows-csharp-manage.md).
