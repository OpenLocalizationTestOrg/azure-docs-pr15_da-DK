<properties
    pageTitle="Administrere Service Bus med PowerShell | Microsoft Azure"
    description="Administrere Service Bus med PowerShell-scripts"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>Administrere Service Bus med PowerShell

## <a name="overview"></a>Oversigt

Microsoft Azure PowerShell er et scripting-miljø, som du kan bruge til at styre og automatisere installation og administration af din arbejdsmængder i Azure. I denne artikel beskrives, hvordan du kan bruge PowerShell til at klargøre og administration af tjenesten Bus objekter som navneområder, forespørgsler og begivenhed Hubs ved hjælp af en lokal Azure PowerShell console.

## <a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende forudsætninger:

- Et Azure-abonnement. Azure er en abonnementsbaseret platform. Du kan finde flere oplysninger om at få et abonnement, skal du se [Købsmuligheder][], [Medlem tilbyder][]eller [Gratis prøveversion][].

- En computer med Azure PowerShell. Flere oplysninger under [installere og konfigurere Azure PowerShell][].

- En generel forståelse af PowerShell-scripts, NuGet-pakker og .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Herunder en reference til samlingen .NET for tjenesten Bus

Der findes et begrænset antal PowerShell-cmdletter til administration af tjenesten Bus. Hvis du vil klargøre enheder, der ikke vises gennem den eksisterende cmdletter, kan du bruge .NET klienten for tjenesten Bus i [tjenesten Bus NuGet pakke][].

Først skal du sørge for, at scriptet kan finde den samling, **Microsoft.ServiceBus.dll** , som er installeret sammen med NuGet pakken. For at være fleksible, udfører scriptet disse trin:

1. Bestemmer stien, hvor det blev aktiveret.
2. Kører på tværs stien, indtil der er angivet en mappe med navnet `packages`. Denne mappe oprettes, når du installerer NuGet pakker.
3. Gælder søgninger på `packages` mappe for en samling med navnet **Microsoft.ServiceBus.dll**.
4. Referencer samlingen, så de er tilgængelige til senere brug.

Her er, hvordan disse trin er implementeret i et PowerShell-script:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Klargør et Service Bus navneområde

To PowerShell-cmdlet'er understøtter Service Bus navneområde handlinger. Du kan bruge [Get-AzureSBNamespace][] og [Ny AzureSBNamespace][]i stedet for de .NET SDK API'er.

I dette eksempel oprettes et par lokale variabler i scriptet; `$Namespace` and `$Location`.

- `$Namespace`er navnet på tjenesten Bus navneområdet, som vi vil arbejde.
- `$Location`identificerer det datacenter, som scriptet bestemmelser navneområdet.
- `$CurrentNamespace`gemmer navneområdet for den reference, som scriptet henter (eller opretter).

I et faktisk script `$Namespace` og `$Location` kan overføres som parametre.

Denne del af scriptet udfører følgende opgaver:

1. Forsøger at hente en Service Bus navneområde med det angivne navn.
2. Hvis der er nogen navneområdet rapporter det hvad der blev fundet.
3. Hvis navneområdet, der ikke findes, opretter navneområdet og henter derefter det nyoprettede navneområde.

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Hvis du vil klargøre andre Service Bus enheder, du oprette en forekomst af klassen [NamespaceManager][] fra SDK.
Du kan bruge cmdlet'en [Get-AzureSBAuthorizationRule][] til at hente en godkendelsesregel for, der bruges til at give en forbindelsesstreng. Vi vil gemme en reference til den `NamespaceManager` forekomst i den `$NamespaceManager` variabel. Vi bruger `$NamespaceManager` senere i scriptet til at klargøre andre enheder.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Klargøring af andre Service Bus enheder

Brug [.NET API til Service Bus][]for at klargøre andre enheder, som køer, emner og begivenhed Hubs. I denne artikel fokuserer kun på begivenheden Hubs, men trinene til andre enheder er de samme. Desuden mere detaljerede eksempler, herunder andre enheder, der henvises til i slutningen af denne artikel.

Denne del af scriptet opretter fire flere lokale variabler. Disse variabler bruges til at oprette en forekomst af en `EventHubDescription` objekt. Scriptet udfører følgende opgaver:

1. Ved hjælp af den `NamespaceManager` objekt, til at undersøge, hvis begivenheden hubben identificeret med `$Path` findes.
2. Hvis den ikke findes, kan du oprette en `EventHubDescription` og overføre, til den `NamespaceManager` klasse `CreateEventHubIfNotExists` metode.
3. Når bestemmes, den begivenhed Hub er tilgængelig, har oprettet en forbruger gruppe ved hjælp af `ConsumerGroupDescription` og `NamespaceManager`.

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Overføre et navneområde til et andet Azure abonnement

Følgende sekvens af kommandoer flytter et navneområde fra ét Azure abonnement til en anden. For at udføre denne handling, navneområdet skal allerede være aktiv, og den bruger, der kører PowerShell-kommandoer skal være administrator på både kilde- og destinationswebsteder abonnementer.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Næste trin

I denne artikel angivet en grundlæggende disposition til klargøring af Service Bus objekter ved hjælp af PowerShell. Noget, du kan gøre brug af .NET klient-biblioteker, kan du også gøre i et PowerShell-script.

Der findes mere detaljerede eksempler på disse blogindlæg:

- [Sådan oprettes Service Bus køer, emner og abonnementer ved hjælp af en PowerShell-script](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Sådan oprettes en tjeneste Bus Namespace og en begivenhed Hub ved hjælp af en PowerShell-script](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Nogle færdige scripts er også tilgængelig til hentning:
- [Service Bus PowerShell-Scripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Købsmuligheder]: http://azure.microsoft.com/pricing/purchase-options/
[Medlem tilbud]: http://azure.microsoft.com/pricing/member-offers/
[Gratis prøveversion]: http://azure.microsoft.com/pricing/free-trial/
[Installere og konfigurere Azure PowerShell]: ../powershell-install-configure.md
[Tjenesten Bus NuGet pakke]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Ny AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[.NET API til Service Bus]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
