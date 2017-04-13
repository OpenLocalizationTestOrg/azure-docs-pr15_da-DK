<properties 
   pageTitle="Test af ydeevnen for en skybaseret tjeneste | Microsoft Azure"
   description="Teste ydeevnen for en skybaseret tjeneste, ved hjælp af Visual Studio-profiler"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="testing-the-performance-of-a-cloud-service"></a>Test af ydeevnen for en skybaseret tjeneste 

##<a name="overview"></a>Oversigt

Du kan teste ydeevnen for en skybaseret tjeneste på følgende måder:

- Brug Azure diagnosticering til at indsamle oplysninger om anmodninger om og forbindelser og gennemse statistik over websteder, der viser, hvordan tjenesten udfører fra en kunde perspektiv. For at komme i gang med skal du se [konfiguration af diagnosticering til Azure Cloud Services og virtuelle computere]( http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Brug Visual Studio-profiler til at få en indgående analyse af de computational aspekter af hvordan tjenesten kører. Som i dette emne beskrives, kan du bruge profiler til at måle ydeevne, som en tjeneste kører i Azure. Finde oplysninger om hvordan du bruger profiler til at måle ydeevne, som en tjeneste kører lokalt i en Beregn emulator, [test af ydeevnen for en Azure skyen Service lokalt på den beregne Emulator ved hjælp af Visual Studio-Profiler](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## <a name="choosing-a-performance-testing-method"></a>Vælge en performance-test metode

###<a name="use-azure-diagnostics-to-collect"></a>Bruge Azure diagnosticering til at indsamle:###

- Statistik på websider eller tjenester, som anmodninger om og forbindelser.

- Statistikker om roller, såsom hvor ofte en rolle genstartes.

- Oplysninger om hukommelsesforbrug, som procentdelen af tid, spildopsamlingsfunktionen tager eller hukommelsen sæt overordnede en igangværende rolle.

###<a name="use-the-visual-studio-profiler-to"></a>Brug Visual Studio-profiler til:###

- Find ud af, hvilke funktioner tage mest tid.

- Måle hvor meget tid hver del af et beregningsmæssigt intensivt program tager.

- Sammenligne detaljerede ydeevne rapporter til to versioner af en tjeneste.

- Analysere hukommelsesallokering mere detaljeret end niveauet for individuelle hukommelse tildelinger.

- Analysere på dokumentsammenfald problemer i flertrådet kode.

Når du bruger profiler, kan du indsamle data, når en skybaseret tjeneste kører lokalt eller i Azure.

###<a name="collect-profiling-data-locally-to"></a>Indsamle status data lokalt til:###

- Teste ydeevnen for en del af en skybaseret tjeneste, såsom udførelse af bestemt arbejder rolle, der ikke kræver en realistisk simuleret belastning.

- Teste ydeevnen for en skybaseret tjeneste i isolationsniveauet kontrolleret betingelser.

- Teste ydeevnen for en skybaseret tjeneste, før du installerer det til Azure.

- Teste ydeevnen for en skybaseret tjeneste privat, uden at forstyrre de eksisterende installationer.

- Teste ydeevnen for tjenesten uden at skulle betale for at køre i Azure.

###<a name="collect-profiling-data-in-azure-to"></a>Indsamle status data i Azure til:###

- Teste ydeevnen for en skybaseret tjeneste simuleret eller reelle belastning.

- Bruge metoden instrumentation for indsamling af status data, som i dette emne beskrives senere.

- Teste ydeevnen for tjenesten i det samme miljø som når tjenesten kører i fremstilling.

Du typisk simulere en belastning på test skytjenester under normal eller belastning betingelser.

## <a name="profiling-a-cloud-service-in-azure"></a>Oprettelse af profiler en skybaseret tjeneste i Azure

Når du udgiver din skybaseret tjeneste fra Visual Studio, kan du profil tjenesten og angive status-indstillinger, der giver dig de oplysninger, du vil. Der startes en status session for hver forekomst af en rolle. Du kan finde flere oplysninger om, hvordan du publicere din tjeneste fra Visual Studio, se [publicering til en Azure skybaseret tjeneste fra Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Du kan finde flere oplysninger om oprettelse af profiler ydeevne i Visual Studio, se [Begyndere vejledning til oprettelse af ydeevnen profiler](https://msdn.microsoft.com/library/azure/ms182372.aspx) og [Analyse af programmets ydeevne ved hjælp af værktøjer til oprettelse af profiler](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE] Du kan aktivere enten IntelliTrace eller profiler, når du publicerer skybaseret tjeneste. Du kan ikke aktivere begge.

###<a name="profiler-collection-methods"></a>Profiler samling metoder

Du kan bruge forskellige samling metoder til oprettelse af profiler, baseret på din problemer med ydeevnen:

- **Stikprøver, hvor der CPU** - denne metode indsamler statistik for programmet, der er nyttige til indledende analyse af CPU anvendelsen problemer. CPU stikprøver, hvor er den foreslåede metode til at starte de fleste ydeevne efterforskninger. Der er en lav indvirkning på det program, du profiler, når du har indsamlet CPU stikprøver, hvor data.

- **Instrumentation** – denne metode indsamler detaljerede tidsindstilling data, der kan bruges til fokus analyse og til analyse af problemer med ydeevnen input/output. Metoden instrumentation poster hver post, Afslut og funktionskald af funktionerne i et modul under en profiler køre. Denne metode er nyttig til indsamling af tidsindstilling detaljerede oplysninger om en sektion i din kode og for at forstå påvirkningen af input- og outputområder handlinger på programmets ydeevne. Denne metode er deaktiveret, hvis computeren kører en 32-bit-operativsystem. Denne indstilling er tilgængelig, kun, når du kører skytjenesten i Azure, ikke lokalt i Beregn emulatoren.

- **.NET hukommelsesallokering** - denne metode indsamler .NET Framework hukommelse allokering data ved hjælp af stikprøver, hvor profiler metode. De indsamlede data omfatter antallet af og størrelsen af tildelte objekter.

- **På dokumentsammenfald** – denne metode indsamler ressourcedata konflikt, og processen og tråd udførelse af data, der er nyttig analyse flertrådet og med så mange programmer. Metoden på dokumentsammenfald indsamler data for hver enkelt hændelse, dokumentkomponenter udførelse af din kode, som når en tråd venter låst adgang til et program ressource til frigives. Denne metode er nyttig til at analysere flertrådet programmer.

- Du kan også aktivere **Niveau interaktion profiler**, som indeholder flere oplysninger om tidspunkterne, hvor udførelse af synkron ADO.NET kalder i flere niveauer programmer, kommunikerer med en eller flere databaser-funktioner. Du kan indsamle niveau interaktion data med en af de status metoder. Se [Få vist niveau interaktioner](https://msdn.microsoft.com/library/azure/dd557764.aspx), kan finde flere oplysninger om oprettelse af niveau interaktion profiler.

## <a name="configuring-profiling-settings"></a>Konfigurere indstillinger for status

Følgende illustration viser, hvordan du konfigurerer din status indstillinger fra dialogboksen Publicer Azure-program.

![Konfigurere oprettelse af profiler indstillinger](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE] Hvis du vil aktivere afkrydsningsfeltet **Aktiver profiler** , skal du have profiler, der er installeret på den lokale computer, du bruger til at publicere din skybaseret tjeneste. Som standard installeres på profiler, når du installerer Visual Studio.

### <a name="to-configure-profiling-settings"></a>Konfigurere indstillinger for status

1. I Solution Explorer skal du åbne genvejsmenuen for projektet Azure og derefter vælge **Publicer**. Detaljeret vejledning om, hvordan du publicerer en skybaseret tjeneste, skal du se [publicere en skybaseret tjeneste ved hjælp af værktøjerne Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. Valgte fanen **Avancerede indstillinger** i dialogboksen **Publicer Azure-program** .

1. Hvis du vil aktivere profiler, skal du markere afkrydsningsfeltet **aktivere oprettelse af profiler** .

1. Vælg linket **Indstillinger** for at konfigurere indstillingerne for status. Dialogboksen Indstillinger for oprettelse af profiler vises.

1. Vælg typen profiler, skal du fra alternativknapper **hvilken metode til oprettelse af profiler vil du skifte til** .

1. For at indsamle niveau interaktion status data, skal du markere afkrydsningsfeltet **Aktivér niveau interaktion profiler** .

1. Vælg knappen **OK** for at gemme indstillingerne.

    Når du udgiver dette program, anvendes disse indstillinger til at oprette den status session for hver rolle.

## <a name="viewing-profiling-reports"></a>Få vist profiler rapporter

Der oprettes en status session for hver forekomst af en rolle i din skybaseret tjeneste. For at få vist din status rapporter over hver sessionen fra Visual Studio, kan du få vist vinduet Server Stifinder og derefter vælge noden Azure beregne for at vælge en forekomst af en rolle. Derefter kan du se rapporten status, som vist i nedenstående illustration.

![Få vist status rapport fra Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Sådan får du vist status rapporter

1. For at få vist vinduet Server Explorer i Visual Studio, i menuen Vælg visning, Server Explorer.

1. Vælg noden Azure beregne, og vælg derefter noden Azure-installation til skytjenesten, du har valgt til profil, når du har publiceret fra Visual Studio.

1. Vælg rollen i tjenesten for at få vist status rapporter til en forekomst, Åbn genvejsmenuen for en bestemt forekomst, og klik på **Vis profiler rapport**.

    Rapport, en .vsp-fil er nu hentet fra Azure, og status for overførslen vises Azure aktivitet log. Når overførslen er afsluttet, rapporten status vises på en fane i editor til Visual Studio med navnet <Role name> _<Instance Number>_ <identifier>.vsp. Opsummeringsdata for rapporten vises.

1. Hvis du vil have vist forskellige visninger af rapporten, på listen Aktuel visning, skal du vælge typer visning, du vil. Du kan finde yderligere oplysninger finder [Profiler værktøjer rapportvisninger](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Næste trin

[Fejlfinding Cloud Services](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicere på en Azure skybaseret tjeneste fra Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

