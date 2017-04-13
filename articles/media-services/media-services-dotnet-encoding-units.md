<properties 
    pageTitle="Sådan føjer du kodning enheder" 
    description="Lær, hvordan du hvordan du tilføjer kodning enheder med .NET"  
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>Sådan skalere kodning med .NET SDK

> [AZURE.SELECTOR]
- [Portal](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [RESTEN](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Oversigt

>[AZURE.IMPORTANT] Sørg for at gennemse emnet [Oversigt](media-services-scale-media-processing-overview.md) for at få flere oplysninger om skalering medier behandling emne.
 
For at ændre en reserveret enhedstype og antallet af kodning reserveret enheder ved hjælp af .NET SDK, skal du gøre følgende:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>Åbne en Supportbilletter

Hver Media Services-konto kan som standard skaleres til op til 25 kodning og 5 efter behov Streaming reserveret enheder. Du kan anmode om en højere grænse ved at åbne en supportbilletter.

###<a name="open-a-support-ticket"></a>Åbne en supportbilletter

Brugertilladelse Benyt følgende fremgangsmåde for at åbne en support:

1. Klik på [få Support](https://manage.windowsazure.com/?getsupport=true). Hvis du ikke er logget, bliver du bedt om at angive dine legitimationsoplysninger.

1. Vælg dit abonnement.

1. Vælg "Tekniske" under type af support.

1. Klik på "Oprette brugertilladelse".

1. Vælg "Azure Media Services" på produktlisten præsenteres på den næste side.

1. Vælg en "problemtype", der er relevante for dit problem.

1. Klik på Fortsæt.

1. Følg instruktionerne på næste side og derefter angive oplysninger om dit problem.

1. Klik på Send for at åbne brugertilladelse.



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
