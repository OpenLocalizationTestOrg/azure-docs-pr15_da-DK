<properties
   pageTitle="Brugerdefinerede scripts på Linux FOS | Microsoft Azure"
   description="Automatisere Linux VM konfigurationsopgaver ved hjælp af filtypenavnet brugerdefineret Script"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Bruger filtypenavnet Azure brugerdefineret Script med Linux virtuelle maskiner

Filtypenavnet brugerdefineret Script-overførsler og udfører scripts på Azure virtuelle computere. Dette lokalnummer er nyttige til konfiguration af indlæg installation, softwareinstallation eller en anden konfiguration / opgave med administration. Scripts, som kan hentes fra Azure lagerplads eller andre tilgængelige internetplacering eller fået filtypenavnet kørselstid. Filtypenavnet brugerdefineret Script integreres med Azure ressourcestyring skabeloner og kan også køres ved hjælp af Azure CLI, PowerShell, Azure portal eller Azure virtuelt REST-API.

Dette dokument indeholder oplysninger om hvordan du bruger filtypenavnet brugerdefineret Script fra Azure CLI og en skabelon til Azure ressourcestyring og også detaljerede fejlfindingstrin på Linux-systemer.

## <a name="extension-configuration"></a>Filtypenavn konfiguration

Konfigurationen af brugerdefineret Script filtypenavn angiver ting som scriptplacering og kommandoen skal køres. Denne konfiguration kan være gemt på konfigurationsfiler angivet på kommandolinjen eller i en skabelon til Azure ressourcestyring. Følsomme data kan være gemt i en beskyttet konfiguration, som er krypteret og kun dekrypteres i den virtuelle maskine. Beskyttet konfigurationen er nyttig, når kommandoen udførelse af omfatter hemmeligheder som en adgangskode.

### <a name="public-configuration"></a>Offentlige konfiguration

Skema:

- **commandToExecute**: (obligatorisk, streng) posten punkt scriptet til at udføre
- **fileUris**: (valgfrit, strengmatrix) URL-adresser til filer, der skal hentes.
- **tidsstempel** (valgfrit, heltal) Brug dette felt kun til at udløse en Kør scriptet ved at ændre værdien i dette felt.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Beskyttet konfiguration

Skema:

- **commandToExecute**: (valgfrit, streng) posten punkt scriptet til at udføre. I stedet bruge dette felt, hvis din kommando indeholder hemmeligheder som adgangskoder.
- **storageAccountName**: (valgfrit, streng) navnet på lagerplads konto. Hvis du angiver lagerplads legitimationsoplysninger, skal alle fileUris URL-adresser til Azure BLOB.
- **storageAccountKey**: (valgfrit, streng) hurtigtast lagerplads konto.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI

Når du bruger Azure CLI til at køre brugerdefineret Script filtypenavn, kan du oprette en konfigurationsfil eller filer, der indeholder som minimum fil-uri og kommandoen script udførelse af.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Kommandoen kan eventuelt køres, ved hjælp af den `--public-config` og `--private-config` indstilling, der tillader konfiguration skal angives under kørsel af og uden en separat konfigurationsfil.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI eksempler

**Eksempel 1** – offentlige konfiguration med script-fil.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Azure CLI-kommandoen:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Eksempel 2** – offentlige konfiguration med nogen scriptfil.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-kommandoen:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Eksempel 3** – en offentlige konfigurationsfil bruges til at angive scriptfil URI, og en beskyttet konfigurationsfil bruges til at angive kommandoen til at blive udført.

Offentlige konfigurationsfil:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Beskyttet konfigurationsfil:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI-kommandoen:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Ressourcestyring skabelon

Filtypenavnet Azure brugerdefineret Script kan køre på virtuelt installation tid ved hjælp af en ressourcestyring skabelon. Gør du ved at føje korrekt formateret JSON til skabelonen installation.

### <a name="resource-manager-examples"></a>Ressourcestyring eksempler

**Eksempel 1** – offentlige konfiguration.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Eksempel 2** – udførelse af kommandoen i beskyttet konfiguration.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Se .net Core musik Store Demo for en komplet eksempel - [Musik Store Demo](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Når der køres filtypenavnet brugerdefineret Script, scriptet oprettes eller overføres til en mappe, der ligner følgende eksempel. Kommando-output gemmes også i denne mappe i `stdout` og `stderr` fil.

```none
/var/lib/azure/custom-script/download/0/
```

Filtypenavnet Azure Script opretter en logfil, som kan findes her.

```none
/var/log/azure/customscript/handler.log
```

Tilstanden udførelse af filtypenavnet brugerdefineret Script kan også hentes med Azure CLI.

```none
azure vm extension get <resource-group> <vm-name>
```

Output ligner følgende tekst:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Næste trin

Du kan finde oplysninger på andre VM Script udvidelser [Azure Script lokalnummer oversigt til Linux](./virtual-machines-linux-extensions-features.md).