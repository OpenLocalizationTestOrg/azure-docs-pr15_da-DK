<properties
   pageTitle="Cloud Cruiser og Microsoft Azure fakturering API Integration | Microsoft Azure"
   description="Indeholder et entydigt perspektiv fra Microsoft Azure fakturering partner skyen Cruiser på deres oplevelser integrere Azure fakturering API'er i deres produkt.  Dette er især nyttig til Azure og skyen Cruiser kunder, der er interesseret i at bruge/forsøger skyen Cruiser til Microsoft Azure Pack."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cruiser og Microsoft Azure fakturering API Integration i skyen

I denne artikel beskrives, hvordan de oplysninger, der indsamles fra den nye Microsoft Azure fakturering API'er, som kan bruges i skyen Cruiser til arbejdsprocessen omkostninger simulering og analyse.

## <a name="azure-ratecard-api"></a>Azure RateCard API
RateCard API indeholder rente oplysninger fra Azure. Efter godkendelse med de korrekte legitimationsoplysninger, kan du forespørge API for at indsamle metadata om de tjenester, der er tilgængelige på Azure, sammen med de satser, der er knyttet til dit tilbyder-ID.

Følgende er et eksempel svar fra API viser priserne for A0 (Windows) forekomst:

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Cruisers grænsefladen til Azure RateCard API i skyen
Skyen Cruiser kan udnytte RateCard API oplysningerne på forskellige måder. I denne artikel får du vist, hvordan det kan bruges til at foretage IaaS arbejdsbelastningen omkostninger simulering og analyse.

Forestil dig en arbejdsbyrde af flere forekomster, der kører på Microsoft Azure Pack (WAP) for at vise dette Brug tilfælde. Målet er at simulere denne samme arbejdsbelastningen på Azure og anslå omkostninger ved at gøre disse overførsel. For at oprette denne simulering, er der to primære opgaver skal udføres:

1. **Importere og behandle indsamles fra RateCard API oplysningerne om tjenesten.** Denne opgave udføres også på de projektmapper, hvor Udpak fra RateCard API er transformeret og publiceret på en ny rente plan. Denne nye rente plan bruges på simuleringerne til at anslå Azure priser.

2. **Normalisere WAP tjenester og Azure tjenester for IaaS.** Som standard WAP services er baseret på individuelle ressourcer (CPU, hukommelsesstørrelse, diskstørrelse, osv.) under Azure services er baseret på forekomst størrelse (A0, A1, A2 osv.). Denne første opgave kan udføres af skyen Cruiser ETL program, kaldet projektmapper, hvor disse ressourcer kan samles på forekomst størrelser, svarer til Azure forekomst tjenester.

### <a name="import-data-from-the-ratecard-api"></a>Importere data fra RateCard API

Skyen Cruiser projektmapper ikke en automatisk metode til at indsamle og behandle oplysninger fra RateCard API.  ETL (Udpak-transformation-belastning) projektmapper kan du konfigurere indsamling, transformation og publicering af data til skyen Cruiser databasen.

Hver projektmappe kan have en eller flere samlinger, så du kan koordinere oplysninger fra forskellige kilder til supplement eller udvide brugerdata. De følgende to skærmbilleder viser, hvordan du opretter en ny *samling* i en eksisterende projektmappe og import af oplysninger i gruppen *af websteder* fra RateCard API:

![Figur 1 - oprette en ny samling][1]

![Figur 2 - importere data fra den nye websteder][2]

Når dataene er importeret til projektmappen, er det muligt at oprette flere trin og transformation processer, til at ændre og modellere data. I dette eksempel skal da vi er kun interesseret i infrastruktur som-en-tjenesten (IaaS), kan vi bruge transformation trinnene til at fjerne unødvendige rækker eller poster, der vedrører tjenester end IaaS.

Følgende skærmbillede viser de transformation trin, der bruges til at behandle de data, der indsamles fra RateCard API:

![Figur 3 - Transformation trin til at behandle indsamlede data fra RateCard API][3]

### <a name="defining-new-services-and-rate-plans"></a>Definere nye tjenester og rente-planer

Der er forskellige måder at oprette tjenester i skyen Cruiser. En af indstillingerne er at importere tjenesterne fra brugerdata. Denne metode er ofte anvendte, når du arbejder med offentlige skyer, hvor tjenesterne, der allerede er defineret af udbyderen.

En Plan for rente er et sæt af satser eller priser, der kan anvendes på forskellige tjenester baseret på ikrafttrædelsesdatoer eller gruppe af kunder, blandt andre indstillinger. Rente planer kan også bruges på skyen Cruiser til at oprette simulering eller "Hvad hvis" scenarier, for at forstå, hvordan ændringer i tjenester kan påvirke de samlede omkostninger for en arbejdsbyrde.

I dette eksempel bruger vi service oplysningerne fra RateCard API til at definere nye tjenester i skyen Cruiser. På samme måde, kan vi bruge de satser, der er knyttet til tjenesterne til at oprette en ny rente planlægge på skyen Cruiser.

I slutningen af processen transformation er det muligt at oprette et nyt trin og publicere data fra RateCard API som nye tjenester og -satser.

![Figur 4 - udgivelse af dataene fra RateCard API med nye tjenester og -satser][4]

### <a name="verify-azure-services-and-rates"></a>Bekræfte Azure tjenester og -satser

Efter udgivelse tjenester og -satser, kan du kontrollere listen over importerede tjenester i skyen Cruiser under fanen *tjenester* :

![Figur 5 - ved at bekræfte de nye tjenester][5]

Du kan se den nye rente plan kaldet "AzureSimulation" med satserne importeret fra RateCard API under fanen *Rente planer* .

![Figur 6 - bekræftelse af nye rente planlægge og tilknyttede satser][6]

### <a name="normalize-wap-and-azure-services"></a>Normalisere WAP og Azure tjenester

Som standard indeholder WAP brugeroplysninger baseret på brug af Beregn, hukommelse og netværksressourcer. Du kan definere dine tjenester, der er baseret i skyen Cruiser direkte på tildeling eller betalte brugen af disse ressourcer. For eksempel kan du angive en grundlæggende sats for hver time i CPU-brug, eller du kan tage et gebyr GB hukommelse, der er allokeret til en forekomst.

I dette eksempel skal for at sammenligne omkostninger mellem WAP og Azure, vil vi sammenlægge visningen Ressourceforbrug på WAP i pakker, som kan knyttes til Azure services. Denne transformation kan nemt implementeret i projektmapper:

![Figur 7 - at transformere WAP data for at normalisere tjenester][7]

Det sidste trin i projektmappen er at udgive dataene til skyen Cruiser databasen. I dette trin er de nu samlet i tjenester (der knyttes til Azure Services) og bundet til standard satser til at oprette gebyrerne.

Du kan automatisere behandling af data, når projektmappen er færdig, ved at tilføje en opgave på planlæggeren og angive hyppigheden og tidspunktet for projektmappen til at køre.

![Figur 8 - planlægning af projektmappe][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Oprette rapporter til arbejdsbelastningen omkostninger simulering analyse

Efter brug der indsamles, og gebyrer er indlæst i skyen Cruiser databasen, kan vi udnytte modulet skyen Cruiser indsigt for at oprette arbejdsbelastningen omkostninger simulering, som vi ønsker.

For at kunne demonstrerer dette scenario, oprettede vi følgende rapport:

![Sammenligning af omkostninger][9]

Øverste grafen viser en sammenligning af omkostninger-tjenester, sammenligne pris kører arbejdsbelastningen for hver bestemt tjeneste mellem WAP (mørkeblå) og Azure (lyseblå).

Nederste grafen viser de samme data, men opdelt efter afdeling. Dette viser omkostningerne for hver afdeling til at køre deres arbejdsbelastningen på WAP og Azure, sammen med forskellen mellem dem i værktøjslinjen sparer (grøn).

## <a name="azure-usage-api"></a>Azure brugen API


### <a name="introduction"></a>Introduktion

Microsoft introduceret senest Azure brugen API, så abonnenter til at trække fra et program i Brugsdata for at få indsigt i deres forbrug. Dette er gode nyheder til skyen Cruiser kunder, der kan drage fordel af de bedre datasæt, der er tilgængelige via denne API.

Skyen Cruiser kan udnytte integrationen med brugen API på flere måder. Granularitet (hver time oplysninger om brug) og metadata ressourceoplysninger gennem API giver den nødvendige datasæt for at understøtte fleksible Showback eller kortejeren modeller. 

I dette selvstudium skal vi vises et eksempel på hvordan skyen Cruiser kan drage fordel af brugen API oplysninger. Mere specifikt skal vi oprette en ressourcegruppe på Azure, knytte mærker for kontostrukturen og derefter beskrives processen med at og behandlingen af mærke oplysningerne i skyen Cruiser.
 
Den endelige mål er at kunne oprette rapporter som det følgende og kunne analysere omkostninger og forbrug, der er baseret på kontostrukturen udfyldt ved mærkerne.

![Figur 10 - rapport med opdelinger, der bruger mærker][10]

### <a name="microsoft-azure-tags"></a>Microsoft Azure mærker

De data, der er tilgængelige via Azure brugen API omfatter ikke kun forbrug oplysninger, men også ressource metadata, herunder eventuelle mærker, der er knyttet til den. Mærker er en nem måde at organisere dine ressourcer, men for at være effektiv, skal du sikre, at:

- Mærker anvendes korrekt til ressourcer på klargøring tid
- Mærker bruges korrekt på Showback/kortejeren processen til knytter en brugen af organisationens kontostruktur.

Begge af disse krav kan være en udfordring, især hvis der er en manuel proces på levering eller at opkræve side bruges. Fejlskrevne, forkert eller endda manglende mærker er almindelige klager fra kunder, når ved hjælp af mærker og disse fejl kan gøre liv i afgifts side meget svært.

Med det nye Azure brugen API skyen Cruiser adskille mærkning ressourceoplysninger, og gennem en avanceret ETL værktøj, der hedder projektmapper, ret disse almindelige mærkning fejl. Via transformation ved hjælp af regulære udtryk og data korrelations, skyen Cruiser identificere forkert mærket ressourcer og anvende de korrekte koder sikre, at den rigtige tilknytning af ressourcer med forbrugeren.

På siden afgifts skyen Cruiser automatisere Showback/kortejeren proces og kan udnytte oplysningerne mærke for at knytter brugen til den relevante forbruger (afdeling, afdeling, projekt osv.). Denne automatisering giver en stor forbedring og kan sikre, at en ensartet og kan overvåges afgifts proces.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Oprette en ressourcegruppe med koder på Microsoft Azure
Det første trin i dette selvstudium er at oprette en ressourcegruppe i portalen Azure derefter Opret nye mærker skal knyttes til ressourcer. I dette eksempel skal vi opretter følgende koder: afdeling, miljø, ejer, Project.

Skærmbilledet nedenfor viser et eksempel ressourcegruppe med de tilknyttede mærker.

![Figur 11 - ressourcegruppe med tilknyttede koder på Azure-portalen][11]

Næste trin er at trække oplysningerne fra brugen API til skyen Cruiser. Brugen API indeholder aktuelt svar i JSON-format. Her er et eksempel på de hentede data:


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importere data fra brugen API til skyen Cruiser

Skyen Cruiser projektmapper ikke en automatisk metode til at indsamle og behandle oplysninger fra brugen API. En ETL (Udpak-transformation-belastning) projektmappe, kan du konfigurere indsamling, transformation og publicering af data til skyen Cruiser databasen.

Hver projektmappe kan have en eller flere af websteder. Dette kan du koordinere oplysninger fra forskellige kilder til supplement eller udvide brugerdata. I dette eksempel skal vi opretter et nyt ark i projektmappen Azure-skabelon (_UsageAPI)_ , og Angiv en ny _af websteder_ til at importere oplysninger fra brugen API.

![Figur 3 - brugen API dataene er importeret til arket UsageAPI][12]

Bemærk, at denne projektmappe allerede indeholder andre ark, der skal importeres tjenester fra Azure (_ImportServices_) og procesoplysninger forbrug fra fakturering API (_PublishData_).

Næste skal vi bruge brugen API til at udfylde _UsageAPI_ arket og koordinere oplysningerne med forbrugsdata fra API Fakturering på arket _PublishData_ .

### <a name="processing-the-tag-information-from-the-usage-api"></a>Behandling af mærke oplysninger fra brugen API

Når dataene er importeret til projektmappen, opretter vi transformation trin i arket _UsageAPI_ for at behandle oplysningerne fra API. Første trin er at bruge en "JSON opdele" processor til at udtrække mærkerne fra et enkelt felt og derefter oprette felter for hver enkelt af dem (afdeling, Project, ejer og miljø).

![Figur 4 - oprette nye felter for oplysninger om kode][13]

Meddelelse om tjenesten "Netværk" der mangler oplysninger om kode (gul felt), men vi kan du kontrollere, at det er en del af den samme ressourcegruppe ved at se på feltet _ResourceGroupName_ . Da vi har koderne for de andre ressourcer fra denne ressourcegruppe, kan vi bruge disse oplysninger til at anvende de manglende mærker til denne ressource senere i processen.

Næste trin er at oprette en opslagstabel, at tilknytte oplysningerne fra mærkerne til _ResourceGroupName_. Denne opslagstabel bruges på det næste trin til at forbedre forbrugsdata med mærke oplysninger.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Tilføje oplysninger om kode til forbrugsdata

Vi kan nu hoppe til _PublishData_ ark, som behandler forbrug oplysningerne fra API fakturering, og Tilføj de felter, der er hentet fra mærkerne. Denne proces udføres ved at kigge på den opslagstabel, der er oprettet på forrige trin, ved hjælp af _ResourceGroupName_ som nøgle for opslagene.

![Figur 5 - udfylde kontostrukturen med oplysninger fra opslagene][14]

Bemærk, at felterne relevante konto struktur til "Netværk" tjenesten blev anvendt, løse problemet med manglende mærker. Vi også udfyldt felterne konto struktur for ressourcer end vores mål ressourcegruppe med "Andet" for at kunne skelne dem på rapporterne.

Nu skal vi blot at tilføje et trin for at publicere brugerdata. I dette trin anvendes de relevante satser for hver tjeneste, der er defineret i vores rente planlægge til brugeroplysninger med den resulterende gebyr indlæst i databasen.

Den bedste del er, at du kun behøver at gennemgå denne proces én gang. Når projektmappen er fuldført, skal du blot tilføje det til scheduler, og det kører hver time eller dagligt på det planlagte tidspunkt. Er det kun et spørgsmål om oprettelse af nye rapporter eller tilpasse eksisterende, for at analysere data for at få sigende erfaringer fra dit skyen brugen.

### <a name="next-steps"></a>Næste trin

+ Detaljerede oplysninger om oprettelse af skyen Cruiser projektmapper og rapporter, skal du referere til skyen Cruiser online [dokumentation](http://docs.cloudcruiser.com/) (gyldige logon).  Du kan finde flere oplysninger om skyen Cruiser, skal du kontakte [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Se [få indsigt i dine Microsoft Azure Ressourceforbrug](billing-usage-rate-card-overview.md) for en oversigt over RateCard APIs og Azure Ressourceforbrug.
+ Se [Azure fakturering RESTEN API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) til flere oplysninger om både API'er, som er en del af sæt af API'er, der er angivet af Azure ressourcestyring.
+ Hvis du vil have at kaste dig ud direkte i eksempelkoden, se vores Microsoft Azure fakturering API kodeeksempler på [Azure kodeeksempler](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Lær mere
+ Artiklen [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md) for at lære mere om Azure ressourcestyring.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figur 1 - oprette en ny samling"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figur 2 - importere data fra den nye websteder"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figur 3 - Transformation trin til at behandle indsamlede data fra RateCard API"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figur 4 - udgivelse af dataene fra RateCard API med nye tjenester og -satser"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figur 5 - ved at bekræfte de nye tjenester"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figur 6 - bekræftelse af nye rente planlægge og tilknyttede satser"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figur 7 - at transformere WAP data for at normalisere tjenester"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figur 8 - planlægning af projektmappe"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figur 9 - eksempelrapport arbejdsbelastningen omkostninger sammenligning scenarie"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figur 10 - rapport med opdelinger, der bruger mærker"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figur 11 - ressourcegruppe med tilknyttede koder på Azure-portalen"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figur 12 - brugen API dataene er importeret til arket UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figur 13 - oprette nye felter for oplysninger om kode"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figur 14 - udfylde kontostrukturen med oplysninger fra opslagene"
