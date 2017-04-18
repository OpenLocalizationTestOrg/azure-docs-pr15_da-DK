<properties
   pageTitle="Konfiguration af diagnosticering til Azure Cloud Services og virtuelle maskiner | Microsoft Azure"
   description="Beskriver, hvordan du konfigurerer diagnosticeringsoplysninger til fejlfinding Azure cloude tjenester og virtuelle maskiner (VM'er) i Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Konfiguration af diagnosticering til Azure Cloud Services og virtuelle computere

Når du har brug at foretage fejlfinding af en Azure skybaseret tjeneste eller Azure virtuelt, kan du konfigurere Azure diagnosticering nemmere ved hjælp af Visual Studio. Azure diagnosticering registrerer systemdata og logføring data om virtuelle maskiner og virtuelt forekomster, der kører skybaseret tjeneste og overfører disse data til en lagerplads konto efter eget valg. Se [aktivere diagnosticering logføring for webapps i Azure App Service](./app-service-web/web-sites-enable-diagnostic-log.md) kan finde flere oplysninger om logføring i Azure diagnosticering.

Dette emne viser, hvordan du aktivere og konfigurere Azure diagnosticering i Visual Studio, både før og efter installationen, og i Azure virtuelle computere. Også viser, hvordan du kan vælge typerne af diagnosticeringsoplysninger til at indsamle og hvordan du får vist oplysninger, når den er indsamlet.

Du kan konfigurere Azure diagnosticering på følgende måder:

- Du kan ændre indstillinger for søgekonfiguration diagnosticering via dialogboksen **Konfiguration af diagnosticering** i Visual Studio. Indstillingerne gemmes i en fil kaldet diagnostics.wadcfgx (diagnostics.wadcfg i Azure SDK 2.4 eller tidligere). Du kan også ændre konfigurationsfilen direkte. Hvis du opdaterer filen manuelt, ændringerne i konfigurationen træder i kraft næste gang du installerer skyen service til Azure eller køre tjenesten i emulatoren.

- Brug **Skyen Explorer** eller **Server Explorer** i Visual Studio til at ændre diagnosticeringsindstillingerne for en igangværende skybaseret tjeneste eller et virtuelt.

## <a name="azure-26-diagnostics-changes"></a>Azure 2.6 diagnosticering ændringer

For Azure SDK 2.6 projekter i Visual Studio, der blev foretaget følgende ændringer. (Disse ændringer gælder også for senere versioner af Azure SDK.)

- Lokale emulatoren understøtter nu diagnosticering. Dette betyder, at du kan samle diagnosticering data og sikre, at dit program er oprette på rette spor, mens du udvikling og tester i Visual Studio. Forbindelsesstrengen `UseDevelopmentStorage=true` gør det muligt for diagnosticering dataindsamling, mens du kører projektet skybaseret tjeneste i Visual Studio ved hjælp af emulatoren Azure-lager. Alle diagnosticering data indsamles i kontoen (udvikling Storage) lagerplads.

- Diagnosticering lagerplads konto forbindelsesstrengen (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) er gemt igen i service-konfigurationsfil (.cscfg). I Azure SDK 2,5 blev diagnosticering lagerplads kontoen angivet i filen diagnostics.wadcfgx.

Der er nogle vigtige forskelle mellem hvordan forbindelsesstrengen lykkedes i Azure SDK 2.4 og tidligere, og hvordan det fungerer i Azure SDK 2.6 og nyere.

- I Azure SDK 2.4 og tidligere blev forbindelsesstrengen brugt som et runtime af diagnosticering plug-in'en til at få kontooplysninger lagerplads til at overføre logge til diagnosticering.

- I Azure SDK 2.6 og nyere bruges forbindelsesstrengen diagnosticering i Visual Studio til at konfigurere filtypenavnet diagnosticering med passende lager kontooplysninger under udgivelsen. Forbindelsesstrengen kan du angive forskellige lagerplads konti til anden tjeneste konfigurationer, Visual Studio vil bruge, når du udgiver. Da plug-in'en til diagnosticering er ikke længere tilgængelig (efter Azure SDK 2,5), kan filen .cscfg alene kan ikke aktivere filtypenavnet diagnosticering. Du skal aktivere udvidelsen separat via funktioner som Visual Studio eller PowerShell.

- For at forenkle processen med at konfigurere filtypenavnet diagnosticering med PowerShell indeholder pakke output fra Visual Studio også offentlige konfigurationen XML for filtypenavnet diagnosticering for hver rolle. Visual Studio anvender forbindelsesstreng diagnosticering til at udfylde kontooplysninger lagerplads findes i den offentlige konfiguration. Offentlige config-filer er oprettet i mappen filtypenavne og følge mønsteret PaaSDiagnostics. &lt;RoleName >. PubConfig.xml. En hvilken som helst PowerShell baseret installationer kan bruge denne mønster tilknytte hver konfiguration til en rolle.

- Forbindelsesstrengen i filen .cscfg bruges også af [Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) til at få adgang til diagnosticering dataene, så den kan vises på fanen **overvågnings** . Forbindelsesstrengen skal bruges til at konfigurere tjenesten for at få vist detaljeret overvågningsdata på portalen.

## <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Overføre projekter til Azure SDK 2.6 og nyere

Når du overfører fra Azure SDK 2,5 til Azure SDK 2.6 eller nyere, hvis du har en diagnosticering lagerplads konto, der er angivet i filen .wadcfgx, derefter forbliver det der. For at kunne udnytte mulighed for at bruge forskellige lagerplads konti til forskellige lagerplads konfigurationer, har du manuelt føje forbindelsesstrengen til projektet. Hvis du flytter et projekt fra Azure SDK 2.4 eller tidligere til Azure SDK 2.6, bevares diagnosticering forbindelsesstrenge. Vær opmærksom på ændringer i hvordan strenge er behandlet i Azure SDK 2.6 som angivet i forrige afsnit.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hvordan Visual Studio afgør kontoen diagnosticering lagerplads

- Hvis en diagnosticering forbindelsesstreng er angivet i filen .cscfg, bruges Visual Studio til at konfigurere filtypenavnet diagnosticering, når du udgiver, og når der genereres offentlige konfiguration XML-filer under emballagen.

- Hvis strengen ikke diagnosticering forbindelse er angivet i filen .cscfg, derefter går Visual Studio tilbage til ved hjælp af kontoen lagerplads, der er angivet i filen .wadcfgx til at konfigurere filtypenavnet diagnosticering, når publicere og genereres offentlige konfiguration XML-filer, når emballagen.

- Diagnosticering forbindelsesstrengen i filen .cscfg tilsidesætter kontoen lagerplads i filen .wadcfgx. Hvis en diagnosticering forbindelsesstreng er angivet i filen .cscfg, derefter Visual Studio bruger, og ignorerer lagerplads kontoen i .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Hvad betyder "Opdater udvikling lagerplads forbindelse strengene..." afkrydsningsfeltet gøre?

Afkrydsningsfeltet for **opdatering udvikling lagerplads forbindelsesstrenge til diagnosticering og cachelagring med Microsoft Azure lagerplads kontolegitimationsoplysninger, når du udgiver til Microsoft Azure** giver dig en nem måde at opdatere en hvilken som helst udvikling lagerplads konto forbindelsesstrenge med den angivne under udgivelsen Azure-lager-konto.

Antag f.eks., du markerer dette afkrydsningsfelt og diagnosticering forbindelsesstrengen angiver `UseDevelopmentStorage=true`. Når du publicerer projektet til Azure, opdateres Visual Studio automatisk diagnosticering forbindelsesstrengen med kontoen lagerplads du har angivet i guiden Udgiv. Men hvis en reelle lagerplads konto er angivet som forbindelsesstrengen diagnosticering, så kontoen bruges i stedet.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnosticering funktionalitet forskellene mellem Azure SDK 2.4 og tidligere og Azure SDK 2,5 og nyere

Hvis du opgraderer projektet fra Azure SDK 2.4 til Azure SDK 2,5 eller nyere, skal du huske indeholde følgende diagnosticering funktionalitet forskelle.

- **Konfiguration af API'er frarådes** – programmeringsmæssig konfigurationen af diagnosticering er tilgængelig i Azure SDK 2.4 eller tidligere versioner, men frarådes i Azure SDK 2,5 og nyere. Hvis konfigurationen af diagnosticering der aktuelt er angivet i kode, skal du konfigurere disse indstillinger fra bunden i overførte projektet for at diagnosticering for at fortsætte med at arbejde. Konfigurationsfil diagnosticering til Azure SDK 2.4 er diagnostics.wadcfg og diagnostics.wadcfgx for Azure SDK 2,5 og nyere.

- **Diagnosticering til skyen tjenesteprogrammer kan kun konfigureres på niveauet rolle ikke på niveauet for forekomst.**

- **Hver gang du installerer din app konfigurationen af diagnosticering er opdateret** – dette kan medføre problemer med fungerer ensartet, hvis du ændrer konfigurationen af diagnosticering fra Server Explorer og geninstaller derefter din app.

- **I Azure SDK 2,5 og nyere, nedbrud gemmer er konfigureret i konfigurationsfil diagnosticering ikke i kode** – Hvis du har Crashdumps, der er konfigureret i kode, har du manuelt skifte konfigurationen fra kode til konfigurationsfil, da Crashdumps ikke overføres under overførslen til Azure SDK 2.6.

## <a name="enable-diagnostics-in-cloud-service-projects-before-deploying-them"></a>Aktivere diagnosticering i skyen service projekter før du anvender dem

I Visual Studio, kan du vælge at indsamle diagnosticering data til roller, der kører i Azure, når du kører tjenesten i emulatoren før du anvender den. Alle ændringer til diagnosticering indstillinger i Visual Studio gemmes i filen diagnostics.wadcfgx konfiguration. Disse konfigurationsindstillinger angiver kontoen lagerplads, hvor diagnosticering data er gemt, når du installerer skybaseret tjeneste.

### <a name="to-enable-diagnostics-in-visual-studio-before-deployment"></a>Aktivere diagnosticering i Visual Studio før installation

1. Vælg **Egenskaber**i genvejsmenuen for den rolle, der interesserer dig, og vælg derefter fanen **konfiguration** i **den rolle egenskabsvinduet** .

1. Sørg for, at afkrydsningsfeltet **Aktivér diagnosticering** er markeret i afsnittet **diagnosticering** .

    ![Få adgang til indstillingen Aktivér diagnosticering](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Klik på ellipsen (…) for at angive kontoen lagerplads, hvor du vil diagnosticering dataene skal gemmes. Kontoen lagerplads, du vælger, bliver den placering, hvor diagnosticering data er gemt.

    ![Angiv lagerplads-konto, der skal bruges](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. Angiv om du vil oprette forbindelse via Azure lagerplads emulatoren, et Azure-abonnement eller manuelt angivet legitimationsoplysninger i dialogboksen **Opret lagerplads forbindelsesstreng** .

    ![Dialogboksen lagerplads konto](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Hvis du vælger Microsoft Azure lagerplads Emulator indstillingen forbindelsesstrengen er angivet til UseDevelopmentStorage = sand.

  - Hvis du vælger den indstilling for dit abonnement, kan du vælge det Azure abonnement, du vil bruge og kontonavn. Du kan vælge knappen Administrer konti til at administrere dine abonnementer på Azure.

  - Hvis du vælger indstillingen manuelt angivne legitimationsoplysninger, bliver du bedt om at angive navn og tast til den Azure konto, du vil bruge.

1. Vælg knappen **Konfigurer** til at få vist dialogboksen **diagnosticering konfiguration** . Hver fane (med undtagelse af **generelle** og **Log kataloger**) repræsenterer en diagnosticering datakilde, kan du indsamle. Standard under fanen **Generelt**, giver dig de følgende indstillinger for diagnosticering dataindsamling: **kun fejl**, **alle oplysninger**og **brugerdefineret plan**. Standardindstillingen, **kun fejl**, tager den mindste mængde lagerplads, fordi det ikke overføre advarsler eller sporing af meddelelser. Indstillingen alle oplysninger overfører de fleste oplysninger og er derfor indstillingen mest dyr med hensyn til lagerplads.

    ![Aktivere Azure diagnosticerings- og konfigurationstjenester](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. I dette eksempel skal du vælge indstillingen **brugerdefineret plan** , så du kan tilpasse de data, der indsamles.

1. Feltet **Disk kvote i MB** angiver, hvor meget plads der skal tildeles i kontoen lagerplads til diagnosticering data. Du kan ændre standardværdien, hvis du vil have.

1. Vælg på hver fane diagnosticering data, du vil indsamle dens **aktivere overførsel af <log type> ** afkrydsningsfeltet. Hvis du vil indsamle programmet logfiler, Markér afkrydsningsfeltet **Aktiver overførsel af programmet logfiler** under fanen **Program logføring** . Angiv også eventuelle andre oplysninger, der kræves, før hver diagnosticering datatype. I afsnittet **Konfigurer diagnosticering datakilder** senere i dette emne for oplysninger om konfiguration under hver fane.

1. Vælg knappen **OK** , når du har aktiveret samling af alle de ønskede diagnosticering-data.

1. Kør projektet Azure skybaseret tjeneste i Visual Studio på sædvanlig vis. Når du bruger dit program, gemmes den logoplysninger, som du har aktiveret til kontoen Azure lagerplads du har angivet.

## <a name="enable-diagnostics-in-azure-virtual-machines"></a>Aktivere diagnosticering på Azure virtuelle computere

I Visual Studio, kan du vælge at indsamle diagnosticering data til Azure virtuelle computere.

### <a name="to-enable-diagnostics-in-azure-virtual-machines"></a>Aktivere diagnosticering på Azure virtuelle computere

1. Vælg noden Azure **Server Explorer**, og derefter oprette forbindelse til abonnementet Azure, hvis du ikke allerede har forbindelse.

1. Udvid noden **virtuelle computere** . Du kan oprette en ny virtuel maskine, eller Vælg en, der allerede findes.

1. I genvejsmenuen for den virtuelle maskine, der interesserer dig, skal du vælge **Konfigurer**. Dette viser dialogboksen virtuelt konfiguration.

    ![Konfigurere en Azure virtuelt](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Hvis den ikke allerede er installeret, kan du tilføje filtypenavnet Microsoft overvågning Agent diagnosticering. Dette lokalnummer, kan du indsamle diagnosticering data til Azure virtuelt. Vælg Vælg en rullemenuen tilgængelige lokalnummer, og vælg derefter Microsoft overvågning Agent diagnosticering på listen installeret filtypenavne.

    ![Installere et Azure virtuelt filtypenavn](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] Andre diagnosticering udvidelser er tilgængelige for din virtuelle computere. Yderligere oplysninger finder du i Azure VM filtypenavne og funktioner.

1. Klik på **Tilføj** for at tilføje filtypenavnet og få vist dialogboksen dens **diagnosticering konfiguration** .

1. Klik på **Konfigurer** for at angive en lagerplads konto og derefter vælge knappen **OK** .

    Hver fane (med undtagelse af **generelle** og **Log kataloger**) repræsenterer en diagnosticering datakilde, kan du indsamle.

    ![Aktivere Azure diagnosticerings- og konfigurationstjenester](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    Standard under fanen **Generelt**, giver dig de følgende indstillinger for diagnosticering dataindsamling: **kun fejl**, **alle oplysninger**og **brugerdefineret plan**. Standardindstillingen, **kun fejl**, tager den mindste mængde lagerplads, fordi det ikke overføre advarsler eller sporing af meddelelser. Indstillingen **alle oplysninger** overfører de fleste oplysninger og er derfor indstillingen mest dyr med hensyn til lagerplads.

1. I dette eksempel skal du vælge indstillingen **brugerdefineret plan** , så du kan tilpasse de data, der indsamles.

1. Feltet **Disk kvote i MB** angiver, hvor meget plads der skal tildeles i kontoen lagerplads til diagnosticering data. Du kan ændre standardværdien, hvis du vil have.

1. Vælg på hver fane diagnosticering data, du vil indsamle dens **aktivere overførsel af <log type> ** afkrydsningsfeltet.

    Hvis du vil indsamle programmet logfiler, Markér afkrydsningsfeltet **Aktiver overførsel af programmet logfiler** under fanen **Program logføring** . Angiv også eventuelle andre oplysninger, der kræves, før hver diagnosticering datatype. I afsnittet **Konfigurer diagnosticering datakilder** senere i dette emne for oplysninger om konfiguration under hver fane.

1. Vælg knappen **OK** , når du har aktiveret samling af alle de ønskede diagnosticering-data.

1. Gemme det opdaterede projekt.

    Du får vist en meddelelse i vinduet **Microsoft Azure aktivitetslog** , den virtuelle maskine er blevet opdateret.

## <a name="configure-diagnostics-data-sources"></a>Konfigurere diagnosticering datakilder

Når du aktiverer diagnosticering dataindsamling, kan du vælge præcis hvilke datakilder, du vil indsamle og hvilke oplysninger der indsamles. Følgende er en liste over faner i dialogboksen **konfiguration af diagnosticering** og betyder, hvad hver indstilling for søgekonfiguration.

### <a name="application-logs"></a>Programmet logfiler

**Programmet logfiler** indeholder diagnosticeringsoplysninger produceret af et webprogram. Hvis du vil registrere programmet logfiler, Markér afkrydsningsfeltet **Aktiver overførsel af logfiler over programmet** . Du kan forøger eller formindsker antallet af minutter, når programmet loggene overføres til kontoen lager ved at ændre værdien **Viderestil punktum (min)** . Du kan også ændre mængden oplysninger registreres i loggen ved at angive værdien Log niveau. For eksempel kan du vælge **Vis** for at få flere oplysninger eller vælge **kritisk** til at registrere kun alvorlige fejl. Hvis du har en bestemt diagnosticering provider, udsender programmet logge, kan du hente dem ved at tilføje udbyderens GUID til feltet **Udbyder-GUID** .

  ![Programmet logfiler](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Se [aktivere diagnosticering logføring for webapps i Azure App Service](./app-service-web/web-sites-enable-diagnostic-log.md) kan finde flere oplysninger om logfiler over programmet.

### <a name="windows-event-logs"></a>Windows-hændelseslogge

Hvis du vil registrere Windows-hændelseslogge, skal du markere afkrydsningsfeltet **Aktiver overførsel af Windows hændelseslogfiler** . Du kan forøge eller reducere antallet af minutter, når hændelseslogfiler overføres til kontoen lagerplads ved at ændre værdien **Viderestil punktum (min)** . Markér afkrydsningsfelterne for typerne af hændelser, du vil registrere.

  ![Hændelseslogfiler](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Hvis du bruger Azure SDK 2.6 eller nyere og vil angive en brugerdefineret datakilde, kan du angive det i den **<Data source name>** tekst og klik derefter på knappen **Tilføj** ud for den. Datakilden er føjet til filen diagnostics.cfcfg.

Hvis du bruger Azure SDK 2,5 og vil angive en brugerdefineret datakilde, kan du tilføje den til den `WindowsEventLog` del af diagnostics.wadcfgx fil, sådan som i følgende eksempel.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Tællere i ydeevne

Ydeevnen tæller oplysninger kan hjælpe dig med at finde systemflaskehalse og finjustere system- og ydeevne. Finde flere oplysninger i [oprette og bruge tællere i ydeevne i et Azure-program](https://msdn.microsoft.com/library/azure/hh411542.aspx) . Hvis du vil registrere tællere i ydeevne, skal du markere afkrydsningsfeltet **Aktiver overførsel af tællere i ydeevne** . Du kan forøge eller reducere antallet af minutter, når hændelseslogfiler overføres til kontoen lagerplads ved at ændre værdien **Viderestil punktum (min)** . Markér afkrydsningsfelterne for de tællere i ydeevne, du vil registrere.

  ![Tællere i ydeevne](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Angiv den ved hjælp af foreslåede syntaks for at spore en ydeevne tæller, som ikke er angivet, og vælg derefter knappen **Tilføj** . Operativsystemet på den virtuelle maskine bestemmer, hvilke tællere i ydeevne, du kan følge. Du kan finde flere oplysninger om syntaksen se [angive en tællersti](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Infrastruktur logfiler

Hvis du vil kopiere infrastruktur logfiler, der indeholder oplysninger om Azure diagnosticering infrastrukturen, modulet Remote Access og modulet RemoteForwarder, Markér afkrydsningsfeltet **Aktiver overførsel af infrastruktur logfiler** . Du kan forøger eller formindsker antallet af minutter, når loggene overføres til kontoen lager ved at ændre værdien Viderestil punktum (min).

  ![Diagnosticering infrastruktur logfiler](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Du kan få flere oplysninger i [Indsaml logføring Data ved hjælp af Azure diagnosticering](https://msdn.microsoft.com/library/azure/gg433048.aspx) .

### <a name="log-directories"></a>Log kataloger

Hvis du vil kopiere log kataloger, der indeholder data, der indsamles fra log kataloger Internet Information Services (IIS) anmodninger om mislykkede anmodninger eller mapper, som du vælger, markere afkrydsningsfeltet **Aktivér overførsel af Log kataloger** . Du kan forøger eller formindsker antallet af minutter, når loggene overføres til kontoen lager ved at ændre værdien **Viderestil punktum (min)** .

Du kan markere felterne for de logfiler, du vil indsamle som **IIS-logfiler** og **Mislykkedes anmode om** logfiler. Objektbeholder standardnavne lagerplads der leveres, men du kan ændre navne, hvis du vil have.

Du kan også hente logfiler fra en vilkårlig mappe. Kun angive stien i afsnittet **logfil fra absolut Directory** , og vælg derefter knappen **Tilføj adresseliste** . Loggene kan hentes til de angivne beholdere.

  ![Log kataloger](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW logfiler

Hvis du bruger [Begivenhed sporing til Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) og vil hente ETW logfiler, Markér afkrydsningsfeltet **Aktiver overførsel af ETW logfiler** . Du kan forøger eller formindsker antallet af minutter, når loggene overføres til kontoen lager ved at ændre værdien **Viderestil punktum (min)** .

Hændelserne, der er hentet fra begivenhed kilder og begivenhed manifester, du angiver. Angiv et navn i sektionen **Begivenhed kilder** for at angive en begivenhed kilde, og vælg derefter knappen **Tilføj begivenhed kilde** . Du kan også angive en begivenhed manifest i afsnittet **Begivenhed manifester** og derefter vælge knappen **Tilføj begivenhed manifestet** .

  ![ETW logfiler](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  ETW, som understøttes i ASP.NET gennem klasser i [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) navneområde. Navneområdet Microsoft.WindowsAzure.Diagnostics, som nedarver fra og udvider standard [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) klasser, gør det muligt for brug af [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) som en logføring ramme i Azure-miljøet. Se [tage kontrol af logføring og sporing af meddelelser i Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) og [Aktivere diagnosticering i Azure Cloud Services og virtuelle computere](./cloud-services/cloud-services-dotnet-diagnostics.md)kan finde flere oplysninger.

### <a name="crash-dumps"></a>Crashdumps

Hvis du vil registrere oplysninger om, hvornår en forekomst af rolle går ned, skal du vælge afkrydsningsfeltet **Aktiver overførsel af ned gemmer** . (Fordi ASP.NET håndterer de fleste undtagelser, bruges normalt kun til arbejder roller.) Du kan forøge eller formindske procentdelen af lagerplads, der er afsat til nedbrud gemmer ved at ændre værdien **Directory kvote (%)** . Du kan ændre objektbeholderen til lagring af hvor nedbrud gemmer gemmes, og du kan vælge, om du vil kopiere en **fuld** eller **Mini** lagring.

De processer, der spores i øjeblikket er angivet. Markér afkrydsningsfelterne for de processer, som du vil kopiere. Skriv navnet, processen for at tilføje en anden proces til listen, og vælg derefter knappen **Tilføj proces** .

  ![Crashdumps](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Se [tage kontrol af logføring og sporing af meddelelser i Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) og [Microsoft Azure diagnosticering del 4: brugerdefineret logføring komponenter og Azure diagnosticering 1.3 ændringer](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/) kan finde flere oplysninger.

## <a name="view-the-diagnostics-data"></a>Få vist dataene diagnosticering

Når du har indsamlet diagnosticering dataene til en skybaseret tjeneste eller en virtuel maskine, kan du se den.

### <a name="to-view-cloud-service-diagnostics-data"></a>Sådan får du vist data fra skyen webtjeneste diagnosticering

1. Installere din skybaseret tjeneste som normalt, og derefter køre den.

1. Du kan få vist diagnosticering dataene i en rapport, der genererer Visual Studio eller tabeller i kontoen lagerplads. Åbn **Skyen Explorer** eller **Server Explorer**for at få vist dataene i en rapport, åbne genvejsmenuen for node for den rolle, der interesserer dig, og vælg derefter **Vis diagnosticeringsdata**.

    ![Vis diagnosticering Data](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    En rapport, der viser de tilgængelige data vises.

    ![Microsoft Azure diagnosticeringsrapport i Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Hvis de nyeste data ikke vises, skal du muligvis vente på Viderestil punktum forløbe.

    Vælg linket **Opdater** for at opdatere dataene med det samme, eller Vælg et interval i rullemenuen **Automatisk opdatering** at få dataene opdateres automatisk. Vælg knappen **Eksporter til CSV-** for at oprette en kommasepareret fil, du kan åbne i et regneark for at eksportere dataene fejl.

    Åbn lagerplads-konto, der er knyttet til installationen i **Skyen Explorer** eller **Server Explorer**.

1. Åbn diagnosticering tabellerne i tabel-Fremviser, og gennemgå de data, du har indsamlet. Du kan åbne en blob objektbeholder for IIS-logfiler og brugerdefinerede logfiler. Du kan finde den tabel eller blob beholder, der indeholder de data, der interesserer dig ved at gennemgå den følgende tabel. Ud over dataene til logfilen, posterne i tabellen indeholder EventTickCount, DeploymentId, rolle og RoleInstance for at hjælpe dig med at identificere, hvilke virtuelt og rolle genereres dataene og hvornår. 

  	|Diagnosticering data|Beskrivelse|Placering|
  	|---|---|---|
  	|Programmet logfiler|Logfiler, som din kode genererer ved at ringe til metoder til klassen System.Diagnostics.Trace.|WADLogsTable|
  	|Hændelseslogfiler|Disse data er fra Windows-hændelseslogge på virtuelle maskiner. Windows gemmer oplysninger i disse logfiler, men programmer og tjenester også bruge dem til at rapportere fejl eller logoplysninger.|WADWindowsEventLogsTable|
  	|Tællere i ydeevne|Du kan indsamle data på en hvilken som helst ydeevne tæller, der er tilgængelige på den virtuelle maskine. Operativsystemet indeholder tællere i ydeevne, som omfatter mange statistik som hukommelse brugen og processor tid.|WADPerformanceCountersTable|
  	|Infrastruktur logfiler|Disse logfiler genereres fra diagnosticering infrastrukturen sig selv.|WADDiagnosticInfrastructureLogsTable|
  	|IIS-logfilerne|Disse logfiler post webanmodninger. Hvis skybaseret tjeneste bliver trafik lang, kan disse logfiler kan være helt længerevarende, så du skal samle og gemme disse data kun, når det er nødvendigt.|Du kan finde mislykkedes anmodning logfiler i beholderen blob under wad-iis-failedreqlogs under en sti til den installation, rolle og forekomst. Du kan finde fuldført logfiler under wad-iis-logfiles. Der oprettes poster for hver fil i tabellen WADDirectories.|
  	|Crashdumps|Disse oplysninger indeholder binære billeder af dine skytjeneste proces (normalt en kollega rolle).|wad-crush-gemmer blob objektbeholder|
  	|Brugerdefineret logfiler|Logge af data, som du foruddefinerede.|Du kan angive i kode placeringen af brugerdefinerede logfiler i kontoen lagerplads. For eksempel kan du angive en brugerdefineret blob objektbeholder.|

1. Hvis data af enhver type afkortes, kan du prøve at gøre bufferen for de pågældende data type eller forkorte intervallet mellem overførsler af data fra den virtuelle maskine til kontoen lagerplads.

1. (valgfrit) Rydde data fra kontoen lager og til at reducere samlede udgifter til storage.

1. Når du gør en komplet installation, opdateres diagnostics.cscfg filen (.wadcfgx til Azure SDK 2.5) i Azure, og din skytjeneste opfanger eventuelle ændringer af konfigurationen af diagnosticering. Hvis du, i stedet opdaterer en eksisterende installation, opdateres filen .cscfg ikke i Azure. Du kan stadig ændre indstillinger for diagnosticering dog ved at følge trinnene i næste afsnit. Du kan finde flere oplysninger om at udføre en komplet installation og opdatering af en eksisterende installation, [Publicere Azure-guiden](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Sådan får du vist virtuelt diagnosticering data

1. Vælg **Vis diagnosticering Data**i genvejsmenuen for den virtuelle maskine.

    ![Vis diagnosticering data i Azure virtuelt](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Dette åbner vinduet **diagnosticering oversigt** .

    ![Azure virtuelt diagnosticering oversigt](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  

    Hvis de nyeste data ikke vises, skal du muligvis vente på Viderestil punktum forløbe.

    Vælg linket **Opdater** for at opdatere dataene med det samme, eller Vælg et interval i rullemenuen **Automatisk opdatering** at få dataene opdateres automatisk. Vælg knappen **Eksporter til CSV-** for at oprette en kommasepareret fil, du kan åbne i et regneark for at eksportere dataene fejl.

## <a name="configure-cloud-service-diagnostics-after-deployment"></a>Konfigurere skyen service diagnosticering efter installation

Hvis du er ved at undersøge et problem med en skybaseret tjeneste, der allerede kører, kan du vælge at indsamle data, som du ikke angav før du oprindeligt har installeret rollen. I dette tilfælde kan du begynde at indsamle data ved hjælp af indstillingerne i Server Explorer. Du kan konfigurere diagnosticering for en enkelt forekomst eller alle forekomster i en rolle, afhængigt af om du åbner dialogboksen diagnosticering konfiguration i genvejsmenuen for forekomsten eller rollen. Hvis du konfigurerer noden rolle, anvende ændringerne på alle forekomster. Hvis du konfigurerer noden forekomst, anvende ændringerne på forekomsten kun.

### <a name="to-configure-diagnostics-for-a-running-cloud-service"></a>Sådan konfigureres diagnosticering for en aktiv tjeneste i skyen

1. Udvid noden **Cloud Services** i Server Explorer, og udvid derefter noder for at finde rollen eller forekomst, du vil undersøge eller begge dele.

    ![Konfiguration af diagnosticering](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. Vælg **Diagnosticering opdateringsindstillinger**i genvejsmenuen for en forekomst node eller en rolle node, og vælg derefter indstillingerne for diagnosticering, du vil indsamle.

    Finde oplysninger om konfigurationsindstillingerne, **Konfigurer diagnosticering datakilder** i dette emne. Finde oplysninger om hvordan du får vist diagnosticering dataene, kan du se **få vist diagnosticering dataene** i dette emne.

    Hvis du ændrer dataindsamling i **Server Explorer**, forbliver disse ændringer i kraft, indtil du fuldt Geninstaller skybaseret tjeneste. Hvis du bruger standard indstillinger for publicering, overskrives ændringerne ikke, da standard publicere indstilling er at opdatere den eksisterende installation i stedet for at gøre en fuld geninstallation. Gå til fanen **Avancerede indstillinger** i guiden Publicer for at sikre, at installation tid rydde indstillingerne, og fjern markeringen i afkrydsningsfeltet **Opdater installation** . Når du Geninstaller med pågældende afkrydsningsfelt, der er fjernet, indstillingerne for vende tilbage til dem fra filen .wadcfgx (eller .wadcfg) som sæt via redigeringsprogrammet egenskaber for rollen. Hvis du opdaterer din installation, bevares Azure gamle indstillingerne.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Foretage fejlfinding af problemer med tjenesten Azure skyen

Hvis du oplever problemer med din skyen service-projekter, som en rolle, der sidder fast i statussen "optaget" gentagne gange genbruger eller viser en intern serverfejl, der findes værktøjer og teknikker, du kan bruge til at diagnosticere og løse disse problemer. Specifikke eksempler på almindelige problemer og løsninger samt en oversigt over de begreber og værktøjer til diagnosticering og løsning disse fejl, i [Azure PaaS beregne diagnosticering Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Q & A

**Hvad er størrelsen på bufferen, og hvor stor skal det være?**

Kvoter begrænse på hver virtuelt forekomst, hvor meget diagnosticering data kan være gemt på det lokale filsystem. Desuden kan du angive en bufferstørrelse for hver type diagnosticering data, som er tilgængelig. Denne bufferstørrelse fungerer som en enkelt kvoten for typen data. Ved at markere nederst i dialogboksen, kan du se kvoten for overordnede og mængden hukommelse, der mangler. Hvis du angiver større buffere eller flere typer data, skal du løse kvoten overordnede. Du kan ændre kvoten for overordnede ved at ændre filen diagnostics.wadcfg/.wadcfgx konfiguration. Diagnosticerings-data er gemt på det samme filsystem som dit program data, så hvis dit program bruger en masse diskplads, må du øge kvoten overordnede diagnosticering.

**Hvad er perioden, overføre, og hvor lang tid skal det være?**

Viderestil punktum er mængden tid, standardtimeouttiden er udløbet mellem data passer. Efter hver filoverførsel periode flyttes data fra det lokale filsystem på en virtuel maskine til tabeller i kontoen lagerplads. Hvis mængden data, der indsamles overskrider kvoten før slutningen af en overførsel periode, slettes ældre data. Du måske at mindske filoverførsel perioden, hvis du er at miste data, fordi dataene overskrider størrelsen på bufferen eller kvoten overordnede.

**Hvilken tidszone er tidsstempler i?**

Tidsstemplerne er i den lokale tidszone i datacenter, der er vært skybaseret tjeneste. De følgende tre tidsstempel kolonner i logtabellerne bruges.

  - **PreciseTimeStamp** er ETW tidsstemplet for begivenheden. Det vil sige, den tid, hændelsen logføres fra klienten på computeren.

  - **TIDSSTEMPEL** er PreciseTimeStamp rundes ned til grænsen frekvens for overførsel. Dette, hvis din Overfør hyppighed er 5 minutter og hændelsen tid 00:17:12, bliver TIDSSTEMPEL 00:15:00.

  - **Tidsstempel** er tidsstemplet, hvor objektet blev oprettet i tabellen Azure.

**Hvordan administrerer jeg omkostninger, når de indsamler diagnostiske oplysninger?**

Standardindstillingerne (**logføringsniveau** angivet til **fejl** og **overføre periode** angivet til **1 minut**) er designet til at minimere omkostninger. Beregn omkostningerne, øger, hvis du indsamle flere diagnosticering data eller Formindsk filoverførsel perioden. Ikke indsamle flere data, end du ønsker, og Glem ikke at deaktivere dataindsamling, når du ikke længere har brug for. Du kan altid aktivere den igen, selv på kørselstidspunktet, som vist i det forrige afsnit.

**Hvordan indsamling mislykkedes anmodning logfiler fra IIS?**

Som standard indsamle ikke IIS mislykkedes anmodning logfiler. Du kan konfigurere IIS til at indsamle dem, hvis du redigere filen web.config for din web rolle.

**Jeg modtager ikke sporingsoplysninger fra RoleEntryPoint metoder som OnStart. Hvad er forkert?**

Metoder til RoleEntryPoint kaldes i forbindelse med WAIISHost.exe, ikke IIS. Derfor konfigurationsoplysningerne i web.config, der normalt gør det muligt at spore ikke anvende. Føje en .config-fil til projektet web rolle for at løse dette problem, og Navngiv filen, så det svarer til samlingen output, der indeholder RoleEntryPoint koden. I standard web rolle projektet, vil navnet på .config-filen være WAIISHost.exe.config. Tilføj derefter følgende linjer til denne fil:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Angive nu egenskaben **kopi til outputmappe** til **kopi altid**i vinduet **Egenskaber** .

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om logføring i Azure diagnosticering, skal du se [Aktivere diagnosticering i Azure Cloud Services og virtuelle maskiner](./cloud-services/cloud-services-dotnet-diagnostics.md) , og [Aktivér logføring for webapps i Azure App Service diagnosticering](./app-service-web/web-sites-enable-diagnostic-log.md).
