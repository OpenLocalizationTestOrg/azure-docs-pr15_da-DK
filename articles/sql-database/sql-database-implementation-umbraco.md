<properties
   pageTitle="Azure SQL Database Azure Casestudie - Umbraco | Microsoft Azure"
   description="Få mere at vide om, hvordan Umbraco anvender SQL-Database til at klargøre hurtigt og skala tjenester for tusindvis af lejere i skyen"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco bruger Azure SQL-Database til hurtigt klargøring og skalering af tusindvis af lejere i skyen

![Umbraco-Logo](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco er et populære open source-Indholdsstyring system (CMS), som kan køre noget fra small kampagnens eller brochure websteder til komplekse programmer til Fortune 500 virksomheder og global medier websteder. 

> "Vi har helt en stor gruppe af udviklere, som bruger systemet, med mere end 100.000 udviklere på vores fora og mere end 350.000 websteder, der er direkte, kører Umbraco."

> – Morten Christensen, tekniske kundeemne, Umbraco

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

For at forenkle kunde-installationer, Umbraco tilføjet Umbraco som-en-tjenesten (UaaS): en software-som-en-(SaaS) servicetilbud, der fjerner behovet for lokale installationer giver indbyggede skalering og fjerner management spild ved at aktivere udviklere til at fokusere på produkt innovation i stedet for løsning administration. Umbraco er i stand til at give alle disse fordele ved at pege med fleksible platform som en tjeneste (PaaS) modellen, som Microsoft Azure.

UaaS SaaS giver mulighed for at bruge Umbraco CMS-funktioner, som tidligere var af deres rækkevidde. Disse kunder er klargjort med et arbejde CMS-miljø, der indeholder en fremstilling database. Kunder kan føje op til to ekstra databaser for udvikling og midlertidige miljøer, afhængigt af deres krav. Når der anmodes om et nyt miljø, tildeler en automatiseret proces denne kunde en database automatisk. Den nye database er klar i sekunder, fordi databasen er allerede blevet allerede klargjort ved Umbraco fra en Azure elastiske gruppe af tilgængelige databaser (se figur 1).


![Umbraco klargøring livscyklus](./media/sql-database-implementation-umbraco/figure1.png)

Figur 1. Klargør livscyklus for Umbraco som en tjeneste (UaaS)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure elastiske grupper og automatisering forenkle installationer

Med Azure SQL-Database og andre Azure tjenester Umbraco kunder kan selv klargør deres miljøer, og Umbraco kan nemt at overvåge og administrere databaser som en del af en intuitiv arbejdsproces:

1.  Klargøring

    Umbraco fører en kapacitet over 200 allerede klargjort databaser fra elastiske grupper. Når en ny kunde tilmelder sig UaaS, indeholder Umbraco kunde med en ny CMS miljø i nær realtid ved at tildele dem til en database fra puljen tilgængelighed.

    Når en tilgængelighed puljen når dens grænseværdi, der oprettes en ny elastiske puljen, og nye databaser er allerede klargjort skal tildeles til kunder, efter behov.

    Implementering er fuldautomatisk ved hjælp af C# management biblioteker og Azure Service Bus køer.

2.  Bruge

    Kunder anvender en til tre miljøer (for fremstilling, midlertidige og/eller udvikling), hver med sin egen database. Kunde databaser er i elastiske database grupper, som gør det muligt Umbraco til at levere effektive skalering uden at skulle uberettiget Klargør.

    ![Umbraco Projektoversigt](./media/sql-database-implementation-umbraco/figure2.png)

    ![Umbraco project detaljer](./media/sql-database-implementation-umbraco/figure3.png)

    Figur 2. Umbraco som-en-tjenesten (UaaS) kunden websted, der viser projektoversigt og detaljer

    Azure SQL-Database bruger Database transaktion enheder (DTUs) til at repræsentere den relative power kræves til reale databasetransaktioner. Databaser betjene typisk på omkring 10 DTUs for UaaS kunder, men hver har Elasticitet skalere efter behov. Det betyder, at UaaS kan sikre, at kunder altid har nødvendige ressourcer, selv under spidsbelastning. Under seneste søndag NAT. sports hændelser spidser én UaaS kunde erfarne database for eksempel op til 100 DTUs for varigheden af game. Azure elastiske grupper gør det muligt for Umbraco til at understøtte den stor efterspørgsel uden nedsat ydeevne.

3.  Skærm

    Umbraco skærme databasen aktivitet bruge dashboards i portalen Azure sammen med brugerdefineret e-mail-beskeder.

4.  Nedbrud

    Azure har du to muligheder i nedbrud (DR): aktive geografisk-replikering og geografisk gendannelse. Indstillingen DR, skal du vælge en virksomhed, afhænger af dets [Forretningskontinuitet målsætninger](sql-database-business-continuity.md).

    Aktive geografisk replikering giver det hurtigste niveau af svar i tilfælde af nedetid. Bruger aktive geografisk-gentagelse, kan du oprette op til fire læsbare secondaries på servere i forskellige områder, og du derefter kan begynde at bruge failover til en af secondaries i tilfælde af en fejl.

    Umbraco kræver ikke geografisk gentagelse, men det tager fordel af Azure geografisk-Gendan for at sikre mindste nedetid i tilfælde af en afbrydelse. Geografisk-Gendan er afhængig af databasesikkerhedskopier i geografisk overflødige Azure-lager. Gør det muligt at gendanne fra en sikkerhedskopi, når der er en afbrydelse i det primære område.

5.  Ophævelse klargøring

    Når en projektmiljøet slettes, fjernes alle tilknyttede databaser (udvikling, midlertidige eller live) under Azure Service Bus kø oprydning. Denne automatiske proces gendanner ubrugte databaser til Umbracos elastiske database tilgængelighed puljen, gøre dem tilgængelige til fremtidige klargøring og samtidig bevare maksimale anvendelsen.

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Elastiske grupper tillade UaaS skalere med øget

Umbraco ved at drage fordel af Azure elastiske database grupper, kan du optimerer ydeevnen for kunderne, uden at skulle over eller under klargøring. Umbraco har aktuelt næsten 3.000 databaser på tværs af 19 elastiske database grupper mulighed for nemt at skalere efter behov for at medtage en deres eksisterende 325,000 kunder eller nye kunder, der er klar til at installere en CMS i skyen.

Faktisk ifølge Morten Christensen, tekniske ledelse på Umbraco, "UaaS nu problemer væksten i ca. 30 nye kunder om dagen. Vores kunder er glade med mulighed for at kunne klargøre nye projekter i sekunder, med det samme udgive opdateringer til deres direkte websteder fra et udviklingsmiljø ved hjælp af et enkelt klik-installation, og foretag ændringer lige så hurtigt, hvis de finder fejl."

Hvis en kunde ikke kræver et andet og/eller tredje miljø længere, kan det blot fjerne disse miljøer. Frigør ressourcer, der kan bruges til andre kunder som en del af Umbraco elastiske database tilgængelighed puljen.

![Umbraco installationsarkitektur](./media/sql-database-implementation-umbraco/figure4.png)

Figur 3. UaaS installationsarkitektur på Microsoft Azure

##<a name="the-path-from-datacenter-to-cloud"></a>Stien fra datacenter til skyen

Når Umbraco udviklere har truffet din beslutning til at flytte til en SaaS model, kan de vidste, de har brug for en økonomisk og SVG metode til at opbygge tjenesten.

> "Elastiske database grupper er en perfekt for vores SaaS tilbyder fordi vi kan ringe kapacitet op og ned efter behov. Klargøring er nemt, og med vores konfigurationen, kan vi holde anvendelsen maksimalt."

> – Morten Christensen, tekniske kundeemne, Umbraco

"Vi ville bruge vores tid på at løse problemer med at vores kunder, ikke administrere infrastruktur. Vi ville gør det nemt for vores kunder for at få mest muligt"står Niels Hartvig, grundlægger af Umbraco. "Vi betragtes som har vært for serverne os selv, men kapacitetsplanlægning ville have været diasrækkefølge". Coincidentally, anvender Umbraco ikke databaseadministratorer af en, som understregningstegn en nøgleværdi tilbud til brug af UaaS.

Et vigtigt mål for Umbraco udviklere var at give en måde, hvorpå UaaS kunder til at klargøre miljøer hurtigt og uden kapacitetsbebegrænsninger. Men give en dedikeret hostet tjeneste i Umbraco datacentre ville have påkrævet masser af unødvendig kapacitet til at håndtere lyseksplosioner i behandling. Der ville have beregnet tilføje betydelige Beregn infrastruktur, der ville have blevet regelmæssigt yde sit optimale.

Desuden ville Umbraco udviklingsteamet en løsning, der ville muligt at genbruge så meget af deres eksisterende kode som muligt. Som Umbraco udvikler angiver Mikkel Madsen, "Vi er tilfreds med Microsoft development-værktøjer, som vi tidligere allerede kender, som Microsoft SQL Server, Microsoft Azure SQL-Database, ASP.net og Internet Information Services (IIS). Inden du investerer i en IaaS eller en PaaS i skyen løsningen skal vi ønskede Sørg for, at det ville understøtter vores Microsoft-værktøjer og platforme, så vi vil ikke kunne skal foretage massive ændringer i vores kode base. "

Hvis du vil opfylder alle dens kriterier, ledt Umbraco efter en cloud-partner med følgende kvalifikationer:

-   Tilstrækkelig kapacitet og pålidelighed
-   Understøttelse af Microsoft development-værktøjer, så Umbraco teknikere ikke vil blive tvunget til at skulle helt deres udviklingsmiljø
-   Tilstedeværelse i alle geografiske markeder, konkurrerer UaaS (virksomheder har brug for at sikre, at de kan få adgang til deres data hurtigt og, deres data er gemt på en placering, der opfylder deres internationale lovmæssige krav)

##<a name="why-umbraco-chose-azure-for-uaas"></a>Hvorfor Umbraco vælger Azure for UaaS

I henhold til Morten Christensen "efter at have overvejet alle vores indstillinger, vi markeret Azure fordi den opfyldt alle vores kriterier, fra administration og skalerbarhed velkendte og omkostnings. Vi konfigurere miljøer på Azure FOS, og de enkelte miljøer har sin egen Azure SQL Database-forekomst, med alle forekomster i elastiske database grupper. Ved at adskille databaser mellem udvikling, midlertidige og live-miljøer, kan vi tilbyder vores kunder robust ydeevne isolationsniveauet matchet med skalering – en meget stor gevinst. "

Morten fortsætter, "før vi nødt til at klargøre servere for webdatabaser manuelt. Nu har vi ikke bekymre dig om det. Alt er automatiseret – fra klargøring til oprydning. "

Morten er også tilfreds med skalering funktioner, der leveres af Azure. "Elastiske database grupper er en perfekt for vores SaaS tilbyder fordi vi kan ringe kapacitet op og ned efter behov. Klargøring er nemt, og med vores konfigurationen, kan vi holde anvendelsen maksimalt." Morten stater "brugervenlighed elastiske grupper, sammen med assurance af service-niveau-baserede DTUs giver os mulighed for at klargøre nye ressourcepuljer efter behov. For nylig, skal spidst en af vores større kunder til 100 DTUs i dets live-miljø. Hvis du bruger Azure, hvis vores elastiske grupper kundens databaser med de ressourcer, som de skal bruges i realtid uden at forudsige DTU krav. Bare vores kunder får den slå omkring tid, som de forventer, og vi kan opfylder vores ydeevne service niveau aftaler."

Mikkel Madsen summerer: "Vi har din bærbare pc den effektive Azure algoritme, der forbinder et almindeligt scenarie for SaaS (onboarding nye kunder i realtid skaleres) til vores program mønster (før Klargør databaser, begge udvikling og live) oven på den underliggende teknologi (ved hjælp af Azure Service Bus køer sammen med Azure SQL-Database)."

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Azure, UaaS overstiger kundernes forventninger

Siden vælge Azure som dens cloud-partner, har Umbraco været kunne give UaaS kunder optimeret Indholdsstyring ydeevne uden IT-ressource investeringen påkrævet fra en præsentation hostet løsning. Som Morten siger "vi meget udvikler nemmere og skalerbarhed, som giver os en Azure, og vores kunder er thrilled med de funktioner og pålidelighed. Overordnede, der er gået et godt vinde os!"
 
## <a name="more-information"></a>Få mere at vide

- Hvis du vil vide mere om Azure elastiske database grupper skal du se [elastiske database grupper](sql-database-elastic-pool.md).

- Hvis du vil vide mere om Azure Service Bus skal du se [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).

- Hvis du vil vide mere om Web roller og arbejder roller, skal du se [arbejder roller](../fundamentals-introduction-to-azure.md#compute). 

- Hvis du vil vide mere om virtuelt netværk skal du se [virtuelt netværk](https://azure.microsoft.com/documentation/services/virtual-network/).    

- Hvis du vil vide mere om sikkerhedskopiering og gendannelse, skal du se [Forretningskontinuitet](sql-database-business-continuity.md).  

- Hvis du vil vide mere om overvågning ppols, se [overvåge grupper](sql-database-elastic-pool-manage-portal.md). 

- Hvis du vil vide mere om Umbraco som en tjeneste skal du se [Umbraco](https://umbraco.com/cloud).

