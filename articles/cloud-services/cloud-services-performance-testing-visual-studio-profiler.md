<properties 
    pageTitle="Oprettelse af profiler en skybaseret tjeneste lokalt i Beregn emulatoren | Microsoft Azure" 
    services="cloud-services"
    description="Undersøg problemer med ydeevnen i skytjenester med Visual Studio-profiler" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Test af ydeevnen for en skybaseret tjeneste lokalt i Azure Beregn emulatoren ved hjælp af Visual Studio-Profiler

En række forskellige værktøjer og teknikker, der er tilgængelige til test af ydeevnen for skytjenester.
Når du publicerer en skybaseret tjeneste til Azure, kan du få Visual Studio indsamle status data og derefter analysere dem lokalt, som beskrevet i [profiler et Azure-program][1].
Du kan også bruge diagnosticering til at registrere forskellige tællere i ydeevne, som beskrevet i [ved hjælp af tællere i ydeevne i Azure][2].
Du kan også vil profil dit program lokalt på Beregn emulatoren før du anvender den i skyen.

Denne artikel omhandler metoden CPU udvalg af oprettelse af profiler, som kan udføres lokalt på emulatoren. CPU udvalg er en metode til oprettelse af profiler, der ikke er meget forstyrrende. Med udpegede udvalg intervaller tager profiler et øjebliksbillede af kaldestak. Data, der indsamles over en periode, og vist i en rapport. Denne metode til oprettelse af profiler tendens til at angive, hvor i en beregningsmæssigt intensivt program mest muligt ud af arbejdet, der CPU der udføres.  Det giver dig mulighed for at fokusere på "varmt stien" hvor dit program er spilde tid med mest.



## <a name="1-configure-visual-studio-for-profiling"></a>1: konfigurere Visual Studio til oprettelse af profiler

Først, der er et par Visual Studio konfigurationsindstillinger, der kan være nyttigt, når profiler. Hvis du vil give mening status rapporter, skal du symboler (.pdb filer) for dit program og også symboler for systemet biblioteker. Du skal kontrollere, at du refererer til de tilgængelige symbol-servere. Vælg **Indstillinger for**at gøre dette, i menuen **Funktioner** i Visual Studio, og derefter vælge **fejlfinding**, derefter **symboler**. Sørg for, at Microsoft Symbol servere står anført under **placering af Symbol (.pdb)**.  Du kan også referere til http://referencesource.microsoft.com/symbols, som kan have ekstra symbolfiler.

![For Symbolindstillinger][4]

Hvis du vil, kan du forenkle de rapporter, som profiler genererer ved at angive lige mit kode. Med blot mine koden er aktiveret, er funktionen opkald stakke forenklet så opkald helt interne for biblioteker og .NET Framework er skjult fra rapporterne. Vælg **Indstillinger**i menuen **Funktioner** . Derefter Udvid noden **Ydeevne værktøjer** , og vælg **Generelt**. Markér afkrydsningsfeltet **Aktivér kun mine**koden for profiler rapporter.

![Kun mine kode indstillinger][17]

Du kan bruge disse instruktioner, med et eksisterende projekt eller med et nyt projekt.  Hvis du opretter et nyt projekt for at prøve den teknik, der er beskrevet nedenfor, vælger et projekt, C# **Azure skybaseret tjeneste** , og vælg en **Web rolle** og en **Kollega rolle**.

![Azure skybaseret tjeneste projektroller][5]

For eksempel tilføje formål, kode til dit projekt, der tager en masse tid, og der demonstreres nogle indlysende ydelsesproblemet. For eksempel føje følgende kode til en kollega rolle projekt:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Ringe til denne kode fra metoden RunAsync rollen arbejder RoleEntryPoint afledt klasse. (Ignorer advarslen om metoden kører synkront).

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Oprette og køre din skybaseret tjeneste lokalt uden fejlfinding (Ctrl + F5), med løsningskonfigurationen indstillet til **udgivelse**. Dette sikrer, at alle filer og mapper er oprettet for at køre programmet lokalt og sikrer, at alle emulatorer er startet. Start beregne Emulator Brugergrænsefladen fra proceslinjen for at bekræfte, at din kollega rolle kører.

## <a name="2-attach-to-a-process"></a>2: vedhæfte til en proces

I stedet for oprettelse af profiler programmet ved at starte fra Visual Studio 2010 IDE, skal du knytte profiler til en proces, der kører. 

Vælg **Profiler** , og **Vedhæft/Fjern**for at vedhæfte profiler til en proces, i menuen **analysér** .

![Vedhæfte indstillingen for profilen][6]

Find WaWorkerHost.exe processen for en kollega rolle.

![WaWorkerHost proces][7]

Hvis projektmappen er på et netværksdrev, profiler bliver bedt om at angive en anden placering for at gemme rapporterne status.

 Du kan også vedhæfte til en web rolle ved at knytte til WaIISHost.exe.
Hvis der er flere arbejdsprocesser rolle i dit program, skal du bruge proces-id til at skelne dem. Du kan forespørge på proces-id fra et program ved at få adgang til objektet. Eksempelvis hvis du tilføjer denne kode til metoden Kør i klassen RoleEntryPoint udledt i en rolle, kan du se på log in beregne Emulator Brugergrænsefladen til at vide, hvilke ting for at oprette forbindelse til.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Start beregne Emulator Brugergrænsefladen for at se loggen.

![Starte Beregn emulatoren brugergrænseflade][8]

Åbn vinduet arbejder rolle log console i Brugergrænsefladen beregne Emulator ved at klikke på console vinduets titellinje. Du kan se proces-ID'ET log.

![Vis proces-ID][9]

Et du har vedhæftet, udføre trinnene i Brugergrænsefladen i dit program (hvis det er nødvendigt) for at genskabe dette scenario.

Når du vil stoppe profiler, skal du vælge linket **Stoppe profiler** .

![Stoppe profiler indstilling][10]

## <a name="3-view-performance-reports"></a>3: få vist rapporter over ydeevne

Rapporten ydeevnen for dit program vises.

På dette tidspunkt i profiler holder op med at afholde, gemmer data i en .vsp fil og viser en rapport, der viser en analyse af disse data.

![Profiler rapport][11]


Hvis du får vist String.wstrcpy i varmt stien, klik på kun mine kode til at ændre visningen for at få vist kun brugerkode.  Hvis du får vist String.Concat, kan du prøve at trykke på knappen Vis al kode.

Du bør se sammenkædning metode og String.Concat at optage en stor del af kørselstid.

![Analyse af rapport][12]

Hvis du har tilføjet streng sammenkædning koden i denne artikel, skal du se en advarsel i opgavelisten i dette. Du kan også få vist en advarsel, der er meget lang Spildopsamling, som er på grund af antallet strenge, som er oprettet og fjernet.

![Ydeevnen advarsler][14]

## <a name="4-make-changes-and-compare-performance"></a>4: foretage ændringer og sammenligne ydeevne

Du kan også sammenligne ydeevnen før og efter en ændring af koden.  Stoppe processen til igangværende og redigere koden for at erstatte handlingen streng sammenkædning med brug af StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Gøre en anden ydeevne Kør, og derefter sammenligne ydeevnen. I Stifinder ydeevne hvis kørt i den samme session, kan du kun vælge begge rapporter, åbne genvejsmenuen og vælge **Sammenligne ydeevnen rapporter**. Hvis du vil sammenligne med en Kør i en anden ydeevne session, skal du åbne menuen **analysér** og vælg **Sammenligne ydeevnen rapporter**. Angive begge filer i den dialogboks, der vises.

![Sammenligne ydeevnen rapporter indstilling][15]

Rapporterne, der fremhæver forskelle mellem de to kører.

![Af Sammenligningsrapport][16]

Tillykke! Du har kørt i gang med profiler.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

- Kontrollér, at du profiler et Release build og starte uden fejlfinding.

- Hvis indstillingen Vedhæft/Afbryd ikke er aktiveret i menuen Profiler, kan du køre guiden ydeevne.

- Brug beregne Emulator Brugergrænsefladen til at få vist status for dit program. 

- Hvis du har problemer med at starte programmer i emulatoren eller vedhæfte profiler, lukke ned Beregn emulatoren og genstarte den. Hvis det ikke løser problemet, kan du prøve at genstarte. Dette problem kan opstå, hvis du bruger emulatoren beregne suspendere og fjerne kører installationer.

- Hvis du har brugt nogen af kommandoerne status fra kommandolinjen, især de globale indstillinger, Sørg for, at VSPerfClrEnv /globaloff er blevet kaldt og, VsPerfMon.exe er blevet lukket.

- Hvis når indsamle, du får vist meddelelsen "PRF0025: ingen dataene er indsamlet," Kontrollér, at du har knyttet til processen har CPU aktivitet. Programmer, der ikke udføre en hvilken som helst computational arbejde kan ikke give stikprøver, hvor data.  Det er også muligt, at processen afsluttet før der blev foretaget et udvalg. Kontrollér, at Kør metoden til en rolle, som du profiler ikke afslutter.

## <a name="next-steps"></a>Næste trin

Instrumentering Azure binære filer i emulatoren understøttes ikke i Visual Studio-profiler, men hvis du vil teste hukommelsesallokering, kan du vælge indstillingen, når profiler. Du kan også vælge på dokumentsammenfald oprettelse af profiler, der hjælper dig med at afgøre, om tråde er spilde tid med konkurrere for låse eller niveau interaktion oprettelse af profiler, som hjælper med at spore problemer med ydeevnen ved interaktion mellem et program, hyppigst mellem data niveau og en kollega rolle.  Du kan se de databaseforespørgsler, som din app genererer og bruge status dataene til at forbedre din brug af databasen. Finde oplysninger om oprettelse af niveau interaktion profiler, kan du se blogindlægget [Gennemgang: Brug af lag interaktion Profiler i Visual Studio Team System 2010][3].



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 