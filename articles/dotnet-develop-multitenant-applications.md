<properties
    pageTitle="Flere lejer Web Application mønster | Microsoft Azure"
    description="Find arkitektonisk oversigter og designe mønstre, der beskriver, hvordan du kan implementere et med flere lejer webprogram på Azure."
    services=""
    documentationCenter=".net"
    authors="wadepickett" 
    manager="wpickett"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/05/2015"
    ms.author="wpickett"/>

# <a name="multitenant-applications-in-azure"></a>Multiprofiler programmer i Azure

Et multiprofiler program er en delt ressource, der giver mulighed for forskellige brugere eller "lejere," til at få vist programmet, som hvis det var deres egen. Et typisk scenarie, der kan bruges til et multiprofiler program er et, hvor alle brugere af programmet en god ide at tilpasse brugeroplevelsen, men ellers har de samme grundlæggende forretningsbehov. Eksempler på store multiprofiler programmer er Office 365, Outlook.com og visualstudio.com.

Fra et program udbyderens perspektiv relaterer fordelene ved at multitenancy hovedsageligt til driften og omkostninger effektivitet. Én version af dit program kan imødekommer behovet hos mange lejere/kunder, så konsolidering af system administrationsopgaver som overvågning, justering af ydeevnen, softwarevedligeholdelse og sikkerhedskopiering af data.

Følgende indeholder en liste over de vigtigste mål og krav fra en udbyder perspektiv.

- **Artikel Provisioning**: Du skal kunne klargøre nye lejere for programmet.  Til multiprofiler programmer med et stort antal lejere er det som regel nødvendige for at automatisere denne proces ved at aktivere selvbetjening klargøring.
- **Vedligeholde**: Du skal kunne opgradere programmet og udføre andre opgaver til vedligeholdelse, mens flere lejere bruger den.
- **Overvågnings**: Du skal kunne overvåge programmet til enhver tid til at identificere problemer og foretage fejlfinding af dem. Dette omfatter overvågning hvordan hver lejer ved hjælp af programmet.

Et korrekt anvendt multiprofiler program indeholder følgende fordele til brugere.

- **Isolationsniveauet**: aktiviteter af individuelle lejere påvirker ikke brugen af programmet ved andre lejere. Lejere kan ikke få adgang til hinandens data. Den vises for lejeren, som hvis de har udelt adgang af programmet.
- **Tilgængelighed**: individuelle lejere vil programmet skal være konstant tilgængeligt måske med garantier, der er defineret i en SERVICENIVEAUAFTALE. Andre lejere aktiviteter skulle igen, ikke påvirke tilgængeligheden af programmet.
- **Skalerbarhed**: programmet skaleres til opfylde behovet af individuelle lejere. Tilstedeværelse og andre lejere handlinger skulle ikke påvirke ydeevnen af programmet.
- **Omkostninger**: omkostninger er lavere end kører et dedikeret, enkelt lejer program, da flere arkitekturer gør det muligt for Deling af ressourcer.
- **Timesedler**. Muligheden for at tilpasse programmet for en enkelt lejer på forskellige måder, som tilføjelse eller fjernelse af funktioner, ændre farver og logoer eller endda for at tilføje deres egne kode eller script.

Kort sagt, selvom der findes mange overvejelser, du skal tage hensyn til at levere en meget SVG tjeneste, er der også en række mål og krav, der er fælles for mange multiprofiler programmer. Nogle muligvis ikke relevant i bestemte scenarier og vigtigheden af individuelle mål og krav kan variere i de enkelte scenarier. Som en udbyder af multiprofiler programmet får du også krav til f.eks., og mål møde lejere mål og krav, rentabiliteten, fakturering, flere serviceniveauer, klargøring, vedligeholde overvågning og automatisering.

Se [vært for et med flere lejer program på Azure][]kan finde flere oplysninger om yderligere Designovervejelser af et multiprofiler program. Du kan finde oplysninger på almindelige data arkitektur mønstre af flere lejer software som en tjeneste (SaaS) databaseprogrammer [Designs til flere lejer SaaS programmer med Azure SQL-Database](./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure indeholder mange funktioner, så du kan løse de vigtigste problemer, når du designer en multiprofiler system.

**Isolationsniveauet**

- Segmentet websted lejere ved Host-headere med eller uden SSL-forbindelse
- Segmentet websted lejere ved forespørgselsparametre
- Webtjenester i arbejder roller
    - Arbejder roller. der typisk behandle data på back-end for et program.
    - Web roller, der typisk fungere som frontend for programmer.

**Lagerplads**

Datastyring som Azure SQL-Database eller Azure-lager tjenester som tjenesten tabel, som indeholder tjenester til opbevaring af store mængder ustrukturerede data og den Blob-tjeneste, der tilbyder tjenester til opbevaring af store mængder ustrukturerede tekst eller binære data som video, lyd og billeder.

- Sikring af Multitenant Data i SQL-Database, der er relevante SQL Server-logon per lejer.
- Bruger Azure tabeller til programmet ressourcer ved at angive en objektbeholder niveau access-politik, kan du mulighed for at justere tilladelser uden at udstede nye URL'S for de ressourcer, der er beskyttet med delt adgang signaturer.
- Azure køer for programmet ressourcer Azure køer bruges normalt til at drev behandling på vegne af lejere, men kan også bruges til at distribuere arbejde, der kræves til klargøringen eller administration.
- Bus servicekøer for programmet ressourcer, der skubber arbejde til en delt en tjeneste, kan du bruge en enkelt kø hvor hver lejer afsender kun har tilladelser (som afledt af krav, der er udstedt af ACS) for at overføre til køen, mens du kun modtagere fra tjenesten har tilladelse til at trække fra køen af data fra flere lejere.


**Forbindelse and Security Services**

- Azure Service Bus en SMS infrastruktur, der er placeret mellem programmer giver dem mulighed for at udveksle meddelelser i en løst Koblede metode til forbedret skala og fleksibilitet.

**Netværkstjenester**

Azure leverer flere netværk tjenester, der understøtter godkendelse og forbedre administrationen af dine tilknyttede programmer. Disse tjenester omfatter følgende:

- Azure virtuelt netværk kan du tildele og administrere virtuelt privat netværk (VPN) i Azure samt sikker sammenkæde dem med lokale IT-infrastruktur.
- Virtuel Network trafik Manager giver dig mulighed at indlæse balance indgående trafik på tværs af flere hostet Azure tjenester, uanset om de kører i det samme datacenter eller på tværs af forskellige datacentre over hele verden.
- Azure Active Directory (Azure AD) er en moderne, RESTEN-baseret tjeneste, som indeholder funktioner kontrolelement i identitet administration og adgang til dine programmer i skyen. Brug af Azure AD for programmet ressourcer Azure AD til gør det nemt kontrollere og godkende brugere kan få adgang til dine web-programmer og tjenester samtidig med funktionerne i godkendelse og tilladelse til at indregnes af din kode.
- Azure Service Bus giver en sikker messaging og data flow muligheder for at distribueres og hybrid programmer, som kommunikation mellem Azure hostet programmer og lokale programmer og tjenester, uden at kræve komplekse firewall og sikkerhed infrastruktur. Ved hjælp af tjenesten Bus Relay for programmet ressourcer til de tjenester, der vises som slutpunkter kan tilhøre lejeren (for eksempel hostet uden for systemet, som lokalt), eller de kan være services klargjort specifikt for lejeren (fordi følsomme, lejer-specifikke data overføres på tværs af dem).



**Klargøring af ressourcer**

Azure indeholder en række metoder klargøring nye lejere for programmet. Til multiprofiler programmer med et stort antal lejere er det som regel nødvendige for at automatisere denne proces ved at aktivere selvbetjening klargøring.

- Arbejder roller giver dig mulighed for at klargøre, og ophævelse klargøring per lejer ressourcer (såsom når en ny lejer tegn op eller annullerer), indsamle målepunkter for måling bruge og administrere skala efter en bestemt tidsplan eller svar med overskridelse af tærskler af KPI'er. Denne samme rolle kan også bruges til at overføre opdateringer og opgraderinger til løsningen.
- Kan bruges til at klargøre Beregn eller allerede initialiseret Azure BLOB storageressourcer for nye lejere samtidig med at objektbeholder niveau access politikker til at beskytte Beregn service pakker, Virtuelle billeder og andre ressourcer.
- Indstillinger for klargøring af SQL-Database ressourcer til en lejer, omfatter:

    -   DDL i scripts eller integreret som ressourcer i assemblies
    -   SQL Server 2008 R2 DAC pakker installeres ved hjælp af programmering.
    -   Kopiere fra en masterside referencedatabase
    -   Brug af database Import og eksport til at klargøre nye databaser fra en fil.



<!--links-->

[Vært for et med flere lejer program på Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
