<properties 
    pageTitle="Installere og administrere meddelelse Hubs ved hjælp af PowerShell" 
    description="Hvordan du kan oprette og administrere meddelelse Hubs ved hjælp af PowerShell til automatisering" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Installere og administrere meddelelse Hubs ved hjælp af PowerShell

##<a name="overview"></a>Oversigt

I denne artikel viser, hvordan du bruger oprette og administrere Azure meddelelse Hubs ved hjælp af PowerShell. Følgende almindelige automatiseringsopgaver er vist i dette emne.

+ Oprette en meddelelse om Hub
+ Angiv legitimationsoplysninger

Hvis du også vil oprette en ny tjeneste bus navneområdet for din meddelelse hubs skal du se [Administrere Service Bus med PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Administrere beskeder Hubs understøttes ikke direkte af cmdlet'erne inkluderet med Azure PowerShell. Der er den bedste metode fra PowerShell til at referere til samlingen Microsoft.Azure.NotificationHubs.dll. Samlingen distribueres sammen med [Microsoft Azure meddelelse Hubs NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- Et Azure-abonnement. Azure er en abonnementsbaseret platform. Du kan finde flere oplysninger om at få et abonnement, skal du se [Købsmuligheder], [Medlem tilbyder]eller [Gratis prøveversion].

- En computer med Azure PowerShell. Flere oplysninger under [installere og konfigurere Azure PowerShell].

- En generel forståelse af PowerShell-scripts, NuGet-pakker og .NET Framework.


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Herunder en reference til samlingen .NET for tjenesten Bus

Administrere Azure meddelelse Hubs endnu ikke er inkluderet i PowerShell-cmdletter i Azure PowerShell. Hvis du vil klargøre meddelelse hubs, kan du bruge den .NET-klient, der leveres i [Microsoft Azure meddelelse Hubs NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Først skal du sørge for, at dit script kan finde den samling, **Microsoft.Azure.NotificationHubs.dll** , som er installeret som en NuGet pakke i Visual Studio-projekt. For at være fleksible, udfører scriptet disse trin:

1. Bestemmer stien, hvor det blev aktiveret.
2. Kører på tværs stien, indtil der er angivet en mappe med navnet `packages`. Denne mappe oprettes, når du installerer NuGet pakker til Visual Studio-projekter.
3. Gælder søgninger på `packages` mappe for en samling med navnet **Microsoft.Azure.NotificationHubs.dll**.
4. Referencer samlingen, så de er tilgængelige til senere brug.

Her er, hvordan disse trin er implementeret i et PowerShell-script:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Oprette klassen NamespaceManager

Hvis du vil klargøre meddelelse Hubs, kan du oprette en forekomst af klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) fra SDK. 

Du kan bruge cmdlet'en [Get-AzureSBAuthorizationRule] til inkluderet med Azure PowerShell til at hente en godkendelsesregel for, der bruges til at give en forbindelsesstreng. Vi vil gemme en reference til den `NamespaceManager` forekomst i den `$NamespaceManager` variabel. Vi bruger `$NamespaceManager` til at klargøre en meddelelse om hub.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Klargøring af en ny meddelelse-Hub 

Hvis du vil tildele en ny meddelelse-hub, kan bruge [.NET API til meddelelse Hubs].

I denne del af scriptet konfigurere du fire lokale variabler. 

1. `$Namespace`: Angive dette til navnet på navneområdet, der hvor du vil oprette en meddelelse om hub.
2. `$Path`: Angiv denne sti til navnet på den nye meddelelse hub.  Eksempelvis "MyHub".    
3. `$WnsPackageSid`: Konfigurere det til pakken SID for dig Windows-App fra [Windows Udviklercenter](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Konfigurere det til tasten hemmeligt for dig Windows-App fra [Windows Udviklercenter](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Disse variabler bruges til at oprette forbindelse til din navneområde og oprette en ny meddelelse-Hub, der er konfigureret til at håndtere meddelelser om Windows meddelelse (WNS) med WNS legitimationsoplysninger til en Windows-App. Oplysninger om, hvordan du får pakken SID og hemmeligt nøgle se skal [Komme i gang med besked om Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) selvstudiet. 

+ Script anvender den `NamespaceManager` objekt til at kontrollere, hvis besked om hubben identificeret med `$Path` findes.

+ Hvis den ikke findes, scriptet opretter en `NotificationHubDescription` med WNS legitimationsoplysninger og overføre, til den `NamespaceManager` klasse `CreateNotificationHub` metode.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Yderligere ressourcer

- [Administrere Service Bus med PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Sådan oprettes Service Bus køer, emner og abonnementer ved hjælp af en PowerShell-script](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Sådan oprettes en tjeneste Bus Namespace og en begivenhed Hub ved hjælp af en PowerShell-script](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Nogle færdige scripts er også tilgængelig til hentning:
- [Service Bus PowerShell-Scripts](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Købsmuligheder]: http://azure.microsoft.com/pricing/purchase-options/
[Medlem tilbud]: http://azure.microsoft.com/pricing/member-offers/
[Gratis prøveversion]: http://azure.microsoft.com/pricing/free-trial/
[Installere og konfigurere Azure PowerShell]: ../powershell-install-configure.md
[.NET API til meddelelse Hubs]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
