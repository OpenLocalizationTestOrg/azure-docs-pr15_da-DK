<properties 
    pageTitle="Hvordan du kan tilpasse en App i en App Service-miljø" 
    description="Skalering en app i en App Service-miljø" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Skalering apps i en App Service-miljø #

Der er normalt tre ting du kan skalere i tjenesten Azure-App:

- priser plan
- arbejder størrelse 
- antallet af forekomster.

I en ASE er det ikke nødvendigt at vælge eller ændre den priser plan.  Med hensyn til funktioner er det allerede på en egenskab prisniveau Premium.  

I forhold arbejder størrelser kan ASE-administrator tildele størrelsen på Beregn ressourcen skal bruges til hver arbejder puljen.  Det betyder, kan du have arbejder Pulje 1 med P4 Beregn ressourcer og arbejder Pulje 2 med P1 beregne ressourcer, hvis du ønsker.  De behøver ikke at være i orden størrelse.  For detaljer omkring størrelsen og deres priser se dokumentet her [Azure App servicepriser][AppServicePricing].  Dette lader skaleringsindstillinger for webapps og App-tjenesten planer i et miljø til App-tjenesten skal være:

- arbejder puljen markering
- antallet af forekomster

Ændre et element sker via relevante Brugergrænsefladen vises for din ASE hostet App serviceplaner.  

![][1]

Du kan ikke skalere op din ASP ud over antallet tilgængelige Beregn ressourcer i gruppen arbejder, som din ASP er i.  Hvis du skal beregne ressourcer i puljen, der arbejder skal du få administratoren ASE for at tilføje dem.  For oplysninger om at konfigurere din ASE læse oplysningerne her: [hvordan du konfigurerer miljøet App Service][HowtoConfigureASE].  Du kan også drage fordel af funktionerne ASE Autoskalering føje kapacitet, der er baseret på tidsplan eller målepunkter.  For at få flere oplysninger om konfiguration af Autoskalering for ASE miljø selve se, [hvordan du konfigurerer Autoskalering for en App servicemiljø][ASEAutoscale].

Du kan oprette flere app service plan ved hjælp af Beregn ressourcer fra anden arbejder grupper, eller du kan bruge den samme arbejder puljen.  Beregne ressourcer for eksempel hvis du har (10) tilgængelige Beregn ressourcer i arbejder Pulje 1, kan du vælge at oprette en app-serviceaftale ved hjælp af (6) Beregn ressourcer og planlægge en anden app-tjeneste, der bruger (4).

### <a name="scaling-the-number-of-instances"></a>Skalering antallet af forekomster ###

Når du opretter din online i et App-tjenesten miljø starter det med 1 forekomst.  Du kan derefter skalere ud af, at andre forekomster til at levere yderligere Beregn ressourcer til din app.   

Hvis din ASE har tilstrækkelig kapacitet, så det er ganske enkelt.  Du gå til din App Service planlægge, der indeholder de websteder, du vil skalere op, og Markér skala.  Dette åbner brugergrænseflade, hvor du manuelt kan vælge målestoksforhold for din ASP eller konfigurere Autoskalering regler til din ASP.  Med manuelt skalering Angiv din app blot ***skala ved*** til ***en forekomst, tæller, som jeg indtaster manuelt***.  Træk i skyderen til det ønskede antal, eller Angiv det i feltet ud for skyderen her.  

![][2] 

Autoskalering regler til en ASP i en ASE fungerer på samme måde som normalt.  Du kan vælge ***CPU procentdel*** under ***skala ved*** og oprette Autoskalering regler for ASP-baseret på CPU procent, eller du kan oprette mere komplekse regler ved hjælp af ***regler for tidsplan og ydeevne***.  For at se yderligere oplysninger om konfiguration af Autoskalering bruge vejledningen her [skalere en app i Azure App Service][AppScale]. 


### <a name="worker-pool-selection"></a>Arbejder puljen markering ###

Som nævnt tidligere, åbnes arbejder puljen markeringen fra ASP-Brugergrænsefladen.  Åbn bladet for ASP, du vil skalere og vælge arbejder puljen.  Du får vist alle de arbejder grupper, du har konfigureret i dit miljø, App-tjenesten.  Hvis du har kun én arbejder pulje vil du kun vist én puljen vises.  Hvis du vil ændre hvilke arbejder puljen din ASP er i, skal markere du blot arbejder puljen ønskede din App Service planlægger at flytte til.  

![][3]

Det er vigtigt at sikre, at du har tilstrækkelig kapacitet til din ASP før du flytter dine ASP fra én arbejder gruppe til en anden.  På listen over arbejder grupper, du ikke kun arbejder puljen navnet vises, men du kan også se, hvor mange medarbejdere er tilgængelige i gruppe, der arbejder.  Sørg for, at der er tilstrækkelig forekomster, der er tilgængelige til at indeholde planlægge din App-tjenesten.  Hvis du har brug for mere beregne ressourcer i arbejder puljen, du vil flytte til, skal du vælge administratoren ASE for at tilføje dem.  

> [AZURE.NOTE] Flytte en ASP fra én arbejder puljen medfører kolde starter af apps i ASP.  Dette kan medføre anmodninger om at køre langsomt, som din app er kolde gang på de nye Beregn ressourcer.  Kolde start kan undgås ved hjælp af [programmet varm op egenskab] [ AppWarmup] i Azure App-tjeneste.  Modulet initialisering af programmet, der er beskrevet i artiklen fungerer også for kolde starter, fordi initialiseringen startes også, når apps er kolde startet på nye Beregn ressourcer. 

## <a name="getting-started"></a>Kom godt i gang

For at komme i gang med App-tjenesten miljøer skal du se [Hvordan til at oprette en App Service-miljø][HowtoCreateASE]

Du kan finde flere oplysninger om Azure App Service platformen, se [Azure App Service][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
