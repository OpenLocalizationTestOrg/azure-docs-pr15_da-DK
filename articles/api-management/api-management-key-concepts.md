<properties 
    pageTitle="Administration af API vigtige begreber" 
    description="Få mere at vide om API'er, -produkter, roller, grupper og andre vigtige koncepter API administration." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>Hvad er API Management?

Administration af API hjælper med at organisationer publicere API'er til eksterne, partner og interne udviklere for at låse det fulde potentiale af deres data og -tjenester. Virksomheder overalt leder for at udvide deres aktiviteter som en digital platform, oprette nye kanaler, søge efter nye kunder og bil dybere aftale med eksisterende. API Management indeholder core kompetencer for at sikre en vellykket API program gennem udvikler aftale, business viden, analytics, sikkerhed og beskyttelse.

Se følgende video for en oversigt over Azure API Management, og Lær, hvordan du bruger API administration til at føje mange funktioner til din API, herunder adgangskontrol, begrænsning af hastigheden, overvågning, hændelseslogføring og svar cachelagring med minimale arbejde på din side.

> [AZURE.VIDEO azure-api-management-overview]

Hvis du vil bruge API Management, oprette administratorer API'er. Hver API består af en eller flere handlinger, og hver API kan føjes til et eller flere produkter. Hvis du vil bruge en API, udviklere abonnere på et produkt, der indeholder API, og de kan kald derefter den API handling underlagt brugen politikker, der kan være i kraft.

Dette emne indeholder en oversigt over administration af API vigtigste begreber.

>[AZURE.NOTE] Finde flere oplysninger i [skybaseret API administration: udnyttelse Power API'er](http://j.mp/ms-apim-whitepaper) PDF-hvidbog. Denne indledende hvidbog på API administration af CITO Research omhandler: 
>
> - Fælles API krav og udfordringer
>     - Afkobling API'er og præsenterer facades
>     - Sådan får du udviklere og hurtigt i gang
>     - Sikring af adgang
>     - Analyser og målepunkter
> - Få kontrol og indsigt med en API Management platform
> - Brug af skyen vs lokalt løsninger
> - Azure API administration

## <a name="apis"> </a>API'er og handlinger

API'er er grundlaget for en API Management service forekomst. Hver API repræsenterer et sæt handlinger, der er tilgængelige for udviklere. Hver API indeholder en reference til back end-tjenesten, der implementerer API og dens handlinger, er tilknyttet de handlinger, der er implementeret af back end-tjenesten. Handlinger i administration af API er nemme at konfigurere, kontrol over URL-adressen tilknytning, forespørgsel og stien parametre, anmodningen og svaret indhold og handlingen svar cachelagring. Grænsen, kvoter og IP-begrænsning politikker kan også implementeres API eller individuelle handlingen niveau.

Se [hvordan du opretter API'er][] og [hvordan du føjer handlinger til en API][]kan finde flere oplysninger.


## <a name="products"></a> Produkter

Produkter er, hvordan API'er fået for udviklere. Administration af API-produkter har en eller flere API'er og er konfigureret med en titel, beskrivelse og vilkår for anvendelse. Produkter kan være **åben** eller **beskyttet**. Beskyttet produkter skal abonnere på, før de kan bruges, mens du Åbn produkter kan bruges uden et abonnement. Når et produkt er klar til brug af udviklere kan det udgives. Når den er blevet publiceret, den kan vises (og for beskyttet produkter abonnerer på) af udviklere. Abonnement godkendelse kan er konfigureret på niveauet for produkt og enten kræve administratorgodkendelse, eller være godkendt af automatisk.

Grupper bruges til at administrere synligheden af produkter til udviklere. Produkter Giv synlighed til grupper, og udviklere kan få vist og abonnere på de produkter, der er synlige for de grupper, de tilhører. 

Du kan finde flere oplysninger, se, [hvordan til at oprette og publicere et produkt][] og den følgende video.

> [AZURE.VIDEO using-products]

## <a name="groups"></a> Grupper

Grupper bruges til at administrere synligheden af produkter til udviklere. Administration af API har følgende fast systemgrupper.

-   **Administratorer** – Azure abonnement administratorer er medlemmer af denne gruppe. Administratorer administrere API Management service forekomster, oprette API'er, handlinger og produkter, der bruges af udviklere.
-   **Udviklere** - godkendte udvikler portal brugere falder inden for denne gruppe. Udviklere er de kunder, opbygger programmer, der bruger din API'er. Udviklere får adgang til portalen udvikler og opbygge programmer, der kalder en API operationer.
-   **Gæster** - portal ikke-godkendte udvikler-brugere, som kundeemner besøger portalen udvikler af en forekomst, API Management falder inden for denne gruppe. De kan få tildelt bestemte skrivebeskyttet adgang, som muligheden for at få vist API'er, men ikke ringe til dem.

Ud over systemgrupperne, kan administratorer oprette brugerdefinerede grupper eller [udnytte eksterne grupper i tilknyttede Azure Active Directory-lejere](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Brugerdefineret og eksterne grupper kan bruges sammen med systemgrupper i give udviklere synlighed og adgang til API-produkter. For eksempel kan du oprette en brugerdefineret gruppe for udviklere, der er knyttet til en bestemt partnerorganisation og give dem adgang til API'erne fra et produkt, der indeholder relevante API'er. En bruger kan være medlem af mere end én gruppe.

Se, [hvordan du oprette og bruge grupper][]kan finde flere oplysninger.

## <a name="developers"></a> Udviklere

Udviklere repræsenterer brugerkonti i en API Management service forekomst. Udviklere kan oprettes eller inviteret til at deltage i af administratorer, eller de kan tilmelde dig fra [udvikler portal][]. Hver udvikler er medlem af en eller flere grupper, og det kan være abonnere på de produkter, som giver synlighed til disse grupper.

Når udviklere abonnere på et produkt tildeles de primære og sekundære nøgle til produktet. Denne tast bruges, når du ringer op til det produkt API'er.

Se [hvordan du opretter eller invitere udviklere][] og [hvordan du kan knytte grupper med udviklere][]kan finde flere oplysninger.

## <a name="policies"></a> Politikker

Politikker er en effektiv funktion af API-administration, der tillader, at ændre funktionsmåden for API via konfiguration publisher. Politikker er en samling af sætninger, der udføres sekventielt på anmodningen eller svar på en API. Populære sætninger medtage konvertering af filformat fra XML til JSON og begrænsning af opkald hastigheden for at begrænse mængden af indgående opkald fra en udvikler, og mange andre politikker er tilgængelige.

Politik udtryk kan bruges som attributten værdier eller tekstværdier i en af politikkerne for administration af API, medmindre politikken angiver noget andet. Politikker som [Kontrolflow](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) og [angive variabel](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) politikker er baseret på politik udtryk. Få mere at vide under [Avanceret politikker](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [politik udtryk](https://msdn.microsoft.com/library/azure/dn910913.aspx), og se følgende video.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Du kan finde en komplet liste over politikker for administration af API [politikreference][]. Du kan finde flere oplysninger om brug af og konfiguration af politikker, [politikker for administration af API][]. Selvstudium til oprettelse af et produkt med rente grænse og kvote politikker, se, [hvordan at oprette og konfigurere indstillinger for avanceret produkt][]. Se følgende video for en demonstration.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a> Udvikler portal

Portalen udvikler er hvor udviklere kan få mere at vide om dine API'er, få vist og opkald handlinger, og abonnere på produkter. Potentielle kunder kan besøge portalen udvikler kan få vist API'er og drift og tilmelde dig. URL-adressen for din udvikler portal er placeret på dashboardet i portalen Azure klassisk for din API Management service forekomst.

Du kan tilpasse udseendet af din udvikler portal ved at tilføje brugerdefineret indhold, tilpasse typografier og tilføje din mærkning.

## <a name="api-management-and-the-api-economy"></a>API administration og API økonomi

Se følgende præsentationen fra Microsoft Ignite 2015 konferencen for at få mere for at vide om administration af API.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Udvikler portal]: #developer-portal

[Sådan opretter du API'er]: api-management-howto-create-apis.md
[Hvordan du føjer handlinger til en API]: api-management-howto-add-operations.md
[Hvordan du kan oprette og publicere et produkt]: api-management-howto-add-products.md
[Sådan oprettes og bruge grupper]: api-management-howto-create-groups.md
[Sådan tilknyttes udviklere grupper]: api-management-howto-create-groups.md#associate-group-developer
[Hvordan opretter og konfigurere indstillinger for avanceret produkt]: api-management-howto-product-with-rules.md
[Sådan oprettes eller Inviter udviklere]: api-management-howto-create-or-invite-developers.md
[Politikreference]: api-management-policy-reference.md
[Politikker for administration af API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 
