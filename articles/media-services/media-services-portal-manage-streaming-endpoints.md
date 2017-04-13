<properties 
    pageTitle="Administrere streaming slutpunkter ved hjælp af Azure portal | Microsoft Azure" 
    description="Dette emne viser, hvordan du administrerer streaming slutpunkter ved hjælp af Azure portal." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>Administrere streaming slutpunkter ved hjælp af Azure portal

## <a name="overview"></a>Oversigt

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 

I Microsoft Azure Media Services repræsenterer et **Streaming slutpunkt** en streaming tjeneste, som kan levere indhold direkte til en player-klientprogrammet eller til et indhold levering netværk (CDN) til yderligere distribution. Media Services giver også nem integration med Azure CDN. Udgående strømmen fra en StreamingEndpoint tjeneste kan være en live stream eller en video on demand aktiv i kontoen Media Services.

Desuden kan du styre kapaciteten af tjenesten Streaming slutpunkt til at håndtere voksende båndbredde behov ved at justere streaming enheder. Det anbefales at tildele en eller flere skalaenheder til programmer i produktionsmiljø. Streaming enheder giver dig både dedikeret udgangspunkt kapacitet, der kan købes i tidsintervaller 200 Mbps og flere funktioner, som omfatter: [dynamiske emballagen](media-services-dynamic-packaging-overview.md), CDN integration og Avanceret konfiguration.

>[AZURE.NOTE]Du er kun faktureret, når din Streaming slutpunkt er i kører tilstand.

Dette emne giver et overblik over de vigtigste funktioner, der leveres af Streaming slutpunkter. Emnet viser også, hvordan bruge Azure-portalen til at administrere streaming slutpunkter. Du kan finde oplysninger om, hvordan du skalere det streaming slutpunkt [i dette](media-services-portal-scale-streaming-endpoints.md) emne.

## <a name="start-managing-streaming-endpoints"></a>Begynde at administrere streaming slutpunkter

Hvis du vil starte administrere streaming slutpunkter for din konto, skal du gøre følgende.

1. Vælg kontoen Azure Media Services på [Azure-portalen](https://portal.azure.com/).
2. Vælg **Streaming slutpunkter**i vinduet **Indstillinger** .

    ![Streaming slutpunkt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>Tilføje/slette et streaming slutpunkt

Hvis du vil tilføje/slette streaming slutpunktet ved hjælp af portalen Azure, kan du gøre følgende:

1. Hvis du vil tilføje et streaming slutpunkt, klikke på **+ slutpunkt** øverst på siden. 
2. Hvis du vil slette et streaming slutpunkt, skal du trykke på knappen **Slet** . 

    Standard streaming slutpunkt kan ikke slettes.
2. Klik på knappen **Start** for at starte det streaming slutpunkt.

    ![Streaming slutpunkt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Du kan have op til to streaming slutpunkter som standard. Hvis du vil anmode om mere, skal du se [kvoter og begrænsninger](media-services-quotas-and-limitations.md).
    
##<a id="configure_streaming_endpoints"></a>Konfiguration af Streaming slutpunktet

Streaming slutpunkt, kan du konfigurere følgende egenskaber, når du har mindst 1 enhed for tidsskala: 

- Adgangskontrol
- Cache-styring
- Cross politikker for access af websted

Du kan finde detaljerede oplysninger om disse egenskaber, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Du kan konfigurere streaming slutpunkt ved at gøre følgende:

1. Vælg det streaming slutpunkt, som du vil konfigurere.
1. Klik på **Indstillinger**.
  
En kort beskrivelse af felterne, der følger.

![Streaming slutpunkt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Maksimale cachepolitik: bruges til at konfigurere cache levetid for Aktiver tjent med at benytte dette streaming slutpunkt. Hvis ingen værdi er angivet, bruges standard. Standardværdierne kan også være defineret direkte i Azure-lager. Hvis Azure CDN er aktiveret for streaming slutpunktet, skal du ikke indstille værdien cache politik til mindre end 600 sekunder.  

2. Tilladte IP-adresser: bruges til at angive IP-adresser, der ville have tilladelse til at oprette forbindelse til den publicerede streaming slutpunkt. Hvis ingen IP-adresser, der er angivet, vil alle IP-adresser skal kunne oprette forbindelse. IP-adresser kan angives som en enkelt IP-adresse (for eksempel ' 10.0.0.1'), en IP-område, ved hjælp af en IP-adresse og en CIDR undernetmaske (for eksempel ' 10.0.0.1/22') eller en IP-område, ved hjælp af IP-adresse og en stiplet decimal undernetmaske (for eksempel ' 10.0.0.1(255.255.255.0)').

3. Konfiguration for Akamai signatur sidehoved godkendelse: bruges til at angive, hvordan signatur i sidehoved anmodningen om godkendelse fra Akamai servere er konfigureret. Udløb er i UTC.



##<a id="enable_cdn"></a>Aktivere Azure CDN integration

Du kan angive for at aktivere Azure CDN integration for et Streaming slutpunkt (det er deaktiveret som standard).

Sådan angives Azure CDN integrationen sand:

- Streaming slutpunktet skal have mindst én streaming enhed. Hvis du vil senere angivet skalaenheder til 0, skal du først deaktivere CDN integrationen. Som standard når du opretter en ny streaming angives slutpunkt én streaming enhed automatisk.

- Streaming slutpunktet skal være i en standset tilstand. Når CDN bliver aktiveret, kan du begynde streaming slutpunktet. 

Det kan tage op til 90 min til Azure CDN integrationen få aktiveret.  Det tager op til to timer for at ændringerne kan aktiveres på tværs af alle de CDN vises.

LEVERANDØR(er) integration er aktiveret i alle Azure datacentre: os vest, OS Øst, nord Europe, vest Europe, Japan vest, Japan Øst, syd Østasien og Østasien.

Når den er aktiveret, bliver **Adgangskontrol** konfigurationen deaktiveret.

![Streaming slutpunkt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Azure Media Services-integration med Azure CDN er implementeret på **Azure CDN fra Verizon**.  Hvis du vil bruge **Azure CDN fra Akamai** til Azure Media Services, skal du [konfigurere slutpunktet manuelt](../cdn/cdn-create-new-endpoint.md).  Du kan finde flere oplysninger om Azure CDN funktioner, [CDN oversigt](../cdn/cdn-overview.md).

###<a name="additional-considerations"></a>Yderligere overvejelser

- Når CDN er aktiveret for et streaming slutpunkt, kan klienter kan ikke anmode om indhold direkte fra startpunkt. Hvis du har brug for muligheden for at teste dit indhold med eller uden CDN, kan du oprette en anden streaming slutpunkt, der ikke er CDN aktiveret.
- Din streaming slutpunkt hostname forbliver uændret efter aktivering CDN. Du behøver ikke at foretage ændringer i arbejdsprocessen media services, når CDN er aktiveret. Eksempelvis hvis din streaming slutpunkt hostname er strasbourg.streaming.mediaservices.windows.net, når du har aktiveret CDN, bruges den nøjagtige samme hostname.
- For nye streaming slutpunkter, kan du aktivere CDN blot ved at oprette et nyt slutpunkt for eksisterende streaming slutpunkter skal du først stoppe slutpunktet og derefter aktivere CDN.
 

Flere oplysninger under [præsentation af Azure Media Services integration med Azure CDN (indhold netværk til levering af)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##<a name="next-steps"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
