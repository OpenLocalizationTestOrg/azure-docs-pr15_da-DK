<properties
   pageTitle="Oprette en skybaseret tjenestebeholder med PowerShell | Microsoft Azure"
   description="I denne artikel beskrives det, hvordan du opretter en skybaseret tjeneste objektbeholder med PowerShell. Objektbeholderen vært internettet og arbejder roller."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Bruge en Azure PowerShell-kommando til at oprette en tom skyen service objektbeholder
I denne artikel beskrives det, hvordan du kan hurtigt oprette en Cloud Services objektbeholder ved hjælp af Azure PowerShell-cmdlet'er. Følg nedenstående trin:

1. Installer Microsoft Azure PowerShell-cmdlet fra [Azure PowerShell downloads](http://aka.ms/webpi-azps) -siden.
2. Åbn kommandoprompten PowerShell.
3. Bruge [Tilføj AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) til at logge på.

    > [AZURE.NOTE] Yderligere anvisninger til installation af Azure PowerShell-cmdlet og oprette forbindelse til dit Azure-abonnement, skal du henvise til [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

4. Brug cmdlet'en **Ny AzureService** til at oprette en tom Azure skyen service beholder.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Følge dette eksempel for at kalde cmdlet:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Du kan finde flere oplysninger om oprettelse af Azure skybaseret tjeneste, du Kør:
```
Get-help New-AzureService
```

### <a name="next-steps"></a>Næste trin

 * For at administrere skyen service installation skal du henvise til kommandoerne [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Fjern AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)og [Angive AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) . Du kan også henvise til [at konfigurere skytjenester](cloud-services-how-to-configure.md) for yderligere oplysninger.

 * Se eksemplet **PublishCloudService.ps1** kode fra [fortløbende levering for skybaseret tjeneste i Azure](cloud-services-dotnet-continuous-delivery.md)for at publicere projektet skyen service Azure.
