<properties
    pageTitle="Oprette en WebApp i en App Service-miljø"
    description="Lær at oprette webapps og app tjenesteplanerne i en App Service-miljø"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>Oprette en WebApp i en App Service-miljø

## <a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du opretter webapps og App Service planer i en [App Service-miljø](app-service-app-service-environment-intro.md) (ASE). 

> [AZURE.NOTE] Hvis du vil lære at oprette en WebApp, men du ikke behøver at gøre det i en App Service-miljø, kan du se [oprette en .NET WebApp](web-sites-dotnet-get-started.md) eller en af de relaterede selvstudier til andre sprog og rammer.

## <a name="prerequisites"></a>Forudsætninger

Dette selvstudiet, antages, du har oprettet en App Service-miljø. Hvis du ikke har gjort det, skal du se [oprette en App Service-miljø](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Oprette en WebApp

1. [Azure-portalen](https://portal.azure.com/), klik på **Ny > Web + Mobile > online**. 

    ![][1]

2. Vælg dit abonnement.  

    Hvis du har flere abonnementer Vær opmærksom på, hvis du vil oprette en app i dit miljø, App-tjeneste, du skal bruge det samme abonnement, du brugte, da oprettelse af miljøet. 

3. Vælg eller Opret en ressourcegruppe.

    *Grupper* gør det muligt at administrere relaterede Azure ressourcer som en enhed og er praktiske, når du opretter *Rollebaseret adgangskontrol* (RBAC) regler for dine apps. Yderligere oplysninger finder du se [Administration af ressourcerne Azure][ResourceGroups]. 

4. Vælg eller Opret en App-serviceaftale.

    *App tjenesteplanerne* administreres sæt af webapps.  Normalt når du vælger priser, anvendes prisen til App-serviceaftale i stedet for de enkelte apps. I en ASE du betaler for Beregn forekomster tildeles ASE i stedet for at du har angivet med din ASP.  Hvis du vil skalere op antallet af forekomster af en WebApp du skalere op forekomster af din App Service påvirker-plan, og det alle webapps i planen.  Nogle funktioner som websted pladser eller VNET Integration har også antal begrænsninger i planen.  Få mere at vide under [Oversigt over Azure App Service-planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    Du kan identificere App tjenesteplanerne i din ASE ved at kigge på den placering, som er angivet under plannavnet.  

    ![][5]

    Hvis du vil bruge en App-serviceaftale, der allerede findes i dit miljø, App-tjenesten, Vælg planen. Hvis du vil oprette en ny App Service-plan, skal du se følgende afsnit af dette selvstudium, [Opret en App-serviceaftale i en App Service-miljø](#createplan).

5. Skriv navnet på din online, og klik derefter på **Opret**. 

    Hvis din ASE bruger en ekstern VIP er URL-adressen for en app i en ASE: [*sitename*]. [*navnet på din App servicemiljø*]. p.azurewebsites.net i stedet for [*sitename*]. azurewebsites.net
    
    Hvis din ASE bruger en intern VIP derefter URL-adressen for en app i den valgte ASE er: [*sitename*]. [*underdomæne angivet under oprettelse af ASE*]   
    Når du vælger din ASP under oprettelse af ASE får du vist underdomænet, opdatere under **navn**

## <a name="createplan"></a>Oprette en App-serviceaftale

Når du opretter en App-serviceaftale i en App Service-miljø, er valgmulighederne arbejder forskellige, som ikke er nogen delte medarbejdere i en ASE.  De medarbejdere, du skal bruge, er de, der har fået tildelt ASE af administratoren.  Det betyder, at hvis du vil oprette en ny plan, du vil have flere medarbejdere, der allokeres til din ASE arbejder puljen end det samlede antal forekomster på tværs af alle dine planer, der allerede findes i puljen, der arbejder.  Hvis du ikke har nok medarbejdere i din ASE arbejder puljen til at oprette din plan, skal du arbejde med din ASE administrator for at få dem tilføjet.

En anden forskel med App Service-planer, der er hostet af et App-tjenesten miljø er manglen priser markeringen.  Når du har et App-tjenesten miljø du betaler for Beregn ressourcer, der bruges af systemet og har ikke tilføjede gebyrer for planerne i pågældende miljø.  Normalt når du opretter en App-serviceaftale vælger du en priser struktur, som bestemmer din fakturering.  Et App-tjenesten miljø er grundlæggende en privat placering, hvor du kan oprette indhold.  Du betaler for miljøet og ikke til at hoste dit indhold.

Følgende instruktioner viser, hvordan du opretter en App-serviceaftale, mens du opretter en web-app som beskrevet i forrige afsnit af selvstudiet.

1. Klik på **Opret ny** i plan markeringen brugergrænseflade, og Angiv et navn for din plan, ligesom du normalt ville gøre uden for en ASE.

2. Vælg den ASE, du vil bruge fra din placering datovælger.

    Da et App-tjenesten miljø er grundlæggende en privat installation placering, vises den under placering. 

    ![][2]

    Efter valg af en ASE i vælgeren til placering, App Service plan oprettelse af Brugergrænsefladen opdaterer.  Placeringen viser nu navnet på ASE systemet og området, det er i, og datovælgeren priser plan er blevet erstattet med en kollega puljen datovælger.  

    ![][3]

### <a name="selecting-a-worker-pool"></a>Hvis du vælger en kollega samling

Normalt er i Azure App Service og uden for et App-tjenesten miljø, der 3 Beregn størrelser, der er tilgængelige med markeringen af en dedikeret pris plan.  I en lignende måde for en ASE kan du definere op til 3 grupper af medarbejdere og angive Beregn størrelsen, der bruges til puljen, der arbejder.  Hvad det betyder for lejere af ASE er det i stedet for at vælge en priser plan med Beregn størrelse for din App Service-plan, skal du vælge det, der kaldes en *kollega puljen*.  

Valgmuligheden arbejder puljen Brugergrænsefladen viser Beregn størrelsen bruges til denne arbejder gruppe under navnet.  Det antal, der er tilgængelige refererer til hvor mange beregne forekomster er tilgængelige til brug i denne gruppe.  Samlet puljen faktisk have flere forekomster end dette nummer, men denne værdi refererer til blot hvor mange der ikke er i brug.  Hvis du vil justere din App servicemiljø for at tilføje mere beregne ressourcer se [konfiguration af din App Service-miljø](app-service-web-configure-an-app-service-environment.md).

![][4]

I dette eksempel vises kun to arbejder grupper, der er tilgængelige. Det skyldes, at ASE administratoren allokeres kun hosts i disse to arbejder grupper.  Tredjepart skal vises, når der er FOS allokeres til den.  

## <a name="after-web-app-creation"></a>Efter oprettelse af web app

Der er nogle overvejelser i forbindelse med kørsel Onlines og administration af App Service planer i en ASE, der skal være taget i betragtning.  

Som nævnt tidligere ejeren af ASE er ansvarlig for størrelsen på systemet, og de er derfor også ansvarlig for at sikre, at der er tilstrækkelig kapacitet til at være vært for de ønskede App Service-planer. Hvis der ikke er nogen tilgængelige medarbejdere, kan du ikke kan oprette din App-serviceaftale.  Dette er også True til skalering af din online.  Hvis du har brug for flere forekomster vil du have til at få din App servicemiljø administrator for at tilføje flere medarbejdere.

Når du har oprettet din online og App-serviceaftale er det en god ide at skalere det op.  I en ASE skal du altid har mindst 2 forekomster af din App Service for at få vist fejltolerance for dine apps.  Skalering en App-serviceaftale i en ASE er den samme som normalt App Service planen brugergrænseflade.  Du kan finde flere oplysninger om skalering, [hvordan du kan tilpasse en WebApp i en App Service-miljø](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
