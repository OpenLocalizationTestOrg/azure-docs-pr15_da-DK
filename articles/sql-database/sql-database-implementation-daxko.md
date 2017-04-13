<properties
   pageTitle="Azure SQL Database Azure Casestudie - Daxko/CSI | Microsoft Azure"
   description="Få mere at vide om, hvordan Daxko/CSI anvender SQL-Database, at fremskynde sin udviklingscyklus og for at forbedre computerens kundeservice og ydelse"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI bruges Azure at fremskynde sin udviklingscyklus og for at forbedre computerens kundeservice og ydelse

![Daxko/CSI Logo](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI Software gælder en udfordring: dens kunden basis af fitnesspræsentation og fritid centrum vokser hurtigt, sammen takket være en vellykket løsning dens omfattende enterprise-software, men holde med IT-infrastruktur behov for den pågældende voksende kunde base blev test firmaets IT-medarbejdere. Virksomhedens blev mere og mere begrænset af stigende handlinger spild, især til administration af dens voksende databaser. Pågældende handlinger omkostninger dårligere, klippe til udviklingsressourcer for nye initiativer, som nye mobilitetsfunktioner til firmaets software.

Ifølge David Molina, direktør af produktudvikling på Daxko/CSI angivet Azure CSI Software med platform som en tjeneste (PaaS) modellen, som det er nødvendigt at forenkle database management, øge skalerbarhed og frigøre ressourcer til at fokusere på software i stedet for ops. "Azure SQL-Database er en god indstilling til os. Ikke behøver at bekymre dig om at bevare en SQL Server, en sekundær klynge og alle andre infrastrukturen skal kunne ideel til os."

Siden overføre til Azure, CSI Software skal have en handlinger personale af kun to til at administrere over 600 kunde-databaser. Virksomheden bruger Azure SQL-Database elastiske grupper til at flytte kunde databaser, der er baseret på størrelse og har brug for.

Molina fortsætter, "vores kunder mente ændringen med det samme. Før elastiske grupper havde de undertiden timeout og andre problemer under burst perioder. Med Azure elastiske grupper, kan de burst efter behov og bruge softwaren uden problemer."

Ud over at forbedre ydeevnen for kunder, databasen Azure elastiske grupper frigjort CSI Software ressourcer til at fokusere på udvikle nye tjenester og funktioner, i stedet for håndtering af handlinger og administration. Disse IT-ressourcer hjulpet CSI Software forbedre enterprise software tilbyder SpectrumNG, for at deltage anden motion medlemmer, forbedre effektiviteten personale, og give personale og medlemmer mobiladgang til interaktive opgaver og meddelelser i realtid.

Azure har også hjulpet CSI Software fremskynde og forbedre udvikling og kvalitetskontrol (QA) ved at aktivere automatisering indstillinger. Opbyg ledere kan med firmaets Azure implementering pakke op komponenter ved at klikke på en knap. Som Molina forklarer, "som en del af udgivelsescyklus QA er nu kunne installere på et testmiljø i Azure, hvor tæt efterligner vores fremstilling stablen. Vi kan implementere builds med det samme vores Udviklingscenter miljø til behandler ændringer. Der er en stor vinde os, fordi vi ikke havde fungerer ensartet til test før,".

## <a name="offloading-to-the-cloud"></a>Overføre til skyen

Inden du går til skyen, havde CSI Software blev bygget op sin egen multiprofiler infrastruktur i en lokal datacenter i Sørensen. Som virksomhedens udvidet gælder stigende voksende pains fra køber, klargøring og vedligeholde alle hardware og software, der er nødvendige for at understøtte kunderne. IT personale for at håndtere handlinger blev en anden flaskehals, ført til reduceret klargøring af nye ressourcer og distribution af nye tjenester til kunder.

CSI Software set i skyen indstillinger til at fjerne spildet, så den kan fokusere på koden, i stedet for dens handlinger. Virksomheden har opdaget, tilbyder mange af udbyderne af øverste skyen kun infrastruktur som en tjeneste (IaaS) løsninger, der stadig kræver en stor IT-medarbejdere til at administrere IaaS stablen. I sidste ende bestemmes CSI Software, Azure PaaS løsning er de bedste egnede til dets behov. Molina forklarer, "Azure får hardware og softwaren væk, så vi kan fokusere på vores software tilbud, mens du Reducer vores IT-omkostningerne."

## <a name="making-the-transition-to-azure"></a>Gøre overgangen til Azure

Når du har valgt Azure for sin PaaS løsning, startede CSI Software overføre dens back end-infrastruktur og databaser til skyen. Før du Azure, SpectrumNG kunder, der kræves for at installere en klientprogrammet, der har kommunikeret med en Windows Communication Foundation (WCF) tjeneste på back-end. Efter Molina indbygget"Selvom nogle kunder hostet alt i deres egen datacentre vi ud af produktet, der skal være multiprofiler. Vi hostet alt i et datacenter i far, ved hjælp af SQL Server som datalageret.

"Vores produkt tilbyder også medtages et medlem mod webportalen (skrevet på ASP.net), der blev udviklet til at være hvid mærket, så det svarer til kundens web tilstedeværelse og en SOAP API til at understøtte online siderne og integration fra tredjepart".

Overførsel til skyen ikke tager lang tid for arkitekturen. Efter Molina, "Størstedelen af den indsats, behandles at ændre den måde, at vi læser config filoplysninger, en ændring af centralt forbindelsesstreng, og automatisere emballagen, overførsel og installation af vores versioner."

For at udvikle build automatisering, bruges CSI Software teknikere Azure PowerShell og REST API'er til at oprette pakker og overføre dem til et midlertidigt miljø til version hver NAT..
Overordnede overgangen til en skybaseret Azure-installation gik hurtigt og nemt til CSI Software IT-teamet. Molina forklarer, "i alle, vi skulle et beta-miljø i skyen inden for tre eller fire uger tage på projektet. Det var en overraskende gevinst til os."

Når du konfigurerer og test af miljøet, CSI Software startede overføre kunder. For kunder, der allerede bruger CSI Software vært, blev overgangen næsten problemfri. For kunder skifte fra en lokal installation til overførsel til skyen tog nogle ekstra tid, men er stadig primært problemer ledig til både kunder og CSI Software.

For nye kunder og CSI Software's IT-medarbejdere bruge følgende processen til indbyggede dem til Azure:

1.  Azure PowerShell-scripts, der bruges til at dreje op en ny database til kunde. alle kunder starte for et premium trin til at sikre tilstrækkelig indledende overførsel for overgangen.
2.  Når det er muligt, bruger CSI Software Guiden Azure SQL-overførsel til at flytte eksisterende data til en forekomst af Azure SQL-Database.
3.  Microsoft SQL Server Integration Services (SSIS) bruges til sidst skal afstemme eventuelle uoverensstemmelser i dataene eller til at udføre en hvilken som helst Dataoprydning efter behov.

I dag, er om 99 procent af CSI Software kunder hostet i Azure, på tværs af fire internationale datacentre (nord Central, syd Central, Øst og vest). Har datacentre i hver enkelt kundes geografisk område, der bevares ventetid på et minimum.

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Azure elastiske database grupper frigøre IT-ressourcer

Flere funktioner i Azure har hjulpet CSI Software Skift i at blive infrastruktur og drift fokuseret til at blive funktion og udvikling fokuseret. Måske er den største fordel fra elastiske database grupper.

Aktuelt leverer CSI Software om 550 databaser til kunder. Det var svært at administrere så mange databaser i et niveau struktur før elastiske grupper. OPS ledere havde tildele ydeevne niveauer, der er baseret på burst behovene hos kunder, som kræves betydeligt IT-ressource spild. Med elastiske database grupper, kan administratorer tildele lejere, en premium eller standard puljen, efter behov, og flyt derefter kunder, der er baseret på størrelse og har brug for. Kunder mente virkningerne af elastiske database grupper næsten samme. kunder havde timeout og andre problemer under burst brugen perioder før elastiske grupper, men med elastiske grupper kunder kan opleve aktivitet lyseksplosioner efter behov, og de kan fortsætte med at bruge SpectrumNG uden problemer.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure Active geografisk-gentagelse øger rapportering

Flere CSI Software kunder også at drage fordel af Azure Active geografisk-gentagelse. Med aktive geografisk-gentagelse, kan op til fire læsbare sekundær databaser konfigureres i samme eller forskellige datacenter områder. CSI Software gør brug af aktive geografisk-gentagelse på to måder: først sekundær databaser er tilgængelige i forbindelse med et datacenter afbrydelse eller med at oprette forbindelse til den primære database og, for sekundær databaser kan læses og kan bruges til at offload skrivebeskyttet arbejdsmængder som rapportering job. Nogle CSI Software kunder anvender denne fordel for at fremskynde reporting arbejdsprocesser.

## <a name="csi-software-application-logic-and-architecture"></a>CSI Software programlogik og arkitektur

SpectrumNG bruger web roller. Da programmet er flere lejer, bruges en WCF-tjeneste til at håndtere forbindelse første gang anmodningen fra kunder. Som Molina angiver, identificerer"anmodningen hver kunde, så kan vi oprette en forbindelsesstreng ud for deres databaser til at gøre hvad vi skal gøre."

For web-niveauet i dens tjeneste udnytter CSI Software Azure automatisk skalering, baseret på dato og klokkeslæt. Tilgængelige ressourcer øges automatisk for at imødekomme højere brugen i arbejdstiden, efter tidszonen for hver regionale datacenter. Ressourcer er også angive at indskrænke på weekender, når kundernes behov er lavere.

     
![Daxko/CSI arkitektur](./media/sql-database-implementation-daxko/figure1.png)

Figur 1. En cloud services arbejder rolle tegner strukturerede data fra Azure SQL-Database og semistrukturerede data fra tabellagring. SpectrumNG brugere interagere med, at data via en skybaseret services web rolle.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Med webapps og en web-plan niveau for mobilapps

Brug af Azure SQL-Database frigjort ressourcer til CSI Software til at aktivere nye initiativer, herunder en komplet mobil platform baseret på en brugerdefineret API hostet i Azure webapps. Platformen aktiverer anden motion medlemmer og personale at bruge mobilenheder til at kontrollere tidsplaner, reserver klasser og modtage meddelelser.

Platformen bruger tjenesten rundt arkitektur (SOA) til at tage et enkelt objekt – som et POS-system (POS) eller et salg system – flytte den løbende til en anden web-plan, og dreje en tjeneste til at understøtte komponenten, mens alt andet på den oprindelige web-plan. Denne mulighed giver CSI Software enormt fleksibilitet, og det hjælper med at holde omkostningerne.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure kan CSI Software udviklere fokus på apps og tjenester

Azure SQL-Database er ikke bare en stor fordel for SpectrumNG kunder, der nyde det hurtige og pålidelig service, det er også en stor vinde CSI Software IT-medarbejdere og udviklere. Ved at overføre ops til Azure i skyen, CSI Software reduceret dens omkostninger for ressourcer og infrastruktur, hurtigere meget dens udvikling skifter, og ikke længere behov micromanage databaser til optimering af ydeevne til dens lejere.

## <a name="more-information"></a>Få mere at vide

- Hvis du vil vide mere om Azure elastiske database grupper skal du se [elastiske database grupper](sql-database-elastic-pool.md).

- Hvis du vil vide mere om Databaseværktøjer og elastiske skalering skal du se [elastiske Databaseværktøjer og elastiske skalering](sql-database-elastic-scale-get-started.md).

- Hvis du vil vide mere om overførsel af en SQL Server-database skal du se [Azure SQL overførsel guiden](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Hvis du vil vide mere om aktive geografisk-gentagelse, skal du se [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md).

- Hvis du vil vide mere om Web roller og arbejder roller, skal du se [arbejder roller](../fundamentals-introduction-to-azure.md#compute). 

- Hvis du vil vide mere om Azure Service Bus skal du se [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).

- Få flere oplysninger om automatisk skala under [Skalering skytjenester](../cloud-services/cloud-services-how-to-scale.md).
