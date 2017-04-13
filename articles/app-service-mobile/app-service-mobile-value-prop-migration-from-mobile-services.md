<properties
    pageTitle="Brug af Mobile-tjenester, hvordan hjælper App Service?"
    description="Få mere at vide, hvilke fordele App Service overskuelig dine eksisterende projekter i Mobile-tjenester."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>jeg bruge Mobile-tjenester, hvordan hjælper App Service?

##<a name="overview"></a>Oversigt
Din eksisterende Mobile-tjeneste er sikkert og forbliver understøttede. Men der er antallet af fordele *Azure App Service* indeholder platform til din mobile-app, der ikke er tilgængelige i dag med Mobile-tjenester:

- Nemmere, nemmere og billigere tilbud til apps, som indeholder både web og -mobilklienter
- Nye host funktioner, herunder Web job, overvågning brugerdefineret CNames, bedre
- Nøglefærdige integration med trafik Manager
- Forbindelse til din lokale-ressourcer og VPN'er ved hjælp af VNet ud over Hybrid forbindelser
- Overvågning, advarsler og fejlfinding for din app, ved hjælp af NewRelic eller AppInsights
- Bedre spektret af den underliggende beregne ressourcer og priser
- Indbyggede automatisk skala, justering af belastning og overvågning.
- Indbygget arrangere sikkerhedskopiering, Annuller og test af i fremstilling

## <a name="new-hosting-features"></a>Ny vært funktioner
I *Azure App Service* *Mobile-App* kører back end-kode i samme beholder som Web App og API App. Som Sådan kan du drage fordel af alle funktionerne i denne beholder, herunder nogle af dem, der ikke findes i øjeblikket i Mobile-tjenester:

- Føje køre kontinuerligt back end-logik via internettet job
- Kontroller, at din back end-kode altid kører
- Bruge brugerdefinerede CNames til at give brugervenlig og stabil navne for at mobile back end-slutpunkterne
- Geografisk skalering din app med trafik Manager
- Medtag biblioteker og pakker, som du ønsker.
- (For .NET) Udnytte en funktion af ASP.NET, herunder MVC
- (For Node.js) Udnytte en ren JavaScript-bibliotek med Node økosystemet, herunder almindelige MVC biblioteker.

##<a name="access-on-premises-data-using-vnet"></a>Access lokale data ved hjælp af VNet
Med Mobile-tjenester kan i dag du allerede bruge Hybrid forbindelser til at få adgang til lokal ressourcer. Der er dog visse situationer, hvor en VPN-løsning er foretrukne. Du kan bruge Azure VNet med *Azure App-tjenesten* for din Mobile-App back end-kode.

##<a name="use-your-favorite-backend-language"></a>Bruge din foretrukne back end-sprog
*Azure App Service* indeholder bredere og mere omfattende understøttelse af ASP.NET og Node.js platforme, herunder adgang til de seneste eksekverbar kode.

##<a name="set-up-automatic-scale"></a>Konfigurere automatisk skala
Med Mobile-tjenester kørte alle forekomster af din back end-kode på små FOS. *Azure App Service* kan du vælge størrelsen på FOS fra et meget mere omfattende sæt af indstillinger. Du kan også hurtigt skalere op eller ud til at håndtere nogen indgående kunde belastning, baseret på forskellige ydeevne målepunkter.

##<a name="be-in-the-know"></a>I den "ved"
Reagere på problemer i realtid med overvågning og beskeder til at informere dig og dit team automatisk. Integrere avancerede app analyser og overvågning funktionalitet fra nye Relic og AppInsights for at få endnu bedre indsigt i, hvordan det overordnet går din-mobilappen. Du kan nu konfigurere beskeder, der er baseret på række ydeevne målepunkter programmeringsmæssigt og via Azure-portalen med *Azure App Service* .

##<a name="keep-your-assets-safe"></a>Beskytte dine aktiver
Sikkerhedskopiere automatisk back end- og database. Kode og dine data er beskyttet mod nedbrud og gendannes nemt, så du kan køre din virksomhed uden bekymringer.

##<a name="ready-stage-go"></a>Fase, gå klar!
Med *Azure App Service* kan du nu oprette flere private test og arrangere miljøer til din mobile-apps. Bruge dem til at udføre tests, før du installerer. Skifte til fremstilling uden nedetid. Webapps er allerede indlæst, at sikre den bedste oplevelse.

Du kan få start at drage fordel af *App Service* til din eksisterende Mobile Service ved at følge dette [selvstudium](app-service-mobile-migrating-from-mobile-services.md).

