<properties 
    pageTitle="Overføre ofte stillede spørgsmål om | Microsoft Azure"
    description="Svar på nogle ofte stillede spørgsmål om Azure Relay."
    services="service-bus"
    documentationCenter="na"
    authors="jtaubensee"
    manager=""
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub" />

# <a name="relay-faq"></a>Relay ofte stillede spørgsmål

I denne artikel finder du svar på nogle ofte stillede spørgsmål om Microsoft Azure Relay. Du kan også gå [Azure understøtter ofte stillede spørgsmål om](http://go.microsoft.com/fwlink/?LinkID=185083) generelle Azure priser og understøttelse af oplysninger. De følgende emner er inkluderet:

- [Generelle spørgsmål om Azure Relay](#general-questions)
- [Priser](#pricing)
- [Kvoter](#quotas)
- [Administration af abonnement og navneområdet](#subscription-and-namespace-management)
- [Fejlfinding i forbindelse med](#troubleshooting)

## <a name="general-questions"></a>Generelle spørgsmål

### <a name="what-is-azure-relay"></a>Hvad er Azure Relay?

[Relay-tjenesten](relay-what-is-it.md) giver mulighed for at transparent vært og få adgang til WCF-tjenester fra et vilkårligt sted. Det vil sige, den måde kan hybrid programmer, der kører i både en Azure datacenter og et lokalt virksomhedsmiljø.

### <a name="what-is-a-relay-namespace"></a>Hvad er et Relay navneområde?

Et [navneområde](Relay-create-namespace-portal.md) indeholder en angiver området objektbeholder til at håndtere Relay ressourcer i dit program. Oprette et er nødvendigt at bruge Relay og vil være en af de første trin i Introduktion.

## <a name="pricing"></a>Priser

Dette afsnit finder du svar på nogle ofte stillede spørgsmål om Relay priser struktur. Du kan også gå [Azure Support ofte stillede spørgsmål](http://go.microsoft.com/fwlink/?LinkID=185083) til generelle Microsoft Azure prisoplysninger. Yderligere oplysninger om Relay priser, se [Service Bus priser detaljer](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-relay"></a>Hvordan du tage et gebyr for Relay?

Yderligere oplysninger om Relay priser, se [Service Bus priser detaljer][Pricing overview]. Ud over de priser, der er angivet, betaler du for tilknyttede dataoverførsler for udgangspunkt uden for datacenteret, hvor dit program er klargjort.

### <a name="what-usage-of-relay-is-subject-to-data-transfer"></a>Hvilke brugen af Relay er underlagt dataoverførsel?

Relay omfatter 5 GB data vandindtrængen månedsbasis per abonnement. Der er gratis yderligere til Azure vandindtrængen udgangspunkt for data, der bruges af Relay.

Data gebyr for Relay er for indtrængen fra kun afsendere, som Relay lyttere betale ikke et gebyr data. Eksempelvis hvis du sender 1 GB, vil du kun blive faktureret for 1 GB, selvom en lytter også modtaget 1 GB og kan være uden for Azures datacentre.

### <a name="how-are-relay-hours-calculated"></a>Hvordan beregnes Relay timer?

Relay timer faktureres for den akkumulerede mængde af tid, hvor hver Relay er "Åbn" i en given faktureringsperiode. En Relay er implicit en forekomst af og åbnes på en given navneområde, når en Relay lytter først opretter forbindelse til den pågældende adresse. Videresendelse er lukket, kun, når den sidste lytteren afbryder forbindelsen fra dens adresse. Derfor fakturering henblik en Relay betragtes som "Åbn" fra tidspunktet, forbinder første Relay lytteren, til den sidste Relay lytteren afbryder forbindelsen fra navneområdet tid. Med andre ord, en Relay betragtes som Åbn, når en eller flere Relay lyttere har forbindelse til dens Service Bus adresse.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>Hvad nu, hvis jeg har mere end én lytteren forbindelse til en given Relay?

I nogle tilfælde kan en enkelt Relay har flere forbundne lyttere. En Relay betragtes som "åbne", når mindst én Relay lytteren er forbundet til netværket. Tilføje yderligere lyttere i en åben Relay medfører yderligere relay timer. Antallet af Relay afsendere (klienter, der kalde eller sende meddelelser til relæer) forbindelse til en Relay også har ingen indflydelse på beregningen af Relay timer.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hvordan beregnes meddelelser tælleren for WCF relæer?

**Dette gælder kun for WCF relæer og er ikke en omkostning af Hybrid forbindelser**

Generelt, beregnes fakturerbar meddelelser for relæer ved hjælp af den samme metode, som er beskrevet ovenfor til formidlet enheder (køer, emner og abonnementer). Der er dog flere væsentlige forskelle:

Sende en meddelelse til en tjeneste Bus Relay behandles som en "fuld gennem" send til Relay lytteren, der modtager meddelelsen i stedet for en send til tjenesten Bus Relay efterfulgt af en kvittering på levering til Relay lytteren. Derfor en anmodning om autosvar typografi service Start (af op til 64 KB) mod en Relay lytter medfører to fakturerbar meddelelser: én fakturerbar meddelelse for anmodningen og en enkelt fakturerbar meddelelse til svaret (Hvis du allerede har svaret er også \<= 64 KB). Dette er forskellig fra ved hjælp af en kø til at formidle mellem en klient og en tjeneste. I så fald ville gøre det samme anmodning autosvar mønster kræver en anmodning om send til køen, efterfulgt af en dequeue/levering fra køen til tjenesten, efterfulgt af send et svar til en anden kø, og en dequeue/levering fra den pågældende kø til klienten. Ved hjælp af den samme (\<= 64 KB) størrelse antagelser i hele kører uden indgriben kø mønster og som derfor resulterer i fire fakturerbar meddelelser, dobbelt så mange faktureret at implementere det samme mønster ved hjælp af Relay. Der er naturligvis fordele ved at bruge køer til at opnå denne mønster, som holdbarhed og Indlæs udjævning. Disse fordele kan justere de ekstra udgifter.

Relæer, der åbnes ved hjælp af netTCPRelay WCF binding behandler meddelelser ikke som individuelle meddelelser, men som en strøm af data, der flyder gennem systemet. Med andre ord kun afsender og lytteren har indsigt i indramningen af enkelte meddelelser sendes/modtages ved hjælp af denne binding. For relæer ved hjælp af netTCPRelay indbinding, fortolkes alle data og som derfor, som en stream ved beregningen af fakturerbar meddelelser. I dette tilfælde beregner Service Bus den samlede mængde af data sendt eller modtaget via hver enkelt Relay på grundlag af 5 minutter og dividere, total med 64 KB for at bestemme antallet af fakturerbar meddelelser for den pågældende under den angivne periode Relay.

## <a name="quotas"></a>Kvoter

|Kvote navn|Omfang|Type|Funktionsmåde, når er overskredet|Værdi|
|---|---|---|---|---|
|Samtidige lyttere på en relay|Enhed|Statisk|Afvises efterfølgende anmodninger om flere forbindelser, og en undtagelse kan modtages af koden opkald.|25|
|Samtidige relay lyttere|Hele systemet|Statisk|Afvises efterfølgende anmodninger om flere forbindelser, og en undtagelse kan modtages af koden opkald.|2.000|
|Samtidige relay-forbindelser i alle relay slutpunkter i et service navneområde|Hele systemet|Statisk|-|5.000|
|Relay slutpunkter for hver tjeneste navneområde|Hele systemet|Statisk|-|10.000|
|Meddelelsesstørrelsen [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) og [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) videresender|Hele systemet|Statisk|Indgående meddelelser, der overskrider disse kvoter vil blive afvist og en undtagelse kan modtages af koden opkald.|64KB
|Meddelelsesstørrelsen [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) og [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) videresender|Hele systemet|Statisk|-|Ubegrænset|

### <a name="does-relay-have-any-usage-quotas"></a>Har Relay en hvilken som helst kvoter for ressourceforbrug?

Som standard for en hvilken som helst skyen angiver tjenesten Microsoft en samling månedlige kvoten for anvendelse, der beregnes på tværs af alle en kundes abonnementer. Da vi forstår, at du muligvis mere end disse begrænsninger, skal du kontakte kundeservice når som helst så vi kan forstå dine behov og justere disse begrænsninger korrekt. For tjenesten Bus er Sammenlæg brugen kvoter på følgende måde:

- 5 milliarder meddelelser
- 2 millioner Relay timer

Mens vi forbeholder sig ret til at deaktivere en kundekonto, der har overskredet dens kvoter for ressourceforbrug i en given måned, kan vi giver besked via e-mail og foretage flere forsøg på at kontakte en kunde før du udfører en handling. Kunder, der overskrider disse kvoter vil stadig være ansvarlig for gebyrer, der overskrider kvoterne.

#### <a name="naming-restrictions"></a>Begrænsninger for navngivning

Et Relay navneområde navn kan kun være mellem 6-50 tegn.

## <a name="subscription-and-namespace-management"></a>Administration af abonnement og navneområdet

### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hvordan kan jeg overføre et navneområde til et andet Azure abonnement?

Du kan bruge PowerShell-kommandoer (findes i artiklen [her](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription)) til at flytte et navneområde fra ét Azure abonnement til en anden. For at udføre handlingen, skal navneområdet allerede være aktiv. Udfør kommandoerne brugeren skal også være administrator på både kilde- og destinationswebsteder abonnementer.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Hvad er nogle af de undtagelser, der genereres af Azure Relay API'er og deres foreslåede handlinger?

[Relay undtagelser] [ Relay exceptions] artikel beskrives nogle undtagelser med foreslåede handlinger.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Hvad er en delt Access-signatur, og hvilke sprog understøtter oprettelse af en signatur?

Delte Access signaturer er en godkendelsesmetoden, der er baseret på SHA – 256 sikker hashes eller URI'er. Oplysninger om, hvordan du kan oprette dine egne signaturer i Node, PHP, Java og C\#, artiklen [Delt Access signaturer][] .

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Delt adgang signaturer]: service-bus-sas-overview.md

## <a name="next-steps"></a>Næste trin:

- [Oprette et navneområde](relay-create-namespace-portal.md)
- [Introduktion til .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Komme i gang med Node](relay-hybrid-connections-node-get-started.md)