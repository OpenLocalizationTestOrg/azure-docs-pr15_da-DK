<properties
   pageTitle="Ressourcestyring skabelon for lagerplads | Microsoft Azure"
   description="Viser ressourcestyring skemaet til implementering af lager-konti via en skabelon."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>Lagerplads konto skabelon skema

Opretter en lagerplads konto.

## <a name="schema-format"></a>Skema format

For at oprette en lagerplads konto skal du tilføje følgende skema i afsnittet ressourcer af din skabelon.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Værdier

I følgende tabel beskrives de værdier, du vil angive i skemaet.

| Navn | Værdi |
| ---- | ---- |
| type | Optæl<br />Påkrævet<br />**Microsoft.Storage/storageAccounts**<br /><br />Ressourcetype til at oprette. |
| apiVersion | Optæl<br />Påkrævet<br />**2015-06-15** eller **2015-05-01-preview**<br /><br />API versionen skal bruges til at oprette ressourcen. | 
| Navn | Streng<br />Påkrævet<br />Mellem 3 og 24 tegn, tal og små bogstaver.<br /><br />Navnet på lagerplads konto til at oprette. Navnet skal være entydig på tværs af alle Azure. Overvej at bruge funktionen [uniqueString](resource-group-template-functions.md#uniquestring) med din navnekonventionen, som vist i eksemplet nedenfor. |
| placering | Streng<br />Påkrævet<br />Et område, der understøtter lagerplads konti. For at bestemme gyldige områder, kan du se [understøttede områder](resource-manager-supported-services.md#supported-regions).<br /><br />Område til at hoste kontoen lagerplads. |
| Egenskaber | Objekt<br />Påkrævet<br />[Egenskaber for objekt](#properties)<br /><br />Et objekt, der angiver typen lagerplads konto til at oprette. |

<a id="properties" />
### <a name="properties-object"></a>Egenskaber for objekt

| Navn | Værdi |
| ---- | ---- | 
| accountType | Streng<br />Påkrævet<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS**eller **Premium_LRS**<br /><br />Typen lagerplads konto. Tilladte værdier svarer til Standard lokalt overflødige, Standard Zone overflødige, Standard geografisk-overflødigt, Standard-læseadgang geografisk overflødigt og Premium lokalt overflødige. Du kan finde oplysninger om disse kontotyper [Gentagelse Azure-lager](./storage/storage-redundancy.md ). |

    
## <a name="examples"></a>Eksempler

I følgende eksempel installerer en Standard lokalt overflødige lagerplads konto med et entydigt navn, der er baseret på ressource gruppe-id'et.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Hurtig start skabeloner

Der er mange skabeloner i Hurtig start, som omfatter en lagerplads konto. Følgende skabeloner illustrerer nogle almindelige scenarier:

- [Oprette en Standard-lager-konto](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Enkel installation af en Windows-VM](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Enkel installation af en Linux VM](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Oprette et CDN profil, som er et CDN slutpunkt med en lagerplads konto som startpunkt](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Oprette en høj Availabilty SharePoint-Farm med 9 FOS ved hjælp af Powershell DTK lokalnummer](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Enkel installation af en 5 Node secure Service-strukturen klynge med WAD aktiveret](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Oprette en virtuel maskine fra en Windows-afbildning med 4 tomme datadisce](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>Næste trin

- Du kan finde generelle oplysninger om lagerplads, [Introduktion til Microsoft Azure-lager](./storage/storage-introduction.md).
- For eksempel se skabeloner, der bruger en ny lagerplads konto med en virtuel maskine, [Implementer en enkel Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) eller [Implementer en enkelt Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).
