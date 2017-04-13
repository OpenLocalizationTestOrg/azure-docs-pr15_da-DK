<properties 
    pageTitle="Administrere Azure Media Services konti med PowerShell" 
    description="Få mere at vide, hvordan du administrerer Azure Media Services konti med PowerShell-cmdlet'er." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"
    ms.author="juliako"/>


#<a name="manage-azure-media-services-accounts-with-powershell"></a>Administrere Azure Media Services konti med PowerShell

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTEN](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] For at kunne oprette en Azure Media Services-konto, skal du have en Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure gratis prøveversion</a>.

##<a name="overview"></a>Oversigt 

I denne artikel vises Azure PowerShell-cmdlet'er til Azure Media Services (AMS) i Azure ressourcestyring framework. Cmdlet'erne findes i **Microsoft.Azure.Commands.Media** navneområdet.

## <a name="versions"></a>Versioner

**ApiVersion**: "2015-10-01"
               

## <a name="new-azurermmediaservice"></a>Ny AzureRmMediaService

Opretter en medier-tjeneste.

### <a name="syntax"></a>Syntaksen for

Parametersæt: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Parametersæt: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Parametre

**-ResourceGroupName &lt;streng&gt;**

Angiver navnet på den ressourcegruppe, som denne medier-tjeneste tilhører.

Aliasser | ingen
---|---
Påkrævet?   |  SAND
Placere?   |  0
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn?  |FALSK

**-Kontonavn &lt;streng&gt;**

Angiver navnet på tjenesten medier.

Aliasser |Navn
---|---
Påkrævet? |SAND
Placere? |1
Standardværdi |ingen
Acceptér pipeline-oplysninger? |FALSK
Acceptere jokertegn? |FALSK

**-Placering &lt;streng&gt;**

Angiver ressource placeringen af tjenesten medier.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere? |2
Standardværdi  |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-StorageAccountId &lt;streng&gt;**

Angiver en primær lagerplads-konto, der er knyttet til tjenesten medier.

- Ny lagerplads konto (oprettet med ressourcestyring API) understøttes kun.

- Kontoen lagerplads skal findes og har den samme placering med tjenesten medier.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere? |3
Standardværdi  |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Angiv parameternavn |StorageAccountIdParamSet
Acceptere jokertegn?|FALSK

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Angiver lagerplads konti, der er knyttet til tjenesten medier.

- Ny lagerplads konto (oprettet med ressourcestyring API) understøttes kun.

- Kontoen lagerplads skal findes og har den samme placering med tjenesten medier.

- Kun én lagerplads konto kan være angivet som primær.

Aliasser |ingen
---|---
Påkrævet?  |SAND
Placere?  |3
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Angiv parameternavn |StorageAccountsParamSet
Acceptere jokertegn? |FALSK

**-Koder &lt;Hashtable&gt;**

Angiver en hashtabel af de mærker, der er knyttet til tjenesten medier.

- Eksempel:@{"tag1"="value1";"tag2"=:value2"}

Aliasser |ingen
---|---
Påkrævet?  |FALSK
Placere?  |med navnet
Standardværdi |ingen
Acceptér pipeline-oplysninger? |FALSK
Acceptere jokertegn? |FALSK

**&lt;CommandParameters&gt;**

Denne cmdlet understøtter fælles parametre:-fejlfinding, - ErrorAction - ErrorVariable - InformationAction, og - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detaljeret, - WarningAction, og -WarningVariable.

### <a name="inputs"></a>Input

Input-typen er typen af de objekter, som du kan pipe til cmdlet.

### <a name="outputs"></a>Output

Outputtypen er typen af de objekter, cmdlet sender.

## <a name="set-azurermmediaservice"></a>Angiv AzureRmMediaService

Opdaterer en medier-tjeneste.

### <a name="syntax"></a>Syntaksen for

    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Parametre

**-ResourceGroupName &lt;streng&gt;**

Angiver navnet på den ressourcegruppe, som denne medier-tjeneste tilhører.

Aliasser |ingen
---|---
Påkrævet?  |SAND
Placere?  |0
Standardværdi |ingen
Acceptér Pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-Kontonavn &lt;streng&gt;**

Angiver navnet på tjenesten medier.

Aliasser |Navn
---|---
Påkrævet? |SAND
Placere? |1
Standardværdi |Ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Angiver lagerplads konti, der er knyttet til tjenesten medier.

- Ny lagerplads konto (oprettet med ressourcestyring API) understøttes kun.

- Kontoen lagerplads skal findes og har den samme placering med tjenesten medier.

- Kun én lagerplads konto kan være angivet som primær.

Aliasser |ingen
---|---
Påkrævet? |FALSK
Placere? |Med navnet
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Angiv parameternavn |StorageAccountsParamSet
Acceptere jokertegn? |FALSK

**-Koder &lt;Hashtable&gt;**

Angiver en hashtabel af de mærker, der er knyttet til denne medier-tjeneste.

- De mærker, der er knyttet til tjenesten medier erstattes med værdi, der er angivet af kunden.

Aliasser |ingen
---|---
Påkrævet? |FALSK
Placere?  |Med navnet
Standardværdi |Ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**&lt;CommandParameters&gt;**

Denne cmdlet understøtter fælles parametre:-fejlfinding, - ErrorAction - ErrorVariable - InformationAction, og - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detaljeret, - WarningAction, og -WarningVariable.

### <a name="inputs"></a>Input

Input-typen er typen af de objekter, som du kan pipe til cmdlet.

### <a name="outputs"></a>Output

Outputtypen er typen af de objekter, cmdlet sender.

## <a name="remove-azurermmediaservice"></a>Fjern AzureRmMediaService

Fjerner en medier-tjeneste.

### <a name="syntax"></a>Syntaksen for

    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametre

**-ResourceGroupName &lt;streng&gt;**

Angiver navnet på den ressourcegruppe, som denne medier-tjeneste tilhører.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere? |0
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-Kontonavn &lt;streng&gt;**

Angiver navnet på tjenesten medier.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere? |2
Standardværdi |Ingen
Acceptér pipeline-oplysninger?  |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**&lt;CommandParameters&gt;**

Denne cmdlet understøtter fælles parametre:-fejlfinding, - ErrorAction - ErrorVariable - InformationAction, og - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detaljeret, - WarningAction, og -WarningVariable.

### <a name="inputs"></a>Input

Input-typen er typen af de objekter, som du kan pipe til cmdlet.

### <a name="outputs"></a>Output

Outputtypen er typen af de objekter, cmdlet sender.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService

Henter alle media services i en ressourcegruppe eller en medier-tjeneste med et navn.

### <a name="syntax"></a>Syntaksen for

ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>] 

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametre

**-ResourceGroupName &lt;streng&gt;**

Angiver navnet på den ressourcegruppe, som denne medier-tjeneste tilhører.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere?  |0
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Angiv parameternavn |ResourceGroupParameterSet, AccountNameParameterSet
Acceptere jokertegn?   FALSK

**-Kontonavn &lt;streng&gt;**

Angiver navnet på tjenesten medier.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere?  |1
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Angiv parameternavn  |AccountNameParameterSet
Acceptere jokertegn? |FALSK

**&lt;CommandParameters&gt;**

Denne cmdlet understøtter fælles parametre:-fejlfinding, - ErrorAction - ErrorVariable - InformationAction, og - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detaljeret, - WarningAction, og -WarningVariable.

### <a name="inputs"></a>Input

Input-typen er typen af de objekter, som du kan pipe til cmdlet.

### <a name="outputs"></a>Output

Outputtypen er typen af de objekter, cmdlet sender.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys

Får taster til en medier-tjeneste.

### <a name="syntax"></a>Syntaksen for

    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametre

**-ResourceGroupName &lt;streng&gt;**

Angiver navnet på den ressourcegruppe, som denne medier-tjeneste tilhører.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere?  |0
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-Kontonavn &lt;streng&gt;**

Angiver navnet på tjenesten medier.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere? |1
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**&lt;CommandParameters&gt;**

Denne cmdlet understøtter fælles parametre:-fejlfinding, - ErrorAction - ErrorVariable - InformationAction, og - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detaljeret, - WarningAction, og -WarningVariable.

### <a name="inputs"></a>Input

Input-typen er typen af de objekter, som du kan pipe til cmdlet.

### <a name="outputs"></a>Output

Outputtypen er typen af de objekter, cmdlet sender.

## <a name="set-azurermmediaservicekey"></a>Angiv AzureRmMediaServiceKey

Genopretter en primær eller sekundær nøgle til en medier-tjeneste.

### <a name="syntax"></a>Syntaksen for

    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Parametre

**-ResourceGroupName &lt;streng&gt;**

Angiver navnet på den ressourcegruppe, som denne medier-tjeneste tilhører.

Aliasser |ingen
---|---
Påkrævet?  |SAND
Placere?  |0
Standardværdi |ingen
Acceptér pipeline-oplysninger?  |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-Kontonavn &lt;streng&gt;**

Angiver navnet på tjenesten medier.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere?  |1
Standardværdi |ingen
Acceptér pipeline-oplysninger?   |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-KeyType &lt;KeyType&gt;**

Angiver de vigtigste typer tjenesten medier.

- Primær eller sekundær

Aliasser |ingen
---|---
Påkrævet?  |SAND
Placere?  |2
Standardværdi |ingen
Acceptér pipeline-oplysninger? |FALSK
Acceptere jokertegn? |FALSK

**&lt;CommandParameters&gt;**

Denne cmdlet understøtter fælles parametre:-fejlfinding, - ErrorAction - ErrorVariable - InformationAction, og - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detaljeret, - WarningAction, og -WarningVariable.

### <a name="inputs"></a>Input

Input-typen er typen af de objekter, som du kan pipe til cmdlet.

### <a name="outputs"></a>Output

Outputtypen er typen af de objekter, cmdlet sender.

## <a name="sync-azurermmediaservicestoragekeys"></a>Synkroniser AzureRmMediaServiceStorageKeys

Synkroniserer lagerplads konto taster til en lagerplads-konto, der er knyttet til tjenesten medier.

### <a name="syntax"></a>Syntaksen for

    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametre

**-ResourceGroupName &lt;streng&gt;**

Angiver navnet på den ressourcegruppe, som denne medier-tjeneste tilhører.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere? |0
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-Kontonavn &lt;streng&gt;**

Angiver navnet på tjenesten medier.

Aliasser |ingen
---|---
Påkrævet? |SAND
Placere? |1
Standardværdi |ingen
Acceptér pipeline-oplysninger? |True(ByPropertyName)
Acceptere jokertegn? |FALSK

**-StorageAccountId &lt;streng&gt;**

Angiver lagerplads-konto, der er knyttet til tjenesten medier.

Aliasser |Id
---|---
Påkrævet? |SAND
Placere?  |2
Standardværdi |ingen
Acceptér pipeline-oplysninger? |      True(ByPropertyName)
Acceptere jokertegn? |FALSK

**&lt;CommandParameters&gt;**

Denne cmdlet understøtter fælles parametre:-fejlfinding, - ErrorAction - ErrorVariable - InformationAction, og - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detaljeret, - WarningAction, og -WarningVariable.

### <a name="inputs"></a>Input

Input-typen er typen af de objekter, som du kan pipe til cmdlet.

### <a name="outputs"></a>Output

Outputtypen er typen af de objekter, cmdlet sender.

## <a name="next-step"></a>Næste trin 

Se Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
