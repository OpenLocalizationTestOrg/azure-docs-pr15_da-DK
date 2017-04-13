<properties   
    pageTitle="Overføre F.eks eller-Brugergrupper løsninger til BizTalk Services tekniske brugerhåndbog | Microsoft Azure"
    description="Overføre eller-Brugergrupper til MABS; Microsoft Azure BizTalk-tjenester"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Overføre F.eks eller-Brugergrupper løsninger til BizTalk-tjenester: tekniske vejledning

Forfatter: Tim Wieman og Nitin Mehrotra

Korrekturlæsere: Karthik Bharthy

Skrevet ved hjælp af: Microsoft Azure BizTalk-tjenester – februar 2014 slip.

## <a name="introduction"></a>Introduktion

(Eller-Brugergrupper) er et af de mest almindeligt midler hvilke virksomheder exchange data elektronisk, også hedder Business to Business eller B2B transaktioner. F.eks har haft eller-Brugergrupper understøttelse af over en løbet ti år siden den oprindelige BizTalk bryde frigivelse. Microsoft fortsat understøttelse af eller-Brugergrupper løsninger på Microsoft Azure-platformen med BizTalk-tjenester. B2B transaktioner er hovedsageligt ekstern i forhold til en organisation, og det vil sige det er nemmere at implementere, hvis det er implementeret på en skyen platform. Microsoft Azure leverer denne egenskab via BizTalk Services.

Da nogle kunder ser på BizTalk-tjenester som en "greenfield" platform for nye eller-Brugergrupper løsninger, har mange kunder aktuelle BizTalk-serveren eller-Brugergrupper løsninger, de vil overføre til Azure. Fordi BizTalk-tjenester eller-Brugergrupper er designet er baseret på de samme vigtige objekter som BizTalk-serveren eller-Brugergrupper arkitektur (handel partnere, objekter, aftaler), det muligt at overføre BizTalk-serveren eller-Brugergrupper elementer til BizTalk-tjenester.

I dette dokument beskrives nogle af forskellene i forbindelse med overflytning BizTalk-serveren eller-Brugergrupper elementer til BizTalk-tjenester. Dette dokument forudsætter et vist kendskab til BizTalk-serveren eller-Brugergrupper behandling og handel Partner aftaler. Du kan finde flere oplysninger om BizTalk-serveren eller-Brugergrupper, [Handel Partner administration ved hjælp af F.eks](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Hvilken version af BizTalk-serveren eller-Brugergrupper elementer kan overføres til BizTalk-tjenester?

Modulet BizTalk-serveren eller-Brugergrupper blev betydeligt forbedret til BizTalk Server 2010, når den er blevet ændrede medtage partnere, profiler og aftaler. BizTalk Services bruger den samme model til at organisere handel partnere og business divisioner i disse handel partnere. Som et resultat, er overføre eller-Brugergrupper elementer fra BizTalk Server 2010 og nyere versioner til BizTalk-tjenester, en meget mere direkte videresende proces. Hvis du vil overføre eller-Brugergrupper elementer, der er knyttet til versioner før BizTalk Server 2010, skal du først opgradere til BizTalk Server 2010 og derefter overføre din eller-Brugergrupper elementer til BizTalk-tjenester.

## <a name="scenariosmessage-flow"></a>Scenarier/meddelelse Flow

Som med F.eks, er eller-Brugergrupper behandling i BizTalk Services opbygget omkring en handel Partner (TPM)-løsning. Løsningen TPM har følgende vigtige komponenter:

- Handel partnere, som repræsenterer organisation i en B2B transaktion.
- Profiler, som repræsenterer divisioner i en handel partner.
- Handel partner aftaler eller aftaler, som repræsenterer business aftalen mellem to partnere/profiler.

I nedenstående illustration vises ligheder samt forskelle mellem en BizTalk-serveren eller-Brugergrupper løsning og BizTalk-tjenester eller-Brugergrupper løsning:

![][EDImessageflow]

De vigtigste forskelle og ligheder mellem en eller-Brugergrupper løsning flow i F.eks og BizTalk Services er:

- Ligesom F.eks benytter en EDIReceive pipeline til at modtage en meddelelse eller-Brugergrupper og en EDISend pipeline for at sende en meddelelse eller-Brugergrupper, bruger BizTalk Services en modtager eller-Brugergrupper bro modtage og eller-Brugergrupper sende bro udsender eller-Brugergrupper meddelelser. I F.eks, rørledningerne er knyttet til en aftale ved hjælp af send eller modtage porte. BizTalk-tjenesterne, aftalen selv angiver send eller modtage bro.
- I F.eks, når EDIReceive rørledningen behandler meddelelsen eller-Brugergrupper er meddelelsen gemt i en SQL Server-database. EdiSend pipeline derefter opfanger meddelelsen fra SQL Server-databasen, behandler den og derefter sender den til handel partner.

    BizTalk-tjenesterne, når eller-Brugergrupper modtage bro behandler eller-Brugergrupper meddelelsen, skal den dirigerer meddelelsen til en ekstern proces. Eksterne processen kan køre på Microsoft Azure eller i det lokale miljø. Eksterne processen skal omdirigere meddelelsen til eller-Brugergrupper send bro; send bro trække ikke uden videre meddelelsen. Efter behandling af meddelelsen, dirigerer eller-Brugergrupper send bro meddelelsen til handel partneren.

BizTalk Services indeholder en nemt bruge konfiguration oplevelse for at hurtigt at oprette og installere en B2B aftale mellem handel partnere uden konfiguration af et Microsoft Azure beregne forekomster (Web eller arbejder roller), et Microsoft Azure SQL-databaser eller et Microsoft Azure-lager konti. Mere komplekse scenarier kræver, at knytte i arbejdsprocesser eller andre service behandling "rundt om kanterne" i en handel Partner-aftale, det vil sige, før eller efter handel Partner aftale eller-Brugergrupper bro behandling. De følgende sekvens af hændelser indtræffer i detaljer, under meddelelsen eller-Brugergrupper behandling i BizTalk-tjenester.

1. En meddelelse eller-Brugergrupper modtages fra handel Fabrikam-partner.  For at modtage eller-Brugergrupper meddelelser fra handel partnere, understøtter BizTalk Services transportprotokoller som FTP-, SFTP, AS2 og HTTP/S.

2. Handel partner aftale modtage side behandling opdeler eller-Brugergrupper meddelelsen til XML-format.  Du kan distribuere afmonterede eller-Brugergrupper meddelelsen (i XML-format) til Service Bus slutpunkter som et slutpunkt Service Bus Relay, Service Bus emne, Service Bus kø eller en bro BizTalk-tjenester.

3. Afmonterede XML-meddelelser kan derefter modtaget fra slutpunktet for yderligere brugerdefinerede behandling.  Disse slutpunkter kunne behandles af en lokal komponent eller en Microsoft Azure beregne forekomst for yderligere processen meddelelse i en Windows-arbejdsproces (Windows Firewall) eller Windows Communication Foundation (WCF) tjeneste, f.eks.

4. "Send side behandling" af samhandelspartneren aftale derefter samler XML-meddelelsen til eller-Brugergrupper format og sender den til handel partner Contoso.  Til afsendelse af meddelelser eller-Brugergrupper for de partnere, handel, understøtter BizTalk-tjenester de samme protokoller som dem, der bruges til at modtage meddelelser eller-Brugergrupper.

Dette dokument yderligere indeholder grundlæggende vejledning på overføre nogle af de forskellige BizTalk-serveren eller-Brugergrupper elementer til BizTalk-tjenester.

## <a name="sendreceive-ports-to-trading-partners"></a>Send/modtag porte til udveksling partnere

I F.eks du konfigurere modtage steder og modtag porte for at modtage eller-Brugergrupper/XML-meddelelser fra handel partnere, og du konfigurere Send porte til at sende eller-Brugergrupper/XML-meddelelser til handel partner. Du knytter derefter op portene til en handel partneraftale ved hjælp af Administrationskonsol til BizTalk-Server. BizTalk-tjenesterne, de placeringer, hvor du modtager meddelelser fra handel partnere og hvor du sender meddelelser til udveksling partnere er konfigureret som en del af handel partneraftalen selv (som del af Transport indstillinger) på portalen BizTalk-tjenester.  Så har du virkelig ikke "send porte" og "modtager placeringer", som repræsenterer i sig selv i BizTalk-tjenester. Du kan finde flere oplysninger [Oprette aftaler](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Rørledninger (broer)

I BizTalk-serveren eller-Brugergrupper er rørledninger meddelelse behandling enheder, der kan også indeholde brugerdefineret logik til bestemte behandlingsfunktioner, som kræves af programmet. For BizTalk-tjenester vil tilsvarende en bro eller-Brugergrupper. Men BizTalk-tjenesterne, nu skal eller-Brugergrupper broer er "afsluttet".  Det vil sige, kan du tilføje din egen brugerdefinerede aktiviteter til en eller-Brugergrupper bro. En brugerdefineret behandling udføres uden for eller-Brugergrupper bro i dit program, enten før eller efter meddelelsen indsætter bro konfigureret som en del af handel Partner aftale. EAI broer har mulighed for at gøre brugerdefinerede behandling. Hvis du vil brugerdefinerede behandling, kan du bruge EAI broer, før eller efter meddelelsen behandles af eller-Brugergrupper bro. Se, [hvordan du medtage brugerdefineret kode i broer](https://msdn.microsoft.com/library/azure/dn232389.aspx)kan finde flere oplysninger.

Du kan indsætte et Udgiv/Abonner flow med brugerdefineret kode og/eller ved hjælp af Service Bus messaging køer og emner, før handel partneraftalen modtager meddelelsen, eller når aftalen behandler meddelelsen og sender den til et Service Bus slutpunkt.

Se **Scenarier/meddelelse Flow** i dette emne for meddelelsen flow mønster.

## <a name="agreements"></a>Aftaler

Hvis du er bekendt med den BizTalk-Server 2010 sportsmotiver Partner aftaler bruges til eller-Brugergrupper behandling, derefter se BizTalk Services handel partner aftaler meget bekendt for dig. De fleste af indstillingerne for aftale er de samme og bruge den samme terminologi. I nogle tilfælde indstillingerne aftale er meget nemmere sammenlignet med de samme indstillinger i F.eks. Microsoft Azure BizTalk Services understøtter X12, EDIFACT og AS2 transport.

Microsoft Azure BizTalk Services indeholder også en **TPM Data overflytningsværktøj** til overførsel handel partnere og aftaler fra BizTalk-Server handel Partner modul BizTalk Services-portalen. Værktøjet TPM dataoverførsel er tilgængeligt som en del af en pakke med værktøjer, som kan hentes fra [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Pakken indeholder også en vigtige oplysninger, der indeholder instruktioner om, hvordan du bruger værktøjet og grundlæggende oplysninger om fejlfinding for værktøjet.

## <a name="schemas"></a>Ved hjælp af skemaer

BizTalk Services indeholder eller-Brugergrupper skemaer, der kan bruges i BizTalk Services-løsninger.  Desuden kan BizTalk-serveren eller-Brugergrupper ved hjælp af skemaer også bruges sammen med BizTalk-tjenester, fordi noden i roden af eller-Brugergrupper skemaet er den samme på tværs af F.eks samt BizTalk-tjenester. Dermed, du vil kunne direkte tage dine BizTalk-serveren eller-Brugergrupper skemaer og bruge dem i de eller-Brugergrupper løsninger, du udvikler ved hjælp af BizTalk-tjenester. Du kan også hente ved hjælp af skemaerne fra [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Kort (transformeringer)

Kort i F.eks kaldes transformationer i BizTalk-tjenester. Overføre kort fra F.eks til BizTalk-tjenester kan være en af de mere komplekse opgaver til at opnå (afhængigt af kort kompleksitet). Værktøjet tilknytning, der bruges til BizTalk-tjenester er forskellig fra BizTalk mapper. Selvom mapperen ligner hovedsageligt på samme måde, er det underliggende kort format anderledes. Functoids (kaldet **Kort handlinger** i BizTalk Services) tilgængelige for brugerne, der er forskellige samt.  Du kan ikke i kraft direkte bruge et BizTalk-kort i BizTalk-tjenester. Ikke alle tilgængelige i F.eks functoids er også tilgængelige kort handlinger i BizTalk-tjenester.

### <a name="new-transform-operations"></a>Ny transformationshandlinger

Mens på listen over kort transformationshandlinger tilgængelige kan virke helt forskellige fra F.eks mapper, har BizTalk Services transformerer nye måder at udføre de samme opgaver. For eksempel har BizTalk Services transformerer **Listen handlinger** . Dette er ikke tilgængelig i BizTalk mapper.  **Liste over handlinger** gør det muligt at oprette og arbejde på en "liste", hvor en liste er et sæt af elementer (også kaldet "række"), og hvor hvert element kan have flere medlemmer (også kaldet "kolonner").  Du kan sortere listen skal du vælge elementer, der er baseret på en betingelse, osv.

Et andet eksempel de nye funktioner i BizTalk Services transformerer er de **Løkke handlinger**.  Det er svært at oprette indlejrede løkker i F.eks mapper.  Derfor tilføjes løkke kort handlinger for BizTalk-tjenester transformationer.

Endnu er et andet eksempel handlingen **If-Then-Else** -udtryk kort.  At gøre en if-then-else handling var muligt i BizTalk mapper, men det kræver flere functoids til at udføre en tilsyneladende enkelt opgave.

### <a name="migrating-biztalk-server-maps"></a>Overføre F.eks kort

Microsoft Azure BizTalk Services indeholder et værktøj til at overføre F.eks knyttes til BizTalk Services transformationer. **BTMMigrationTool** er tilgængelige som en del af pakken **værktøjer** , der følger med de [BizTalk Services SDK hente](http://go.microsoft.com/fwlink/p/?LinkId=235057). Du kan finde flere oplysninger om værktøjet se [konvertere et BizTalk-kort til at transformere en BizTalk-tjenester](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Du kan også se på en stikprøve af Sandro Pereira, BizTalk MVP om, hvordan du [overfører F.eks kort til BizTalk Services transformationer](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orkestreringerne

Hvis du vil flytte F.eks organisering behandling til Microsoft Azure, skal orkestreringerne omskrives, fordi Microsoft Azure ikke understøtter kører F.eks orkestreringerne.  Du kan skrive organisering funktionaliteten i en Windows Workflow Foundation 4.0 (WF4)-tjeneste.  Dette vil være en komplet omskrivning, da der ikke er i øjeblikket ingen overførsel fra F.eks orkestreringerne til WF4. Her er nogle ressourcer for Windows arbejdsproces:

- [*Hvordan du integrerer en WCF-tjenesten med Bus servicekøer og emner arbejdsproces*](https://msdn.microsoft.com/library/azure/hh709041.aspx) ved Paolo Salvatori. 

- [ *Bygning apps med Windows Workflow Foundation og Azure* -session](http://go.microsoft.com/fwlink/p/?LinkId=237314) fra Build 2011 konferencen.

- [*Windows Workflow Foundation Developer Center*](http://go.microsoft.com/fwlink/p/?LinkId=237315) på MSDN.

- [*Windows Workflow Foundation 4 (WF4) dokumentation*](https://msdn.microsoft.com/library/dd489441.aspx) på MSDN.

## <a name="other-considerations"></a>Andre overvejelser

Følgende er nogle overvejelser, du skal foretage prøveversionen BizTalk-tjenester.

### <a name="fallback-agreements"></a>Fallback aftaler

BizTalk-serveren eller-Brugergrupper behandling har begrebet "Reserve aftaler".  BizTalk-tjenester fungerer **ikke** har en reserve aftale som hidtil.  Se BizTalk dokumentation emner [Feltet rolle af aftaler i behandling af eller-Brugergrupper](http://go.microsoft.com/fwlink/p/?LinkId=237317) og [konfiguration af Global eller reserve aftale egenskaber](https://msdn.microsoft.com/library/bb245981.aspx) for du kan finde oplysninger på sådan reserve aftaler bruges i F.eks.

### <a name="routing-to-multiple-destinations"></a>Routing til flere destinationer

BizTalk Services broer, i den aktuelle tilstand ikke understøtter routing af meddelelser til flere destinationer ved hjælp af en Publicer-abonnere model. I stedet kan du distribuere meddelelser fra en BizTalk-tjenester bro til en tjeneste Bus emnet, som kan derefter har flere abonnementer modtage meddelelser på mere end et slutpunkt.

## <a name="conclusion"></a>Konklusion

Microsoft Azure BizTalk Services er opdateret på almindelig milepæle for at tilføje flere funktioner og egenskaber. Med hver opdatering glæder vi os til at understøtte flere funktioner for at lette oprette til slut løsninger ved hjælp af BizTalk-tjenester og andre Azure teknologier.

## <a name="see-also"></a>Se også

[Enterprise udviklingsprogrammer med Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
