<properties
   pageTitle="Microsoft Azure brugen og RateCard API'er Aktivér Cloudyn til at levere ITFM til kunder | Microsoft Azure"
   description="Indeholder et entydigt perspektiv fra Microsoft Azure fakturering partner Cloudyn, på deres oplevelser integrere Azure fakturering API'er i deres produkt.  Dette er især nyttig til Azure og Cloudyn kunder, der er interesseret i at bruge/forsøger Cloudyn for Azure-tjenester."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>Microsoft Azure brugen og RateCard API'er aktivere Cloudyn til at levere ITFM til kunder

Cloudyn, en partner til udvikling af Microsoft og en foranstillet udbyder af funktioner til skyen, blev valgt til en privat eksempelvisning af den nye Microsoft Azure Ressourceforbrug og RateCard APIs.  Brug af API giver adgang til anslåede Azure forbrugsdata til et abonnement. RateCard API indeholder fuldført prisoplysninger for alle Azure tjenester for kunder, der ikke er Enterprise aftale EA. Integreret sammen, giver disse API'er grundlag kan finde detaljerede oplysninger om input til IT finansielle Management (ITFM) funktioner som dem, der leveres af Cloudyn.

## <a name="introduction"></a>Introduktion

Den papirark "multiplikation" af data fra brugen API med data fra RateCard API (brugen [enheder] pris [$unit] = detaljerede brugen og omkostninger) opretter mest detaljerede, nøjagtige og pålidelige faktureringsoplysninger tilgængelige for Azure i dag.

![Oversigt over ITFM][1]

Forbrug disse API'er indeholder vigtige oplysninger om kundernes forbrug og omkostninger, så Cloudyn for at analysere kundekonti på en enkel, programmeringsmæssig måde og til at udføre forskellige ITFM opgaver for sine kunders.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Integrering af Cloudyn med RateCard og brug API'er
RateCard API kræver flere inputparametre – som område oplysninger, valuta og landestandard – men det vigtigste er OfferDurableID, der angiver typen Azure tilbyder kunden bruger (Pay-as-you-Go, ældre 6 og 12-måneders anvendelsen planer, MSDN tilbud, MPN tilbud, tilbud og andre). OfferDurableID kan findes i [Azure brugen og fakturering portal](https://account.windowsazure.com/Subscriptions), under den "tilbyder-ID" til det angivne abonnement.

Kunder kan tilføje deres OfferDurableID kode, som gør det muligt for Cloudyn til at trække deres relevante prisoplysninger gennem API'EN RateCard ved registrering for [Cloudyn til Azure](https://www.cloudyn.com/microsoft-azure/) -tjenester.  Oplysninger om de forskellige typer af tilbud kan findes en oplysningssiden [Microsoft Azure tilbyder](https://azure.microsoft.com/support/legal/offer-details/) .

![Oversigt over Cloudyn ITFM program][2]

Cloudyn bruger både forbrug og RateCard APIs, ud over Azure ydeevne API til at oprette flere lag af visualisering, analytics, advarer, rapportering, omkostningsstyring og praktiske anbefalinger, give Azure kunder en pålidelig enterprise cloud ITFM værktøj.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Cloudyn ITFM use cases, der er aktiveret af brugen og RateCard API-integration
Almindelige Cloudyn ITFM use cases, der er aktiveret af brugen og RateCard APIs omfatter:

+ **Omkostningsanalyse** – gør det muligt for omkostninger skal opdeles ned til en hvilken som helst oprindelige identificerende dimension (udbyder service-konto, og område osv.) i skyen. Azure forbrug og RateCard APIs gøre dette en nem opgave ved at indsende den mest detaljerede opdeling af brugen og omkostningsoplysninger per-konto, som derefter grupperet og filtreret efter Cloudyn og vises for brugeren, i en formular, grafik eller tabelformat.

![Omkostning analyse cirkeldiagram][3]

+ **Omkostninger allokering 360** - aktiverer økonomi og IT-administratorer til at afdække de faktiske omkostninger breakdown, drivere og tendenser af deres skyen installation. Det kan yderligere ledere nemt knytte installation udgifter til afdelinger, afdelinger, områder og mere, give hidtil ukendt indsigt i skyen omkostninger og lette enterprise tilbageførte posteringer og showbacks. Azure forbrug og RateCard APIs fungere som input til Cloudyns omkostninger allokering program, som er et supplement API'erne ved at definere metoder og forretningslogik for tildeling af umærkede eller untaggable ressourcer.

![Omkostninger allokering 360 diagram][4]

+ **Cost-Effective størrelseshåndtag** - indeholder højre størrelseshåndtag anbefalinger til underudnyttet virtuelle maskiner, hvilket reducerer kundens deres udgifter i store eller uberettiget klargjort computere. Sker det ved at undersøge virtuelt CPU og RAM målepunkter (via ydeevne API), timer på kørselstidspunktet (via brugen API) og omkostninger (via RateCard API). Cloudyn derefter giver højre størrelseshåndtag anbefalinger, der er baseret på underudnyttet CPU eller RAM ressourcer (ydeevne) og beregnes anslåede spare ved at gange pris delta (RateCard) mellem FOS ved den faktiske tid udnyttelse (brug) på den underudnyttede computer.

![Rentable størrelseshåndtag][5]

+ **Konvertere anbefalinger i skyen** - indeholder finansielle råd om skyen overførsel. Den undersøger en brugers aktuelle omkostninger skyen ressourcer, som er installeret på overordnet skyen leverandører, og det sammenlignes med omkostninger for en tilsvarende installation på Azure. Den indeholder detaljerede, per-ressource, økonomisk-baserede konvertere anbefalinger til Azure. Efter vurdering af den tilsvarende installation, der kræves på Azure (baseret på indstillingerne for ydeevne målepunkter og bruger), bruger Cloudyn RateCard API til at vurdere omkostningerne for den tilsvarende installation på Azure.

+ **Rapporter over ydeevne** - aktiveret af Azures ydeevne API, disse rapporter indeholder en række funktioner fra CPU- og RAM anvendelsen til optimering anbefalinger. Nedenfor finder du en forekomst anvendelsen rapport eksempel, præsenterer forekomst opdeling af gennemsnitlige CPU-forbrug.

![Rapporter over ydeevne][6]

+ **Lederen** - en effektiv funktion i Cloudyn, der bringer rækkefølge flugter skyen ressourcer. Den giver brugerne fri til at oprette deres egne entydige kategorier (koder) til effektive måling og rapportering, der er på linje med forretningsmetoder. Desuden kan brugere nemt fastsætte og kategorisere inkonsekvent mærkning (det vil sige slåfejl og andre uoverensstemmelser) og registrerer automatisk umærkede ressourcer til nøjagtige omkostninger afskrivningen.

![Lederen][7]

## <a name="video"></a>Video

Her er en kort video, som viser, hvordan en Azure kunde kan bruge Cloudyn til Azure og Azure fakturering API'er til at få indsigt fra deres Azure forbrugsdata.

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>Næste trin

+ Starte en gratis [Cloudyn til Azure](https://www.cloudyn.com/microsoft-azure/) -prøveversion for at se, hvordan du kan få omkostninger gennemsigtighed med tilpasset rapportering og analyse til Microsoft Azure skyen installationen.
+ Se [få indsigt i dine Microsoft Azure Ressourceforbrug](billing-usage-rate-card-overview.md) for en oversigt over RateCard APIs og Azure Ressourceforbrug.
+ Se [Azure fakturering RESTEN API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) til flere oplysninger om både API'er, som er en del af sæt af API'er, der er angivet af Azure ressourcestyring.
+ Hvis du vil have at kaste dig ud direkte i eksempelkoden, se vores Microsoft Azure fakturering API kodeeksempler på [Azure kodeeksempler](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Lær mere
+ Hvis du vil vide mere om Microsoft Azure Enterprise aftale (EA) tilbud, skal du gå til [licensering Azure for virksomheden] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Artiklen [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md) for at lære mere om Azure ressourcestyring.
+ Yderligere oplysninger om pakken med værktøjer, der er nødvendige for at hjælpe med at få indsigt i skyen bruger, skal du henvise til Gartner artikel [Marked vejledning til IT finansielle Management (ITFM) værktøjer](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
