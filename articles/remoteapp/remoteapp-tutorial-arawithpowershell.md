<properties
   pageTitle="Bruge PowerShell-cmdlet'er med Azure RemoteApp | Microsoft Azure"
   description="Lær at bruge Windows PowerShell-cmdletter i Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Bruge Windows PowerShell-cmdlet'er med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

 Du kan bruge Azure RemoteApp PowerShell-cmdlet'er til at administrere og vedligeholde din af websteder. Brug følgende oplysninger til at komme i gang.

## <a name="get-the-cmdlets"></a>Få cmdlet'erne 
-------------
Først hente Azure Powershell-cmdlet'er [her](http://go.microsoft.com/?linkid=9811175)RemoteApp cmdletter er inkluderet i den. 

Se den [Azure RemoteApp cmdlet Hjælp](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Konfigurere Azure cmdletter for at bruge dit abonnement
------------------
Følg [denne vejledning](../powershell-install-configure.md) , så du kan bruge cmdlet'erne mod abonnementet Azure.

Du kan bruge disse trin til at komme hurtigt i gang:

1.  Hent og Installer [Azure PowerShell-cmdlet'er](http://go.microsoft.com/?linkid=9811175).
2.  Start Microsoft Azure PowerShell.
3.  Køre **Tilføj AzureAccount** til at godkende abonnementet Azure. Når du bliver bedt om det, kan du angive det samme brugernavn og adgangskode, du bruger til at logge på Azure-portalen.  
4.  Kør **Get-AzureSubscription** for at få vist de abonnementer, der er knyttet til din brugerkonto. 
5.  Kør **Vælg AzureSubscription** , og Angiv et navn på abonnement eller -ID til brug i PowerShell console.

Tillykke, Microsoft Azure PowerShell Management console er konfigureret og klar til brug. Vær opmærksom på, skal du repeate trin 2 til 5 hver gang du starter den Azure PowerShell console.  

## <a name="create-a-cloud-collection"></a>Oprette en skyen af websteder
--------------------
Det er nemt, køre følgende kommando:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

Ovenstående kommando publicerer automatisk Microsoft Office 365-programmer (Excel, OneNote, Outlook, PowerPoint, Visio og Word).

Oprettelsen af websteder kan tage 30 minutter eller længere tid at fuldføre. Denne kommando returnerer derfor en sporing-ID, som du kan bruge på følgende måde:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Når samlingen er færdig, kan du føje brugere til samlingen med følgende kommando:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Og du er færdig! Brugeren skal kunne oprette forbindelse til programmet ved hjælp af Azure RemoteApp-klienten, findes [her](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Tilgængelige cmdletter
Der er mange andre kommandoer, som vi har, i dokumentationen til dem kommer snart:

Cmdletter til grundlæggende RemoteApp af websteder: 

- Ny AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Angiv AzureRemoteAppCollection
- Opdater AzureRemoteAppCollection
- Fjern AzureRemoteAppCollection
- Tilføje AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Fjern AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Afbryde AzureRemoteAppSession
- Kalde AzureRemoteAppSessionLogoff
- Send AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publicere AzureRemoteAppProgram
- Annullere publiceringen af AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

RemoteApp virtuelt netværk cmdletter:

- Ny AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Angiv AzureRemoteAppVNet
- Fjern AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get - AzureRemoteAppVpnDeviceConfigScript
- Nulstil AzureRemoteAppVpnSharedKey

RemoteApp skabelon billede-cmdletter:

- Ny AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Omdøb AzureRemoteAppTemplateImage
- Fjern AzureRemoteAppTemplateImage

Andre RemoteApp-cmdletter:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Angiv AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 
