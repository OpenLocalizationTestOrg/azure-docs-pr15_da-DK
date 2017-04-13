<properties
   pageTitle="Fejlfinding af dit program i Visual Studio | Microsoft Azure"
   description="Forbedre pålideligheden og ydeevnen for dine tjenester ved at udvikle og fejlfinding i Visual Studio på en lokal udvikling klynge."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Fejlfinding programmets Service-strukturen ved hjælp af Visual Studio

## <a name="debug-a-local-service-fabric-application"></a>Fejlfinding af et lokalt program som Service-strukturen

Du kan spare tid og penge ved implementering og fejlfinding programmets Azure Service-strukturen i en lokal computer udvikling klynge. Visual Studio kan installere programmet til den lokale klynge og Opret automatisk forbindelse fejlfindingsværktøjet til alle forekomster af programmet.

1. Starte en lokal udvikling klynge ved at følge trinnene i [konfigurationen af din Service-strukturen udviklingsmiljø](service-fabric-get-started.md).

2. Tryk på **F5** , eller klik på **fejlfinding af** > **Starte fejlfinding**.

    ![Starte fejlfindingen et program][startdebugging]

3. Angive pausepunkter i din kode og trin via programmet ved at klikke på kommandoerne i menuen **fejlfinding** .

    > [AZURE.NOTE] Visual Studio vedhæfter alle forekomster af programmet. Mens du er væk via kode, kan få påløber pausepunkter af flere processer, hvilket resulterer i samtidige sessioner. Prøv at deaktivere pausepunkterne, når de rammer, ved at gøre hvert pausepunkt betinget på tråd-ID eller ved hjælp af diagnosticering begivenheder.

4. Vinduet **Diagnosticering begivenheder** åbnes automatisk, så du kan få vist diagnosticering hændelser i realtid.

    ![Vis diagnosticering hændelser i realtid][diagnosticevents]

5. Du kan også åbne vinduet **Diagnosticering begivenheder** i skyen Explorer.  Højreklik på en hvilken som helst node under **Service-strukturen**og vælge **Vis Streaming sporinger**.

    ![Åbn vinduet diagnosticering begivenheder][viewdiagnosticevents]

    Hvis du vil filtrere dine sporinger til en bestemt tjeneste eller et program, skal du blot aktivere streaming sporinger på denne bestemte tjeneste eller et program.

6. Diagnosticering begivenheder kan ses i filen automatisk oprettede **ServiceEventSource.cs** og kaldes fra programkode.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. Vinduet **Diagnosticering begivenheder** understøtter filtrering, afbrydelsen og undersøge begivenheder i realtid.  Filteret er en simpel streng søgning meddelelsens begivenhed, herunder dens indhold.

    ![Filtrere, afbryde og genoptage eller Undersøg begivenheder i realtid][diagnosticeventsactions]

8. Fejlfinding services minder om fejlfinding af andre programmer. Du indstiller normalt pausepunkter gennem Visual Studio til nem fejlfinding. Selvom pålidelig samlinger gentage på tværs af flere noder, kan de stadig implementere IEnumerable. Det betyder, at du kan bruge visningen resultater i Visual Studio under fejlfinding til at se, hvad du har gemt i. Angive et pausepunkt blot et vilkårligt sted i din kode.

    ![Starte fejlfindingen et program][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Fejlfinding af en eksterne Service-strukturen-program

Hvis dine Service-strukturen programmer kører på en tjeneste-strukturen klynge i Azure, er du kan foretage fejlfinding ved hjælp af disse direkte fra Visual Studio.

> [AZURE.NOTE] Funktionen kræver [Service strukturen SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) og [Azure SDK til .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [AZURE.WARNING] Ekstern fejlfinding er beregnet til Udviklingscenter/afprøve scenarier og ikke skal bruges i fremstilling miljøer på grund af indvirkning på de programmer, der kører.

1. Gå til din klynge i **Skyen Explorer**, højreklik, og vælg **Aktivér fejlfinding**

    ![Aktivere ekstern fejlfinding][enableremotedebugging]

    Dette vil påbegynde processen med at aktivere din klyngenoder samt nødvendige netværkskonfigurationer remote fejlfinding filtypenavnet.

2. Højreklik på Klyngenoden i **Skyen Explorer**, og vælg **Vedhæft fejlfinding**

    ![Vedhæfte fejlfinding][attachdebugger]

3. Vælg den proces, du vil foretage fejlfinding i dialogboksen **Vedhæft til at behandle** , og klik på **Vedhæft**

    ![Vælg proces][chooseprocess]

    Navnet på den proces, du vil vedhæfte, er lig med navnet på din tjeneste projektnavn samling.

    Fejlfindingsværktøjet vedhæftes til alle noder, der kører processen.
    - I tilfælde, hvor du foretager fejlfinding af en uden status tjeneste, er alle forekomster af tjenesten på alle noder en del af sessionen med fejlfinding.
    - Hvis du foretager fejlfinding af en med høj sikkerhed tjeneste, der kun den primære kopi af alle partitioner er aktivt og derfor taget af fejlfindingsværktøjet. Hvis den primære replika flytter under sessionen med fejlfinding, vil behandling for stadig være en del af sessionen med fejlfinding.
    - For at fange kun relevante partitioner eller forekomster af en given tjeneste, kan du bruge betinget pausepunkter kun bryde en bestemt partition eller forekomst.

    ![Betinget pausepunkt][conditionalbreakpoint]

    > [AZURE.NOTE] Vi understøtter i øjeblikket ikke fejlfinding en tjeneste-strukturen klynge med flere forekomster af det samme service eksekverbare navn.

4. Når du er færdig med at fejlfinding dit program, kan du deaktivere filtypenavnet remote fejlfinding ved at højreklikke på klynge i **Skyen Explorer** og vælge **Deaktiver fejlfinding**

    ![Deaktivere ekstern fejlfinding][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming sporinger fra en eksterne klyngenode

Du kan også på stream spor direkte fra en eksterne klynge knude til Visual Studio. Denne funktion kan du værdistrøm ETW sporingshændelser, der er produceret på en tjeneste-strukturen klyngenode, direkte i Visual Studio.

> [AZURE.NOTE] Funktionen kræver [Service strukturen SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) og [Azure SDK til .NET 2.9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING]Streaming sporinger er beregnet til Udviklingscenter/afprøve scenarier og ikke skal bruges i fremstilling miljøer på grund af indvirkning på de programmer, der kører.
> I et produktionsmiljø, bør du stole på viderestilling af hændelser ved hjælp af Azure diagnosticering.

1. Gå til din klynge i **Skyen Explorer**, højreklik, og vælg **Aktivér Streaming sporinger**

    ![Aktivere ekstern streaming sporinger][enablestreamingtraces]

    Dette vil påbegynde processen med at aktivere filtypenavnet streaming sporinger i din klyngenoder samt nødvendige netværkskonfigurationer.

2. Udvid **noderne** elementet i **Skyen Explorer**, højreklik på den knude, du vil streame sporinger fra, og vælg **Vis Streaming sporinger**

    ![Vis remote streaming sporinger][viewremotestreamingtraces]

    Gentag trin 2 for så mange noder, som du vil have vist sporinger fra. Hver noder stream vises i et dedikeret vindue.

    Du er nu kunne se sporinger udsendes af Service-strukturen og dine tjenester. Hvis du vil filtrere hændelserne, der kun at vise et bestemt program, nøjes med at skrive navnet på programmet i filteret.

    ![Få vist streaming sporer][viewingstreamingtraces]

4. Når du er færdig streaming sporinger fra din klynge, kan du deaktivere remote streaming sporinger, ved at højreklikke på klynge i **Skyen Explorer** og vælge **Deaktiver Streaming sporinger**

    ![Deaktivere remote streaming sporinger][disablestreamingtraces]

## <a name="next-steps"></a>Næste trin

- [Test en tjeneste-strukturen tjeneste](service-fabric-testability-overview.md).
- [Administrere programmerne Service-strukturen i Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
