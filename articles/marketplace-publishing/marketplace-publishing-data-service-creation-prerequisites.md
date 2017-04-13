<properties
   pageTitle="Tekniske forudsætninger for at oprette en datatjeneste på Marketplace | Microsoft Azure"
   description="Forstå kravene til oprettelse af en datatjeneste for at distribuere og sælge på Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Tekniske forudsætninger for at oprette en datatjeneste tilbyder på Azure Marketplace

>[AZURE.IMPORTANT] **På nuværende tidspunkt vi er ikke længere er onboarding alle nye datatjeneste udgivere. Ny dataservices kan ikke få godkendt til listen.** Du kan finde flere oplysninger, hvis du har en SaaS forretningsprogram, du vil have til at publicere AppSource [her](https://appsource.microsoft.com/partners). Hvis du har en IaaS programmer eller udvikler tjeneste, du gerne vil publicere Azure Marketplace du kan finde flere oplysninger [her](https://azure.microsoft.com/marketplace/programs/certified/).

Læs processen grundigt før du begynder, og forstå hvor og hvorfor hvert trin udføres. Som muligt, du skal forberede dine firmaoplysninger og andre data, hente nødvendige værktøjer og/eller oprette tekniske komponenter før du begynder processen til oprettelse af tilbud.

Du skal have følgende elementer klar, før du begynder processen:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Gøre en beslutning om, hvordan der skal bruges til at udgive dit datatjeneste tilbud

En udgiver kan vælge mellem flere teknologier, når du udgiver datatjeneste i Azure Marketplace. De vigtigste teknologier, der understøttes beskrevet nedenfor. Uanset forbruger hvilken teknologi, der bruges til at publicere den datatjeneste, slutbrugeren data via et **OData-feed** , der vises af Azure Marketplace Service. Alle oplysninger om OData-tjeneste, du kan finde på [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure-Database

Har du datasæt klar i SQL Azure er Publishers ansvar. Du skal abonnere på Azure, klargøre relevante størrelsen på databasen og overføre dine Data til SQL Azure DB. Publisher er også ansvarlig for at holde sit data altid er opdateret. Du kan finde flere oplysninger om at abonnere på Azure Services, som du kan finde på [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Når du flytter dataene i SQL Azure, kan Azure Marketplace fremvise tabeller og visninger. Udgiveren kan angive, hvilke tabeller/visninger og kolonner vises slutbrugeren. Yderligere kan udbyderen af indholdet også angive, hvilke kolonner der kan forespørges af slutbrugeren og hvilke der kun returneres i overførslen. Det giver en høj grad af fleksibilitet om, hvilke data i databasen skal være synlig. Kolonner, der kan forespørges skal være understøttet af en eller flere database indeks.

## <a name="rest-based-web-service"></a>Baseret REST-webtjeneste

Understøttede protokol: **HTTPS kun**

Eksisterende RESTEN baseret tjenester kan være fremvises på Azure Marketplace. Fordi datasættet er altid fremvises til slutbrugeren som et OData-feed, baseret Azure Marketplace-tjenesten behov skal kunne knytte tjenesten til et OData-tjeneste. For at gøre dette RESTEN baseret slutpunkter har du brug at få vist alle parametre som HTTP parametre.

Data skal være i en formular, som kan knyttes til en ATOM-svar. Derfor svaret fra tjenester der skal være i XML-format og kan kun indeholde én gentagne element, der indeholder data værdierne (som postsæt). Tjenesten Azure Marketplace Knyt noden gentaget til noden indtastning i ATOM og data værdierne i egenskaben noderne i noden indtastning.

Godkendelsesoplysninger (såsom API nøgle, godkendelse adgangstoken, osv.) skal være leveres som en HTTP-parameter eller i overskriften HTTP (nøgleværdi par) – grundlæggende godkendelse er også understøttet. En gyldig nøgle skal angives, og alle anmodninger via Azure Marketplace foretages gennem tasten. Bruger overvågning og fakturering sker på Azure Marketplace-lag.

Fejl, der returneres af tjenesten skal knyttes til HTTP statuskoder. I tilfælde af tjenesten returnerer en XML, der indeholder fejl disse skal tilknyttes med Azure Marketplace-tjenesten til HTTP statuskoder.

## <a name="soap-based-web-services"></a>SOAP baseret webtjenester

Protokol: **HTTPS kun**

Kravene er de samme som i RESTEN baseret tjeneste sektion. Den eneste forskel er, kan også gives parametre i en XML-brødtekst, der sendes til den udgiver tjenesten med alle anmodninger via Azure Marketplace. Det betyder, at HTTP parametre giver brugeren på den front end oversættes til XML-elementer i en XML-dokument, der sendes med anmodningen til provideren indhold webtjeneste.

## <a name="odata-based-web-services"></a>OData-baseret webtjenester

Protokol: **HTTPS kun**

Data, der kan være vises som et OData-tjeneste til Azure Marketplace. Systemet vil overføre tjenesten via og erstatter i roden af tjenesten med Azure Marketplace tjenestens rod – at sikre, at alle efterfølgende opkald går igennem Azure Marketplace.

OData webtjenester behøver ikke kun at gå mod en database i backend-version. OData understøtter alle slags lagerplads eller business logik til at drive tjenesten.


## <a name="next-steps"></a>Næste trin
Nu, hvor du gennemgås forudsætninger og fuldførte opgaver som det er nødvendigt, kan du flytte frem til at oprette dit datatjeneste tilbud som beskrevet i [Data Service publicering Guide](marketplace-publishing-data-service-creation.md).

Eller, hvis du vil gennemgå den overordnede proces og de respektive artikler for hver af de publicering faser, skal du gå til artiklen [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
