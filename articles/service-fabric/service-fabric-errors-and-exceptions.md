<properties
   pageTitle="Almindelige FabricClient undtagelser udløst | Microsoft Azure"
   description="I denne artikel beskrives de almindelige undtagelser og fejl, der kan være udløst af FabricClient APIs under udførelse af programmet og klynge management handlinger."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Almindelige undtagelser og fejl, når du arbejder med FabricClient APIs
[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) API'er aktivere klynge og programmet administratorer for at udføre administrative opgaver på en tjeneste-strukturen programmet, tjenesten eller klynge. Programmet installation, opgradering og fjernelse af kontrollere tilstanden en klynge eller test af en tjeneste. Programmet udviklere og klynge administratorer kan bruge de FabricClient APIs til at udvikle værktøjer til administration af tjenesten strukturen klynge og programmer.

Der er mange forskellige typer handlinger, der kan udføres ved hjælp af FabricClient.  Hver enkelt metode kan Udløs undtagelser for fejl på grund af forkert input, kørselsfejl eller midlertidige infrastruktur problemer.  Se dokumentationen til reference API til at finde ud af, hvilke undtagelser er udløst af en bestemt metode. Der er nogle undtagelser, men der kan være udløst af mange forskellige [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) API'er. I følgende tabel vises de undtagelser, der er almindelige på tværs af de FabricClient APIs.

|Undtagelse| Udløst hvornår|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) objektet er i en lukket tilstand. Sælge for objektet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) , du bruger og oprette en ny [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) objekt. |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|Handlingen blev afbrudt. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) returneres, når handlingen tager mere end MaxOperationTimeout til at fuldføre.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|Adgangskontrol af for handlingen mislykkedes. E_ACCESSDENIED returneres.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Der opstod en runtime under udførelse af handlingen. Alle FabricClient metoder kan potentielt Udløs [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), egenskaben [fejlkode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) angiver den nøjagtige årsag til undtagelsen. Fejlkoder er defineret i [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) optælling.|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|Handlingen mislykkedes på grund af en midlertidig fejltilstand slags. Handlingen kan for eksempel mislykkes, fordi en quorum replikaer ikke kan midlertidigt nås. Midlertidige undtagelser svarer til mislykkedes handlinger, som kan blive forsøgt igen.|

Nogle almindelige [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) fejl, der kan returneres i en [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Fejl| Betingelse|
|---------|:-----------|
|CommunicationError|Fejlen kommunikation årsagen til handlingen mislykkes, skal du prøve igen.|
|InvalidCredentialType|Typen legitimationsoplysninger er ugyldige.|
|InvalidX509FindType|X509FindType er ugyldig.|
|InvalidX509StoreLocation|X509 placering er ugyldig.|
|InvalidX509StoreName|X509 Butiksnavn er ugyldig.|
|InvalidX509Thumbprint|X509 certifikat miniature streng er ugyldig.|
|InvalidProtectionLevel|Beskyttelsesniveauet er ugyldig.|
|InvalidX509Store|X509 lager ikke kan åbnes.|
|InvalidSubjectName|Emnenavnet er ugyldig.|
|InvalidAllowedCommonNameList|Formatet af almindelige navn listen streng er ugyldige. Det skal være en kommasepareret liste.|
