<properties
   pageTitle="Vejledning i at oprette en datatjeneste på Marketplace | Microsoft Azure"
   description="Detaljerede oplysninger om, hvordan du opretter, bekræfte og installerer en datatjeneste til køb på Azure Marketplace."
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

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Datatjeneste publicering vejledning til Azure Marketplace

>[AZURE.IMPORTANT] **På nuværende tidspunkt vi er ikke længere er onboarding alle nye datatjeneste udgivere. Ny dataservices kan ikke få godkendt til listen.** Du kan finde flere oplysninger, hvis du har en SaaS forretningsprogram, du vil have til at publicere AppSource [her](https://appsource.microsoft.com/partners). Hvis du har en IaaS programmer eller udvikler tjeneste, du gerne vil publicere Azure Marketplace du kan finde flere oplysninger [her](https://azure.microsoft.com/marketplace/programs/certified/).

Efter du har udført trin 1, [oprettelse af en konto og registrering](marketplace-publishing-accounts-creation-registration.md), vejledt vi du igennem [Generelt ikke-teknisk](marketplace-publishing-pre-requisites.md) og [Tekniske krav](marketplace-publishing-data-service-creation-prerequisites.md) i et datatjeneste tilbud på Azure Marketplace. Nu vi fører dig gennem trinnene til at oprette et datatjeneste tilbud på [Udgivelsesportal] [ link-pubportal] på Azure Marketplace.

## <a name="1---login-to-the-publishing-portal"></a>1. logon til udgivelse Portal.

Gå til [https://publish.windowsazure.com](https://publish.windowsazure.com. )

**Brug den samme konto, som virksomhedens sælgeren profil er registreret i Developer Center for første gang logon til Udgivelsesportal.**  (Senere kan du tilføje en medarbejder i virksomheden som en co-administrator i portalen publicering).

Klik på feltet **Publicer en datatjenester** , hvis dette er den første logon til portalen publicering.

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2. Vælg **Data Services** i navigationsmenuen i venstre side.

  ![tegning](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3. Opret en ny datatjeneste

Skrive en titel til dit nye Data Service tilbyder, og klik på "+" i højre side.

  ![tegning](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4. Gennemse undermenuen under det nyoprettede datatjeneste i navigationsmenuen.

Klik på fanen **gennemgang** og gennemse alle de nødvendige skridt, der skal bruges til at publicere korrekt datatjeneste på Azure Marketplace.

> [AZURE.TIP] Du kan altid klikke på linkene på siden "Gennemgang" eller bruge faner på tilbuddet datatjeneste undermenu i venstre side.

## <a name="5---create-a-new-plan"></a>5. Opret en ny Plan.

### <a name="offers-plans-transactions"></a>Tilbud, planer, transaktioner.

Hvert tilbud kan have flere planer, men skal have mindst én (1) Plan. Når slutbrugere abonnerer på dit tilbud abonnere de for en af de tilbud Plan. Hver enkelt plan definerer, hvordan slutbrugere vil kunne bruge din tjeneste.

Azure Marketplace understøtter kun månedlige abonnement transaktion baseret model for datatjenester, det vil sige slutbrugere betaler månedligt gebyr efter den specifikke struktur, de har abonneret på pris og vil kunne bruge hver måned antal transaktion, der er defineret af planen.

Hver transaktion normalt defineret som antallet af poster, der er Data Service returnerer baseret på den forespørgsel, der sendes til tjenesten. Standard er 100. Antallet af transaktioner returneres til hver forespørgsel bliver tal poster divideret med 100 og afrundet op til det nærmeste heltal.

Det er Azure Marketplace Service lag ansvar til at overvåge (meter) antallet af transaktioner consumed ved hver forespørgsel.

> [AZURE.IMPORTANT] Slutbrugere som nået transaktion grænsen løbet af måneden, blokeres fra fortsætter med at bruge tjenesten indtil slutningen af deres månedlige abonnement cyklus.

> Planen eller et af planerne kan (men ikke skal) omfatter ubegrænset antal transaktioner.

### <a name="create-a-plan"></a>Oprette en plan.
1. Klik på **"+"** ud for "Tilføj en ny plan".

2. Vælg en af indstillingerne: **ubegrænset** eller **begrænset** forbrug for denne plan.  Hvis begrænset derefter angive antallet transaktion planen, så til forbrug i en måned.

    ![tegning](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    Publicering af portalen også foreslår "Plan id", som skal bruges til at kommunikere til slutbrugere navnet på planen i Brugergrænsefladen og også bruges af tjenesten Markedsplads til at identificere planen. Hvis du vil, kan du ændre det "planlægge id".

    > [AZURE.NOTE] Det "planlægge id" skal være entydig i omfanget af hvert tilbud. Mens mange andre identifikatorer, der bruges i det publicering Portal planlægge id vil være låst, når den første udgivelse fremstilling, og du ikke kan ændre dette id.

3. Klik for at bekræfte valget.

4. Og du bliver bedt om nogle yderligere spørgsmål vedrørende din nyoprettede Plan.

    ![tegning](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Spørgsmål|Betydning|
|----|----|
|**Denne Plan er gratis og tilgængelig på verdensplan?**|Du kan oprette en helt fri af-gratis plan. Hvis det er kun planen for dette tilbud – betyder det, du udgiver "Gratis tilbyder" på markedet. Hvis det er kun for en (af få)-Plan, skal it giver dig mulighed for at tilbyde slutbrugere for at få flere oplysninger om din tjeneste med et lille antal transaktioner månedsbasis.  Hvis svaret er "Ja", derefter ingen yderligere spørgsmål vil blive bedt om.|

> [AZURE.NOTE] Slutbrugere kan altid opgradere til de betalte planer.

|Spørgsmål|Betydning|
|----|----|
|**Er gratis prøveversion tilgængelig?**|Du kan vælge mellem "Ingen prøveversion" overhovedet eller give mulighed for at bruge din Plan for "En måned". Udgivere, som du bruger denne indstilling til at give slutbrugere muligheden for at forstå fordelene tilbuddet gratis til en måned.|

> [AZURE.IMPORTANT] Slutbrugere vil kun kunne købe en gratis prøveversion, hvis de har nedsat betalingsinstrument fx kreditkort, virksomhedsaftale.

> Når du har en måned efter den gratis prøveversion starter Azure Marketplace opladning kunder prisen og datoen for abonnementet, medmindre kunden startet abonnement annulleringen. Ingen speciel meddelelser vil blive leveret til slutbrugere.

|Spørgsmål|Betydning|
|----|----|
|**Denne plan kræver en koden til at købe?**| Udgivere har mulighed for at begrænse adgangen til deres Service plan ved hjælp af en speciel kode, kaldet "A Promocode" til bestemte kunder. Slutbrugere, som har denne Promocode vil kunne abonnere på planen. Hvis du vælger "Nej", så accepterer, at alle fra det område, hvor tilbuddet er tilgængelig (se [Marketplace Marketing indhold vejledning](marketplace-publishing-push-to-staging.md) få mere at vide) vil kunne abonnere på denne plan. Ingen yderligere spørgsmål vil blive bedt om.|
|**Også skjule denne plan fra alle, der ikke har en gyldig koden?**|Hvis svaret til det forrige spørgsmål er "Ja" har udgiveren mulighed for helt at fjerne denne plan vises i Brugergrænsefladen på markedet. Det betyder, kunder kan ikke se denne plan inden for den tilbud oplysningssiden. Slutbrugere, der skal modtage en promocode for at købe det, vil kunne abonnere på den ved hjælp af denne promocode.|

## <a name="6---create-your-marketplace-marketing-content"></a>6. Opret din markedsplads marketingindhold
Til at angive oplysninger, der kræves i fanerne **Marketing, priser, Support og kategorier** skal du besøge [Marketplace Marketing indhold vejledning](marketplace-publishing-push-to-staging.md) , der er fælles for alle elementer, der er udgivet i Azure Marketplace.  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7. Forbind dit tilbud til din tjeneste (SQL Azure baseret eller webtjeneste baseret).

Klik på menuen **Data Services** underordnede.

På den øverste halvdel af siden bliver du bedt om at angive den tilbud **Namespace**.  

  ![tegning](media/marketplace-publishing-data-service-creation/step-7.png)

Den under spørgsmål, der definerer, hvordan visning nyoprettede tilbud til Azure Marketplace skal udgiveren. (Se få mere at vide [Data Services tekniske nødvendige vejledning](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![tegning](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Publicere databasen baseret tjeneste**

Klik på **Database**. Den efterfølgende side vises:

  ![tegning](media/marketplace-publishing-data-service-creation/step-7.3.png)

Sådan oprettes en CSDL tilknytning for datasættet baseret på SQL Azure DB:

  ![tegning](media/marketplace-publishing-data-service-creation/step-7.4.png)

Og gå derefter til hver tabel

  ![tegning](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![tegning](media/marketplace-publishing-data-service-creation/step-7.6.png)

Hvis-webtjeneste

  ![tegning](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Læs [tilknytning af en eksisterende webtjeneste til OData gennem CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) detaljerede instruktioner og eksempler til oprettelse af en CSDL webtjeneste.

## <a name="next-steps"></a>Næste trin
Nu, hvor du har oprettet dit datatjeneste tilbud, skal du sikre dig, at du udfører instruktionerne i [Marketplace Marketing indhold vejledning](marketplace-publishing-push-to-staging.md) , før du flytte frem til [Test din datatjeneste i midlertidige](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Se også
- [Introduktion: Hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)
- Hvis du er interesseret i at forstå de overordnede OData tilknytningsproces og formål, kan du læse denne artikel [Data Service OData tilknytning](marketplace-publishing-data-service-creation-odata-mapping.md) til Gennemse definitioner, strukturer og instruktioner.
- Hvis du er interesseret i at lære og forstå noderne bestemte og deres parametre Læs denne artikel [Data Service OData tilknytning noder](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) til definitioner og forklaringer, eksempler og bruge tilfælde kontekst.
- Hvis du er interesseret i at gennemgå eksempler, kan du læse denne artikel [Data Service OData tilknytning eksempler](marketplace-publishing-data-service-creation-odata-mapping-examples.md) , der kan se eksempler på kode og forstå syntaks for feltkoder og kontekst.


[link-pubportal]:https://publish.windowsazure.com
