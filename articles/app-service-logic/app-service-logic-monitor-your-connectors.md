<properties
    pageTitle="Administrere og overvåge dine forbindelser og API Apps i App Service | Microsoft Azure"
    description="Få vist ydeevne dine forbindelser og API Apps i logik Apps; microservices arkitektur"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Administrere og overvåge dine indbyggede API Apps og forbindelser

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen.

Du har oprettet en indbygget API-App. Hvad nu?

I Azure er hver API-App et separat websted hostet på Azure. Som et resultat, kan du nemt se antallet af anmodninger om foretages, og se, hvor meget data, der bruges af forbindelsen. Du kan også sikkerhedskopiere din API-App, Opret beskeder, aktivere Tinfoil sikkerhed og tilføje brugere og roller.

Dette emne beskrives nogle af de forskellige indstillinger til at administrere din API-App.

Åbn din API-App for at få vist disse indbyggede funktioner, i [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). Hvis Appen API er på din startboard, Vælg den for at åbne egenskaberne. Du kan også vælge **Gennemse**, Vælg **API Apps**og derefter vælge din API-App:

![][browse]

## <a name="see-the-properties-you-entered"></a>Få vist egenskaberne, du har angivet

Når du åbner Appen API, findes der flere funktioner og opgaver:

![][settings]

Du kan:

- **Indstillinger for** viser specifikke oplysninger på API-App, herunder dine abonnementsoplysninger, og viser en liste over de brugere, der har adgang til din API-app. Du kan også øge eller mindske antallet af forekomster af din API-App ved hjælp af funktionen skala; blandt andre funktioner.
- Brug knapperne **Start** og **Stop** til at styre API App.
- Når produktopdateringer er foretaget i de underliggende filer, der anvendes af din API-App, kan du klikke på **Opdater** for at få de seneste versioner. Eksempelvis hvis der er en løsning eller en sikkerhedsopdatering, der er udgivet af Microsoft, opdaterer at klikke på **Opdater** automatisk din API-App Hvis du vil medtage denne rettelse.
- Vælg **Skift Plan** til opgradering eller nedgraderingsrettigheder baseret på dataene brugen af API-App. Du kan også bruge denne funktion til at se din brug af data.
- Du kan eventuelt angive navnet på en tabel til at oprette forbindelse til, når du opretter en forbindelse, der indeholder tabeller, som SQL-forbindelsen. Et skema baseret på tabellen er automatisk oprettes og er tilgængelige, når du klikker på **Hent skemaer**. Derefter kan du bruge dette hentede skema til at oprette en transformering eller et kort.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Ændre din forbindelse eller API konfigurationsværdier, du har angivet

Når du har konfigureret eller oprettet oprettet forbindelsen, kan du ændre de værdier, du har angivet. Eksempelvis hvis du har konfigureret SQL-forbindelsen, og du vil ændre navnet på SQL Server eller tabelnavn, kan du gøre dette i bladet API App på forbindelsen.

Trin omfatter:

1. Åbn din forbindelse eller API App. Når du gør, åbnes bladet API App.
2. Klik på linket under egenskaben Host i **Essentials**. Linket hedder noget som *slackconnector* eller *microsoftsqlconnector123*:

    ![][apiapphost]

3. Vælg **Indstillinger**i bladet API App Host. Vælg **Programindstillinger**i bladet indstillinger. Din konfiguration af værdier er angivet under **Indstillinger**:

    ![][hostsettings]

4. Klik på den indstilling, du vil ændre, skal du angive den nye værdi, og **Gem** dine ændringer.


## <a name="install-the-hybrid-connection-manager---optional"></a>Installere Forbindelsesstyring Hybrid - som valgfri

![][hcsetup]

Forbindelsesstyring Hybrid giver dig mulighed for at oprette forbindelse til et lokalt system, som SQL Server eller SAP. Denne hybrid connectivity bruger Azure Service Bus til at oprette forbindelse og til at styre sikkerhed mellem ressourcerne Azure og dine lokale ressourcer.

Under [Brug af Forbindelsesstyring hybride i Azure App-tjeneste](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Hybrid Forbindelsesstyring er påkrævet, kun, hvis du opretter forbindelse til en lokal ressource bag firewallen. Hvis du ikke opretter forbindelse til et lokalt system, kan Forbindelsesstyring Hybrid ikke er angivet i din forbindelse blade.

## <a name="monitor-the-performance"></a>Overvåge ydeevnen
Ydeevnen målepunkter er indbyggede funktioner og inkluderet med hver API-App, du opretter. Denne statistik er specifikke for din API-App hostet i Azure. Eksempel målepunkter:

![][monitoring]

Du kan:

- Vælg **anmodninger og fejl** til at føje forskellige ydeevne målepunkter herunder almindeligt kendte HTTP fejlkoder, som 200, 400 eller 500 HTTP status-koder. Du kan også se svar gange, se, hvor mange anmodninger til API-App, og se, hvor meget data kommer, og hvor meget data går. Baseret på ydeevne målepunkter, kan du oprette mail beskeder, hvis en metrikværdi indeholder mere end en grænseværdi efter eget valg.
- I **brugen**, kan du se hvor meget **CPU** bruges af API-App skal du gennemse den aktuelle **Kvote** i MB og se din maksimale databrug, der er baseret på dine omkostninger niveau. **Anslået bruger** kan hjælpe dig med at bestemme de potentielle omkostninger ved at køre din API-App.
- Vælg **processer** for at åbne Process Explorer. Dette viser forekomster din web og deres egenskaber, herunder tråd Tæl og hukommelse brugen.

Med disse værktøjer, kan du se Hvis App tjeneste planlægge skal skaleret eller skaleret ned, baseret på virksomhedens behov. Disse funktioner er indbyggede til portalen med ingen yderligere værktøjer, der kræves.

## <a name="control-the-security"></a>Styre sikkerheden

API Apps Brug rollebaseret sikkerhed. Disse roller gælder for hele Azure oplevelse, herunder API Apps og andre Azure ressourcer. Rollerne, der omfatter:

Rolle | Beskrivelse
--- | ---
Ejer | Har fuld adgang til administration af oplevelsen og kan give adgang til andre brugere eller grupper.
Bidragyder | Har fuld adgang til administration af oplevelsen. Kan ikke give adgang til andre brugere eller grupper.
Reader | Kan få vist alle ressourcer undtagen hemmeligheder.
Access Brugeradministrator | Kan få vist alle ressourcer, oprette/Administrer roller, og oprette/administrere understøtter billetter.

Se [Rollebaseret adgangskontrol i Microsoft Azure-portalen](../active-directory/role-based-access-control-configure.md).

Du kan nemt tilføje brugere og tildele dem bestemte roller til din API-App. På portalen viser dig de brugere, der har adgang og deres tildelte rolle:

![][access]  

- Vælg **brugere** for at tilføje en bruger, Tildel en rolle og fjerne en bruger.
- Vælg **roller** for at se alle brugere i en bestemt rolle, tilføje en bruger til en rolle, og fjerne en bruger fra en rolle.


## <a name="more-good-stuff"></a>Flere god ting
- Vælg **API definition** for at åbne filen oprettes automatisk Swagger for din specifikke API-app.
- Vælg **afhængigheder** til at få vist de filer, der kræves, før din API-App. Eksempelvis hvis du bruger SAP-forbindelsen, skal installere du nogle yderligere filer på den lokale Hybrid-Forbindelsesstyring. Disse afhængigheder vises i din API app blade.

>[AZURE.IMPORTANT] Når du åbner din API app egenskaber og se under **Essentials**, er der **vært** og **gatewayen** links, der åbner nye blade:
>
> ![][host]
>
>Disse egenskaber er specifikke for det websted, der hoster din API-App. Når du bruger en indbygget API App eller en forbindelse, de fleste af disse egenskaber er det virkelig gælder ikke, og vi anbefaler, at du ikke opdaterer disse egenskaber. Hvis du har oprettet din egen API-App i Visual Studio og installeret på din Azure abonnement, kan du bruge Host og gatewayen blade. <br/><br/>


>[AZURE.NOTE] For at komme i gang med logik Apps før tilmelding til en Azure-konto skal du gå til [Prøve logik App](https://tryappservice.azure.com/?appservice=logic). Du kan oprette en forbigående starter logik app. Ingen kreditkort, der er påkrævet, og ingen forpligtelser.

## <a name="read-more"></a>Få mere at vide

[Overvåge dine logik Apps](app-service-logic-monitor-your-logic-apps.md)<br/>
[Forbindelser og API listen over Apps i App-tjeneste](app-service-logic-connectors-list.md)<br/>
[Rollebaseret adgangskontrol i Microsoft Azure-portalen](../active-directory/role-based-access-control-configure.md)<br/>
[Brug af Forbindelsesstyring Hybrid i Azure App Service](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
