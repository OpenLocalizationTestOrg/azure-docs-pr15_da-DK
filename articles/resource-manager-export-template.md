<properties
    pageTitle="Eksportere Azure ressourcestyring skabelon | Microsoft Azure"
    description="Brug Azure ressource Administrer for at eksportere en skabelon fra en eksisterende ressourcegruppe."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="tomfitz"/>

# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Eksportere en Azure ressourcestyring skabelon fra eksisterende ressourcer

Ressourceleder, der gør det muligt at eksportere en ressourcestyring skabelon fra eksisterende ressourcer i dit abonnement. Du kan bruge den oprettede skabelon til at få mere at vide om syntaksen skabelon eller til at automatisere geninstallation af din løsning efter behov.

Det er vigtigt at være opmærksom på, at der er to forskellige måder at eksportere en skabelon:

- Du kan eksportere den faktiske skabelon, du har brugt til en installation. Skabelonen eksporterede indeholder alle de parametre og variabler, nøjagtigt som de vises i den oprindelige skabelon. Denne metode er nyttigt, når du har installeret ressourcer via portalen. Nu, du vil se, hvordan du oprette skabelon for at oprette disse ressourcer.
- Du kan eksportere en skabelon, der repræsenterer den aktuelle tilstand for ressourcegruppen. Skabelonen eksporterede er ikke baseret på en skabelon, du har brugt til installation. I stedet, oprettes der en skabelon, der er et øjebliksbillede af ressourcegruppen. Skabelonen eksporterede har mange faste værdier og sandsynligvis ikke så mange parametre, som du normalt ville gøre definerer. Denne metode er nyttig, når du har ændret ressourcegruppe via portalen eller scripts. Nu skal du registrere ressourcegruppe som en skabelon.

Dette emne viser begge tilgange.

I dette selvstudium, du logger på portalen Azure, oprette en lagerplads-konto, og Eksportér skabelonen for lagerplads kontoen. Du tilføjer et virtuelt netværk for at ændre ressourcegruppen. Til sidst skal eksportere du en ny skabelon, der repræsenterer den aktuelle tilstand. Selvom i denne artikel fokuserer på en forenklet infrastruktur, kan du bruge samme fremgangsmåde til at eksportere en skabelon til en mere kompliceret løsning.

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

1. Vælg **Ny**på [Azure-portalen](https://portal.azure.com), > **lagerplads** > **lagerplads konto**.

      ![oprette lagerplads](./media/resource-manager-export-template/create-storage.png)

2. Oprette en lagerplads konto med navnet **lagerplads**, dine initialer og datoen. Kontonavn lager skal være entydig på tværs af Azure. Hvis navnet allerede er i brug, vises der en fejl, der angiver navnet er i brug. Prøv en variation. Oprette en ny ressourcegruppe til ressourcegruppe, og kald den **ExportGroup**. Du kan bruge standardværdierne for de andre egenskaber. Vælg **Opret**.

      ![Angiv værdier for lagerplads](./media/resource-manager-export-template/provide-storage-values.png)

Installationen kan tage nogle minutter. Når installationen er fuldført, indeholder dit abonnement kontoen lagerplads.

## <a name="view-a-template-from-deployment-history"></a>Få vist en skabelon fra installation oversigt

1. Gå til bladet ressource gruppe til den nye ressourcegruppe. Bemærk, at bladet viser resultatet af den sidste installation. Vælg dette link.

      ![ressource gruppe blade](./media/resource-manager-export-template/resource-group-blade.png)

2. Du kan se en oversigt over installationer for gruppen. I din sag viser bladet sandsynligvis kun én installation. Vælg denne installation.

     ![sidste installation](./media/resource-manager-export-template/last-deployment.png)

3. Bladet viser en oversigt over installationen. Oversigten omfatter status for installationen og dens handlinger og de værdier, som du angav for parametre. Vælg **visningsskabelon**for at se den skabelon, du har brugt til installationen.

     ![Oversigt over installation af visning](./media/resource-manager-export-template/deployment-summary.png)

4. Ressourcestyring henter følgende seks filer for dig:

   1. **Skabelon** - skabelonen, der definerer infrastruktur til din løsning. Da du oprettede kontoen lagerplads via portalen, Ressourceleder, der bruges en skabelon til at installere den og gemmes skabelonen til senere brug.
   2. **Parametre** - en parameterfil, du kan bruge til at videregive værdier under installationen. Den indeholder de værdier, som du angav under den første installation, men du kan ændre nogen af disse værdier, når du Geninstaller skabelonen.
   3. **CLI** - en Azure command-line-grænseflade (CLI) script-fil, du kan bruge til at udrulle skabelonen.
   4. **PowerShell** - en Azure PowerShell script-fil, du kan bruge til at udrulle skabelonen.
   5. **.NET** - A .NET klasse, du kan bruge til at installere skabelonen.
   6. **Fonetisk** - A fonetisk klasse, du kan bruge til at installere skabelonen.

     Filerne, der er tilgængelige via links på tværs af bladet. Som standard viser bladet skabelonen.

       ![visningsskabelon](./media/resource-manager-export-template/view-template.png)

     Lad os særligt opmærksomme på skabelonen. Skabelonen bør se nogenlunde sådan:

        {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parametre": {"navn": {"type": "Streng"}, "accountType": {"type": "Streng"}, "placering": {"type": "Streng"}, "encryptionEnabled": {"standardværdi": falsk "type": "Boolesk"}}, "ressourcer": [{"type": "Microsoft.Storage/storageAccounts", "sku": {"navn": "[parameters('accountType')]"}, "venlig": "Lagring", "navn": "[parameters('name')]", "apiVersion": "2016-01-01", "placering": "[parameters('location')]", "Egenskaber": {"kryptering": {"services": {"blob": {"aktiveret": "[parameters('encryptionEnabled')]"}}, "keySource": "Microsoft.Storage"}}}]}
 
Denne skabelon er den faktiske skabelon, der bruges til at oprette kontoen lagerplads. Bemærk, den indeholder parametre, der gør det muligt at installere forskellige typer lagerplads konti. Hvis du vil vide mere om strukturen i en skabelon, se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md). Du kan finde den komplette liste over de funktioner, du kan bruge i en skabelon, [Azure ressourcestyring skabelon funktioner](resource-group-template-functions.md).


## <a name="add-a-virtual-network"></a>Tilføje et virtuelt netværk

Den skabelon, du har hentet i forrige afsnit repræsenteret infrastruktur til den oprindelige installation. Det kan dog ikke højde for de ændringer, du foretager efter installationen.
For at illustrere dette problem, Lad os redigere ressourcegruppen ved at tilføje et virtuelt netværk via portalen.

1. Vælg **Tilføj**bladet ressource gruppe.

      ![føje ressource](./media/resource-manager-export-template/add-resource.png)

2. Vælg **virtuelle netværk** tilgængelige ressourcer.

      ![Vælg virtuelt netværk](./media/resource-manager-export-template/select-vnet.png)

2. Navngiv netværket virtuel **VNET**, og brug standardværdierne for de andre egenskaber. Vælg **Opret**.

      ![Indstil en vigtig besked](./media/resource-manager-export-template/create-vnet.png)

3. Når det virtuelle netværk har installeret dit ressourcegruppe, kan du se igen på oversigten for installation. Du kan nu se to installationer. Hvis du ikke kan se den anden installation, skal du muligvis lukke din ressource gruppe blade og åbner den igen. Vælg den nyeste installation.

      ![Oversigt over installation](./media/resource-manager-export-template/deployment-history.png)

4. Få vist skabelonen til denne installation. Bemærk, at den definerer det virtuelle netværk. Det omfatter ikke kontoen lagerplads du har installeret tidligere. Du har ikke længere en skabelon, der repræsenterer alle ressourcerne i din ressourcegruppe.

## <a name="export-the-template-from-resource-group"></a>Eksportere skabelonen fra ressourcegruppe

For at få den aktuelle tilstand for dit ressourcegruppe skal du eksportere en skabelon, der viser et øjebliksbillede af ressourcegruppen.  

> [AZURE.NOTE] Du kan ikke eksportere en skabelon til en ressourcegruppe, der indeholder mere end 200 ressourcer.

1. Vælg **automatiseringsscript**for at få vist skabelonen til en ressourcegruppe.

      ![eksportere ressourcegruppe](./media/resource-manager-export-template/export-resource-group.png)

     Ikke alle ressourcetyper understøtter funktionen Eksportér skabelon. Hvis din ressourcegruppe indeholder kun lagerplads konto og virtuelt netværk, der vises i denne artikel, kan du ikke se en fejl. Hvis du har oprettet andre ressourcetyper, kan du se en fejlmeddelelse om, at der er et problem med eksporten. Du lære at håndtere disse problemer i afsnittet [rette problemer med eksport](#fix-export-issues) .

      

2. Du ser seks filer, som du kan bruge til at installere løsningen igen, men denne gang skabelonen er en smule anderledes. Denne skabelon har kun to parametre: én for kontonavn lager og én til navnet på det virtuelle netværk.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Ressourcestyring ikke hente de skabeloner, du har brugt under installationen. I stedet genereres den en ny skabelon, der er baseret på den aktuelle konfiguration af ressourcer. For eksempel angiver skabelonen lagerplads konto placering og gentagelse værdi til:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Du har et par muligheder for at fortsætte med at arbejde med denne skabelon. Du kan hente skabelonen og arbejde på den lokalt en JSON-editor. Eller du kan gemme skabelonen til biblioteket og arbejde på den via portalen.

     Hvis du er tryg ved at bruge en JSON editor som [Eller-kode](resource-manager-vs-code.md) eller [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), kan du foretrækker hente skabelonen lokalt og bruge editoren. Hvis du ikke er konfigureret med en JSON-editor, kan du foretrækker at redigere skabelonen via portalen. I resten af dette emne antages, du har gemt skabelonen til biblioteket på portalen. Dog ændrer du de samme syntaks til skabelonen om arbejde lokalt med en JSON-editor eller via portalen.

     Hvis du vil arbejde lokalt, klik på **Hent**.

      ![Hent skabelon](./media/resource-manager-export-template/download-template.png)

     For at arbejde via portalen skal du vælge **Føj til biblioteket**.

      ![føje til bibliotek](./media/resource-manager-export-template/add-to-library.png)

     Når du tilføjer en skabelon til biblioteket, Giv skabelonen et navn og beskrivelse. Vælg derefter **Gem**.

     ![Angiv skabelonværdier](./media/resource-manager-export-template/set-template-values.png)

4. For at få vist en skabelon, der er gemt i biblioteket, vælge **flere tjenester**, skriver **skabeloner** til at filtrere resultaterne, skal du vælge **skabeloner**.

      ![søge efter skabeloner](./media/resource-manager-export-template/find-templates.png)

5. Vælg skabelonen med det navn, du har gemt.

      ![Vælg skabelon](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>Tilpasse skabelonen

Skabelonen eksporterede fungerer fint, hvis du vil oprette den samme lagerplads konto og virtuelt netværk for hver installation. Dog indeholder ressourceleder, der indstillinger, så du kan installere skabeloner med meget større fleksibilitet. For eksempel under installationen, du muligvis vil angive typen lagerplads konto til at oprette eller værdierne, der bruges til virtuelt netværk adressepræfikset og undernetpræfiks.

I dette afsnit, skal tilføje du parametre til den eksporterede skabelon, så du kan genbruge skabelonen, når du installerer disse ressourcer til andre miljøer. Du kan også tilføje nogle funktioner til din skabelon til at mindske risikoen for støder en fejl, når du installerer din skabelon. Du behøver ikke længere at gætte et entydigt navn til kontoen lagerplads. I stedet opretter skabelonen et entydigt navn. Du kan begrænse de værdier, der kan angives for kontotypen lagerplads til kun gyldige indstillinger.

1. Vælg **Rediger** for at tilpasse skabelonen.

     ![Vis skabelon](./media/resource-manager-export-template/show-template.png)

1. Vælg skabelonen.

     ![Rediger skabelon](./media/resource-manager-export-template/edit-template.png)

1. For at kunne overføre de værdier, som du muligvis vil angive under installationen, skal du erstatte sektionen **parametre** med nye parameterdefinitioner. Bemærk værdierne af **allowedValues** for **storageAccount_accountType**. Hvis du ved et uheld giver en ugyldig værdi, genkendes denne fejl, før installationen starter. Bemærk også, at du har angivet kun et præfiks for kontonavn lager, og præfikset er begrænset til 11 tegn. Når du begrænse præfikset til 11 tegn, skal du sikre dig, at hele navnet ikke overstiger det maksimale antal tegn for en lagerplads konto. Præfikset gør det muligt at anvende en navngivningsregel til dine konti på lagerplads. Du vil se, hvordan du opretter et entydigt navn i næste trin.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. Afsnittet **variabler** i skabelonen er tom. I sektionen **variabler** skal oprette du værdier, der forenkler syntaksen for resten af din skabelon. Erstatte denne sektion med en ny variabel definition. Variablen **storageAccount_name** sammenkæder præfikset fra parameteren til en entydig streng, der genereres baseret på id for ressourcegruppen. Du behøver ikke længere at gætte et entydigt navn, når give en parameterværdi.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Erstat afsnittet **ressourcer** for at bruge parametre og variabel i ressourcedefinitioner, med nye ressourcedefinitioner. Bemærk, at lidt er blevet ændret i ressourcedefinitioner end den værdi, der er tildelt egenskaben ressource. Egenskaberne er den samme som egenskaberne fra den eksporterede skabelon. Du blot tildeler egenskaber til parameterværdier i stedet for faste værdier. Placeringen af ressourcerne, der er angivet til at bruge den samme placering som ressourcegruppe via **resourceGroup () .location** udtryk. Den variabel, som du har oprettet for kontonavn lager der henvises til via **variabler** udtryk.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

4. Vælg **OK** , når du er færdig med redigering af skabelonen.

5. Vælg **Gem** til at gemme ændringerne i skabelonen.

     ![Gem skabelon](./media/resource-manager-export-template/save-template.png)

6. Vælg **Installer**for at installere den opdaterede skabelon.

     ![installere skabelon](./media/resource-manager-export-template/deploy-template.png)

7. Få parameterværdier, og vælg en ny ressourcegruppe til at udrulle ressourcer til.

## <a name="update-the-downloaded-parameters-file"></a>Opdatere parameterfilen hentede

Hvis du arbejder med de hentede filer (i stedet for biblioteket portalen), skal du opdatere parameterfilen hentede. Den ikke længere svarer til parametrene i skabelonen. Du behøver ikke at bruge en parameterfil, men det kan forenkle processen, når du Geninstaller et miljø. Du kan bruge de standardværdier, der er defineret i skabelonen for mange af parametrene, så parameterfilen skal kun to værdier.

Erstat indholdet af parameters.json filen med:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Opdaterede Parameterfilen indeholder værdier kun til parametre, der ikke har en standardværdi. Du kan angive værdier for de andre parametre, når du vil have en værdi, der adskiller sig fra standardværdien.

## <a name="fix-export-issues"></a>Løse problemer med eksport

Ikke alle ressourcetyper understøtter funktionen Eksportér skabelon. Ressourcestyring eksportere specifikt ikke nogle ressourcetyper for at undgå at afsløre følsomme data. Eksempelvis hvis du har en forbindelsesstreng i config websted, vil du sandsynligvis ikke det udtrykkeligt vises i en eksporterede skabelon. Du kan få løse dette problem ved manuelt at tilføje de manglende ressourcer i din skabelon igen.

> [AZURE.NOTE] Der opstår problemer med eksport kun, når du eksporterer fra en ressourcegruppe i stedet for en oversigt over dine installation. Hvis din sidste installation nøjagtigt repræsenterer den aktuelle tilstand for ressourcegruppen, skal du eksportere skabelonen fra installation oversigten i stedet for fra ressourcegruppen. Kun eksportere fra en ressourcegruppe, når du har foretaget ændringer i ressourcegruppen, der ikke er defineret i en enkelt skabelon.

Eksempelvis hvis du eksporterer en skabelon til en ressourcegruppe, der indeholder en WebApp, SQL-Database og forbindelsesstrengen i websted config, vist du følgende meddelelse:

![Vis fejl](./media/resource-manager-export-template/show-error.png)

At markere meddelelsen viser præcis hvilke ressourcetyper blev ikke eksporteret. 
     
![Vis fejl](./media/resource-manager-export-template/show-error-details.png)

Dette emne viser almindelige løsninger.

### <a name="connection-string"></a>Forbindelsesstreng

Føje en definition af forbindelsesstrengen til databasen i ressourcen websteder:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Websted lokalnummer

Tilføj en definition for at installere kode i ressource websted:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Virtuelt lokalnummer

Eksempler på virtuelt filtypenavne, se [Azure Windows VM lokalnummer konfiguration eksempler](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### <a name="virtual-network-gateway"></a>Virtuelt netværksgateway

Tilføje et virtuelt netværk gateway ressourcetype.

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Lokale netværksgateway

Tilføje en ressourcetype lokale netværk gateway.

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Forbindelse

Føje en forbindelsestype ressource.

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Næste trin

Tillykke! Du har lært at eksportere en skabelon fra ressourcer, du oprettede i portalen.

- Du kan installere en skabelon via [PowerShell](resource-group-template-deploy.md), [Azure CLI](resource-group-template-deploy-cli.md)eller [REST-API](resource-group-template-deploy-rest.md).
- Se [Bruge Azure PowerShell med Azure ressourcestyring](powershell-azure-resource-manager.md)for at se, hvordan du kan eksportere en skabelon via PowerShell.
- For at se, hvordan du kan eksportere en skabelon til Azure CLI skal du se [Brug af Azure CLI til Mac, Linux, og Windows Azure Resource Manager](xplat-cli-azure-resource-manager.md).
