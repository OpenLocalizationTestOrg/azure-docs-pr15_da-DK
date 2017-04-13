<properties
   pageTitle="Fejlfinding i forbindelse med din lokale Service-strukturen klynge konfiguration | Microsoft Azure"
   description="Denne artikel omhandler et sæt af forslag til fejlfinding i forbindelse med din lokale udvikling klynge"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>Fejlfinding i forbindelse med din lokale udvikling klynge konfiguration

Hvis der opstår et problem med at arbejde med din lokale Azure Service strukturen udvikling klynge, kan du gennemgå følgende forslag til mulige løsninger.

## <a name="cluster-setup-failures"></a>Klynge konfiguration af fejl

### <a name="cannot-clean-up-service-fabric-logs"></a>Kan ikke rydde op i tjenesten strukturen logfiler

#### <a name="problem"></a>Problem

Mens du kører scriptet DevClusterSetup, vist du en fejlmeddelelse som denne:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Løsning

Lukke den aktuelle PowerShell-vindue, og Åbn et nyt vindue i PowerShell som administrator. Du bør nu kunne køre scriptet.

## <a name="cluster-connection-failures"></a>Klynge internetforbindelse, fejl

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Tjenesten strukturen PowerShell-cmdlet'er genkendes ikke i Azure PowerShell

#### <a name="problem"></a>Problem

Hvis du forsøger at køre en af Service-strukturen PowerShell-cmdletterne, såsom `Connect-ServiceFabricCluster` i en Azure PowerShell-vinduet, det mislykkes, siger, at cmdlet ikke genkendes. Årsagen til dette er, Azure PowerShell bruger 32-bit version af Windows PowerShell (selv på 64-bit versioner af Operativsystemet), mens tjenesten strukturen cmdletter kun kan fungere i 64-bit miljøer.

#### <a name="solution"></a>Løsning

Altid køre Service-strukturen cmdletter direkte fra Windows PowerShell.

>[AZURE.NOTE] Den seneste version af Azure PowerShell opretter ikke en speciel genvej, så det ikke længere skal forekomme.

### <a name="type-initialization-exception"></a>Skriv initialisering undtagelse

#### <a name="problem"></a>Problem

Når du opretter forbindelse til klynge i PowerShell, kan du se fejlen TypeInitializationException for System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Løsning

Path-variabel er ikke angivet korrekt under installationen. Log af Windows, og log på igen. Dette opdaterer fuldt din sti.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Klynge-forbindelse afbrydes med "Objektet er lukket"

#### <a name="problem"></a>Problem

Et opkald for at forbinde ServiceFabricCluster mislykkes med en fejl som denne:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Løsning

Lukke den aktuelle PowerShell-vindue, og Åbn et nyt vindue i PowerShell som administrator. Du bør nu kunne oprette forbindelse.

### <a name="fabric-connection-denied-exception"></a>Strukturen forbindelse nægtet undtagelse

#### <a name="problem"></a>Problem

Når du foretager fejlfinding fra Visual Studio, får du en FabricConnectionDeniedException fejl.

#### <a name="solution"></a>Løsning

Denne fejl opstår normalt, når du prøve at prøve at starte en tjenestevært behandler manuelt i stedet for at give mulighed for at tjenesten strukturen runtime skal starte den for dig.

Sørg for, at du ikke har en hvilken som helst service projekter, der er angivet som Start projekter i din løsning. Kun Service-strukturen programmet projekter skal du vælge Start projekter.

>[AZURE.TIP] Hvis efter installationen, din lokale klynge begynder at fungerer korrekt, kan du nulstille den ved hjælp af programmet lokale klynge manager system bakke. Dette fjerner den eksisterende klynge og konfigurere en ny. Vær opmærksom på, at alle udrulles programmer og tilknyttede data fjernes.

## <a name="next-steps"></a>Næste trin

- [Forstå og foretage fejlfinding af din klynge med system tilstandsrapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualisere din klynge med Service-strukturen Stifinder](service-fabric-visualizing-your-cluster.md)
