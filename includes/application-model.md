# <a name="compute"></a>Beregne

Azure gør det muligt at implementere og overvåge dit programkode, der kører i Microsoft datacenter. Når du opretter et program og køre den på Azure, kode og konfiguration sammen der kaldes en Azure hostet tjeneste. Hostet tjenester, der er nemt at administrere, skalere op og ned, omkonfigurere og opdatere med nye versioner af dit program kode. I denne artikel fokuserer på Azure hostet service programmet model.<a id="compare" name="compare"></a>

## Indholdsfortegnelse<a id="_GoBack" name="_GoBack"></a>

-   [Azure-program Model fordele][]
-   [Hostet Service Grundkoncepterne "][]
-   [Hostet Service Designovervejelser][]
-   [Designe dit program for skala][]
-   [Definitionen hostet tjenesten og konfiguration][]
-   [Tjenesten definitionsfil][]
-   [Tjenesten konfigurationsfil][]
-   [Oprette og implementere en hostet tjeneste][]
-   [Referencer][]

## <a id="benefits"> </a>Azure-program Model fordele

Når du installerer programmet som en hostet tjeneste, Azure opretter en eller flere virtuelle maskiner (VM'er), der indeholder dit program kode og starter FOS på fysiske maskiner bosiddende i en af Azure datacentre. Når klient-anmodninger til din værtsprogram indtaster datacenteret, distribuerer belastningsjustering forespørgslerne lige til FOS. Mens din programmet er placeret i Azure, bliver tre vigtige fordele:

-   **Høj tilgængelighed.** Høj tilgængelighed betyder Azure sikrer, at dit program kører som muligt og kan svare på klient-anmodninger. Hvis dit program ophører (på grund af en ikke-afviklet undtagelse, for eksempel), og derefter Azure registrerer dette, og den vil automatisk start igen dit program. Hvis maskinen dit program kører på oplevelser en slags hardwarefejl, derefter Azure også registrere dette og automatisk oprette en ny VM på en anden arbejde fysisk maskine og køre din kode derfra. Bemærk: Hvis dit program tilladelse til at hente Microsoft-serviceaftale 99.95% tilgængelig, skal du skal have mindst to VM'er, der kører din programkode. Dette giver mulighed for én VM til at behandle klient-anmodninger, mens Azure flytter din kode fra en mislykkedes VM til en ny, god VM.

-   **Skalerbarhed.** Azure kan du nemt og ændre dynamisk antallet af VM'er, der kører din programkode til at håndtere de faktiske belastning på dit program. Dette kan du justere dit program til arbejdsbelastningen, som dine kunder placere på den, mens du betaler kun for de FOS, du skal bruge, når du har brug for dem. Hvis du vil ændre antallet FOS, reagerer Azure efter minutter, der gør det muligt at ændre dynamisk antallet af VM'er, der kører som ofte efter behov.

-   **Administration.** Da Azure er en Platform som en tjeneste (PaaS) med tilbud, administrerer den infrastruktur (hardware selve, el og netværk), der kræves for at beholde disse maskiner, der kører. Azure håndterer også platformen, sikre, at et opdateret operativsystem med alle de korrekte rettelser og sikkerhedsopdateringer samt komponentopdateringer som .NET Framework og Internet Information Server. Da alle FOS kører Windows Server 2008, leverer Azure yderligere funktioner som diagnosticering overvågning, remote desktop support, firewalls og konfiguration af certifikater store. Alle disse funktioner er angivet i ingen ekstra udgifter. Når du kører dit program i Azure, er Windows Server 2008 operativsystem (OS)-licensen faktisk inkluderet. Da alle FOS kører Windows Server 2008, virker en hvilken som helst kode, der kører på Windows Server 2008 fint, når der benyttes i Azure.

## <a id="concepts"> </a>Hostet Service Grundkoncepterne "

Når programmet installeres som en hostet tjeneste i Azure, der køres som et eller flere *roller.* En *rolle* blot refererer til programfiler og konfiguration. Du kan angive en eller flere roller for dit program, hver med sit eget sæt af programfiler og konfiguration. For hver rolle i programmet, kan du angive antallet af FOS eller *rolle forekomster*, for at køre. Figuren nedenfor viser to enkle eksempler på et program, der er formateret som en hostet tjenesten ved hjælp af roller og rolle forekomster.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figur 1: En enkelt rolle med tre forekomster (FOS) kører i en Azure datacenter

![Billede af][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figur 2: To roller, hver med to forekomster (FOS), kører i en Azure datacenter

![Billede af][1]

Rolle forekomster behandle typisk Internet klient anmodninger om at angive datacentret via det, der kaldes en *input slutpunkt*. En enkelt rolle kan have 0 eller mere input slutpunkter. Hvert slutpunkt angiver en protokol (HTTP, HTTPS eller TCP) og en port. Det er normalt til at konfigurere en rolle for at få to input slutpunkter: HTTP lytter på port 80 og HTTPS lytte på port 443. I nedenstående figur vises et eksempel på to forskellige roller med forskellige input slutpunkter klient-anmodninger til dem, der fører.

![Billede af][2]

Når du opretter en hostet tjeneste i Azure, tildeles en offentligt tilgængelige IP-adresser, kunder kan bruge til at få adgang til den. Ved oprettelse af tjenesten hostet skal du også vælge et præfiks, du URL-adresse, der er knyttet til den pågældende IP-adresse. Dette præfiks skal være entydig, som du er grundlæggende at reservere *præfiks*. cloudapp.net URL-adresse, så den ingen andre kan bruge den. Klienter kommunikere med din rolle forekomster ved hjælp af URL-adressen. Som regel, du ikke vil dele eller publicere Azure *præfiks*. cloudapp.net URL-adresse. I stedet kan du købe et DNS-navn fra din DNS-registrator efter valg og konfigurere dine DNS-navn for at omdirigere klient-anmodninger til Azure URL-adressen. Se [konfigurere et brugerdefineret domænenavn i Azure][]kan finde flere oplysninger.

## <a id="considerations"> </a>Hostet Service Designovervejelser

Når du designer en program tilladelse til at køre i et skybaseret miljø, er der flere overvejelser kan tænke over som ventetid, høj tilgængelighed og skalerbarhed.

Du placerer dine programkode er en vigtig overvejelse, når du kører en hostet tjeneste i Azure. Det er normalt til at installere programmet datacentre, der er tættest på dine kunder til at reducere ventetid og få den bedste ydeevne mulige.
Du kan vælge et datacenter tættere på din virksomhed eller tættere på dine data, hvis du har nogle om straffemyndighed eller juridisk problemstillinger om dine data, og hvor den er placeret. Der er seks datacentre hele verden kan vært for din programkode. Tabellen nedenfor viser de tilgængelige placeringer:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Land/område**

</td>
<td style="width: 200px;">
**Underordnede områder**

</td>
</tr>
<tr>
<td>
USA

</td>
<td>
Syd Central & North Central

</td>
</tr>
<tr>
<td>
Europe

</td>
<td>
Nord og vest

</td>
</tr>
<tr>
<td>
Asien

</td>
<td>
Sydøst og øst

</td>
</tr>
</tbody>
</table>
Når du opretter en hostet tjeneste, vælger du et underordnet område, der angiver den placering, hvor du vil din kode for at udføre.

For at opnå høj tilgængelighed og skalerbarhed, er det alvorligt vigtigt, at dit program dataene opbevares i et centralt lager, der er tilgængeligt for flere forekomster af rollen. For at hjælpe med dette, indeholder Azure flere indstillinger for lagring af som blob, tabeller og SQL-Database. Se [Data lagerplads tilbud i Azure][] artiklen finde flere oplysninger om disse storage-teknologier. Figuren nedenfor viser, hvordan justering af belastning i Azure datacenteret distribuerer klient-anmodninger på forekomster af anden rolle som alle har adgang til lagring af samme data.

![Billede af][3]

Som regel, du vil finde din programkode og dine data i den samme datacenter, som dette giver mulighed for kort ventetid (bedre ydeevne) når din programkode får adgang til dataene. Desuden kan betaler du ikke for båndbredde når data flyttes i det samme datacenter.

## <a id="scale"> </a>Designe dit program for skala

Nogle gange kan du vil tage et enkelt program (som et enkelt websted), så den Azure som vært. Men ofte, dit program kan bestå af flere roller, der alle arbejder sammen. For eksempel i nedenstående illustration er der to forekomster af rollen websted, tre forekomster af rollen behandling og én forekomst af rollen rapport Generator. Disse roller alle arbejder sammen og koden for dem alle kan pakkes sammen og implementeres som en enkelt enhed op til Azure.

![Billede af][4]

Den primære grund til at opdele et program i forskellige roller hver kører på sit eget sæt rolle forekomster (det vil sige, FOS) er at skalere rollerne uafhængigt af hinanden. For eksempel under jul, kan mange kunder købe produkter i virksomheden, så kan du vil øge antallet af rolle forekomster, der kører din rolle websted samt antallet af rolle forekomster, der kører din ordrebehandling rolle. Du kan få en masse produkter, der returneres, når jul, så du kan stadig har brug for en masse websted forekomster, men færre behandling af forekomster. Du muligvis under resten af året, kun nogle få websteds- og behandling af forekomster. I hele alt dette over, skal du muligvis kun én rapport Generator forekomst. Fleksibilitet af rollebaseret installationer i Azure giver dig mulighed for nemt at tilpasse dit program til virksomhedens behov.

Det er fælles for har rollen forekomster i din hostet tjeneste kommunikere med hinanden. For eksempel rollen websted accepterer en kundeordre, men derefter den sender rækkefølgen behandling til de behandling rolle forekomster. Den bedste måde at overføre arbejde formular ét sæt rolle forekomster til et andet sæt forekomster bruger den kø teknologi, der leveres af Azure, enten kø Service eller Bus servicekøer. Brug af en kø er en vigtig del af tekstenheden her. Køen kan tjenesten hostet skalere sine roller, så du kan fordele arbejdsbelastningen mod omkostninger uafhængigt af hinanden. Hvis antallet af beskeder i køen øges over tid, kan du skalere op antallet af forekomster af behandling rolle. Hvis antallet af beskeder i køen reduceres over tid, kan du skalere ned antallet af forekomster af behandling rolle. Denne måde, du betaler kun for de forekomster, der kræves for at håndtere faktisk arbejdsbelastningen.

Køen indeholder også pålidelighed. Når skalering ned antallet af forekomster af behandling af rolle, beslutter Azure hvilke forekomster til at afslutte. Det kan beslutte at afslutte en forekomst, der er placeret i midten behandling af en kø meddelelse. Da behandlingen af meddelelsen ikke fuldføres korrekt, bliver meddelelsen dog synligt igen til en anden rækkefølge behandling rolle forekomst, der henter den og behandler den. Meddelelser er på grund af kø meddelelse synlighed garanti for at få behandlet til sidst. Køen fungerer også som en belastningsjustering ved at distribuere effektivt dens meddelelser til alle rolle forekomster, der anmoder om meddelelser fra den.

Efter websted rolle forekomster, kan du overvåge trafikken kommer til dem og beslutter at skalere antallet op eller ned samt. Køen kan du tilpasse antallet af websted rolle forekomster uafhængigt af rolle behandling af forekomster. Det er meget effektive og giver dig en masse fleksibilitet. Naturligvis, hvis dit program består af flere roller, kan du tilføje yderligere køer som bane vejen til at kommunikere mellem dem for at udnytte det samme skalering og omkostninger fordele.

## <a id="defandcfg"> </a>Hostet tjenestedefinitionen og konfiguration

Anvender en hostet tjeneste for Azure, skal du også har en tjeneste definitionsfil og en tjeneste konfigurationsfil. Begge disse filer er XML-filer, og de gør det muligt at angive med en erklæring installationsindstillinger for din hostet tjeneste. Tjenesten definitionsfil beskriver alle de roller, der udgør din hostet tjeneste, og hvordan de kommunikere. Konfigurationsfil service beskrives antallet af forekomster for hver rolle og indstillinger, der bruges til at konfigurere hver rolle forekomst.

## <a id="def"> </a>Definitionsfil Service

Filen er en XML-fil, der beskriver de forskellige roller, der udgør din fuldført program, som jeg tidligere nævnt definitionen af tjenesten (CSDEF). Fuldført skemaet for XML-filen kan findes her: [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx] [].
Filen CSDEF indeholder et WebRole eller WorkerRole element for hver rolle, du vil i programmet. Implementere en rolle som en web rolle (ved hjælp af elementet WebRole), betyder det, koden skal køres på en rolle-forekomst, der indeholder Windows Server 2008 og Internet Information Server (IIS).
Implementere en rolle, som en kollega rolle (ved hjælp af elementet WorkerRole) betyder, at den rolle forekomst skal være Windows Server 2008 på den (IIS bliver ikke installeret).

Du kan selvfølgelig oprette og installere en kollega rolle, der bruger andre måder for at lytte til indgående anmodninger om web (for eksempel din kode kan oprette og bruge en .NET HttpListener). Da alle rolle forekomster kører Windows Server 2008, skal kan din kode udføre handlinger, der er normalt tilgængelig for et program, der kører på Windows Server
2008.

For hver rolle angiver du den ønskede VM størrelse, der skal bruge forekomster af rollen. Tabellen nedenfor viser de forskellige VM-størrelser, der er tilgængelige i dag og attributterne i hver:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**VM størrelse**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disk**

</td>
<td>
**Spidsbelastning netværk i/o-**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Ekstra små**

</td>
<td>
1 x 1,0 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Lille**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Mediet**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Store**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Ekstra stor**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Du betaler hver time for hver VM, du bruger som en forekomst af rolle, og du er også betale for data, at sende din rolle forekomster uden for datacenteret. Du betaler ikke for at angive datacenteret data. Du kan finde flere oplysninger [Azure priser] []. Generelt er det anbefales at bruge mange forekomster af lille rolle i modsætning til et par store forekomster, så dit program er mere fleksibel over for fejl. Når alle færre rolle forekomster, du har, mere uoverskuelige en fejl i en af dem i dit overordnede program. Som nævnt før, skal du også installere mindst to forekomster for hver rolle Hvis du vil have 99.95% serviceaftalen Microsoft yder.

Service-definitionsfil (CSDEF) er også, hvor du vil angive mange attributter om hver rolle i programmet. Her er nogle af elementerne mere tilgængelige for dig:

-   **Certifikater**. Du kan bruge certifikater til at kryptere data, eller hvis din webtjeneste understøtter SSL. En hvilken som helst certifikater skal overføres til Azure. Du kan finde yderligere oplysninger finder [Administration af certifikater i Azure][]. XML-indstillingen installerer certifikater, der tidligere blev overført til den rolle forekomst lager, så de kan bruges af din programmet kode.

-   **Konfiguration af indstillingen navne**. For værdier, du vil din program(mer) at læse, mens du kører på en forekomst af rollen. Den faktiske værdi for konfigurationsindstillingerne er angivet i filen service konfiguration (CSCFG) som kan blive opdateret, når som helst uden at du skal geninstallere din kode. Du kan faktisk kode dine programmer på en sådan måde, til at registrere de ændrede konfigurationsværdier uden at der påløber en hvilken som helst nedetid.

-   **Skriv slutpunkter**. Her angiver du en hvilken som helst HTTP, HTTPS eller TCP slutpunkter (med porte), du vil vise til verden udenfor via din *præfiks*. cloadapp.net URL-adresse. Når Azure installerer din rolle, vil det konfigurere firewallen på forekomsten rolle automatisk.

-   **Interne slutpunkter**. Her kan du angive en hvilken som helst HTTP eller TCP-slutpunkter, der skal vises til andre rolle forekomster, der er installeret, som en del af dit program. Interne slutpunkter Tillad alle forekomster rolle i dit program tilladelse til at tale med hinanden, men er ikke tilgængelige på alle forekomster af rolle, der er uden for dit program.

-   **Importér moduler**. Du kan også installere disse nyttige komponenter på din rolle forekomster. Komponenter findes til diagnosticering overvågning, Fjernskrivebord og Azure forbinde (som gør det muligt for din rolle forekomst adgang til lokale ressourcer via en sikker kanal).

-   **Lokale lager**. Dette tildeler en undermappe på den rolle forekomst for dit program du bruger. Det er beskrevet mere detaljeret i artiklen [Data lagerplads tilbud i Azure][] .

-   **Start opgaver**. Start opgaver giver dig en metode til at installere nødvendige komponenter på en forekomst af rolle, som den starter. Opgaverne, der kan køre øgede som administrator, hvis det er nødvendigt.

## <a id="cfg"> </a>Konfigurationsfil Service

Service-konfigurationsfil (CSCFG) er en XML-fil, der beskriver indstillinger, som kan ændres uden geninstallation af dit program. Fuldført skemaet for XML-filen kan findes her: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
Filen CSCFG indeholder et rolle element for hver rolle i programmet. Her er nogle af de elementer, du kan angive i filen CSCFG:

-   **OS-Version**. Denne attribut kan du vælge den operativsystem (OS)-version, du vil bruges til alle forekomster for rolle, der kører din programkode. Denne OS kendes som *Gæst OS*, og hver ny version omfatter de nyeste sikkerhedsopdateringer og opdateringer tilgængelige på det tidspunkt, som gæst OS frigives. Hvis du angiver attributværdien osVersion til "\*", og derefter Azure opdateringer automatisk gæst OS på hver af din rolle forekomster, efterhånden som gæst OS versioner bliver tilgængelige. Dog kan du vælge af automatiske opdateringer ved at vælge en bestemt gæst OS-version. For eksempel angive, at osVersion attributten til en værdi på "WA-GÆST-OS-2,8\_201109-01" medfører, at alle forekomster din rolle at få det er beskrevet på denne webside: [http://msdn.microsoft.com/library/hh560567.aspx][]. Du kan finde flere oplysninger om gæst OS versioner, [Administration af opgraderinger til Azure gæster OS].

-   **Forekomster**. Dette element værdi angiver antallet rolle forekomster ønskede klargjort kører koden for en bestemt rolle. Du kan overføre en ny CSCFG-fil til Azure (uden geninstallation af dit program), og det er derfor trivially nemt at ændre værdien for dette element, og overfør filen CSCFG for at øge eller mindske antallet af rolle forekomster, der kører din programkode dynamisk. Dette kan du nemt at skalere dit program, op eller ned for at opfylde faktisk arbejdsbelastningen mens også styre, hvor meget du betaler for kører rolle forekomster.

-   **Konfiguration af indstillingsværdier**. Dette element angiver værdier for indstillinger (som defineret i filen CSDEF). Din rolle kan læse disse værdier, mens den kører. Disse indstillinger værdier bruges typisk til strenge til SQL-Database eller til Azure-lager, men de kan bruges til andre formål, du ønsker.

## <a id="hostedservices"> </a>Oprettelse og implementere en hostet tjeneste

Oprette en hostet tjeneste kræver, at du først gå til [Azure Management Portal] og klargøre en hostet tjeneste ved at angive et præfiks, du DNS og datacenteret i sidste ende ønskede din kode, der kører. Derefter i dit udviklingsmiljø skal du oprette din service-definitionsfil (CSDEF), opbygge din programkode og pakke (zip) alle disse filer til en tjeneste-pakkefilen (CSPKG). Du skal også forberede din service-konfigurationsfil (CSCFG). Hvis du vil installere din rolle, kan du overføre filerne CSPKG og CSCFG med Azure Service Management API. Når installeret, Azure, vil klargøring rolle forekomster i datacenteret (baseret på konfigurationsdataene), Pak din programkode i pakken, kopiere det til de rolle forekomster og starte forekomster. Nu er din kode oppe at køre.

I nedenstående figur vises de CSPKG og CSCFG filer, du opretter på computeren udvikling. Filen CSPKG indeholder filen CSDEF og koden for to roller. Når du har overført CSPKG og CSCFG filerne med Azure Service Management API, opretter Azure rolle forekomster i et datacenter. I dette eksempel filen CSCFG angivet, Azure skal oprette tre forekomster af rolle \#1 og to forekomster af rolle \#2.

![Billede af][5]

Du kan finde flere oplysninger om installation af artiklen opgradering og omkonfigurere dine roller [installere og opdatere Azure-programmer][] .<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referencer

-   [Oprette en hostet tjeneste til Azure][]

-   [Administration af tjenester i Azure][]

-   [Overføre programmer til Azure][]

-   [Konfigurere et Azure-program][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Skrevet af Jeffrey Richter (Wintellect)</p>

</div>

  [Azure-program Model fordele]: #benefits
  [Hostet Service Grundkoncepterne "]: #concepts
  [Hostet Service Designovervejelser]: #considerations
  [Designe dit program for skala]: #scale
  [Definitionen hostet tjenesten og konfiguration]: #defandcfg
  [Tjenesten definitionsfil]: #def
  [Tjenesten konfigurationsfil]: #cfg
  [Oprette og implementere en hostet tjeneste]: #hostedservices
  [Referencer]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Konfigurere et brugerdefineret domænenavn i Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Data lagerplads tilbud i Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Administrere certifikater i Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.Microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.Microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Administrere opgraderinger til Azure gæster OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Azure Management-portalen]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Installation og opdatering af Azure-programmer]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Oprette en hostet tjeneste til Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Administration af tjenester i Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Overføre programmer til Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Konfigurere et Azure-program]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
